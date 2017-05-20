---
title: "Azure PowerShell 脚本 - 将 SQL 数据库复制到新服务器 | Microsoft 文档"
description: "Azure PowerShell 脚本示例 - 使用 PowerShell 将 SQL 数据库复制到新服务器"
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
ms.date: 05/19/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 826a5213b5b64529df60d9b9ce8433d5c6b3a522
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017

---

# <a name="copy-a-sql-database-to-a-new-server-using-powershell"></a>使用 PowerShell 将 SQL 数据库复制到新服务器

此示例 PowerShell 脚本在新服务器中创建现有数据库的副本。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="copy-a-database-to-a-new-server"></a>将数据库复制到新服务器

[!code-powershell[主要](../../../powershell_scripts/sql-database/copy-database-to-new-server/copy-database-to-new-server.ps1 "将数据库复制到新服务器")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | 创建用于托管数据库或弹性池的逻辑服务器。 |
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | 在逻辑服务器中创建数据库作为单一数据库或入池数据库。 |
| [New-AzureRmSqlDatabaseCopy](/powershell/module/azurerm.sql/new-azurermsqldatabasecopy) | 创建当前使用快照的数据库副本。 |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure SQL 数据库 PowerShell 脚本](../sql-database-powershell-samples.md)中找到更多 SQL 数据库 PowerShell 脚本示例。

