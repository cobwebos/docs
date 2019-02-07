---
title: 将 Azure Active Directory 与 Azure Kubernetes Service 集成
description: 如何创建支持 Azure Active Directory 的 Azure Kubernetes Service (AKS) 群集。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/09/2018
ms.author: iainfou
ms.openlocfilehash: 78f00b00465b6d834f30411485a874d6d2116ea4
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2019
ms.locfileid: "55081239"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>将 Azure Active Directory 与 Azure Kubernetes Service 集成

可将 Azure Kubernetes Service (AKS) 配置为使用 Azure Active Directory (AD) 进行用户身份验证。 在此配置中，你可以使用自己的 Azure Active Directory 身份验证令牌登录到 AKS 群集。 此外，群集管理员可以根据用户标识或目录组成员身份来配置 Kubernetes 基于角色的访问控制 (RBAC)。

本文介绍如何部署 AKS 和 Azure AD 的先决条件、部署支持 Azure AD 的群集以及在 AKS 群集中创建简单的 RBAC 角色。

以下限制适用：

- 只有在创建新的启用 RBAC 的群集时，才能启用 Azure AD。 不能在现有 AKS 群集上启用 Azure AD。
- 不支持 Azure AD 中的来宾用户，例如，从其他目录使用联合登录。

## <a name="authentication-details"></a>身份验证详细信息

使用 OpenID Connect 向 AKS 群集提供 Azure AD 身份验证。 OpenID Connect 是构建在 OAuth 2.0 协议顶层的标识层。 有关 OpenID Connect 的详细信息，请参阅 [Open ID Connect 文档][open-id-connect]。

在 Kubernetes 群集内部，使用 Webhook 令牌身份验证来验证身份验证令牌。 Webhook 令牌身份验证作为 AKS 群集的一部分进行配置和管理。 有关 Webhook 令牌身份验证的详细信息，请参阅 [Webhook 身份验证文档][kubernetes-webhook]。

> [!NOTE]
> 将 Azure AD 配置用于 AKS 身份验证时，会配置两个 Azure AD 应用程序。 此操作必须由 Azure 租户管理员完成。

## <a name="create-server-application"></a>创建服务器应用程序

第一个 Azure AD 应用程序用于获取用户的 Azure AD 组成员身份。

1. 选择“Azure Active Directory” > “应用注册” > “新建应用程序注册”。

  为应用程序命名，选择“Web 应用/API”作为应用程序类型，然后为“登录 URL”输入采用 URI 格式的任何值。 完成后，选择“创建”。

  ![创建 Azure AD 注册](media/aad-integration/app-registration.png)

2. 选择“清单”，将 `groupMembershipClaims` 值编辑为 `"All"`。

  完成后保存更新。

  ![将组成员身份更新为“所有”](media/aad-integration/edit-manifest.png)

3. 返回 Azure AD 应用程序，选择“设置” > “密钥”。

  添加密钥说明，选择过期截止时间，然后选择“保存”。 记下密钥值。 部署支持 Azure AD 的 AKS 群集时，此值称为 `Server application secret`。

  ![获取应用程序私钥](media/aad-integration/application-key.png)

4. 返回 Azure AD 应用程序，选择“设置” > “所需的权限” > “添加” > “选择 API” > “Microsoft Graph” > “选择”。

  ![选择图形 API](media/aad-integration/graph-api.png)

5. 在“应用程序权限”下，勾选“读取目录数据”。

  ![设置应用程序 Graph 权限](media/aad-integration/read-directory.png)

6. 在“委派权限”下，勾选“登录并读取用户个人资料”和“读取目录数据”。 完成后保存更新。

  ![设置应用程序 Graph 权限](media/aad-integration/delegated-permissions.png)

  选择“完成”。

7. 从 API 列表中选择“Microsoft Graph”，然后选择“授予权限”。 如果当前帐户不是租户管理员，此步骤将会失败。

  ![设置应用程序 Graph 权限](media/aad-integration/grant-permissions.png)

  成功授予权限后，门户中会显示以下通知：

  ![权限授予成功的通知](media/aad-integration/permissions-granted.png)

8. 返回应用程序并记下“应用程序 ID”。 部署支持 Azure AD 的 AKS 群集时，此值称为 `Server application ID`。

  ![获取应用程序 ID](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>创建客户端应用程序

使用 Kubernetes CLI (kubectl) 登录时，将使用第二个 Azure AD 应用程序。

1. 选择“Azure Active Directory” > “应用注册” > “新建应用程序注册”。

  为应用程序命名，选择“本机”作为应用程序类型，然后为“重定向 URI”输入采用 URI 格式的任何值。 完成后，选择“创建”。

  ![创建 AAD 注册](media/aad-integration/app-registration-client.png)

2. 在 Azure AD 应用程序中，选择“设置” > “所需的权限” > “添加” > “选择API”，并搜索本文档最后一个步骤中创建的服务器应用程序的名称。

  ![配置应用程序权限](media/aad-integration/select-api.png)

3. 勾选该应用程序，并单击“选择”。

  ![选择 AKS AAD 服务器应用程序终结点](media/aad-integration/select-server-app.png)

  选择“完成”

4. 从列表中选择服务器 API，然后选择“授予权限”：

  ![授予权限](media/aad-integration/grant-permissions-client.png)

5. 返回 AD 应用程序并记下“应用程序 ID”。 部署支持 Azure AD 的 AKS 群集时，此值称为 `Client application ID`。

  ![获取应用程序 ID](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>获取租户 ID

最后，获取 Azure 租户的 ID。 部署 AKS 群集时，也要使用此值。

在 Azure 门户中，选择“Azure Active Directory” > “属性”并记下“目录 ID”。 部署支持 Azure AD 的 AKS 群集时，此值称为 `Tenant ID`。

![获取 Azure 租户 ID](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>部署群集

使用 [az group create][az-group-create] 命令为 AKS 群集创建资源组。

```azurecli
az group create --name myResourceGroup --location eastus
```

使用 [az aks create][az-aks-create] 命令部署群集。 请将以下示例命令中的值替换为创建 Azure AD 应用程序时收集的值。

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>创建 RBAC 绑定

在对 AKS 群集使用 Azure Active Directory 帐户之前，需要创建角色绑定或群集角色绑定。 “角色”定义要授予的权限，“绑定”将这些权限应用于目标用户。 这些分配可应用于特定命名空间或整个群集。 有关详细信息，请参阅[使用 RBAC 授权][rbac-authorization]。

首先，使用管理员访问权限，结合 `--admin` 参数运行 [az aks get-credentials][az-aks-get-credentials] 命令登录到群集。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

接下来，使用以下清单为 Azure AD 帐户创建 ClusterRoleBinding。 此示例向该帐户授予对群集所有命名空间的完全访问权限。 创建一个文件（例如 *rbac-aad-user.yaml*），然后粘贴以下内容。 将用户名更新为 Azure AD 租户中的某个用户名：

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: "user@contoso.com"
```

使用 [kubectl apply][kubectl-apply] 命令应用绑定，如以下示例所示：

```console
kubectl apply -f rbac-aad-user.yaml
```

此外，可为 Azure AD 组的所有成员创建角色绑定。 使用组对象 ID 指定 Azure AD 组，如以下示例所示。 创建一个文件（例如 *rbac-aad-group.yaml*），然后粘贴以下内容。 将组对象 ID 更新为 Azure AD 租户中的某个组对象 ID：

 ```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

使用 [kubectl apply][kubectl-apply] 命令应用绑定，如以下示例所示：

```console
kubectl apply -f rbac-aad-group.yaml
```

有关使用 RBAC 保护 Kubernetes 群集的详细信息，请参阅[使用 RBAC 授权][rbac-authorization]。

## <a name="access-cluster-with-azure-ad"></a>使用 Azure AD 访问群集

接下来，使用 [az aks get-credentials][az-aks-get-credentials] 命令提取非管理员用户的上下文。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

运行任何 kubectl 命令后，系统会提示在 Azure 上进行身份验证。 请遵照屏幕说明操作。

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-1   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-2   Ready     agent     1h        v1.9.9
```

完成后，身份验证令牌将会缓存。 仅当令牌已过期或者重新创建了 Kubernetes 配置文件时，系统才会再次提示。

如果在成功登录后看到授权错误消息，请确认你在 Azure AD 中不是以来宾用户的身份登录（在使用来自不同目录中的联合登录时，通常会出现此情况）。

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>后续步骤

在[使用 RBAC 授权][rbac-authorization]文档中详细了解如何使用 RBAC 保护 Kubernetes 群集。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
