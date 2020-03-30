---
title: 在 Azure 库伯奈斯服务中使用托管标识
description: 了解如何在 Azure 库伯奈斯服务 （AKS） 中使用托管标识
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 03/10/2019
ms.author: saudas
ms.openlocfilehash: 85efc6d9d203ca06c5f7566376993b4c13950788
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369971"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>在 Azure 库伯奈斯服务中使用托管标识

目前，Azure Kubernetes 服务 （AKS） 群集（特别是 Kubernetes 云提供商）需要*服务主体*才能在 Azure 中创建其他资源，如负载均衡器和托管磁盘。 您必须提供服务主体，要么 AKS 代表您创建服务主体。 服务主体通常具有到期日期。 群集最终达到必须续订服务主体以保持群集工作的状态。 管理服务主体会增加复杂性。

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
