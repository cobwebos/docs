---
title: 添加和删除 Service Fabric 托管群集的节点类型（预览版）
description: 本教程介绍如何添加和删除 Service Fabric 托管群集的节点类型。
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 860345f5b297edaeea9d099ac392243176dfaca7
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410187"
---
# <a name="tutorial-add-and-remove-node-types-from-a-service-fabric-managed-cluster-preview"></a>教程：在 Service Fabric 托管群集中添加和删除节点类型（预览版）

在此教程系列中，我们将讨论以下内容：

> [!div class="checklist"]
> * [如何部署 Service Fabric 托管群集](tutorial-managed-cluster-deploy.md)
> * [如何横向扩展 Service Fabric 托管群集](tutorial-managed-cluster-scale.md)
> * 如何在 Service Fabric 托管群集中添加和删除节点
> * [如何将应用程序部署到 Service Fabric 托管群集](tutorial-managed-cluster-deploy-app.md)

本系列中的这一部分介绍如何执行以下操作：

> [!div class="checklist"]
> * 将节点类型添加到 Service Fabric 托管群集
> * 从 Service Fabric 托管群集中删除节点类型

## <a name="prerequisites"></a>先决条件

* Service Fabric 托管群集（请参阅[部署托管群集](tutorial-managed-cluster-deploy.md)）。
* [Azure PowerShell 4.7.0](https://docs.microsoft.com/powershell/azure/release-notes-azureps?view=azps-4.7.0&preserve-view=true#azservicefabric) 或更高版本（请参阅[*安装 Azure PowerShell*](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-4.7.0&preserve-view=true)）。

## <a name="add-a-node-type-to-a-service-fabric-managed-cluster"></a>将节点类型添加到 Service Fabric 托管群集

可以通过 Azure 资源管理器模板、PowerShell 或 CLI 将节点类型添加到 Service Fabric 托管群集。 在本教程中，我们将使用 Azure PowerShell 添加节点类型。

若要新建节点类型，需要定义三个属性：

* **节点类型名称**：在群集的任何现有节点类型中独一无二的名称。
* **实例计数**：新节点类型的初始节点数。
* **VM 大小**：节点的 VM SKU。 如果未指定，则使用默认值“Standard_D2”。

> [!NOTE]
> 如果要添加的节点类型是群集中的第一个节点类型或唯一的节点类型，则必须使用 Primary 属性。

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "nt2"
$vmSize = "Standard_D2_v2"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeTypeName -InstanceCount 3 -vmSize $vmSize
```

## <a name="remove-a-node-type-from-a-service-fabric-managed-cluster"></a>从 Service Fabric 托管群集中删除节点类型

若要从 Service Fabric 托管群集中删除节点类型，必须使用 PowerShell 或 CLI。 在本教程中，我们将使用 Azure PowerShell 删除节点类型。

> [!NOTE]
> 如果某个主节点类型是群集中唯一的主节点类型，则不能将其删除。  

若要删除某个节点类型，请使用以下代码：

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "myCluster"
$nodeTypeName = "nt2"

Remove-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName  -Name $nodeTypeName
```

## <a name="next-steps"></a>后续步骤

 在本部分中，我们添加和删除了节点类型。 若要了解如何将应用程序部署到 Service Fabric 托管群集，请参阅：

> [!div class="nextstepaction"]
> [将应用部署到 Service Fabric 托管群集](tutorial-managed-cluster-deploy-app.md)
