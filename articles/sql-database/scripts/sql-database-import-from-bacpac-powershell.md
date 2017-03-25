---
title: "Azure PowerShell 脚本 - 导入 bacpac SQL 数据库 | Microsoft 文档"
description: "Azure PowerShell 脚本示例 - 使用 PowerShell 从 bacpac 导入 SQL 数据库"
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
ms.openlocfilehash: 6ca1560d6d3fa313bd24cb6226cf1e78992af257
ms.lasthandoff: 03/10/2017

---

# <a name="import-from-a-bacpac-into-a-sql-database-using-powershell"></a>使用 PowerShell 从 bacpac 导入 SQL 数据库

此示例 PowerShell 脚本从 bacpac 导入数据库。  

在运行此脚本前，请确保已使用 `Add-AzureRmAccount` cmdlet 创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

[!code-powershell[主要](../../../powershell_scripts/sql-database/import-from-bacpac/import-from-bacpac.ps1 "创建 SQL 数据库")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzureRmResourceGroup]() | 创建用于存储所有资源的资源组。 |
| [New-AzureRmSqlServer]() | 创建用于托管 SQL 数据库的逻辑服务器。 |
| [New-AzureRmSqlServerFirewallRule]() | 创建一个防火墙规则，以允许从输入的 IP 地址范围访问服务器上的所有 SQL 数据库。 |
| [New-AzureRmSqlDatabase]() | 在逻辑服务器中创建 SQL 数据库。 |
| [Remove-AzureRmResourceGroup]() | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

其他 SQL 数据库 PowerShell 脚本示例可以在 [Azure SQL 数据库 PowerShell 脚本](../sql-database-powershell-samples.md)中找到。
