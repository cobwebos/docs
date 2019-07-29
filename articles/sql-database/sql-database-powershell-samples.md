---
title: 适用于 SQL 数据库的 Azure PowerShell 脚本示例 | Microsoft Docs
description: 可帮助创建并管理 Azure SQL 数据库服务器、弹性池、数据库和防火墙的 Azure PowerShell 脚本示例。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: c32803fec6107e39a0b8329adaee072e3e291575
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567055"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>适用于 Azure SQL 数据库的 Azure PowerShell 示例

Azure SQL 数据库允许你使用 Azure PowerShell 配置数据库、实例和池。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 AZ PowerShell 1.4.0 或更高版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="single-database-and-elastic-pools"></a>单一数据库和弹性池

下表包括了适用于 Azure SQL 数据库的示例 Azure PowerShell 脚本的链接。

| |  |
|---|---|
|**创建和配置单一数据库和弹性池**||
| [创建单一数据库和配置数据库服务器防火墙规则](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本创建单个 Azure SQL 数据库，并配置服务器级防火墙规则。 |
| [创建弹性池并移动共用数据库](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本创建 Azure SQL 数据库弹性池，移动共用数据库并更改计算大小。|
|**配置异地复制和故障转移**||
| [配置单一数据库并使用活动异地复制对其进行故障转移](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本为单个 Azure SQL 数据库配置活动异地复制，并将其故障转移到次要副本。 |
| [配置共用数据库并使用活动异地复制对其进行故障转移](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本为 SQL 弹性池中的 Azure SQL 数据库配置活动异地复制，并将其故障转移到次要副本。 |
|**配置故障转移组**||
| [为单一数据库配置故障转移组](scripts/sql-database-add-single-db-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本创建一个数据库和一个故障转移组，将此数据库添加到该故障转移组，并测试到辅助服务器的故障转移。 | 
|**缩放单一数据库和弹性池**||
| [缩放单一数据库](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本监视 Azure SQL 数据库的性能指标，将其缩放为更高的计算大小，并基于其中一个性能指标创建警报规则。 |
| [缩放弹性池](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本监视 Azure SQL 数据库弹性池的性能指标，将其缩放为更高的计算大小，并基于其中一个性能指标创建警报规则。 |
| **审核和威胁检测** |
| [配置审核和威胁检测](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本为 Azure SQL 数据库配置审核和威胁检测策略。 |
| **还原、复制和导入数据库**||
| [还原数据库](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本从异地冗余备份还原 Azure SQL 数据库，并将已删除的 Azure SQL 数据库还原到最新备份。 |
| [将数据库复制到新服务器](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本在新的 Azure SQL 服务器中创建现有 Azure SQL 数据库的副本。 |
| [从 bacpac 文件导入数据库](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本将数据库从 bacpac 文件导入到 Azure SQL 服务器。 |
| **在数据库之间同步数据**||
| [在 SQL 数据库之间同步数据](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本将数据同步配置为在多个 Azure SQL 数据库之间进行同步。 |
| [在 SQL 数据库和本地 SQL Server 之间同步数据](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本将数据同步配置为在 Azure SQL 数据库和 SQL Server 本地数据库之间进行同步。 |
| [更新 SQL 数据同步的同步架构](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本将在数据同步的同步架构中添加或删除项。 |
|||

详细了解[单一数据库 Azure PowerShell API](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)。

## <a name="managed-instance"></a>托管实例

下表包括了适用于 Azure SQL 数据库 - 托管实例的示例 Azure PowerShell 脚本的链接。

| |  |
|---|---|
|**创建和配置托管实例**||
| [创建和管理托管实例](scripts/sql-database-create-configure-managed-instance-powershell.md) | 此 PowerShell 脚本演示如何使用 Azure PowerShell 创建和管理托管实例 |
| [使用 Azure 资源管理器模板创建和管理托管实例](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本演示如何使用 Azure PowerShell 和 Azure 资源管理器模板创建和管理托管实例。|
| [将数据库还原到另一个地理区域中的托管实例](scripts/sql-managed-instance-restore-geo-backup.md) | 此 PowerShell 脚本正在备份一个数据库并将其还原到另一个区域。 这称为异地还原灾难恢复方案。 |
| **配置透明数据加密 (TDE)**||
| [使用 Azure Key Vault 中自己的密钥管理托管实例中的透明数据加密](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本使用 Azure Key Vault 中的密钥在自带密钥方案中为 Azure SQL 托管实例配置透明数据加密 (TDE)|
|||

详细了解[托管实例 Azure PowerShell API](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)。

## <a name="additional-resources"></a>其他资源

此页上列出的示例使用 [Azure SQL 数据库 cmdlet](/powershell/module/az.sql/) 来创建和管理 Azure SQL 资源。 用于运行查询和执行多个数据库任务的其他 cmdlet 位于 [sqlserver](/powershell/module/sqlserver/) 模块中。 有关详细信息，请参阅 [SQL Server PowerShell](/sql/powershell/sql-server-powershell/)。
