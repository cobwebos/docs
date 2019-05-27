---
title: Azure PowerShell 脚本示例 - 将应用连接到 SQL 数据库 | Microsoft Docs
description: Azure PowerShell 脚本示例 - 将应用服务应用连接到 SQL 数据库
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.service: app-service
ms.devlang: multiple
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: b008ef47ba530affd65e4d0e5e293312cfe74b69
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "66136524"
---
# <a name="connect-an-app-service-app-to-a-sql-database"></a>将应用服务应用连接到 SQL 数据库

在此方案中，将了解如何创建 Azure SQL 数据库和应用服务应用。 然后，将使用应用设置将 SQL 数据库链接到应用。

必要时，请使用 [Azure PowerShell 指南](/powershell/azure/overview)中的说明安装 Azure PowerShell，并运行 `Connect-AzAccount` 创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to a SQL database")]

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、应用服务应用以及所有相关资源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | 创建应用服务计划。 |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 创建应用服务应用。 |
| [New-AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | 创建 SQL 数据库服务器。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 为 SQL 数据库服务器创建防火墙规则。 |
| [New-AzSQLDatabase](/powershell/module/az.sql/new-azsqldatabase) | 创建数据库或弹性数据库。 |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | 修改应用服务应用的配置。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../samples-powershell.md)中找到 Azure 应用服务的其他 Azure Powershell 示例。
