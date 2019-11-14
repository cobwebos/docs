---
title: PowerShell 示例 - 故障转移组 - Azure SQL 数据库弹性池
description: Azure PowerShell 示例脚本，用于创建 Azure SQL 数据库弹性池，将其添加到故障转移组，然后测试故障转移。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: a8c9b7dbddba59408808e1c33de045ad0b7ffd7a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691789"
---
# <a name="use-powershell-to-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>使用 PowerShell 将 Azure SQL 数据库弹性池添加到故障转移组 

此 PowerShell 脚本示例创建一个单一数据库，将其添加到弹性池，创建一个故障转移组，然后测试故障转移。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 AZ PowerShell 1.4.0 或更高版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="sample-scripts"></a>示例脚本

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

## <a name="clean-up-deployment"></a>清理部署

使用以下命令删除资源组及其相关的所有资源。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 创建托管单一数据库和弹性池的 SQL 数据库服务器。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 为逻辑服务器创建防火墙规则。 | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 新建 Azure SQL 数据库单一数据库。 | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | 为 Azure SQL 数据库创建弹性数据库池。| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | 设置数据库的属性，或将现有数据库移到弹性池中。 | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | 新建故障转移组。 |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | 获取一个或更多个 SQL 数据库。 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | 将一个或更多个 Azure SQL 数据库添加到故障转移组。 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 获取或列出 Azure SQL 数据库故障转移组。 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| 执行 Azure SQL 数据库故障转移组的故障转移。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组 | 

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure SQL 数据库 PowerShell 脚本](../sql-database-powershell-samples.md)中找到更多 SQL 数据库 PowerShell 脚本示例。
