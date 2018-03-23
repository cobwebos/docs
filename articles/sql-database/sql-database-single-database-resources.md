---
title: Azure SQL 数据库单一数据库 | Microsoft Docs
description: 管理单个 Azure SQL 数据库的服务层、性能级别和存储量。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 02/12/2018
ms.author: carlrab
ms.openlocfilehash: 167a72ae55052b8ac1dfe8f032f136a9bf8bcedf
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="manage-resources-for-a-single-database-in-azure-sql-database"></a>管理 Azure SQL 数据库中单一数据库的资源

对于单一数据库，需要确定数据库处理在服务层、性能级别的工作负荷所需的资源量，以及所需的存储量。 

## <a name="manage-single-database-resources-using-the-azure-portal"></a>使用 Azure 门户管理单一数据库资源

若要使用 Azure 门户为新的或现有 Azure SQL 数据库设置或更改服务层、性能级别或存储量，请单击“定价层(缩放 DTU)”打开数据库的“配置性能”窗口 - 如以下屏幕截图中所示。 

- 通过为你的工作负荷选择服务层来设置或更改服务层。 
- 使用“DTU”滑块在服务层内设置或更改性能级别 (**DTU**)。
- 使用“存储”滑块为性能级别设置或更改存储量。 

![配置服务层和性能级别](./media/sql-database-single-database-resources/change-service-tier.png)

单击“概述”以监视和/或取消正在进行的操作。

![取消操作](./media/sql-database-single-database-resources/cancel-operation.png)

> [!IMPORTANT]
> 当选择 P11 或 P15 服务层时，请查看[最大大小为 4 TB 的 P11 和 P15 数据库的当前限制](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。
>

## <a name="manage-single-database-resources-using-powershell"></a>使用 PowerShell 管理单一数据库资源

若要使用 PowerShell 设置或更改 Azure SQL 数据库服务层、性能级别和存储量，请使用以下 PowerShell cmdlet。 如果需要安装或升级 PowerShell，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 

| Cmdlet | 说明 |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|创建数据库 |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|获取一个或多个数据库|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|设置数据库的属性，或将现有数据库移到弹性池中。 例如，使用 MaxSizeBytes 属性来设置数据库的最大大小。|
|[Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity)|获取数据库操作的状态。 |
|[Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)|取消对数据库的异步更新操作。|


> [!TIP]
> 可以使用 PowerShell 脚本监视数据库的性能指标，将其缩放到更高的性能级别，并基于性能指标之一创建警报规则，有关这样的示例脚本，请参阅[使用 PowerShell 监视和缩放单一 SQL 数据库](scripts/sql-database-monitor-and-scale-database-powershell.md)。

## <a name="manage-single-database-resources-using-the-azure-cli"></a>使用 Azure CLI 管理单一数据库资源

若要使用 Azure CLI 设置或更改 Azure SQL 数据库服务层、性能级别和存储量，请使用以下 [Azure CLI SQL 数据库](/cli/azure/sql/db)命令。 使用 [Cloud Shell](/azure/cloud-shell/overview) 在浏览器中运行 CLI，或者在 macOS、Linux 或 Windows 上[安装](/cli/azure/install-azure-cli)它。 若要创建并管理 SQL 弹性池，请参阅[弹性池](sql-database-elastic-pool.md)。

| Cmdlet | 说明 |
| --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|配置服务器防火墙规则|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|列出服务器上的防火墙规则|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|显示防火墙规则的详细信息|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|更新防火墙规则|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|删除防火墙规则|
|[az sql db op list](/cli/azure/sql/db/op?#az_sql_db_op_list)|获取对数据库执行的操作的列表。|
|[az sql db op cancel](/cli/azure/sql/db/op#az_sql_db_op_cancel)|取消对数据库的异步操作。|

> [!TIP]
> 可以使用 Azure CLI 脚本在查询数据库的大小信息后将单一 Azure SQL 数据库缩放到不同的性能级别，有关这样的示例脚本，请参阅[使用 CLI 监视和缩放单一 SQL 数据库](scripts/sql-database-monitor-and-scale-database-cli.md)。
>

## <a name="manage-single-database-resources-using-transact-sql"></a>使用 Transact-SQL 管理单一数据库资源

若要使用 Transact-SQL 设置或更改 Azure SQL 数据库服务层、性能级别和存储量，请使用以下 T-SQL 命令。 可以使用 Azure 门户、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Visual Studio Code](https://code.visualstudio.com/docs) 或可以连接到 Azure SQL 数据库服务器并传递 Transact-SQL 命令的其他任何程序，发出这些命令。 

| 命令 | 说明 |
| --- | --- |
|[CREATE DATABASE（Azure SQL 数据库）](/sql/t-sql/statements/create-database-azure-sql-database)|新建数据库。 必须连接到 master 数据库，才能新建数据库。|
| [ALTER DATABASE（Azure SQL 数据库）](/sql/t-sql/statements/alter-database-azure-sql-database) |修改 Azure SQL 数据库。 |
|[sys.database_service_objectives（Azure SQL 数据库）](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|返回 Azure SQL 数据库或 Azure SQL 数据仓库的版本（服务层）、服务目标（定价层）和弹性池名称（若有）。 如果已登录 Azure SQL 数据库服务器中的 master 数据库，返回所有数据库的相关信息。 对于 Azure SQL 数据仓库，必须连接到 master 数据库。|
|[sys.database_usage (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|列出 Azure SQL 数据库服务器上的数据库的数目、类型和持续时间。|

以下示例演示如何使用 ALTER DATABASE 命令更改数据库的最大大小：

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-database-resources-using-the-rest-api"></a>使用 REST API 管理单一数据库资源

若要设置或更改 Azure SQL 数据库服务层、性能级别和存储量，请使用以下 REST API 请求。

| 命令 | 说明 |
| --- | --- |
|[数据库 - 创建或更新](/rest/api/sql/databases/createorupdate)|创建新数据库或更新现有数据库。|
|[数据库 - 获取](/rest/api/sql/databases/get)|获取数据库。|
|[数据库 - 按弹性池获取](/rest/api/sql/databases/getbyelasticpool)|获取弹性池内的数据库。|
|[数据库 - 按推荐的弹性池获取](/rest/api/sql/databases/getbyrecommendedelasticpool)|获取推荐弹性池内的数据库。|
|[数据库 - 按弹性池列出](/rest/api/sql/databases/listbyelasticpool)|返回弹性池中数据库的列表。|
|[数据库 - 按推荐的弹性池列出](/rest/api/sql/databases/listbyrecommendedelasticpool)|返回推荐弹性池内的数据库列表。|
|[数据库 - 按服务器列出](/rest/api/sql/databases/listbyserver)|返回服务器中的数据库列表。|
|[数据库 - 更新](/rest/api/sql/databases/update)|更新现有的数据库。|
|[操作 - 列表](/rest/api/sql/Operations/List)|列出所有可用的 SQL Rest API 操作。|



## <a name="next-steps"></a>后续步骤

- 要了解关于服务层、性能级别和存储量的相关信息，请参阅[服务层](sql-database-service-tiers.md)。
- 有关弹性池的信息，请参阅[弹性池](sql-database-elastic-pool.md)。
- 了解 [Azure 订阅和服务的限制、配额和约束](../azure-subscription-service-limits.md)
