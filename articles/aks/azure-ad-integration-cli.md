---
title: 将 Azure Active Directory 与 Azure Kubernetes Service 集成
description: 了解如何使用 Azure CLI 创建和启用 Azure Active Directory 的 Azure Kubernetes 服务 (AKS) 群集
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: 0216a8c7d4e52e89098979223e9b792398e25038
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920169"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>将 Azure Active Directory 集成通过使用 Azure CLI 的 Azure Kubernetes 服务

可将 Azure Kubernetes Service (AKS) 配置为使用 Azure Active Directory (AD) 进行用户身份验证。 在此配置中，你可以登录到 AKS 群集使用 Azure AD 身份验证令牌。 群集操作员还可以配置基于用户的标识或目录的组成员身份的 Kubernetes 基于角色的访问控制 (RBAC)。

本文介绍如何创建所需 Azure AD 组件，然后部署 Azure AD 启用群集，并在 AKS 群集中创建基本的 RBAC 角色。 此外可以[完成这些步骤使用 Azure 门户][azure-ad-portal]。

在本文中使用的完整示例脚本，请参阅[Azure CLI 示例-Azure AD 与 AKS 集成][complete-script]。

以下限制适用：

- 只有在创建新的启用 RBAC 的群集时，才能启用 Azure AD。 不能在现有 AKS 群集上启用 Azure AD。
- *来宾*用户在 Azure AD 中，此类就像使用联合的登录中从另一个目录，不支持。

## <a name="before-you-begin"></a>开始之前

你需要 Azure CLI 版本 2.0.61 或更高版本安装和配置。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

为了保持一致并帮助运行本文中的命令，创建所需的 AKS 群集名称的变量。 下面的示例使用名称*myakscluster*:

```azurecli-interactive
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Azure AD 身份验证概述

使用 OpenID Connect 向 AKS 群集提供 Azure AD 身份验证。 OpenID Connect 是构建在 OAuth 2.0 协议顶层的标识层。 有关 OpenID Connect 的详细信息，请参阅 [Open ID Connect 文档][open-id-connect]。

在 Kubernetes 群集内部，使用 Webhook 令牌身份验证来验证身份验证令牌。 Webhook 令牌身份验证作为 AKS 群集的一部分进行配置和管理。 有关 Webhook 令牌身份验证的详细信息，请参阅 [Webhook 身份验证文档][kubernetes-webhook]。

> [!NOTE]
> 将 Azure AD 配置用于 AKS 身份验证时，会配置两个 Azure AD 应用程序。 此操作必须由 Azure 租户管理员完成。

## <a name="create-azure-ad-server-component"></a>创建 Azure AD 服务器组件

若要将与 AKS 集成，您创建并使用充当标识请求的终结点的 Azure AD 应用程序。 第一个 Azure AD 应用程序需要获取用户的 Azure AD 组成员身份。

创建服务器应用程序组件使用[az ad app 创建][ az-ad-app-create]命令，然后更新使用的组成员身份声明[az ad app update] [az-ad-app-update]命令。 下面的示例使用*aksname*中定义的变量[在开始之前](#before-you-begin)部分，并创建一个变量

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

现在，创建服务器应用程序使用的服务主体[az ad sp 创建][ az-ad-sp-create]命令。 此服务主体用于 Azure 平台中验证自己的身份。 然后，服务主体密钥使用获得[az ad sp 凭据重置][ az-ad-sp-credential-reset]命令，并将分配给名为变量*serverApplicationSecret*在之一中使用以下步骤：

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD 需要有权执行以下操作：

* 读取目录数据
* 登录并读取用户配置文件

将使用这些权限分配[az ad 应用程序的权限添加][ az-ad-app-permission-add]命令：

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

最后，授予为服务器应用程序使用在上一步中分配的权限[az ad 应用程序的权限授予][ az-ad-app-permission-grant]命令。 此步骤失败，如果当前帐户不是租户管理员联系。此外需要添加对 Azure AD 应用程序请求可能需要管理许可使用的信息的权限[az ad app 权限管理员同意][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>创建 Azure AD 客户端组件

第二个 Azure AD 应用程序使用时在用户登录到 AKS 群集与 Kubernetes CLI (`kubectl`)。 此客户端应用程序从用户所需的身份验证请求，并验证其凭据和权限。 创建适用于客户端组件使用的 Azure AD 应用[az ad app 创建][ az-ad-app-create]命令：

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

创建客户端应用程序使用的服务主体[az ad sp 创建][ az-ad-sp-create]命令：

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

获取服务器应用 oAuth2 ID，以允许使用的两个应用程序组件之间的身份验证流[az ad app show] [ az-ad-app-show]命令。 下一步中使用此 oAuth2 ID。

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

添加客户端应用程序的权限和服务器应用程序组件，可使用 oAuth2 通信流使用[az ad 应用程序的权限添加][ az-ad-app-permission-add]命令。 然后，授予为与服务器应用程序使用的通信的客户端应用程序的权限[az ad 应用程序的权限授予][ az-ad-app-permission-grant]命令：

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions $oAuthPermissionId=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>部署群集

两个 Azure AD 应用程序创建，现在创建 AKS 群集本身。 首先，创建资源组使用[az 组创建][ az-group-create]命令。 下面的示例创建中的资源组*EastUS*区域：

创建群集的资源组：

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

获取 Azure 订阅使用的租户 ID [az 帐户 show] [ az-account-show]命令。 然后，创建 AKS 群集使用[az aks 创建][ az-aks-create]命令。 创建 AKS 群集的命令提供了服务器和客户端应用程序 Id、 服务器应用程序服务主体密码和你的租户 ID:

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

最后，获取群集使用的管理员凭据[az aks get-credentials 来获取凭据][ az-aks-get-credentials]命令。 在以下步骤之一，获取常规*用户*群集以查看 Azure AD 身份验证的凭据运行中的流。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>创建 RBAC 绑定

在对 AKS 群集使用 Azure Active Directory 帐户之前，需要创建角色绑定或群集角色绑定。 “角色”定义要授予的权限，“绑定”将这些权限应用于目标用户。 这些分配可应用于特定命名空间或整个群集。 有关详细信息，请参阅[使用 RBAC 授权][rbac-authorization]。

获取用户当前登录使用的用户主体名称 (UPN) [az ad 中用户签名 show] [ az-ad-signed-in-user-show]命令。 此用户帐户可用于下一步中的 Azure AD 集成。

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> 如果的用户授予的 RBAC 绑定是相同的 Azure AD 租户中，分配权限基于*userPrincipalName*。 如果用户是在不同的 Azure AD 中租户、 查询和使用*objectId*属性改为。

创建名为的 YAML 清单`basic-azure-ad-binding.yaml`并粘贴以下内容。 在最后一行中，替换*userPrincipalName_or_objectId*与前一命令生成的 UPN 或对象 ID 输出：

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

创建使用 ClusterRoleBinding [kubectl 适用][ kubectl-apply]命令并指定 YAML 清单的文件名：

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>使用 Azure AD 访问群集

现在让我们测试的 AKS 群集的 Azure AD 身份验证集成。 设置`kubectl`要使用普通用户凭据的配置上下文。 此上下文将传递回通过 Azure AD 的所有身份验证请求。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

现在，使用[kubectl get pod] [ kubectl-get]命令来查看 pod 中的所有命名空间：

```console
kubectl get pods --all-namespaces
```

在提示符下使用使用 web 浏览器的 Azure AD 凭据进行身份验证收到一个符号。 您已成功通过身份验证后，`kubectl`命令在 AKS 群集中，显示 pod，如以下示例输出中所示：

```console
$ kubectl get pods --all-namespaces

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

为接收身份验证令牌`kubectl`缓存。 您仅再次提示登录的令牌已过期或 Kubernetes 配置文件重新创建。

如果您已成功登录后使用 web 浏览器，如以下示例输出所示看到授权错误消息，请检查以下可能的问题：

```console
error: You must be logged in to the server (Unauthorized)
```

* 在不是你已登录的用户*来宾*（这通常是这种情况时使用不同的目录从联合身份验证的登录） 的 Azure AD 实例中。
* 用户不是 200 多个组的成员。

## <a name="next-steps"></a>后续步骤

包含在本文中所示的命令的完整脚本，请参阅[在 AKS 中的 Azure AD 集成脚本示例存储库][complete-script]。

若要使用 Azure AD 用户和组来控制对群集资源的访问，请参阅[控制在 AKS 中使用基于角色的访问控制和 Azure AD 标识的群集资源的访问权限][azure-ad-rbac]。

有关如何保护 Kubernetes 群集的详细信息，请参阅[适用于 AKS 的访问和标识选项)][rbac-authorization]。

标识和资源控制的最佳实践，请参阅[身份验证和授权在 AKS 中的最佳做法][operator-best-practices-identity]。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
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
