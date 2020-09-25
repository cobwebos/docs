---
title: 在 Kubernetes 中通过 Azure 管理 RBAC
titleSuffix: Azure Kubernetes Service
description: 了解如何使用 azure RBAC 通过 Azure Kubernetes 服务 (AKS) 进行 Kubernetes 授权。
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 15bd917a16c250807d6848f7bc0ffbdba06b4019
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329085"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>使用 Azure RBAC 进行 Kubernetes 授权（预览）

现在，你可以在 [Azure Active Directory (Azure AD) 和 AKS 之间充分利用集成身份验证](managed-aad.md)。 启用后，此集成允许客户将 Azure AD 用户、组或服务主体用作 Kubernetes RBAC 中的主题，请参阅 [此处](azure-ad-rbac.md)的详细信息。
此功能使你不必单独管理 Kubernetes 的用户标识和凭据。 但是，你仍需要单独设置和管理 Azure RBAC 和 Kubernetes RBAC。 有关 AKS 上的身份验证、授权和 RBAC 的详细信息，请参阅 [此处](concepts-identity.md)。

本文档介绍一种新的方法，允许跨 Azure 资源、AKS 和 Kubernetes 资源实现统一的管理和访问控制。

## <a name="before-you-begin"></a>开始之前

通过 Azure 管理 Kubernetes 资源的 RBAC 功能，你可以选择使用 Azure 或本机 Kubernetes 机制管理群集资源的 RBAC。 启用后，将由 Azure RBAC 专门验证 Azure AD 主体，同时 Kubernetes RBAC 以独占方式验证常规 Kubernetes 用户和服务帐户。 有关 AKS 上的身份验证、授权和 RBAC 的详细信息，请参阅 [此处](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview)。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>先决条件 
- 确保具有 Azure CLI 版本2.9.0 或更高版本
- 确保已 `EnableAzureRBACPreview` 启用功能标志。
- 确保已 `aks-preview` 安装 [CLI extension][az-extension-add] v 0.4.55 或更高版本
- 请确保已安装 [kubectl v 1.18.3 +][az-aks-install-cli]。

#### <a name="register-enableazurerbacpreview-preview-feature"></a>注册 `EnableAzureRBACPreview` 预览功能

若要创建使用 Azure RBAC 进行 Kubernetes 授权的 AKS 群集，必须 `EnableAzureRBACPreview` 在订阅上启用功能标志。

`EnableAzureRBACPreview`使用[az feature register][az-feature-register]命令注册功能标志，如以下示例中所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"
```

 可以使用 [az feature list][az-feature-list] 命令检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"
```

准备就绪后，请使用 [az provider register] [az-provider-register] 命令刷新 *ContainerService* 资源提供程序的注册：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要创建使用 Azure RBAC 的 AKS 群集，需要 *AKS* CLI 扩展版本0.4.55 或更高版本。 使用[az extension add][az-extension-add]命令安装*aks-preview* Azure CLI 扩展，或使用[az extension update][az-extension-update]命令安装任何可用更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>限制

- 需要 [托管 Azure AD 集成](managed-aad.md)。
- 预览期间，不能将用于 Kubernetes 授权的 Azure RBAC 集成到现有群集中，但你可以 (GA) 正式上市。
- 使用 [kubectl v 1.18.3 +][az-aks-install-cli]。
- 在预览期间，只能通过 Azure CLI 添加 *命名空间级别* 权限。
- 如果你有 CRDs 并且正在进行自定义角色定义，则现在涵盖 CRDs 的唯一方法是提供 `Microsoft.ContainerService/managedClusters/*/read` 。 AKS 正在努力为 CRDs 提供更精细的权限。 对于剩余的对象，可以使用特定的 API 组，例如： `Microsoft.ContainerService/apps/deployments/read` 。
- 新角色分配最多可能需要就才能进行传播，并由授权服务器进行更新。

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>使用 Azure RBAC 和 managed Azure AD 集成创建新群集

使用以下 CLI 命令创建 AKS 群集。

创建 Azure 资源组：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

创建具有托管 Azure AD 集成的 AKS 群集和用于 Kubernetes 授权的 Azure RBAC。

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

成功创建具有 Azure AD 集成的群集和用于 Kubernetes 授权的 Azure RBAC 的响应正文中包含以下部分：

```json
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "enableAzureRbac": true,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "****-****-****-****-****"
  }
```

## <a name="create-role-assignments-for-users-to-access-cluster"></a>为用户创建用于访问群集的角色分配

AKS 提供下列四种内置角色：


| 角色                                | 描述  |
|-------------------------------------|--------------|
| Azure Kubernetes 服务 RBAC 查看者  | 允许进行只读访问并查看命名空间中的大多数对象。 不允许查看角色或角色绑定。 此角色不允许查看 `Secrets` ，因为读取机密内容可以访问命名空间中的 ServiceAccount 凭据，这将允许 API 访问命名空间中的任何 ServiceAccount (一种形式的权限提升)   |
| Azure Kubernetes 服务 RBAC 写入者 | 允许对命名空间中的大多数对象进行读/写访问。 此角色不允许查看或修改角色或角色绑定。 但是，此角色允许以命名空间中任何 ServiceAccount 的身份访问 `Secrets` 和运行 Pod，因此可用于获取命名空间中任何 ServiceAccount 的 API 访问级别。 |
| Azure Kubernetes 服务 RBAC 管理员  | 允许要在命名空间内授予的管理员访问权限。 允许对命名空间（或群集范围）中的大多数资源进行读/写访问，包括在命名空间内创建角色和角色绑定。 此角色不允许对资源配额或命名空间本身进行写入访问。 |
| Azure Kubernetes 服务 RBAC 群集管理员  | 允许超级用户访问权限（对任何资源执行任何操作）。 它提供对群集中每个资源和所有命名空间的完全控制。 |


作用域为 **整个 AKS 群集** 的角色分配可以在 Azure 门户上群集资源 (IAM) 边栏选项卡上完成，也可以使用 Azure CLI 命令完成，如下所示：

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

其中 `<AAD-ENTITY-ID>` ，可以是用户名 (例如， user@contoso.com) 甚至是服务主体的 ClientID。

你还可以创建作用域为群集中特定 **命名空间** 的角色分配：

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

目前，需要通过 Azure CLI 配置作用域为命名空间的角色分配。


### <a name="create-custom-roles-definitions"></a>创建自定义角色定义

或者，您可以选择创建自己的角色定义，并按上述方式分配。

下面是一个角色定义的示例，该角色允许用户仅读取部署，而无需读取其他内容。 你可以在 [此处](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)查看可能的操作的完整列表。


将下面的 json 复制到名为的文件中 `deploy-view.json` 。

```json
{
    "Name": "AKS Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.ContainerService/managedClusters/apps/deployments/read"  
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<YOUR SUBSCRIPTION ID>"
    ]   
}
```

将 `<YOUR SUBSCRIPTION ID>` 你的订阅中的 ID 替换为你可以通过运行以下内容获取：

```azurecli-interactive
az account show --query id -o tsv
```


现在，我们可以通过从你保存的文件夹运行以下命令来创建角色定义 `deploy-view.json` ：

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

现在，你已拥有角色定义，可以通过运行以下内容将其分配给用户或其他标识：

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>使用 Azure RBAC 进行 Kubernetes 授权 `kubectl`

> [!NOTE]
> 通过运行以下命令，确保具有最新的 kubectl：
>
> ```azurecli-interactive
> az aks install-cli
> ```
> 你可能需要以权限运行它 `sudo` 。 

现在，已分配了所需的角色和权限。 例如，你可以开始调用 Kubernetes API `kubectl` 。

为此，我们首先使用以下命令获取群集的 kubeconfig：

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> 需要 [Azure Kubernetes Service 群集用户](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) 内置角色才能执行上述步骤。

现在，你可以使用 kubectl 来列出群集中的节点。 首次运行时，需要登录，并且后续命令将使用各自的访问令牌。

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>使用 Azure RBAC 进行 Kubernetes 授权 `kubelogin`

若要取消阻止非交互式登录、较早 `kubectl` 版本或利用 SSO 跨多个群集的其他方案，而无需登录到新群集，AKS 创建了一个名为的 exec 插件 [`kubelogin`](https://github.com/Azure/kubelogin) 。

可以通过运行以下方法来使用它：

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

首次使用时，你必须使用常规 kubectl 以交互方式登录，但此后你将不再需要，即使对于新 Azure AD 群集 (，只要你的令牌仍有效) 。

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>清理

### <a name="clean-role-assignment"></a>清理角色分配

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
复制您执行的所有分配的 ID 或 id，然后复制。

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>清除角色定义

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>删除群集和资源组

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>后续步骤

- [在此处](concepts-identity.md)了解有关 AKS Authentication、AUTHORIZATION 和 RBAC 的详细信息。
- [在此处](../role-based-access-control/overview.md)阅读有关 Azure RBAC 的详细信息。
- 在 [此处](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)了解有关可用于以粒度定义 Kubernetes 授权的自定义 Azure 角色的所有操作的详细信息。


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli&preserve-view=true
