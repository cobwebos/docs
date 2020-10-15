---
title: Azure PowerShell 脚本示例
description: 使用 Azure PowerShell 脚本示例，有助于创建和管理 Azure SQL 数据库和 Azure SQL 托管实例资源。
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 77220fabc78dd10b4299219ef84c1f6eb32ba82e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85987276"
---
# <a name="azure-powershell-samples-for-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database 和 Azure SQL 托管实例的 Azure PowerShell 示例
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL 数据库和 Azure SQL 托管实例允许使用 Azure PowerShell 配置数据库、实例和池。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 AZ PowerShell 1.4.0 或更高版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="azure-sql-database"></a>[Azure SQL 数据库](#tab/single-database)

下表包括了适用于 Azure SQL 数据库的示例 Azure PowerShell 脚本的链接。

|链接|说明|
|---|---|
|创建并配置单一数据库和弹性池||
| [创建单一数据库并配置服务器级防火墙规则](scripts/create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本创建单一数据库，并配置服务器级 IP 防火墙规则。 |
| [创建弹性池并移动共用数据库](scripts/move-database-between-elastic-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本创建弹性池，移动共用数据库并更改计算大小。|
|**配置异地复制和故障转移**||
| [配置单一数据库并使用活动异地复制对其进行故障转移](scripts/setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 这个 PowerShell 脚本用于为单一数据库配置活动异地复制，并将其故障转移到辅助副本。 |
| [配置共用数据库并使用活动异地复制对其进行故障转移](scripts/setup-geodr-and-failover-elastic-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本为弹性池中的数据库配置活动异地复制，并将其故障转移到次要副本。 |
|**配置故障转移组**||
| [为单一数据库配置故障转移组](scripts/add-database-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本创建一个数据库和一个故障转移组，将该数据库添加到该故障转移组，并测试到辅助服务器的故障转移。 |
| [为弹性池配置故障转移组](scripts/add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本创建一个数据库，将其添加到弹性池，将弹性池添加到故障转移组，并测试到辅助服务器的故障转移。 |
|**缩放单一数据库和弹性池**||
| [缩放单一数据库](scripts/monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本监视单一数据库的性能指标，将其缩放到更高的计算大小，并根据某个性能指标创建预警规则。 |
| [缩放弹性池](scripts/monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 这个 PowerShell 脚本用于监视弹性池的性能指标，将其扩展到更高的计算大小，并基于性能指标之一创建警报规则。 |
| **审核和威胁检测** |
| [配置审核和威胁检测](scripts/auditing-threat-detection-powershell-configure.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本为数据库配置审核和威胁检测策略。 |
| **还原、复制和导入数据库**||
| [还原数据库](scripts/restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 这个 PowerShell 脚本用于从异地冗余备份还原数据库，并将已删除的数据库还原到最新备份。 |
| [将数据库复制到新服务器](scripts/copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 这个 PowerShell 脚本用于在新服务器中创建现有数据库的副本。 |
| [从 bacpac 文件导入数据库](scripts/import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 这个 PowerShell 脚本用于将数据库从 bacpac 文件导入 Azure SQL 数据库。 |
| **在数据库之间同步数据**||
| [在数据库之间同步数据](scripts/sql-data-sync-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 这个 PowerShell 脚本用于将数据同步配置为在 Azure SQL 数据库中的多个数据库间进行同步。 |
| [在 SQL 数据库和本地 SQL Server 之间同步数据](scripts/sql-data-sync-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本将数据同步配置为在 Azure SQL 数据库中的数据库和 SQL Server 本地数据库之间进行同步。 |
| [更新 SQL 数据同步的同步架构](scripts/update-sync-schema-in-sync-group.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本将在数据同步的同步架构中添加或删除项。 |
|||

详细了解[单一数据库 Azure PowerShell API](single-database-manage.md#powershell)。 

## <a name="azure-sql-managed-instance"></a>[Azure SQL 托管实例](#tab/managed-instance)

下表包括了适用于 Azure SQL 托管实例的示例 Azure PowerShell 脚本的链接。

|链接|说明|
|---|---|
|**创建和配置托管实例**||
| [创建和管理托管实例](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | 此 PowerShell 脚本演示如何使用 Azure PowerShell 创建和管理托管实例。 |
| [使用 Azure 资源管理器模板创建和管理托管实例](../managed-instance/scripts/create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本演示如何使用 Azure PowerShell 和 Azure 资源管理器模板创建和管理托管实例。|
| [将数据库还原到另一个地理区域中的托管实例](../managed-instance/scripts/restore-geo-backup.md) | 此 PowerShell 脚本备份一个数据库并将其还原到另一个区域。 这称为异地还原灾难恢复方案。 |
| 配置透明数据加密||
| [使用 Azure Key Vault 中自己的密钥管理托管实例中的透明数据加密](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 此 PowerShell 脚本使用 Azure Key Vault 中的密钥在 Azure SQL 托管实例的“创建自己的密钥”方案中配置透明数据加密。|
|**配置故障转移组**||
| [为托管实例配置故障转移组](../managed-instance/scripts/add-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 此 PowerShell 脚本创建两个托管实例，将其添加到故障转移组，然后测试从主托管实例到辅助托管实例的故障转移。 |
|||

详细了解 [Azure SQL 托管实例的 PowerShell cmdlet](../managed-instance/api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances)。

---

## <a name="additional-resources"></a>其他资源

此页上列出的示例使用 [PowerShell cmdlet](/powershell/module/az.sql/) 创建和管理 Azure SQL 资源。 用于运行查询和执行多个数据库任务的其他 cmdlet 位于 [sqlserver](/powershell/module/sqlserver/) 模块中。 有关详细信息，请参阅 [SQL Server PowerShell](/sql/powershell/sql-server-powershell/)。
