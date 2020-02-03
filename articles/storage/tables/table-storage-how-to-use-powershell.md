---
title: 使用 PowerShell 执行 Azure 表存储操作 | Microsoft 文档
description: 了解如何使用 PowerShell 从 Azure 表存储帐户运行创建、查询和删除数据等常见任务。
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: tables
ms.openlocfilehash: f1846fae4cbf473df688a2b184c307d72ab2f8d0
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721466"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>使用 Azure PowerShell 执行 Azure 表存储操作 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Azure 表存储是一种 NoSQL 数据存储，可用于存储和查询大量的结构化非关系型数据。 该服务的主要组件包括表、实体和属性。 表是实体的集合。 实体是一组属性。 每个实体最多可以有 252 个属性（都是一些名称-值对）。 本文假设用户熟知 Azure 表存储服务的概念。 有关详细信息，请参阅 [Understanding the Table Service Data Model](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model)（了解表服务数据模型）和[通过 .NET 开始使用 Azure 表存储](../../cosmos-db/table-storage-how-to-use-dotnet.md)。

此操作指南文章介绍常见的 Azure 表存储操作。 学习如何： 

> [!div class="checklist"]
> * 创建表
> * 检索表
> * 添加表实体
> * 查询表
> * 删除表实体
> * 删除表

本操作指南文章介绍如何在新的资源组中新建 Azure 存储帐户，以便可以在创建完成后轻松删除。 如果你要使用现有存储帐户，也可以改用现有帐户。

这些示例要求 Az PowerShell 模块 `Az.Storage (1.1.0 or greater)` 和 `Az.Resources (1.2.0 or greater)`。 在 PowerShell 窗口中，运行 `Get-Module -ListAvailable Az*` 可查找版本。 如果未显示任何信息或需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。

> [!IMPORTANT]
> 使用 PowerShell 的 Azure 功能必须已安装 `Az` 模块。 `AzTable` 的当前版本与较旧的 AzureRM 模块不兼容。
> 如果需要，请遵循[安装 Az module 的最新安装说明](/powershell/azure/install-az-ps)。

安装或更新 Azure PowerShell 之后，必须安装 module **AzTable**，其中包含用于管理实体的命令。 若要安装此模块，请以管理员身份运行 PowerShell 并使用 Install-Module 命令。

> [!IMPORTANT]
> 出于模块名称兼容性原因，我们仍将在 PowerShell 库中的旧名称下发布此相同模块 `AzureRmStorageTables`。 本文档将仅引用新名称。

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>登录 Azure

运行 `Add-AzAccount` 命令以登录 Azure 订阅，并按照屏幕上的说明操作。

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>检索位置列表

如果你不知道要使用哪个位置，可以列出可用的位置。 显示列表后，找到要使用的位置。 这些示例使用 eastus。 将此值存储在变量 location 中，以供以后使用。

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令创建资源组。 

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 将资源组名称存储在变量中，以供以后使用。 本示例在 eastus 区域中创建名为 pshtablesrg 的资源组。

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>创建存储帐户

使用 [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) 创建具有本地冗余存储 (LRS) 的标准常规用途存储帐户。 请确保指定唯一的存储帐户名称。 接下来，获取表示存储帐户的上下文。 在存储帐户上操作时，可以引用上下文，而不是重复提供凭据。

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>创建新表

若要创建表，请使用[AzStorageTable](/powershell/module/az.storage/New-AzStorageTable) cmdlet。 在本示例中，表名为 `pshtesttable`。

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>在存储帐户中检索表列表

使用[AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable)在存储帐户中检索表的列表。

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>检索对特定表的引用

若要对表执行操作，需要引用特定表。 使用[AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable)获取引用。

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>引用特定表的 CloudTable 属性

> [!IMPORTANT]
> 使用**AzTable** PowerShell 模块时，CloudTable 的使用是必需的。 调用**AzTableTable**命令获取对此对象的引用。 此命令还会创建表（如果该表尚不存在）。

若要使用**AzTable**对表执行操作，需要引用特定表的 CloudTable 属性。

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>删除表

若要删除表，请使用[AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable)。 此 cmdlet 将删除表，包括表中的所有数据。

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>清理资源

如果在本操作指南开始时创建了新的资源组和存储帐户，则可以通过删除资源组来删除在本练习中创建的所有资产。 该命令会删除包含在组中的所有资源以及资源组本身。

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>后续步骤

本操作指南文章介绍了使用 PowerShell 执行常见的 Azure 表存储操作，其中包括如何： 

> [!div class="checklist"]
> * 创建表
> * 检索表
> * 添加表实体
> * 查询表
> * 删除表实体
> * 删除表

有关详细信息，请参阅以下文章：

* [存储 PowerShell cmdlet](/powershell/module/az.storage#storage)

* [从 PowerShell 使用 Azure 表-AzureRmStorageTable/AzTable PS Module v2。0](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [Microsoft Azure 存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md)是 Microsoft 免费提供的独立应用，适用于在 Windows、macOS 和 Linux 上以可视方式处理 Azure 存储数据。
