---
title: 将 Azure Active Directory 与 Azure Kubernetes Service 集成
description: 如何创建支持 Azure Active Directory 的 Azure Kubernetes 服务（AKS）群集
services: container-service
ms.topic: article
ms.date: 02/02/2019
ms.openlocfilehash: 0476acadf5af3a3e2c470fe6c08ebbd355653e22
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596583"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>将 Azure Active Directory 与 Azure Kubernetes Service 集成

可以将 Azure Kubernetes 服务（AKS）配置为使用 Azure Active Directory （Azure AD）进行用户身份验证。 在此配置中，可以使用 Azure AD 身份验证令牌登录到 AKS 群集。

群集管理员可以基于用户的标识或目录组成员身份配置基于 Kubernetes 角色的访问控制（RBAC）。

本文介绍如何执行以下操作：

- 部署 AKS 和 Azure AD 的必备组件。
- 部署已启用 Azure AD 的群集。
- 使用 Azure 门户在 AKS 群集中创建基本 RBAC 角色。

还可以通过使用[Azure CLI][azure-ad-cli]来完成这些步骤。

> [!NOTE]
> 仅当创建启用了 RBAC 的新群集时，才能启用 Azure AD。 不能在现有 AKS 群集上启用 Azure AD。

## <a name="authentication-details"></a>身份验证详细信息

Azure AD 身份验证提供给具有 OpenID Connect 的 AKS 群集。 OpenID Connect 是构建在 OAuth 2.0 协议顶层的标识层。

有关 OpenID Connect 的详细信息，请参阅[使用 Openid connect 和 Azure AD 向 web 应用程序授予访问权限][open-id-connect]。

在 Kubernetes 群集中，webhook 令牌身份验证用于身份验证令牌。 Webhook 令牌身份验证作为 AKS 群集的一部分进行配置和管理。

有关 webhook 令牌身份验证的详细信息，请参阅 Kubernetes 文档中的[Webhook 令牌身份验证][kubernetes-webhook]部分。

若要为 AKS 群集提供 Azure AD 身份验证，请创建两个 Azure AD 应用程序。 第一个应用程序是提供用户身份验证的服务器组件。 第二个应用程序是在 CLI 提示你进行身份验证时使用的客户端组件。 此客户端应用程序使用服务器应用程序进行客户端提供的凭据的实际身份验证。

> [!NOTE]
> 为 AKS authentication 配置 Azure AD 时，将配置两个 Azure AD 应用程序。 为每个应用程序委派权限的步骤必须由 Azure 租户管理员完成。

## <a name="create-the-server-application"></a>创建服务器应用程序

应用第一个 Azure AD 应用程序以获取用户的 Azure AD 组成员身份。 若要在 Azure 门户中创建此应用程序：

1. 选择 " **Azure Active Directory** > **应用注册**" > "**新注册**"。

    a. 为应用程序指定一个名称，例如*AKSAzureADServer*。

    b. 对于**支持的帐户类型**，请**仅选择此组织目录中的帐户**。
    
    c. 为 "重定向 URI 类型" 选择 " **Web** "，然后输入任何 URI 格式的值，例如 " *https://aksazureadserver* "。

    d. 完成后，选择 "**注册**"。

2. 选择 "**清单**"，然后编辑 " **groupMembershipClaims：** value **"。** 完成更新后，请选择 "**保存**"。

    ![将组成员身份更新为“所有”](media/aad-integration/edit-manifest.png)

3. 在 Azure AD 应用程序的左窗格中，选择 "**证书 & 机密**"。

    a. 选择 " **+ 新建客户端密码**"。

    b. 添加密钥说明，例如*AKS Azure AD server*。 选择过期时间，然后选择 "**添加**"。

    c. 请注意密钥值，该值仅在此时显示。 部署已启用 Azure AD 的 AKS 群集时，此值称为 "服务器应用程序密钥"。

4. 在 Azure AD 应用程序的左窗格中，选择 " **API 权限**"，然后选择 " **+ 添加权限**"。

    a. 在 " **Microsoft api**" 下，选择**Microsoft Graph**。

    b. 选择 "**委托的权限**"，然后选中 "**目录 > 目录**" 旁边的复选框。

    c. 如果用户 > 的用户的默认委托权限 **（登录和读取用户配置文件）** 不存在，请选中它旁边的复选框。

    d. 选择 "**应用程序权限**"，然后选中 "**目录 >** " 目录 "旁边的复选框。

    ![设置图形权限](media/aad-integration/graph-permissions.png)

    e. 选择 "**添加权限**" 保存更新。

    f. 在 "**授权**" 下，选择 "**授予管理员许可**"。 此按钮不可用，当前正在使用的帐户未作为租户管理员列出。

    成功授予权限后，门户中会显示以下通知：

   ![权限授予成功的通知](media/aad-integration/permissions-granted.png)

5. 在 Azure AD 应用程序的左窗格中，选择 "**公开 API**"，然后选择 " **+ 添加作用域**"。
    
    a. 输入**范围名称**、**管理员同意显示名称**，然后输入**管理员同意说明**（如*AKSAzureADServer*）。

    b. 确保 "**状态**" 设置为 "**已启用**"。

    ![将服务器应用作为 API 公开用于其他服务](media/aad-integration/expose-api.png)

    c. 选择 "**添加作用域**"。

6. 返回到 "应用程序**概述**" 页，并记下**应用程序（客户端） ID**。 部署已启用 Azure AD 的 AKS 群集时，此值称为 "服务器应用程序 ID"。

    ![获取应用程序 ID](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>创建客户端应用程序

使用 Kubernetes CLI （kubectl）登录时，将使用第二个 Azure AD 应用程序。

1. 选择 " **Azure Active Directory** > **应用注册**" > "**新注册**"。

    a. 为应用程序指定一个名称，例如*AKSAzureADClient*。

    b. 对于**支持的帐户类型**，请**仅选择此组织目录中的帐户**。

    c. 对于 "重定向 URI 类型"，请选择 " **Web** "，然后输入任何 URI 格式的值，例如 *https://aksazureadclient* 。

    >[!NOTE]
    >如果要创建新的启用 RBAC 的群集以支持容器 Azure Monitor，请将以下两个其他重定向 Url 作为**Web**应用程序类型添加到此列表。 应 `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` 第一个基本 URL 值，并且应 `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`第二个基 URL 值。
    >
    >如果在 Azure 中国区使用此功能，则应 `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` 第一个基本 URL 值，并且应 `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`第二个基本 URL 值。
    >
    >有关详细信息，请参阅如何为容器的 Azure Monitor[设置实时数据（预览）功能](../azure-monitor/insights/container-insights-livedata-setup.md)，以及在[配置 AD 集成身份验证](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication)部分下配置身份验证的步骤。

    d. 完成后，选择 "**注册**"。

2. 在 Azure AD 应用程序的左窗格中，选择 " **API 权限**"，然后选择 " **+ 添加权限**"。

    a. 选择 "**我的 api**"，然后选择在上一步中创建的 Azure AD 服务器应用程序，如*AKSAzureADServer*。

    b. 选择 "**委托的权限**"，然后选中 Azure AD 服务器应用旁边的复选框。

    ![配置应用程序权限](media/aad-integration/select-api.png)

    c. 选择“添加权限”。

    d. 在 "**授权**" 下，选择 "**授予管理员许可**"。 如果当前帐户不是租户管理员，则此按钮不可用。授予权限时，门户中会显示以下通知：

    ![权限授予成功的通知](media/aad-integration/permissions-granted.png)

3. 在 Azure AD 应用程序的左窗格中，选择 "**身份验证**"。

    - 在 "**默认客户端类型**" 下，选择 **"是"** 以将**客户端视为公共客户端**。

5. 在 Azure AD 应用程序的左窗格中，记下应用程序 ID。 部署已启用 Azure AD 的 AKS 群集时，此值称为 "客户端应用程序 ID"。

   ![获取应用程序 ID](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>获取租户 ID

接下来，获取 Azure 租户的 ID。 此值在创建 AKS 群集时使用。

在 Azure 门户中，选择**Azure Active Directory** > **属性**并记下**目录 ID**。 创建启用 Azure AD 的 AKS 群集时，此值称为 "租户 ID"。

![获取 Azure 租户 ID](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>部署 AKS 群集

使用[az group create][az-group-create]命令为 AKS 群集创建资源组。

```azurecli
az group create --name myResourceGroup --location eastus
```

使用[az aks create][az-aks-create]命令部署 aks 群集。 接下来，请替换下面的示例命令中的值。 使用在为服务器应用 ID、应用密钥、客户端应用 ID 和租户 ID 创建 Azure AD 应用程序时收集的值。

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

创建 AKS 群集需要几分钟时间。

## <a name="create-an-rbac-binding"></a>创建 RBAC 绑定

> [!NOTE]
> 群集角色绑定名称区分大小写。

在将 Azure Active Directory 帐户用于 AKS 群集之前，必须创建角色绑定或群集角色绑定。 角色定义要授予的权限，绑定将它们应用到所需的用户。 这些分配可应用于特定命名空间或整个群集。 有关详细信息，请参阅[使用 RBAC 授权][rbac-authorization]。

首先，将[az aks get 凭据][az-aks-get-credentials]命令与 `--admin` 参数一起使用，以使用管理员访问权限登录到群集。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

接下来，为你想要授予对 AKS 群集的访问权限的 Azure AD 帐户创建 ClusterRoleBinding。 下面的示例为帐户提供了对群集中所有命名空间的完全访问权限：

- 如果为其授予 RBAC 绑定的用户在同一 Azure AD 租户中，请根据用户主体名称（UPN）分配权限。 转到创建 ClusterRoleBinding 的 YAML 清单的步骤。

- 如果用户位于不同的 Azure AD 租户中，请改为查询并使用**objectId**属性。 如果需要，请使用[az ad user show][az-ad-user-show]命令获取所需用户帐户的 objectId。 提供所需帐户的用户主体名称（UPN）：

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

创建一个文件（例如*rbac-yaml*），并粘贴以下内容。 在最后一行中，将**userPrincipalName_or_objectId**替换为 UPN 或对象 ID。 选择取决于用户是否 Azure AD 租户相同。

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

使用[kubectl apply][kubectl-apply]命令应用绑定，如以下示例中所示：

```console
kubectl apply -f rbac-aad-user.yaml
```

此外，可为 Azure AD 组的所有成员创建角色绑定。 Azure AD 使用组对象 ID 指定组，如以下示例中所示。

创建一个文件（例如*rbac-yaml*），并粘贴以下内容。 将组对象 ID 更新为 Azure AD 租户中的某个组对象 ID：

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

使用[kubectl apply][kubectl-apply]命令应用绑定，如以下示例中所示：

```console
kubectl apply -f rbac-aad-group.yaml
```

有关使用 RBAC 保护 Kubernetes 群集的详细信息，请参阅[使用 Rbac 授权][rbac-authorization]。

## <a name="access-the-cluster-with-azure-ad"></a>使用 Azure AD 访问群集

使用[az aks get 凭据][az-aks-get-credentials]命令拉取非管理员用户的上下文。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

运行 `kubectl` 命令后，系统会提示使用 Azure 进行身份验证。 按照屏幕上的说明完成该过程，如以下示例中所示：

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

完成此过程后，将缓存身份验证令牌。 仅当令牌过期时，才会提示你再次登录，否则会重新创建 Kubernetes 配置文件。

如果你在成功登录后看到授权错误消息，请检查以下条件：

```console
error: You must be logged in to the server (Unauthorized)
```


- 根据用户帐户是否在同一 Azure AD 租户中，定义了相应的对象 ID 或 UPN。
- 用户不是超过200个组的成员。
- 在应用程序注册中为服务器定义的机密与使用 `--aad-server-app-secret`配置的值匹配。

## <a name="next-steps"></a>后续步骤

若要使用 Azure AD 的用户和组来控制对群集资源的访问，请参阅[在 AKS 中使用基于角色的访问控制和 Azure AD 标识控制对群集资源的访问][azure-ad-rbac]。

有关如何保护 Kubernetes 群集的详细信息，请参阅[AKS 的访问和标识选项][rbac-authorization]。

若要详细了解标识和资源控制，请参阅[AKS 中的身份验证和授权的最佳实践][operator-best-practices-identity]。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
