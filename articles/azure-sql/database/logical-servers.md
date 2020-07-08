---
title: 什么是 Azure SQL 数据库和 Azure Synapse Analytics 中的服务器？
titleSuffix: ''
description: 了解 Azure SQL 数据库和 Azure Synapse Analytics 使用的逻辑 SQL server，以及如何对其进行管理。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: b45714489f13d33d90b5694f458e6339e33595f3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367559"
---
# <a name="what-is-a-logical-sql-server-in-azure-sql-database-and-azure-synapse"></a>什么是 Azure SQL 数据库中的逻辑 SQL server 和 Azure Synapse？
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

在 Azure SQL 数据库和 Azure Synapse Analytics 中，服务器是一个逻辑构造，充当数据库集合的中心管理点。 在服务器级别，可以管理[登录名](logins-create-manage.md)、[防火墙规则](firewall-configure.md)、[审核规则](../../azure-sql/database/auditing-overview.md)、[威胁检测策略](threat-detection-configure.md)和[自动故障转移组](auto-failover-group-overview.md)。 一个服务器可以与其资源组位于不同的区域。 服务器必须存在，然后才能在 Azure SQL 数据库中创建数据库，或在 Azure Synapse Analytics 中创建数据仓库数据库。 由单个服务器管理的所有数据库都在与服务器相同的区域中创建。

此服务器不同于在本地环境中可能熟悉的 SQL Server 实例。 具体而言，与管理数据库或数据仓库数据库的服务器有关的位置没有任何保证。 此外，Azure SQL 数据库和 Azure Synapse 都不会公开任何实例级访问权限或功能。 与此相反，托管实例中的实例数据库的物理位置与您熟悉在本地或虚拟机领域中的 SQL Server 的方式完全相同。

创建服务器时，将提供一个服务器登录帐户和密码，该帐户和密码对该服务器上的 master 数据库以及在该服务器上创建的所有数据库具有管理权限。 这一初始帐户就是 SQL 登录帐户。 Azure SQL 数据库和 Synapse 分析支持 SQL 身份验证，并 Azure Active Directory 身份验证进行身份验证。 若要详细了解登录名和身份验证，请参阅[在 Azure SQL 数据库中管理数据库和登录名](logins-create-manage.md)。 不支持 Windows 身份验证。

SQL 数据库和 Azure Synapse 中的服务器：

- 在 Azure 订阅中创建，但可以与其包含的资源一起移到另一个订阅
- 是数据库、弹性池和数据仓库的父资源
- 为数据库、弹性池和数据仓库数据库提供命名空间
- 是具有强生存期语义的逻辑容器-删除服务器并删除其数据库、弹性池和 SQK 池
- 参与[Azure 基于角色的访问控制（RBAC）](/azure/role-based-access-control/overview) -服务器中的数据库、弹性池和数据仓库数据库从服务器继承访问权限
- 是数据库、弹性池和数据仓库数据库的标识的高阶元素，用于 Azure 资源管理目的（请参阅数据库和池的 URL 方案）
- 并置区域中的资源
- 为数据库访问提供连接终结点 (`<serverName>`.database.windows.net)
- 连接到 master 数据库通过 DMV 提供对与包含资源相关的元数据的访问
- 提供应用于数据库的管理策略的作用域，即登录名、防火墙、审核、威胁检测等
- 受父订阅中的配额限制（默认情况下，每个订阅六个服务器，[请单击此处了解订阅限制](../../azure-resource-manager/management/azure-subscription-service-limits.md)）
- 提供所含资源的数据库配额和 DTU 或 vCore 配额范围（例如，45,000 个 DTU）
- 是在包含资源上启用的功能的版本控制作用域
- 服务器级主体登录名可以管理服务器上的所有数据库
- 可以包含类似于本地环境中 SQL Server 的登录名，这些登录名可以访问服务器上的一个或多个数据库，并可以被授予有限的管理权限。 有关详细信息，请参阅[登录名](logins-create-manage.md)。
- 在服务器上创建的所有数据库的默认排序规则为 `SQL_LATIN1_GENERAL_CP1_CI_AS` ，其中 `LATIN1_GENERAL` 为英语（美国）， `CP1` 是代码页1252，不区分 `CI` 大小写，并且 `AS` 区分重音。

## <a name="manage-servers-databases-and-firewalls-using-the-azure-portal"></a>使用 Azure 门户管理服务器、数据库和防火墙

可以提前为服务器创建资源组，也可以在创建服务器本身时创建资源组。 转到新 SQL 服务器表单的方法有多种，可以通过新建 SQL 服务器，也可以在新建数据库期间。

### <a name="create-a-blank-server"></a>创建空白服务器

若要使用[Azure 门户](https://portal.azure.com)创建服务器（不包含数据库、弹性池或数据仓库数据库），请导航到空白 sql server （逻辑 sql server）表单。

### <a name="create-a-blank-or-sample-database-in-azure-sql-database"></a>在 Azure SQL 数据库中创建空白或示例数据库

若要使用[Azure 门户](https://portal.azure.com)在 SQL 数据库中创建数据库，请导航到空白 SQL 数据库表单，并提供所需的信息。 可以提前创建资源组和服务器，也可以创建数据库本身。 可以创建空白数据库，也可以创建基于 Adventure Works LT 的示例数据库。

  ![创建数据库 - 1](./media/logical-servers/create-database-1.png)

> [!IMPORTANT]
> 有关为数据库选择定价层的信息，请参阅[基于 DTU 的购买模型](service-tiers-dtu.md)和[基于 vCore 的购买模型](service-tiers-vcore.md)。

若要创建托管实例，请参阅[创建托管实例](../managed-instance/instance-create-quickstart.md)

### <a name="manage-an-existing-server"></a>管理现有服务器

若要管理现有服务器，请使用多种方法（如 "从特定数据库" 页、" **SQL 服务器**" 页或 "**所有资源**" 页）导航到服务器。

若要管理现有数据库，请转到“SQL 数据库”**** 页，再单击要管理的数据库。 下面的屏幕截图展示了如何通过数据库的“概述”**** 页开始为数据库设置服务器级防火墙。

   ![服务器防火墙规则](./media/single-database-create-quickstart/server-firewall-rule.png)

> [!IMPORTANT]
> 要配置数据库的性能属性，请参阅[基于 DTU 的购买模型](service-tiers-dtu.md)和[基于 vCore 的购买模型](service-tiers-vcore.md)。
> [!TIP]
> 有关 Azure 门户快速入门，请参阅[Azure 门户中的在 SQL 数据库中创建数据库](single-database-create-quickstart.md)。

## <a name="manage-servers-databases-and-firewalls-using-powershell"></a>使用 PowerShell 管理服务器、数据库和防火墙

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受支持，但所有将来的开发都适用于 Az .Sql 模块。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

若要使用 Azure PowerShell 创建和管理服务器、数据库和防火墙，请使用以下 PowerShell cmdlet。 如果需要安装或升级 PowerShell，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 若要创建并管理弹性池，请参阅[弹性池](elastic-pool-overview.md)。

| Cmdlet | 说明 |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|创建数据库 |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|获取一个或多个数据库|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|设置数据库的属性，或将现有数据库移到弹性池中|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|删除数据库|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|创建资源组|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|创建服务器|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|返回服务器的相关信息|
|[Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|修改服务器的属性|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|删除服务器|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|创建服务器级防火墙规则 |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|获取服务器的防火墙规则|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|修改服务器中的防火墙规则|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|从服务器中删除防火墙规则。|
| New-AzSqlServerVirtualNetworkRule | 基于作为虚拟网络服务终结点的子网创建[*虚拟网络规则*](vnet-service-endpoint-rule-overview.md)。 |

> [!TIP]
> 有关 PowerShell 快速入门，请参阅[使用 powershell 在 AZURE SQL 数据库中创建数据库](single-database-create-quickstart.md)。 有关 PowerShell 示例脚本，请参阅[使用 powershell 在 AZURE Sql 数据库中创建数据库和配置防火墙规则](scripts/create-and-configure-database-powershell.md)，并[使用 POWERSHELL 在 azure Sql 数据库中监视和缩放数据库](scripts/monitor-and-scale-database-powershell.md)。
>

## <a name="manage-servers-databases-and-firewalls-using-the-azure-cli"></a>使用 Azure CLI 管理服务器、数据库和防火墙

若要使用[Azure CLI](/cli/azure)创建和管理服务器、数据库和防火墙，请使用以下[Azure CLI SQL 数据库](/cli/azure/sql/db)命令。 使用 [Cloud Shell](/azure/cloud-shell/overview) 在浏览器中运行 CLI，或者在 macOS、Linux 或 Windows 上[安装](/cli/azure/install-azure-cli)它。 若要创建并管理弹性池，请参阅[弹性池](elastic-pool-overview.md)。

| Cmdlet | 说明 |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |创建数据库|
|[az sql db list](/cli/azure/sql/db#az-sql-db-list)|列出由服务器管理的所有数据库或弹性池中的所有数据库|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|列出可用的服务目标和存储上限|
|[az sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|返回数据库使用情况|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|获取数据库
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|更新数据库|
|[az sql db delete](/cli/azure/sql/db#az-sql-db-delete)|删除数据库|
|[az group create](/cli/azure/group#az-group-create)|创建资源组|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|创建服务器|
|[az sql server list](/cli/azure/sql/server#az-sql-server-list)|列出服务器|
|[az sql server list-usages](/cli/azure/sql/server#az-sql-server-list-usages)|返回服务器使用情况|
|[az sql server show](/cli/azure/sql/server#az-sql-server-show)|获取服务器|
|[az sql server update](/cli/azure/sql/server#az-sql-server-update)|更新服务器|
|[az sql server delete](/cli/azure/sql/server#az-sql-server-delete)|删除服务器|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|配置服务器防火墙规则|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|列出服务器上的防火墙规则|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|显示防火墙规则的详细信息|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|更新防火墙规则|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|删除防火墙规则|

> [!TIP]
> 有关 Azure CLI 快速入门，请参阅[使用 Azure CLI 在 AZURE SQL 数据库中创建数据库](az-cli-script-samples-content-guide.md)。 有关 Azure CLI 示例脚本，请参阅[使用 cli 在 AZURE Sql 数据库中创建数据库和配置防火墙规则](scripts/create-and-configure-database-cli.md)和[使用 CLI 监视和缩放 Azure sql 数据库中的数据库](scripts/monitor-and-scale-database-cli.md)。
>

## <a name="manage-servers-databases-and-firewalls-using-transact-sql"></a>使用 Transact-sql 管理服务器、数据库和防火墙

若要使用 Transact-sql 创建和管理服务器、数据库和防火墙，请使用以下 T-sql 命令。 您可以使用 Azure 门户、 [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、 [Visual Studio Code](https://code.visualstudio.com/docs)或可以连接到服务器并传递 transact-sql 命令的任何其他程序来发出这些命令。 若要管理弹性池，请参阅[弹性池](elastic-pool-overview.md)。

> [!IMPORTANT]
> 无法使用 Transact-SQL 创建或删除服务器。

| Command | 描述 |
| --- | --- |
|[CREATE DATABASE（Azure SQL 数据库）](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current) | 在 Azure SQL 数据库中创建新的数据库。 您必须连接到 master 数据库才能创建新的数据库。|
|[CREATE DATABASE （Azure Synapse）](/sql/t-sql/statements/create-database-transact-sql?view=azure-sqldw-latest) | 在 Azure Synapse 中创建新的数据仓库数据库。 您必须连接到 master 数据库才能创建新的数据库。|
| [ALTER DATABASE（Azure SQL 数据库）](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |修改数据库或弹性池。 |
|[ALTER DATABASE（Azure SQL 数据仓库）](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=sql-server-ver15)|修改 Azure Synapse 中的数据仓库数据库。|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|删除数据库。|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|返回数据库的版本（服务层）、服务目标（定价层）和弹性池名称（如果有）。 如果登录到服务器的 master 数据库，则将返回有关所有数据库的信息。 对于 Azure Synapse，必须连接到 master 数据库。|
|[sys.dm_db_resource_stats（Azure SQL 数据库）](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| 返回 Azure SQL 数据库中数据库的 CPU、IO 和内存消耗。 每隔 15 秒会显示一行，即使该数据库中没有任何活动也是如此。|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|返回 Azure SQL 数据库中数据库的 CPU 使用率和存储数据。 在五分钟的间隔内收集和聚合数据。|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|包含 Azure SQL 数据库的数据库连接事件的统计信息，提供数据库连接成功和失败的概述。 |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|为 Azure SQL 数据库返回成功的 Azure SQL 数据库连接、连接失败和死锁。 您可以使用此信息来跟踪您的数据库活动或对其进行故障排除。|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|创建或更新服务器的服务器级防火墙设置。 此存储过程只在 master 数据库中适用于服务器级主体登录名。 只有在拥有 Azure 级权限的用户创建首个服务器级防火墙规则后，才能使用 Transact-SQL 创建服务器级防火墙规则|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|返回与服务器关联的服务器级防火墙设置的相关信息。|
|[sp_delete_firewall_rule（Azure SQL 数据库）](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|从服务器中删除服务器级防火墙设置。 此存储过程只在 master 数据库中适用于服务器级主体登录名。|
|[sp_set_database_firewall_rule（Azure SQL 数据库）](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|为 Azure SQL 数据库中的数据库创建或更新数据库级防火墙规则。 可以为 master 数据库以及 SQL 数据库中的用户数据库配置数据库防火墙规则。 使用包含的数据库用户时，数据库防火墙规则非常有用。 Azure Synapse 中不支持数据库防火墙规则。|
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|返回有关 Azure SQL 数据库中数据库的数据库级防火墙设置的信息。 |
|[sp_delete_database_firewall_rule（Azure SQL 数据库）](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|在 Azure SQL 数据库中删除你的数据库的数据库级防火墙设置。 |

> [!TIP]
> 有关在 Microsoft Windows 上使用 SQL Server Management Studio 的快速入门，请参阅 [Azure SQL 数据库：使用 SQL Server Management Studio 进行连接和数据查询](connect-query-ssms.md)。 有关在 macOS、Linux 或 Windows 上使用 Visual Studio Code 的快速入门，请参阅 [Azure SQL 数据库：使用 Visual Studio Code 进行连接和数据查询](connect-query-vscode.md)。

## <a name="manage-servers-databases-and-firewalls-using-the-rest-api"></a>使用 REST API 管理服务器、数据库和防火墙

若要创建和管理服务器、数据库和防火墙，请使用这些 REST API 请求。

| Command | 描述 |
| --- | --- |
|[服务器-创建或更新](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|创建或更新新服务器。|
|[Servers - Delete](https://docs.microsoft.com/rest/api/sql/servers/delete)|删除服务器。|
|[Servers - Get](https://docs.microsoft.com/rest/api/sql/servers/get)|获取服务器。|
|[Servers - List](https://docs.microsoft.com/rest/api/sql/servers/list)|返回服务器列表。|
|[服务器-按资源组列出](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|返回资源组中服务器的列表。|
|[Servers - Update](https://docs.microsoft.com/rest/api/sql/servers/update)|更新现有服务器。|
|[数据库 - 创建或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|创建新数据库或更新现有数据库。|
|[数据库 - 删除](https://docs.microsoft.com/rest/api/sql/databases/delete)|删除数据库。|
|[数据库 - 获取](https://docs.microsoft.com/rest/api/sql/databases/get)|获取数据库。|
|[数据库-按弹性池列出](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|返回弹性池中数据库的列表。|
|[数据库 - 按服务器列出](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|返回服务器中的数据库列表。|
|[数据库 - 更新](https://docs.microsoft.com/rest/api/sql/databases/update)|更新现有的数据库。|
|[防火墙规则-创建或更新](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|创建或更新防火墙规则。|
|[防火墙规则-删除](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|删除防火墙规则。|
|[防火墙规则-获取](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|获取防火墙规则。|
|[防火墙规则-按服务器列出](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|返回防火墙规则的列表。|

## <a name="next-steps"></a>后续步骤

- 若要了解如何将 SQL Server 数据库迁移到 Azure SQL 数据库，请参阅[迁移到 AZURE Sql database](migrate-to-database-from-sql-server.md)。
- 有关支持的功能的信息，请参阅[功能](features-comparison.md)。
