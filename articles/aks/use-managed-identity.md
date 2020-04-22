---
title: 在 Azure 库伯奈斯服务中使用托管标识
description: 了解如何在 Azure 库伯奈斯服务 （AKS） 中使用托管标识
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 04/02/2020
ms.author: saudas
ms.openlocfilehash: 907aa83bc293aacd9920d8fd79a1b3184dd1d5dc
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767598"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>在 Azure 库伯奈斯服务中使用托管标识

目前，Azure Kubernetes 服务 （AKS） 群集（特别是 Kubernetes 云提供程序）需要一个标识来创建其他资源，如 Azure 中的负载均衡器和托管磁盘，此标识可以是*托管标识*或*服务主体*。 如果使用[服务主体](kubernetes-service-principal.md)，则必须提供一个或 AKS 代表您创建一个。 如果您使用托管标识，AKS 将自动为您创建此标识。 使用服务主体的群集最终达到必须续订服务主体以保持群集工作的状态。 管理服务主体会增加复杂性，这就是为什么它更容易使用托管标识。 相同的权限要求适用于服务主体和托管标识。

*托管标识*本质上是围绕服务主体的包装，并简化其管理。 要了解更多信息，请阅读[Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

AKS 创建两个托管标识：

- **系统分配的托管标识**：Kubernetes 云提供程序用于代表用户创建 Azure 资源的标识。 系统分配的标识的生命周期与群集的生命周期相关联。 删除群集时，标识将被删除。
- **用户分配的托管标识**：用于群集中授权的标识。 例如，用户分配的标识用于授权 AKS 使用 Azure 容器注册表 （ACL），或授权 kubelet 从 Azure 获取元数据。

加载项还会使用托管标识进行身份验证。 对于每个加载项，托管标识由 AKS 创建，并持续加载项的生命周期。 要创建和使用资源位于MC_* 资源组之外的您自己的 VNet、静态 IP 地址或附加的 Azure 磁盘，请使用群集的主体 ID 执行角色分配。 有关角色分配的详细信息，请参阅[委派对其他 Azure 资源的访问权限](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)。

## <a name="before-you-begin"></a>开始之前

您必须安装以下资源：

- Azure CLI，版本 2.2.0 或更高版本

## <a name="create-an-aks-cluster-with-managed-identities"></a>使用托管标识创建 AKS 群集

现在，您可以使用以下 CLI 命令创建具有托管标识的 AKS 群集。

首先，创建 Azure 资源组：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

然后，创建 AKS 群集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

使用托管标识成功创建群集包含此服务主体配置文件信息：

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

最后，获取访问群集的凭据：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

群集将在几分钟内创建。 然后，您可以将应用程序工作负载部署到新群集，并与它进行交互，就像使用基于服务主体的 AKS 群集一样。

> [!IMPORTANT]
>
> - 只能在创建群集期间启用具有托管标识的 AKS 群集。
> - 无法更新或升级现有 AKS 群集以启用托管标识。
