---
title: 在 Azure Kubernetes 服务中使用托管标识
description: 了解如何使用 Azure Kubernetes Service （AKS）中的托管标识
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.openlocfilehash: 6d00fd72c338fc101420bf78b5608516715d44ad
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77592962"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>预览-在 Azure Kubernetes 服务中使用托管标识

目前，Azure Kubernetes Service （AKS）群集（具体而言，是 Kubernetes 云提供商）需要*服务主体*才能在 Azure 中创建负载均衡器和托管磁盘等其他资源。 您必须提供一个服务主体或 AKS 代表您创建一个服务主体。 服务主体通常具有到期日期。 群集最终达到了必须续订服务主体以使群集正常运行的状态。 管理服务主体增加了复杂性。

*托管标识*本质上是服务主体的包装，使其管理更简单。 若要了解详细信息，请参阅[Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

AKS 创建两个托管标识：

- **系统分配的托管标识**： Kubernetes 云提供程序用来代表用户创建 Azure 资源的标识。 系统分配的标识的生命周期绑定到群集的生命周期。 删除群集时，将删除该标识。
- **用户分配的托管标识**：用于在群集中进行身份验证的标识。 例如，用户分配的标识用于授权 AKS 使用访问控制记录（Acr），或授权 kubelet 从 Azure 获取元数据。

在此预览期间，仍需要服务主体。 它用于授权加载项，例如监视、虚拟节点、Azure 策略和 HTTP 应用程序路由。 正在进行的工作是删除服务主体名称（SPN）上加载项的依赖项。 最终，将完全删除 AKS 中的 SPN 要求。

> [!IMPORTANT]
> AKS 预览版功能提供自助服务，可选择使用。 预览按 "原样" 提供，并从服务级别协议和有限担保中排除。 AKS 预览版已在最大程度上由客户支持部分覆盖。 因此，这些功能并不用于生产。 有关详细信息，请参阅以下支持文章：
>
> - [AKS 支持策略](support-policies.md)
> - [Azure 支持常见问题](faq.md)

## <a name="before-you-begin"></a>开始之前

必须安装下列资源：

- Azure CLI 2.0.70 或更高版本
- Aks-preview 0.4.14 扩展

若要安装 aks-preview 0.4.14 扩展或更高版本，请使用以下 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension list
```

> [!CAUTION]
> 在订阅上注册功能后，当前无法取消注册该功能。 启用某些预览功能后，默认值可用于在订阅中创建的所有 AKS 群集。 不要对生产订阅启用预览功能。 请改用单独的订阅来测试预览功能并收集反馈。

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

状态显示为 "**已注册**" 可能需要几分钟时间。 您可以使用[az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)命令检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

当状态显示为 "已注册" 时，使用[az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)命令刷新 `Microsoft.ContainerService` 资源提供程序的注册：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>使用托管标识创建 AKS 群集

现在可以使用以下 CLI 命令创建具有托管标识的 AKS 群集。

首先，创建一个 Azure 资源组：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

然后，创建 AKS 群集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

最后，获取用于访问群集的凭据：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

将在几分钟内创建该群集。 然后，你可以将应用程序工作负荷部署到新群集，并与之进行交互，就像你对基于服务主体的 AKS 群集进行的一样。

> [!IMPORTANT]
>
> - 只能在创建群集的过程中启用具有托管标识的 AKS 群集。
> - 无法更新或升级现有的 AKS 群集以启用托管标识。
