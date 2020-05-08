---
title: 将 Azure Active Directory 与 Azure Kubernetes Service 集成
description: 了解如何使用 Azure CLI 创建支持 Azure Active Directory 的 Azure Kubernetes 服务 (AKS) 群集
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: dba6590daf5c64dd1e53663e71a0cc27941b1470
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779937"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>使用 Azure CLI 将 Azure Active Directory 与 Azure Kubernetes 服务集成

可将 Azure Kubernetes Service (AKS) 配置为使用 Azure Active Directory (AD) 进行用户身份验证。 在此配置中，可以使用 Azure AD 身份验证令牌登录到 AKS 群集。 群集操作员还可以根据用户标识或目录组成员身份来配置 Kubernetes 基于角色的访问控制 (RBAC)。

本文介绍如何创建所需的 Azure AD 组件，然后部署支持 Azure AD 的群集并在 AKS 群集中创建一个基本的 RBAC 角色。 也可以[使用 Azure 门户完成这些步骤][azure-ad-portal]。

有关本文中使用的完整示例脚本，请参阅 [Azure CLI 示例 - AKS 与 Azure AD 集成][complete-script]。

以下限制适用：

- 只有在创建新的启用 RBAC 的群集时，才能启用 Azure AD。 不能在现有 AKS 群集上启用 Azure AD。

## <a name="before-you-begin"></a>准备阶段

需要安装并配置 Azure CLI 2.0.61 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

在浏览器中访问 [https://shell.azure.com](https://shell.azure.com) 以打开 Cloud Shell。

为了保持一致并帮助运行本文中的命令，请为所需的 AKS 群集名称创建一个变量。 以下示例使用名称 *myakscluster*：

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Azure AD 身份验证概述

使用 OpenID Connect 向 AKS 群集提供 Azure AD 身份验证。 OpenID Connect 是构建在 OAuth 2.0 协议顶层的标识层。 有关 OpenID Connect 的详细信息，请参阅[OPEN ID connect 文档][open-id-connect]。

在 Kubernetes 群集内部，使用 Webhook 令牌身份验证来验证身份验证令牌。 Webhook 令牌身份验证作为 AKS 群集的一部分进行配置和管理。 有关 Webhook 令牌身份验证的详细信息，请参阅 [Webhook 身份验证文档][kubernetes-webhook]。

> [!NOTE]
> 若要配置 Azure AD 以进行 AKS 身份验证，需配置两个 Azure AD 应用程序。 此操作必须由 Azure 租户管理员完成。

## <a name="create-azure-ad-server-component"></a>创建 Azure AD 服务器组件

若要与 AKS 集成，请创建并使用充当标识请求终结点的 Azure AD 应用程序。 所需的第一个 Azure AD 应用程序获取用户的 Azure AD 组成员身份。

使用 [az ad app create][az-ad-app-create] 命令创建服务器应用程序组件，然后使用 [az ad app update][az-ad-app-update] 命令更新组成员身份声明。 以下示例使用[开始之前](#before-you-begin)部分中定义的 *aksname* 变量，并创建一个变量

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group membership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

现在，使用 [az ad sp create][az-ad-sp-create] 命令创建服务器应用的服务主体。 此服务主体用于在 Azure 平台中对自身进行身份验证。 然后，使用 [az ad sp credential reset][az-ad-sp-credential-reset] 命令获取服务主体机密，并将其分配到名为 *serverApplicationSecret* 的变量，以便在以下步骤之一中使用：

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD 需要执行以下操作的有权：

* 读取目录数据
* 登录和读取用户配置文件

使用 [az ad app permission add][az-ad-app-permission-add] 命令分配这些权限：

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

最后，使用 [az ad app permission grant][az-ad-app-permission-grant] 命令授予在上一步骤中为服务器应用程序分配的权限。 如果当前帐户不是租户管理员，则此步骤将失败。还需要为 Azure AD 应用程序添加权限，以便请求可能需要使用[az AD app 权限管理员同意][az-ad-app-permission-admin-consent]进行管理许可的信息：

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>创建 Azure AD 客户端组件

当用户使用 Kubernetes CLI (`kubectl`) 登录到 AKS 群集时，将使用第二个 Azure AD 应用程序。 此客户端应用程序从用户接收身份验证请求，并验证其凭据和权限。 使用 [az ad app create][az-ad-app-create] 命令创建客户端组件的 Azure AD 应用：

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

使用 [az ad sp create][az-ad-sp-create] 命令创建客户端应用程序的服务主体：

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

使用 [az ad app show][az-ad-app-show] 命令获取服务器应用的 oAuth2 ID，以允许两个应用组件之间的身份验证流。 下一步骤将使用此 oAuth2 ID。

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

使用 [az ad app permission add][az-ad-app-permission-add] 命令添加对客户端应用程序和服务器应用程序组件的权限，以使用 oAuth2 通信流。 然后，使用 [az ad app permission grant][az-ad-app-permission-grant] 命令授予客户端应用程序与服务器应用程序通信的权限：

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>部署群集

创建两个 Azure AD 应用程序后，请创建 AKS 群集本身。 首先使用 [az group create][az-group-create] 命令创建资源组。 以下示例在*EastUS*区域中创建资源组：

为群集创建资源组：

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

使用 [az account show][az-account-show] 命令获取 Azure 订阅的租户 ID。 然后使用 [az aks create][az-aks-create] 命令创建 AKS 群集。 用于创建 AKS 群集的命令可提供服务器和客户端应用程序 ID、服务器应用程序服务主体机密和租户 ID：

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

最后，使用 [az aks get-credentials][az-aks-get-credentials] 命令获取群集管理员凭据。 在以下步骤之一中，你将获取普通用户群集凭据，以查看 Azure AD 身份验证流的运作方式。**

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>创建 RBAC 绑定

在对 AKS 群集使用 Azure Active Directory 帐户之前，需要创建角色绑定或群集角色绑定。 “角色”定义要授予的权限，“绑定”将这些权限应用于目标用户****。 这些分配可应用于特定命名空间或整个群集。 有关详细信息，请参阅[使用 RBAC 授权][rbac-authorization]。

使用 [az ad signed-in-user show][az-ad-signed-in-user-show] 命令获取用户当前登录用户的用户主体名称 (UPN)。 在下一步骤中，将为 Azure AD 集成启用此用户帐户。

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> 如果为其授予 RBAC 绑定的用户在同一个 Azure AD 租户中，请根据 *userPrincipalName* 分配权限。 如果该用户位于不同的 Azure AD 租户中，请查询并改用 *objectId* 属性。

创建名为 `basic-azure-ad-binding.yaml` 的 YAML 清单并粘贴以下内容。 在最后一行中，请将 *userPrincipalName_or_objectId* 替换为前一命令的 UPN 或对象 ID 输出：

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

使用 [kubectl apply][kubectl-apply] 命令创建群集角色绑定，并指定 YAML 清单的文件名：

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>使用 Azure AD 访问群集

现在，让我们测试 AKS 群集的 Azure AD 身份验证集成。 将 `kubectl` 配置上下文设置为使用常规用户凭据。 此上下文通过 Azure AD 传回所有身份验证请求。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

现在，使用 [kubectl get pods][kubectl-get] 命令查看所有命名空间中的 pod：

```console
kubectl get pods --all-namespaces
```

你将收到一条登录提示，指出在 Web 浏览器中使用 Azure AD 凭据进行身份验证。 成功完成身份验证后，`kubectl` 命令会显示 AKS 群集中的 pod，如以下示例输出中所示：

```console
kubectl get pods --all-namespaces
```

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

收到的 `kubectl` 身份验证令牌将会缓存。 仅当令牌已过期或者重新创建了 Kubernetes 配置文件时，系统才会再次提示登录。

如果在使用 Web 浏览器成功登录后看到了以下示例输出中所示的授权错误消息，请检查以下问题：

```output
error: You must be logged in to the server (Unauthorized)
```

* 你定义了适当的对象 ID 或 UPN，具体取决于用户帐户是否在同一 Azure AD 租户中。
* 用户不是 200 多个组的成员。
* 服务器应用程序注册中定义的机密与使用 `--aad-server-app-secret` 配置的值相匹配

## <a name="next-steps"></a>后续步骤

有关包含本文中所示命令的完整脚本，请参阅 [AKS 中的 Azure AD 集成脚本示例存储库][complete-script]。

若要使用 Azure AD 用户和组来控制对群集资源的访问，请参阅[在 AKS 中使用基于角色的访问控制和 Azure AD 标识来控制对群集资源的访问][azure-ad-rbac]。

有关如何保护 Kubernetes 群集的详细信息，请参阅[AKS 的访问和标识选项）][rbac-authorization]。

有关标识和资源控制的最佳做法，请参阅[有关 AKS 中的身份验证和授权的最佳做法][operator-best-practices-identity]。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
