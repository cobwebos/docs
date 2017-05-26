---
title: "Azure PowerShell 脚本 - 监视和缩放单个 SQL 数据库 | Microsoft 文档"
description: "Azure PowerShell 脚本示例 - 使用 PowerShell 监视和缩放单个 SQL 数据库"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: PowerShell
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 05/23/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: b214b2d0317ff9e7adf2d75fe462a5ad9f640298
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017

---

# <a name="monitor-and-scale-a-single-sql-database-using-powershell"></a>使用 PowerShell 监视和缩放单个 SQL 数据库

此示例 PowerShell 脚本监视一个数据库的性能指标，将其缩放为更高的性能级别，并基于性能指标之一创建警报规则。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.ps1 "监视和缩放单个 SQL 数据库")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | 创建用于托管数据库或弹性池的逻辑服务器。 |
| [Get-AzureRmMetric](/powershell/module/azurerm.insights/get-azurermmetric) | 显示数据库的大小使用情况信息。|
| [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) | 更新数据库属性，或者将数据库移入、移出弹性池或在弹性池之间移动。 |
| [Add-AzureRMMetricAlertRule](/powershell/module/azurerm.insights/add-azurermmetricalertrule) | 设置警报规则，以便在将来自动监视 DTU。 |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure SQL 数据库 PowerShell 脚本](../sql-database-powershell-samples.md)中找到更多 SQL 数据库 PowerShell 脚本示例。

