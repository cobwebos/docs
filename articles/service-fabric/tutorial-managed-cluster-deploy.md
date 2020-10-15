---
title: 部署 Service Fabric 托管群集（预览版）
description: 在本教程中，你将部署一个 Service Fabric 托管群集来进行测试。
ms.topic: tutorial
ms.date: 08/27/2020
ms.custom: references_regions
ms.openlocfilehash: c7ed1a8fceeddecb942edb541c6112492a6e5a2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91410185"
---
# <a name="tutorial-deploy-a-service-fabric-managed-cluster-preview"></a>教程：部署 Service Fabric 托管群集（预览版）

在本系列教程中，我们将讨论以下内容：

> [!div class="checklist"]
> * 如何部署 Service Fabric 托管群集 
> * [如何横向扩展 Service Fabric 托管群集](tutorial-managed-cluster-scale.md)
> * [如何在 Service Fabric 托管群集中添加和删除节点](tutorial-managed-cluster-add-remove-node-type.md)
> * [如何将应用程序部署到 Service Fabric 托管群集](tutorial-managed-cluster-deploy-app.md)

本系列中的这一部分介绍如何执行以下操作：

> [!div class="checklist"]
> * 连接到 Azure 帐户
> * 创建新的资源组
> * 部署 Service Fabric 托管群集
> * 将主节点类型添加到群集

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前：

* 如果你还没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* 安装 [Service Fabric SDK 和 PowerShell 模块](service-fabric-get-started.md)。

* 安装 [Azure PowerShell 4.7.0](https://docs.microsoft.com/powershell/azure/release-notes-azureps?view=azps-4.7.0&preserve-view=true#azservicefabric)（或更高版本）。

## <a name="connect-to-your-azure-account"></a>连接到 Azure 帐户

将 `<your-subscription>` 替换为 Azure 帐户的订阅字符串，并进行连接：

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <your-subscription>

```

## <a name="create-a-new-resource-group"></a>创建新的资源组

接下来，为托管 Service Fabric 群集创建资源组，创建时请将 `<your-rg>` 和 `<location>` 替换为所需的组名称和位置。

> [!NOTE]
> 公共预览版支持的区域包括 `centraluseuap`、`eastus2euap`、`eastasia`、`northeurope`、`westcentralus` 和 `eastus2`。

```powershell
$resourceGroup = "myResourceGroup"
$location = "EastUS2"

New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="deploy-a-service-fabric-managed-cluster"></a>部署 Service Fabric 托管群集

### <a name="create-a-service-fabric-managed-cluster"></a>创建 Service Fabric 托管群集

在这一步中，你将使用 New-AzServiceFabricManagedCluster PowerShell 命令来创建 Service Fabric 托管群集。 以下示例在名为 myResourceGroup 的资源组中创建名为 myCluster 的群集。 此资源组是上一步中在 eastus2 区域创建的。

在这一步中，请为以下参数提供你自己的值：

* **群集名称**：输入群集的唯一名称，如“mysfcluster”。
* **管理员密码**：输入要用于群集中基础 VM 上 RDP 的管理员密码。
* 客户端证书指纹：提供要用于访问群集的客户端证书的指纹。 如果你没有证书，请按照[设置和检索证书](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-portal)中的说明来创建自签名证书。
* 群集 SKU：指定要部署的 [Service Fabric 托管群集的类型](overview-managed-cluster.md#service-fabric-managed-cluster-skus)。 基本 SKU 群集仅适用于测试部署，不允许添加或删除节点类型。

```powershell
$clusterName = "<unique cluster name>"
$password = "Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$clientThumbprint = "<certificate thumbprint>"
$clusterSku = "Standard"

New-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroup -Location $location -ClusterName $clusterName -ClientCertThumbprint $clientThumbprint -ClientCertIsAdmin -AdminPassword $password -Sku $clusterSKU -Verbose
```

### <a name="add-a-primary-node-type-to-the-service-fabric-managed-cluster"></a>将主节点类型添加到 Service Fabric 托管群集

在这一步中，你将为刚才创建的群集添加主节点类型。 每一个 Service Fabric 群集都必须至少有一个主节点类型。

在这一步中，请为以下参数提供你自己的值：

* 节点类型名称：为要添加到群集的节点类型输入唯一名称，如“NT1”。

> [!NOTE]
> 如果要添加的节点类型是群集中的第一个或唯一的节点类型，则必须使用 Primary 属性。

```powershell
$nodeType1Name = "NT1"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeType1Name -Primary -InstanceCount 5
```

此命令可能需要花费几分钟时间完成。

## <a name="validate-the-deployment"></a>验证部署

### <a name="review-deployed-resources"></a>查看已部署的资源

部署完成之后，请在门户中的 Service Fabric 托管群集资源概述页中查找 Service Fabric Explorer 值。 在系统提示你提供证书时，请使用在 PowerShell 命令中为其提供了客户端指纹的证书。

## <a name="next-steps"></a>后续步骤

在这一步中，我们创建并部署了第一个 Service Fabric 托管群集。 若要详细了解如何缩放群集，请参阅：

> [!div class="nextstepaction"]
> [横向扩展 Service Fabric 托管群集](tutorial-managed-cluster-scale.md)
