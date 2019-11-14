---
title: PowerShell 审核和高级威胁防护示例 - Azure SQL 数据库
description: 在 Azure SQL 数据库中配置审核和高级威胁防护的 Azure PowerShell 示例脚本
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: security
ms.devlang: PowerShell
ms.topic: sample
author: ronitr
ms.author: ronitr
ms.reviewer: carlrab, vanto
ms.date: 08/05/2019
ms.openlocfilehash: 1dee8031b00e7d291844790fb86efd4d00075d87
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691745"
---
# <a name="use-powershell-to-configure-sql-database-auditing-and-advanced-threat-protection"></a>使用 PowerShell 配置 SQL 数据库审核和高级威胁防护

此 PowerShell 脚本示例配置 SQL 数据库审核和高级威胁防护。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 AZ PowerShell 1.4.0 或更高版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.ps1?highlight=15-16 "Configure auditing and threat detection")]

## <a name="clean-up-deployment"></a>清理部署

使用以下命令删除资源组及其相关的所有资源。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 创建托管单一数据库或弹性池的 SQL 数据库服务器。 |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 创建单一数据库或弹性池。 |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | 创建存储帐户。 |
| [Set-AzSqlDatabaseAuditing](/powershell/module/az.sql/set-azsqldatabaseauditing) | 设置数据库的审核策略。 |
| [Set-AzSqlDatabaseThreatDetectionPolicy](/powershell/module/az.sql/set-azsqldatabasethreatdetectionpolicy) | 在数据库上设置高级威胁防护策略。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure SQL 数据库 PowerShell 脚本](../sql-database-powershell-samples.md)中找到更多 SQL 数据库 PowerShell 脚本示例。
