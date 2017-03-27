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
ms.date: 03/07/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: ce4cbe6ee01fcd56d2a3c311a45e47a6f06593fd
ms.lasthandoff: 03/10/2017

---

# <a name="monitor-and-scale-a-single-sql-database-using-powershell"></a>使用 PowerShell 监视和缩放单个 SQL 数据库

此示例 PowerShell 脚本监视一个数据库的性能指标，将其缩放为更高的性能级别，并基于性能指标之一创建警报规则。 

在运行此脚本前，请确保已使用 `Add-AzureRmAccount` cmdlet 创建了到 Azure 的连接。

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
 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) | 创建用于托管数据库或弹性池的逻辑服务器。 |
| [Get-AzureRmMetric](https://docs.microsoft.com/powershell/resourcemanager/azurerm.insights/v2.5.0/get-azurermmetric) | 显示数据库的大小使用情况信息。|
| [Set-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/set-azurermsqldatabase) | 更新数据库属性，或者将数据库移入、移出弹性池或在弹性池之间移动。 |
| [Add-AzureRMMetricAlertRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.insights/v2.5.0/add-azurermmetricalertrule) | 设置警报规则，以便在将来自动监视 DTU。 |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure SQL 数据库 PowerShell 脚本](../sql-database-powershell-samples.md)中找到更多 SQL 数据库 PowerShell 脚本示例。

