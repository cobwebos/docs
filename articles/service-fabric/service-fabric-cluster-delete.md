---
title: "删除 Azure 群集及其资源 | Microsoft 文档"
description: "了解如何通过删除包含该群集的资源组或有选择地删除资源彻底删除 Service Fabric 群集。"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: de422950-2d22-4ddb-ac47-dd663a946a7e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/24/2017
ms.author: chackdan
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: c2792287ef8b25a57beb4af069ffc5a3eed85e15
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017

---
<a id="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses" class="xliff"></a>

# 在 Azure 上删除 Service Fabric 群集及其所用资源
Service Fabric 群集由群集资源本身及众多其他 Azure 资源组成。 因此，若要彻底删除 Service Fabric 群集，还需删除组成该群集的所有资源。
可使用两种方法：删除该群集所在的资源组（此操作将删除该资源组中的群集资源及其他任何资源），或特定删除群集资源及其关联资源（而不删除资源组中的其他资源）。

> [!NOTE]
> 删除群集资源**不会**删除组成 Service Fabric 的其他所有资源。
> 
> 

<a id="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in" class="xliff"></a>

## 删除 Service Fabric 群集所在的整个资源组 (RG)
这是确保删除与你的群集相关联的所有资源（包括资源组）的最简方法。 可使用 PowerShell 或通过 Azure 门户删除资源组。 如果你的资源组中有与 Service Fabric 群集不相关的资源，你可以删除特定资源。

<a id="delete-the-resource-group-using-azure-powershell" class="xliff"></a>

### 使用 Azure PowerShell 删除资源组
也可通过运行以下 Azure PowerShell cmdlet 删除资源组。 请确保计算机上已安装 Azure PowerShell 1.0 或更高版本。 如果尚未安装，请按照[如何安装和配置 Azure PowerShell](/powershell/azure/overview) 中所述的步骤进行安装

打开 PowerShell 窗口并运行以下 PS cmdlet：

```powershell
Login-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

如果未使用 *-Force* 选项，则将提示你确认删除。 确认后，即删除 RG 及其包含的所有资源。

<a id="delete-a-resource-group-in-the-azure-portal" class="xliff"></a>

### 在 Azure 门户中删除资源组
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到要删除的 Service Fabric 群集。
3. 在群集基本信息页上单击资源组名称。
4. 此时将显示“**资源组概要**”页。
5. 单击“删除” 。
6. 按照该页面上的说明进行操作，以完成资源组的删除。

![资源组删除][ResourceGroupDelete]

<a id="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group" class="xliff"></a>

## 删除群集资源及其所用资源，但不删除资源组中的其他资源
如果你的资源组仅包含与要删除的 Service Fabric 群集相关的资源，那么删除整个资源组更方便。 如果你想有选择地逐个删除资源组中的资源，请按照下以步骤进行操作。

如果使用门户或通过模板库中的 Service Fabric Resource Manager 模板部署群集，该群集使用的所有资源都带有以下两个标记。 可以使用它们来确定要删除的资源。

***标记 # 1：***键 = clusterName，值 = “群集名称”

***标记 2：***键 = resourceName，值 = ServiceFabric

<a id="delete-specific-resources-in-the-azure-portal" class="xliff"></a>

### 在 Azure 门户中删除特定资源
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到要删除的 Service Fabric 群集。
3. 在“基本信息”边栏选项卡上转到“**所有设置**”。
4. 在“设置”边栏选项卡中，单击“**资源管理**”下的“**标记**”。
5. 在“标记”边栏选项卡中单击“**标记**”之一，以获取带有该标记的所有资源的列表。
   
    ![资源标记][ResourceTags]
6. 获得带标记的资源的列表后，单击每个资源并将其删除。
   
    ![带标记的资源][TaggedResources]

<a id="delete-the-resources-using-azure-powershell" class="xliff"></a>

### 使用 Azure PowerShell 删除资源
可通过运行以下 Azure PowerShell cmdlet 逐个删除资源。 请确保计算机上已安装 Azure PowerShell 1.0 或更高版本。 如果尚未安装，请按照[如何安装和配置 Azure PowerShell](/powershell/azure/overview) 中所述的步骤进行安装

打开 PowerShell 窗口并运行以下 PS cmdlet：

```powershell
Login-AzureRmAccount
```
对要删除的每项资源，运行以下命令：

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

若要删除群集资源，请运行以下命令：

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

<a id="next-steps" class="xliff"></a>

## 后续步骤
参阅以下文章以了解如何升级群集以及对服务进行分区：

* [了解群集升级](service-fabric-cluster-upgrade.md)
* [了解如何为有状态服务分区以最大程度地实现缩放](service-fabric-concepts-partitioning.md)

<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG

