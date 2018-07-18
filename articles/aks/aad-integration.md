---
title: 将 Azure Active Directory 与 Azure Kubernetes Service 集成
description: 如何创建支持 Azure Active Directory 的 Azure Kubernetes Service 群集。
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/17/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 7d157d50bbcd25edd9cd6693a71fb04535cbeb79
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937375"
---
# <a name="integrate-azure-active-directory-with-aks---preview"></a>将 Azure Active Directory 与 AKS 集成 - 预览版

可将 Azure Kubernetes Service (AKS) 配置为使用 Azure Active Directory 进行用户身份验证。 在此配置中，你可以使用自己的 Azure Active Directory 身份验证令牌登录到 Azure Kubernetes Service 群集。 此外，群集管理员可以根据用户标识或目录组成员身份来配置 Kubernetes 基于角色的访问控制。

本文档详细介绍如何创建 AKS 和 Azure AD 的所有必备组件、部署支持 Azure AD 的群集，以及在 AKS 群集中创建简单的 RBAC 角色。

> [!IMPORTANT]
> Azure Kubernetes Service (AKS) RBAC 与 Azure AD 集成目前以**预览版**提供。 需同意[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)才可使用预览版。 在正式版推出之前，此功能的某些方面可能会有所更改。
>

## <a name="authentication-details"></a>身份验证详细信息

使用 OpenID Connect 向 Azure Kubernetes 群集提供 Azure AD 身份验证。 OpenID Connect 是构建在 OAuth 2.0 协议顶层的标识层。 [Open ID Connect 文档][open-id-connect]中提供了有关 OpenID Connect 的详细信息。

在 Kubernetes 群集内部，使用 Webhook 令牌身份验证来验证身份验证令牌。 Webhook 令牌身份验证作为 AKS 群集的一部分进行配置和管理。 [Webhook 身份验证文档][kubernetes-webhook]中提供了有关 Webhook 令牌身份验证的详细信息。

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

  在“应用程序权限”下，勾选“读取目录数据”。

  ![设置应用程序 Graph 权限](media/aad-integration/read-directory.png)

5. 在“委派权限”下，勾选“登录并读取用户个人资料”和“读取目录数据”。 完成后保存更新。

  ![设置应用程序 Graph 权限](media/aad-integration/delegated-permissions.png)

6. 选择“完成”和“授予权限”完成此步骤。 如果当前帐户不是租户管理员，此步骤将会失败。

  ![设置应用程序 Graph 权限](media/aad-integration/grant-permissions.png)

7. 返回应用程序并记下“应用程序 ID”。 部署支持 Azure AD 的 AKS 群集时，此值称为 `Server application ID`。

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

4. 选择“完成”和“授予权限”完成此步骤。

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
az group create --name myAKSCluster --location eastus
```

使用 [az aks create][az-aks-create] 命令部署群集。 请将以下示例命令中的值替换为创建 Azure AD 应用程序时收集的值。

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --generate-ssh-keys --enable-rbac \
  --aad-server-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 7ee598bb-0000-0000-0000-83692e2d717e \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>创建 RBAC 绑定

在对 AKS 群集使用 Azure Active Directory 帐户之前，需要创建角色绑定或群集角色绑定。

首先，使用管理员访问权限，结合 `--admin` 参数运行 [az aks get-credentials][az-aks-get-credentials] 命令登录到群集。

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster --admin
```

接下来，使用以下清单为 Azure AD 帐户创建 ClusterRoleBinding。 将用户名更新为 Azure AD 租户中的某个用户名。 此示例向该帐户授予对群集所有命名空间的完全访问权限。

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

此外，可为 Azure AD 组的所有成员创建角色绑定。 以下清单向 `kubernetes-admin` 组的所有成员授予对群集的管理员访问权限。

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
   name: "kubernetes-admin"
```

有关使用 RBAC 保护 Kubernetes 群集的详细信息，请参阅[使用 RBAC 授权][rbac-authorization]。

## <a name="access-cluster-with-azure-ad"></a>使用 Azure AD 访问群集

接下来，使用 [az aks get-credentials][az-aks-get-credentials] 命令提取非管理员用户的上下文。

```azurecli
az aks get-credentials --resource-group myAKSCluster --name myAKSCluster
```

运行任何 kubectl 命令后，系统会提示在 Azure 上进行身份验证。 请遵照屏幕说明操作。

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-42032720-0   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-1   Ready     agent     1h        v1.9.6
aks-nodepool1-42032720-2   Ready     agent     1h        v1.9.6
```

完成后，身份验证令牌将会缓存。 仅当令牌已过期或者重新创建了 Kubernetes 配置文件时，系统才会再次提示。

## <a name="next-steps"></a>后续步骤

在[使用 RBAC 授权][rbac-authorization]文档中详细了解如何使用 RBAC 保护 Kubernetes 群集。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]: ../active-directory/develop/active-directory-protocols-openid-connect-code.md
