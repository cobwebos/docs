---
title: PowerShell 示例 - 监视 - 缩放 - 弹性池 - Azure SQL 数据库
description: 用于在 Azure SQL 数据库中监视和缩放弹性池的 Azure PowerShell 脚本示例
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 6ab361b67741f2b96f593d04dafbabe355fc9121
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691618"
---
# <a name="use-powershell-to-monitor-and-scale-an-elastic-pool-in-azure-sql-database"></a>使用 PowerShell 在 Azure SQL 数据库中监视和缩放弹性池

此 PowerShell 脚本示例监视弹性池的性能指标，将其扩展到更高的计算大小，并基于性能指标之一创建警报规则。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 AZ PowerShell 1.4.0 或更高版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/monitor-and-scale-pool/monitor-and-scale-pool.ps1?highlight=17-18 "Monitor and scale a single SQL Database")]

## <a name="clean-up-deployment"></a>清理部署

使用以下命令删除资源组及其相关的所有资源。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 创建托管单一数据库或弹性池的 SQL 数据库服务器。 |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | 创建弹性池。 |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 创建单一数据库或创建弹性池中的数据库。 |
| [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) | 显示数据库的大小使用情况信息。|
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | 添加或更新基于指标的警报规则。 |
| [Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) | 更新弹性池属性 |
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | 设置警报规则，以便在将来自动监视 DTU。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure SQL 数据库 PowerShell 脚本](../sql-database-powershell-samples.md)中找到更多 SQL 数据库 PowerShell 脚本示例。
