---
title: Azure CLI 脚本示例
description: 创建和管理 Azure SQL 数据库服务器、弹性池、数据库和防火墙的 Azure CLI 脚本示例。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 459a5ea69e11a8614c572f68fce039b6cabbb1ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061722"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>适用于 Azure SQL 数据库的 Azure CLI 示例

可以使用 <a href="/cli/azure">Azure CLI</a> 配置 Azure SQL 数据库。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

# <a name="single-database--elastic-pools"></a>[单一数据库和弹性池](#tab/single-database)

| | |
|---|---|
|**创建单一数据库和弹性池**||
| [创建单一数据库和配置防火墙规则](scripts/sql-database-create-and-configure-database-cli.md) | 创建 Azure SQL 数据库并配置服务器级防火墙规则。 |
| [创建弹性池并移动共用数据库](scripts/sql-database-move-database-between-pools-cli.md) | 创建 SQL 弹性池，移动共用 Azure SQL 数据库，并更改计算大小。 |
|**缩放单一数据库和弹性池**||
| [缩放单一数据库](scripts/sql-database-monitor-and-scale-database-cli.md) | 在查询数据库的大小信息后，将 Azure SQL 数据库缩放为不同的计算大小。 |
| [缩放弹性池](scripts/sql-database-scale-pool-cli.md) | 将 SQL 弹性池缩放为不同的计算大小。 |
|**配置异地复制和故障转移**||
| [将单一数据库添加到故障转移组](scripts/sql-database-add-single-db-to-failover-group-cli.md)| 创建一个数据库和一个故障转移组，将此数据库添加到该故障转移组，然后测试到辅助服务器的故障转移。 |
| [为弹性池配置故障转移组](scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | 创建一个数据库，将其添加到弹性池，将弹性池添加到故障转移组，然后测试到辅助服务器的故障转移。 |
| [配置单一数据库并使用活动异地复制对其进行故障转移](scripts/sql-database-setup-geodr-and-failover-database-cli.md)| 为 Azure SQL 数据库配置活动异地复制，并将其故障转移到辅助副本。 |
| [配置共用数据库并使用活动异地复制对其进行故障转移](scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| 为 SQL 弹性池中的 Azure SQL 数据库配置活动异地复制，然后将其故障转移到辅助副本。 |
| **审核和威胁检测** |
| [配置审核和威胁检测](scripts/sql-database-auditing-and-threat-detection-cli.md)| 为 Azure SQL 数据库配置审核和威胁检测策略。 |
| **备份、还原、复制和导入数据库**||
| [备份数据库](scripts/sql-database-backup-database-cli.md)| 将 Azure SQL 数据库备份到 Azure 存储备份。 |
| [还原数据库](scripts/sql-database-restore-database-cli.md)| 从异地冗余备份中还原 Azure SQL 数据库以及将已删除的 Azure SQL 数据库还原到最新备份。 |
| [将数据库复制到新服务器](scripts/sql-database-copy-database-to-new-server-cli.md) | 在新的 Azure SQL 服务器中创建现有 Azure SQL 数据库的副本。 |
| [从 bacpac 文件导入数据库](scripts/sql-database-import-from-bacpac-cli.md)| 通过 *.bacpac* 文件将数据库导入到 Azure SQL 服务器。 |
|||

详细了解[单一数据库 Azure CLI API](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)。

# <a name="managed-instance"></a>[托管实例](#tab/managed-instance)

下表包括适用于 Azure SQL 数据库 - 托管实例的 Azure CLI 脚本示例的链接。

| | |
|---|---|
| **创建托管实例**||
| [创建托管实例](scripts/sql-database-create-configure-managed-instance-cli.md)| 创建托管实例。 |
| **配置透明数据加密 (TDE)**||
| [使用 Azure Key Vault 管理托管实例中的透明数据加密](scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| 将 Azure Key Vault 用于各种关键方案，在 Azure SQL 托管实例中配置透明数据加密 (TDE)。 |
|**配置故障转移组**||
| [为托管实例配置故障转移组](scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | 创建两个托管实例，将其添加到故障转移组，然后测试从主托管实例到辅助托管实例的故障转移。 |
|||

有关其他托管实例示例，请参阅[创建](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/)、[更新](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/)、[移动数据库](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)，[使用](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)脚本。

详细了解[托管实例 Azure CLI API](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances)。

---
