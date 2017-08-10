---
title: "适用于 SQL 数据库的 Azure PowerShell 脚本示例 | Microsoft Docs"
description: "Azure PowerShell 脚本示例有助于创建并管理 Azure SQL 数据库服务器、弹性池、数据库和防火墙。"
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: jhubbard
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: overview-samples
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 06/23/2017
ms.author: janeng
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: b54775ec0bd3497e9700db0e3d1428dcc75cc119
ms.contentlocale: zh-cn
ms.lasthandoff: 08/02/2017

---

# <a name="azure-powershell-samples-for-azure-sql-database"></a>适用于 Azure SQL 数据库的 Azure PowerShell 示例

下表包括了适用于 Azure SQL 数据库的示例 Azure PowerShell 脚本的链接。

| |  |
|---|---|
|**创建单一数据库和弹性池**||
| [创建单一数据库和配置防火墙规则](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本创建单个 Azure SQL 数据库，并配置服务器级防火墙规则。 |
| [创建弹性池并移动入池数据库](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本创建 Azure SQL 数据库弹性池，移动入池数据库并更改性能级别。|
|**配置异地复制和故障转移**||
| [配置单一数据库并使用活动异地复制对其进行故障转移](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本为单个 Azure SQL 数据库配置活动异地复制，并将其故障转移到次要副本。 |
| [配置入池数据库并使用活动异地复制对其进行故障转移](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本为 SQL 弹性池中的 Azure SQL 数据库配置活动异地复制，并将其故障转移到次要副本。 |
| [针对单个数据库配置并故障转移一个故障转移组（预览版）](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本为 Azure SQL 数据库服务器实例配置故障转移组，将数据库添加到故障转移组，并将其故障转移到辅助服务器 |
|**缩放单一数据库和弹性池**||
| [缩放单一数据库](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本监视单个 Azure SQL 数据库的性能指标，将其缩放为更高的性能级别，并基于性能指标之一创建预警规则。 |
| [缩放弹性池](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本监视 Azure SQL 数据库弹性池的性能指标，将其缩放为更高的性能级别，并基于性能指标之一创建预警规则。  |
| **审核和威胁检测** |
| [配置审核和威胁检测](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本为 Azure SQL 数据库配置审核和威胁检测策略。 |
| **还原、复制和导入数据库**||
| [还原数据库](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本从异地冗余备份还原 Azure SQL 数据库，并将已删除的 Azure SQL 数据库还原到最新备份。 |
| [将数据库复制到新服务器](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本在新的 Azure SQL 服务器中创建现有 Azure SQL 数据库的副本。 |
| [从 bacpac 文件导入数据库](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本将数据库从 bacpac 文件导入到 Azure SQL 服务器。 |
| **在数据库之间同步数据**||
| [在 SQL 数据库之间同步数据](scripts/sql-database-sync-data-between-sql-databases.md) | 此 PowerShell 脚本将数据同步配置为在多个 Azure SQL 数据库之间进行同步。 |
| [在 SQL 数据库和本地 SQL Server 之间同步数据](scripts/sql-database-sync-data-between-azure-onprem.md) | 此 PowerShell 脚本将数据同步配置为在 Azure SQL 数据库和 SQL Server 本地数据库之间进行同步。 |
|||
|||

