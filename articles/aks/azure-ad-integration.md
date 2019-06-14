---
title: 将 Azure Active Directory 与 Azure Kubernetes Service 集成
description: 如何创建启用了 Azure Active Directory 的 Azure Kubernetes 服务 (AKS) 群集
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: iainfou
ms.openlocfilehash: db166c82e39e9184528fde67ff868229cf9b1d57
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67061108"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>将 Azure Active Directory 与 Azure Kubernetes Service 集成

Azure Kubernetes 服务 (AKS) 可以配置为使用 Azure Active Directory (Azure AD) 进行用户身份验证。 在此配置中，你可以登录到 AKS 群集使用 Azure AD 身份验证令牌。

群集管理员可以配置基于用户的标识或目录的组成员身份的 Kubernetes 基于角色的访问控制 (RBAC)。

本文介绍以下操作：

- 部署 AKS 和 Azure AD 的先决条件。
- 部署 Azure AD 启用群集。
- 使用 Azure 门户在 AKS 群集中创建基本的 RBAC 角色。

此外可以通过使用完成这些步骤[Azure CLI][azure-ad-cli]。

> [!NOTE]
> 创建新的 RBAC 启用群集时，可以仅启用 azure AD。 不能在现有 AKS 群集上启用 Azure AD。

## <a name="authentication-details"></a>身份验证详细信息

Azure AD 身份验证提供给具有 OpenID Connect 的 AKS 群集。 OpenID Connect 是构建在 OAuth 2.0 协议顶层的标识层。

OpenID Connect 的详细信息，请参阅[授权访问 web 应用程序使用 OpenID Connect 和 Azure AD][open-id-connect]。

在 Kubernetes 群集，webhook 令牌身份验证用于身份验证令牌。 Webhook 令牌身份验证作为 AKS 群集的一部分进行配置和管理。

有关 webhook 令牌身份验证的详细信息，请参阅[Webhook 令牌身份验证][ kubernetes-webhook] Kubernetes 文档中的部分。

若要为 AKS 群集提供 Azure AD 身份验证，需创建两个 Azure AD 应用程序。 第一个应用程序是一个提供用户身份验证的服务器组件。 第二个应用程序是在系统提示由 CLI 进行身份验证时使用的客户端组件。 此客户端应用程序使用服务器应用程序通过客户端提供的凭据进行实际的身份验证。

> [!NOTE]
> 在配置 Azure AD 进行 AKS 身份验证时，配置两个 Azure AD 应用程序。 Azure 租户管理员必须完成步骤为每个应用程序委派权限。

## <a name="create-the-server-application"></a>创建服务器应用程序

第一个 Azure AD 应用程序被应用来获取用户的 Azure AD 组成员身份。 在 Azure 门户中创建此应用程序：

1. 选择“Azure Active Directory” > “应用注册” > “新建注册”。   

    a. 为应用程序命名，例如 *AKSAzureADServer*。

    b. 有关**支持的帐户类型**，选择**此组织目录中的帐户**。
    
    c. 选择**Web**重定向 URI 的键入，然后输入任何 URI 格式的值，例如 *https://aksazureadserver* 。

    d. 选择**注册**何时完成。

2. 选择**清单**，然后编辑**groupMembershipClaims:** 值作为**所有**。 与更新完成后，选择**保存**。

    ![将组成员身份更新为“所有”](media/aad-integration/edit-manifest.png)

3. 在 Azure AD 应用程序的左侧窗格中，选择**证书和机密**。

    a. 选择 **+ 新客户端机密**。

    b. 添加密钥说明，例如“AKS Azure AD 服务器”。  选择到期时间，然后选择**添加**。

    c. 请注意此时只显示密钥值。 在部署 Azure AD 启用 AKS 群集时，此值称为服务器应用程序机密。

4. 在 Azure AD 应用程序的左侧窗格中，选择**API 的权限**，然后选择 **+ 添加权限**。

    a. 下**Microsoft Api**，选择**Microsoft Graph**。

    b. 选择**委托的权限**，然后选择复选框旁边**Directory > Directory.Read.All （读取目录数据）** 。

    c. 如果默认委派权限**用户 > User.Read (登录并读取用户配置文件)** 不存在，请选择它旁边的复选框。

    d. 选择**应用程序权限**，然后选择复选框旁边**Directory > Directory.Read.All （读取目录数据）** 。

    ![设置 Graph 权限](media/aad-integration/graph-permissions.png)

    e. 选择**添加权限**以保存的更新。

    f. 下**授予许可**，选择**授予管理员同意**。 此按钮不是不可用，如果当前帐户不是租户管理员联系。

    已成功授予权限，在门户中显示以下通知：

   ![权限授予成功的通知](media/aad-integration/permissions-granted.png)

5. 在 Azure AD 应用程序的左侧窗格中，选择**公开一个 API**，然后选择 **+ 添加作用域**。
    
    a. 输入**作用域名称**，则**管理员许可显示名称**，然后**管理员许可说明**如*AKSAzureADServer*。

    b. 请确保**状态**设置为**已启用**。

    ![将服务器应用作为 API 公开，以便与其他服务配合使用](media/aad-integration/expose-api.png)

    c. 选择**添加作用域**。

6. 返回到应用程序**概述**页并记下**应用程序 （客户端） ID**。 在部署 Azure AD 启用 AKS 群集时，此值称为服务器应用程序 id。

    ![获取应用程序 ID](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>创建客户端应用程序

使用 Kubernetes CLI (kubectl) 登录时，会使用第二个 Azure AD 应用程序。

1. 选择“Azure Active Directory” > “应用注册” > “新建注册”。   

    a. 为应用程序命名，例如 *AKSAzureADClient*。

    b. 有关**支持的帐户类型**，选择**此组织目录中的帐户**。

    c. 选择**Web**重定向 URI 的键入，并如输入 URI 格式的任何值 *https://aksazureadclient* 。

    d. 选择**注册**何时完成。

2. 在 Azure AD 应用程序的左侧窗格中，选择**API 的权限**，然后选择 **+ 添加权限**。

    a. 选择**我的 Api**，然后选择 Azure AD 服务器应用程序创建在上一步骤中，例如*AKSAzureADServer*。

    b. 选择**委派权限**，然后选择你的 Azure AD 服务器应用旁边的复选框。

    ![配置应用程序权限](media/aad-integration/select-api.png)

    c. 选择“添加权限”  。

    d. 下**授予许可**，选择**授予管理员同意**。 此按钮不可用，如果当前帐户不是租户管理员联系。当授予权限时，在门户中显示以下通知：

    ![权限授予成功的通知](media/aad-integration/permissions-granted.png)

3. 在 Azure AD 应用程序的左侧窗格中，选择**身份验证**。

    - 下**默认客户端类型**，选择**是**到**视为公共客户端的客户端**。

5. 在 Azure AD 应用程序的左侧窗格中，记下应用程序 id。 在部署 Azure AD 启用 AKS 群集时，此值称为客户端应用程序 id。

   ![获取应用程序 ID](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>获取租户 ID

接下来，获取你的 Azure 租户的 ID。 创建 AKS 群集时，要使用此值。

从 Azure 门户中，选择**Azure Active Directory** > **属性**并记下**目录 ID**。 创建 Azure AD 启用 AKS 群集时，此值称为租户 id。

![获取 Azure 租户 ID](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>部署 AKS 群集

使用 [az group create][az-group-create] 命令为 AKS 群集创建资源组。

```azurecli
az group create --name myResourceGroup --location eastus
```

使用[az aks 创建][ az-aks-create]命令部署 AKS 群集。 接下来，将在以下示例命令中的值。 使用创建的服务器应用程序 ID、 应用程序机密、 客户端应用 ID 和租户 id。 Azure AD 应用程序时收集的值

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

AKS 群集需要几分钟时间来创建。

## <a name="create-an-rbac-binding"></a>创建 RBAC 绑定

在使用 Azure Active Directory 帐户与 AKS 群集之前，必须创建角色绑定或群集角色的绑定。 角色定义的权限授予，并绑定将将其应用到所需的用户。 这些分配可应用于特定命名空间或整个群集。 有关详细信息，请参阅[使用 RBAC 授权][rbac-authorization]。

首先，使用管理员访问权限，结合 `--admin` 参数运行 [az aks get-credentials][az-aks-get-credentials] 命令登录到群集。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

接下来，为你想要授予 AKS 群集的访问权限的 Azure AD 帐户创建 ClusterRoleBinding。 下面的示例会对群集中的所有命名空间的帐户完全访问权限：

- 如果为其授予 RBAC 绑定的用户在同一个 Azure AD 租户中，请根据用户主体名称 (UPN) 分配权限。 继续到步骤创建 ClusterRoleBinding YAML 清单。

- 如果该用户位于不同的 Azure AD 租户中，请查询并改用 **objectId** 属性。 如果需要通过获取所需的用户帐户的 objectId [az ad 用户显示][ az-ad-user-show]命令。 提供所需帐户的用户主体名称 (UPN)：

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

创建一个文件，如*rbac aad user.yaml*，然后将粘贴以下内容。 在最后一行中，替换**userPrincipalName_or_objectId** UPN 或对象 id。 选择取决于用户是否相同的 Azure AD 租户。

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
  name: userPrincipalName_or_objectId
```

通过使用应用绑定[kubectl 适用][ kubectl-apply]命令，在下面的示例所示：

```console
kubectl apply -f rbac-aad-user.yaml
```

此外，可为 Azure AD 组的所有成员创建角色绑定。 Azure AD 组是使用指定的组对象 ID，如下面的示例中所示。

创建一个文件，如*rbac aad group.yaml*，然后将粘贴以下内容。 将组对象 ID 更新为 Azure AD 租户中的某个组对象 ID：

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

通过使用应用绑定[kubectl 适用][ kubectl-apply]命令，在下面的示例所示：

```console
kubectl apply -f rbac-aad-group.yaml
```

有关使用 RBAC 保护 Kubernetes 群集的详细信息，请参阅[使用 RBAC 授权][rbac-authorization]。

## <a name="access-the-cluster-with-azure-ad"></a>访问群集与 Azure AD

通过使用提取非管理员用户的上下文[az aks get-credentials 来获取凭据][ az-aks-get-credentials]命令。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

在您运行`kubectl`命令，系统将提示你使用 Azure 进行身份验证。 请按照屏幕说明完成该过程中，如下面的示例中所示：

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

完成后，缓存身份验证令牌。 系统只会提示重新登录时在令牌过期，或重新创建 Kubernetes 配置文件。

如果成功登录后看到授权错误消息，请检查以下条件：

```console
error: You must be logged in to the server (Unauthorized)
```


- 您定义了相应的对象 ID 或 UPN，具体取决于用户帐户是否相同的 Azure AD 租户中。
- 用户不是 200 多个组的成员。
- 为服务器与使用配置的值匹配的应用程序注册中定义的机密`--aad-server-app-secret`。

## <a name="next-steps"></a>后续步骤

若要使用 Azure AD 用户和组来控制对群集资源的访问，请参阅[在 AKS 中使用基于角色的访问控制和 Azure AD 标识来控制对群集资源的访问][azure-ad-rbac]。

有关如何对安全的 Kubernetes 群集的详细信息，请参阅[访问和标识选项适用于 AKS][rbac-authorization]。

若要了解有关标识和资源控制的详细信息，请参阅[身份验证和授权在 AKS 中的最佳做法][operator-best-practices-identity]。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
