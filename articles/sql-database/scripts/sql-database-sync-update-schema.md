---
title: PowerShell 示例 - 更新 SQL 数据同步（预览版）的同步架构 | Microsoft Docs
description: 用于更新 SQL 数据同步的同步架构的 Azure PowerShell 示例脚本
services: sql-database
documentationcenter: sql-database
author: jognanay
manager: craigg
editor: ''
tags: ''
ms.assetid: ''
ms.service: sql-database
ms.custom: load & move data, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 01/10/2018
ms.author: jognanay
ms.reviewer: douglasl
ms.openlocfilehash: 6b117484eaa1a9ac3ed85dfe547b3f6163d702cf
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>使用 PowerShell 更新现有同步组中的同步架构

此 PowerShell 示例可更新现有 SQL 数据同步（预览版）同步组中的同步架构。 同步多个表时，此脚本可帮助你有效地更新同步架构。

此示例演示如何使用 **UpdateSyncSchema** 脚本，该脚本在 GitHub 上以 [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1) 的形式提供。

有关 SQL 数据同步的概述，请参阅[使用 Azure SQL 数据同步（预览版）跨多个云和本地数据库同步数据](../sql-database-sync-data.md)。
## <a name="prerequisites"></a>先决条件

本示例需要 Azure PowerShell 模块 4.2 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。
 
运行 `Login-AzureRmAccount`，创建与 Azure 的连接。

## <a name="examples"></a>示例

### <a name="example-1---add-all-tables-to-the-sync-schema"></a>示例 1 - 将所有表添加到同步架构

以下示例可刷新数据库架构，并可将中心数据库中的所有有效表添加到同步架构。

```powershell
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="example-2---add-and-remove-tables-and-columns"></a>示例 2 - 添加和删除表和列

以下示例将 `[dbo].[Table1]` 和 `[dbo].[Table2].[Column1]` 添加到同步架构并删除 `[dbo].[Table3]`。

```powershell
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>脚本参数

**UpdateSyncSchema** 脚本具有以下参数：

| 参数 | 说明 |
|---|---|
| $SubscriptionId | 在其中创建同步组的订阅。 |
| $ResourceGroupName | 在其中创建同步组的资源组。|
| $ServerName | 中心数据库的服务器名称。|
| $DatabaseName | 中心数据库名称。 |
| $SyncGroupName | 同步组名称。 |
| $MemberName | 如果要从同步成员而不是中心数据库加载数据库架构，请指定成员名称。 如果要从中心加载数据库架构，则将此参数保留为空。 |
| $TimeoutInSeconds | 该脚本刷新数据库架构时超时。 默认为 900 秒。 |
| $RefreshDatabaseSchema | 指定该脚本是否需要刷新数据库架构。 如果数据库架构已从以前的配置更改（例如，如果添加了新表或新列），则需要在重新配置之前刷新架构。 默认值为 false。 |
| $AddAllTables | 如果此值为 true，所有有效的表和列都将添加到同步架构。 $TablesAndColumnsToAdd 和 $TablesAndColumnsToRemove 的值将被忽略。 |
| $TablesAndColumnsToAdd | 指定要添加到同步架构的表或列。 每个表名或列名必须与架构名称完全分隔。 例如：`[dbo].[Table1]`、`[dbo].[Table2].[Column1]`。 可以指定多个表名或列名，并将其用逗号 (,) 分隔。 |
| $TablesAndColumnsToRemove | 指定要从同步架构中删除的表或列。 每个表名或列名必须与架构名称完全分隔。 例如：`[dbo].[Table1]`、`[dbo].[Table2].[Column1]`。 可以指定多个表名或列名，并将其用逗号 (,) 分隔。 |
|||

## <a name="script-explanation"></a>脚本说明

**UpdateSyncSchema** 脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [Get-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncgroup) | 返回有关同步组的信息。 |
| [Update-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncgroup) | 更新同步组。 |
| [Get-AzureRmSqlSyncMember](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncmember) | 返回有关同步成员的信息。 |
| [Get-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncschema) | 返回有关同步架构的信息。 |
| [Update-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncschema) | 更新同步架构。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure SQL 数据库 PowerShell 脚本](../sql-database-powershell-samples.md)中找到更多 SQL 数据库 PowerShell 脚本示例。

有关 SQL 数据同步的详细信息，请参阅：

-   [使用 Azure SQL 数据同步跨多个云和本地数据库同步数据](../sql-database-sync-data.md)
-   [设置 Azure SQL 数据同步](../sql-database-get-started-sql-data-sync.md)
-   [Azure SQL 数据同步最佳实践](../sql-database-best-practices-data-sync.md)
-   [使用 Log Analytics 监视 Azure SQL 数据同步](../sql-database-sync-monitor-oms.md)
-   [Azure SQL 数据同步问题疑难解答](../sql-database-troubleshoot-data-sync.md)

-   演示如何配置 SQL 数据同步的完整 PowerShell 示例：
    -   [使用 PowerShell 在多个 Azure SQL 数据库之间进行同步](sql-database-sync-data-between-sql-databases.md)
    -   [使用 PowerShell 在 Azure SQL 数据库和 SQL Server 本地数据库之间进行同步](sql-database-sync-data-between-azure-onprem.md)

-   [下载 SQL 数据同步 REST API 文档](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

有关 SQL 数据库的详细信息，请参阅：

-   [SQL 数据库概述](../sql-database-technical-overview.md)
-   [数据库生命周期管理](https://msdn.microsoft.com/library/jj907294.aspx)
