---
title: 使用 PowerShell 执行 Azure Cosmos DB 表 API 操作 | Microsoft 文档
description: 如何使用 PowerShell 执行 Azure Cosmos DB 表 API 操作
services: storage
author: SnehaGunda
manager: kfile
editor: tysonn
ms.service: storage
ms.component: cosmosdb-table
ms.devlang: na
ms.topic: how-to
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: cce1cfc4cf883fcecab0e339177bc70c18e7af0b
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798638"
---
# <a name="perform-azure-cosmos-db-table-api-operations-with-azure-powershell"></a>使用 Azure PowerShell 执行 Azure Cosmos DB 表 API 操作 

>[!NOTE]
>Azure Cosmos DB 表 API 提供了用于表存储的高级功能，如统包全局分发、低延迟读取和写入、自动辅助索引和专用吞吐量。 在大多数情况下，本文中的 PowerShell 命令适用于 Azure Cosmos DB 表 API 和 Azure 表存储，但本文特定于 Azure Cosmos DB 表 API。 如果要使用 Azure 表存储，请参阅[使用 Azure PowerShell 执行 Azure 表存储操作](../storage/tables/table-storage-how-to-use-powershell.md)。
>

可以通过 Azure Cosmos DB 表 API 存储和查询大型结构化的非关系型数据。 该服务的主要组件包括表、实体和属性。 表是实体的集合。 实体是一组属性。 每个实体最多可以有 252 个属性（都是一些名称-值对）。 本文假设用户已熟悉 Azure Cosmos DB 表 API 的概念。 有关详细信息，请参阅 [Azure Cosmos DB 表 API 简介](table-introduction.md)和[使用表 API 生成 .NET 应用程序](create-table-dotnet.md)。

此操作指南文章介绍了常见的表 API 操作。 学习如何： 

> [!div class="checklist"]
> * 创建表
> * 检索表
> * 添加表实体
> * 查询表
> * 删除表实体

## <a name="prerequisites"></a>先决条件

本文中的示例需要 Azure PowerShell 模块 4.4.0 或更高版本。 在 PowerShell 窗口中，运行 `Get-Module -ListAvailable AzureRM` 可查找版本。 如果未显示任何信息或需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 

安装或更新 Azure PowerShell 后，必须安装模块 AzureRmStorageTable，其中包含用于管理实体的命令。 若要安装此模块，请以管理员身份运行 PowerShell 并使用 Install-Module 命令。

```powershell
Install-Module AzureRmStorageTable
```

然后在本地安装 Azure Cosmos DB 程序集，以使用这些 PowerShell cmdlet。 有关如何执行该操作的说明，请参阅 [Cosmos DB 表的 Azure RM 存储表 PowerShell 模块](https://blogs.technet.microsoft.com/paulomarques/2017/05/23/azure-rm-storage-tables-powershell-module-now-includes-support-for-cosmos-db-tables/)。

若要尝试以下练习，需要一个 Azure Cosmos DB 数据库帐户。 如果还没有帐户，请使用 [Azure 门户](https://portal.azure.com)新建一个 Azure Cosmos DB 帐户。 有关新建数据库帐户的帮助，请参阅 [Azure Cosmos DB：创建数据库帐户](create-table-dotnet.md#create-a-database-account)。

从门户获取数据库帐户名称和资源组；你需要将这些值放入脚本中来访问表。 

## <a name="sign-in-to-azure"></a>登录 Azure

使用 `Connect-AzureRmAccount` 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```powershell
Connect-AzureRmAccount
```

## <a name="create-a-table-or-reference-a-table"></a>创建表或引用表

若要创建一个表或获取对表的引用，请使用 Get-AzureStorageTableTable。 如果使用一个不存在的表名称来调用此 cmdlet，它将使用该该名称新建一个表，并返回对新表的引用。 如果表存在，则返回对现有表的引用。

```powershell
# set the name of the resource group in which your Cosmos DB Account resides.
$resourceGroup = "contosocosmosrg"
# if you want to make sure the resource group is valid, try this command
Get-AzureRmResourceGroup -Name $resourceGroup

# set the Cosmos DB account name 
$cosmosDBAccountName = "contosocosmostbl" 

# set the table name 
$tableName = "contosotable1"

# Get a reference to a table. This creates the table if it doesn't exist.
$storageTable = Get-AzureStorageTableTable `
  -resourceGroup $resourceGroup `
  -tableName $tableName `
  -cosmosDbAccount $cosmosDBAccountName 
```

不能使用 PowerShell 在 Azure Cosmos DB 帐户中列出表，但可以登录到门户查看该表。 现在让我们看一下如何管理表中的实体。

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>删除表 

PowerShell 不支持从 Azure Cosmos DB 删除表。 若要删除表，请转到 [Azure 门户](https://azure.portal.com)，找到你正在使用的 Azure Cosmos DB 帐户，然后查找并删除表。 

## <a name="clean-up-resources"></a>清理资源

如果创建了新的资源组并在该组中创建了新的 Azure Cosmos DB 帐户，则可以通过删除资源组来删除在本练习中创建的所有资产。 该命令会删除包含在组中的所有资源以及资源组本身。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>后续步骤

本操作指南文章介绍了使用 PowerShell 进行常见的表 API 操作，其中包括如何： 

> [!div class="checklist"]
> * 创建表
> * 检索表
> * 添加表实体
> * 查询表
> * 删除表实体

有关详细信息，请参阅以下文章：

* [从 PowerShell 使用 Azure 存储表](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/)

* [Microsoft Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)是 Microsoft 免费提供的独立应用，适用于在 Windows、macOS 和 Linux 上以可视方式处理 Azure 存储数据。
