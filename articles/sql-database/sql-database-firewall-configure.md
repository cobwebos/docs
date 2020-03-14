---
title: IP 防火墙规则
description: 配置 SQL 数据库或 SQL 数据仓库防火墙的服务器级 IP 防火墙规则。 管理单个或共用数据库的数据库级 IP 防火墙规则。
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 9db6b5ff517a1b0d67e59591ee634dfad685527b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268998"
---
# <a name="azure-sql-database-and-azure-sql-data-warehouse-ip-firewall-rules"></a>Azure SQL 数据库和 Azure SQL 数据仓库 IP 防火墙规则

> [!NOTE]
> 本文适用于 azure sql 服务器，以及 azure sql 数据库和 azure sql 数据库中的 azure sql 数据仓库数据库。 为简单起见， *Sql 数据库*用于引用 sql 数据库和 Sql 数据仓库。

> [!IMPORTANT]
> 本文不适用于 *Azure SQL 数据库托管实例*。 有关网络配置的信息，请参阅[将应用程序连接到 Azure SQL 数据库托管实例](sql-database-managed-instance-connect-app.md)。

例如，在创建名为*mysqlserver*的新 Azure sql server 时，SQL 数据库防火墙会阻止对该服务器（可在*mysqlserver.database.windows.net*中访问）的公共终结点的所有访问。

> [!IMPORTANT]
> SQL 数据仓库仅支持服务器级 IP 防火墙规则。 它不支持数据库级 IP 防火墙规则。

## <a name="how-the-firewall-works"></a>防火墙的工作方式
来自 internet 和 Azure 的连接尝试必须在防火墙到达 SQL server 或 SQL 数据库之前经过防火墙，如下图所示。

   ![防火墙配置关系图][1]

### <a name="server-level-ip-firewall-rules"></a>服务器级别 IP 防火墙规则

  这些规则允许客户端访问整个 Azure SQL 服务器，即同一 SQL 数据库服务器内的所有数据库。 这些规则存储在*master*数据库中。 对于 Azure SQL Server 最多可以有128个服务器级 IP 防火墙规则。
  
  你可以使用 Azure 门户、PowerShell 或 Transact-sql 语句配置服务器级 IP 防火墙规则。
  - 若要使用门户或 PowerShell，你必须是订阅所有者或订阅参与者。
  - 若要使用 Transact-sql，必须以服务器级主体登录名或 Azure Active Directory 管理员身份连接到 SQL 数据库实例。 （服务器级别 IP 防火墙规则必须首先由具有 Azure 级别权限的用户创建。）

### <a name="database-level-ip-firewall-rules"></a>数据库级 IP 防火墙规则

  这些规则允许客户端访问同一 SQL 数据库服务器内的某些（安全）数据库。 为每个数据库创建规则（包括*master*数据库），并将它们存储在单独的数据库中。
  
  只有在配置了第一个服务器级防火墙之后，才能使用 Transact-sql 语句创建和管理 master 数据库和用户数据库的数据库级 IP 防火墙规则。
  
  如果在数据库级 IP 防火墙规则中指定的 IP 地址范围不在服务器级 IP 防火墙规则范围内，则只有在数据库级范围内具有 IP 地址的客户端才可以访问该数据库。
  
  对于每个数据库，最多可以有 128 个数据库级别 IP 防火墙规则。 有关配置数据库级 IP 防火墙规则的详细信息，请参阅本文后面的示例并查看[sp_set_database_firewall_rule （AZURE SQL database）](https://msdn.microsoft.com/library/dn270010.aspx)。

### <a name="recommendations-for-how-to-set-firewall-rules"></a>关于如何设置防火墙规则的建议

建议尽可能使用数据库级 IP 防火墙规则。 这种做法增强了安全性，并使数据库更易于移植。 为管理员使用服务器级 IP 防火墙规则。 如果有多个数据库具有相同的访问要求，并且你不希望单独配置每个数据库，还可以使用它们。

> [!NOTE]
> 有关业务连续性上下文中的可移植数据库的信息，请参阅[灾难恢复的身份验证要求](sql-database-geo-replication-security-config.md)。

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>服务器级别与数据库级别 IP 防火墙规则

*是否应将一个数据库的用户与另一个数据库完全隔离？*

如果*是*，请使用数据库级 IP 防火墙规则授予访问权限。 此方法可避免使用服务器级 IP 防火墙规则，这些规则允许通过防火墙访问所有数据库。 这会降低防御的深度。

*IP 地址的用户是否需要访问所有数据库？*

如果*是*，请使用服务器级 IP 防火墙规则来减少必须配置 IP 防火墙规则的次数。

*配置 IP 防火墙规则的个人或团队是否只能通过 Azure 门户、PowerShell 或 REST API 进行访问？*

如果是这样，则必须使用服务器级 IP 防火墙规则。 仅可通过 Transact-sql 配置数据库级 IP 防火墙规则。  

*在数据库级别配置 IP 防火墙规则的个人或团队是否禁止具有高级权限？*

如果是这样，请使用服务器级 IP 防火墙规则。 在数据库级别，至少需要*CONTROL database*权限才能通过 transact-sql 配置数据库级 IP 防火墙规则。  

*配置或审核 IP 防火墙规则的个人或团队是否集中管理多个（可能几百个）数据库的 IP 防火墙规则？*

在此方案中，最佳实践取决于您的需求和环境。 虽然服务器级别 IP 防火墙规则可能更易于配置，但脚本可以在数据库级别配置规则。 即使使用服务器级 IP 防火墙规则，也可能需要审核数据库级 IP 防火墙规则，以查看对数据库具有*CONTROL*权限的用户是否创建数据库级 ip 防火墙规则。

*能否混合使用服务器级和数据库级 IP 防火墙规则？*

是的。 某些用户（如管理员）可能需要服务器级 IP 防火墙规则。 另一些用户（如数据库应用程序用户）可能需要数据库级别 IP 防火墙规则。

### <a name="connections-from-the-internet"></a>从 Internet 连接

当计算机尝试从 internet 连接到数据库服务器时，防火墙首先根据连接请求的数据库的数据库级 IP 防火墙规则检查请求的原始 IP 地址。

- 如果该地址在数据库级 IP 防火墙规则中指定的范围内，则将连接权限授予包含该规则的 SQL 数据库。
- 如果该地址不在数据库级 IP 防火墙规则的范围内，则防火墙将检查服务器级 IP 防火墙规则。 如果该地址在服务器级别 IP 防火墙规则的范围内，则授予连接。 服务器级别 IP 防火墙规则适用于 Azure SQL 服务器上的所有 SQL 数据库。  
- 如果该地址不在任何数据库级或服务器级 IP 防火墙规则的范围内，则连接请求失败。

> [!NOTE]
> 若要从本地计算机访问 SQL 数据库，请确保网络和本地计算机上的防火墙允许 TCP 端口1433上的传出通信。

### <a name="connections-from-inside-azure"></a>从 Azure 内部连接

若要允许在 Azure 内部托管的应用程序连接到 SQL server，必须启用 Azure 连接。 当 Azure 中的应用程序尝试连接到数据库服务器时，防火墙将验证是否允许 Azure 连接。 如果防火墙设置的开始和结束 IP 地址等于*0.0.0.0* ，则表示允许 Azure 连接。 可以通过设置防火墙规则，并在 "**防火墙" 和 "虚拟网络**" 设置中将 "**允许 Azure 服务和资源**" 切换到 **"打开**"，直接从 Azure 门户边栏选项卡启用此选项。 如果不允许该连接，请求将不会访问 SQL 数据库服务器。

> [!IMPORTANT]
> 此选项将防火墙配置为允许来自 Azure 的所有连接，包括来自其他客户的订阅的连接。 如果选择此选项，请确保登录名和用户权限仅限制授权用户的访问权限。

## <a name="create-and-manage-ip-firewall-rules"></a>创建和管理 IP 防火墙规则

通过使用[Azure 门户](https://portal.azure.com/)或使用[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql)、 [Azure CLI](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule)或 Azure [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)以编程方式创建第一个服务器级防火墙设置。 使用这些方法或 Transact-sql 创建和管理其他服务器级 IP 防火墙规则。

> [!IMPORTANT]
> 只能使用 Transact-sql 创建和管理数据库级 IP 防火墙规则。

为了提升性能，服务器级别 IP 防火墙规则暂时在数据库级别缓存。 若要刷新高速缓存，请参阅 [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx)。

> [!TIP]
> 可以使用[SQL 数据库审核](sql-database-auditing.md)来审核服务器级别和数据库级别防火墙的更改。

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>使用 Azure 门户管理服务器级别 IP 防火墙规则

若要在 Azure 门户中设置服务器级 IP 防火墙规则，请参阅 Azure SQL 数据库或 SQL 数据库服务器的 "概述" 页。

> [!TIP]
> 有关教程，请参阅[使用 Azure 门户创建 DB](sql-database-single-database-get-started.md)。

#### <a name="from-the-database-overview-page"></a>从 "数据库概述" 页

1. 若要从数据库概述页设置服务器级 IP 防火墙规则，请在工具栏上选择 "**设置服务器防火墙**"，如下图所示。 

    ![服务器 IP 防火墙规则](./media/sql-database-get-started-portal/sql-database-server-set-firewall-rule.png)

    此时会打开 SQL 数据库服务器的“防火墙设置”页。

2. 选择工具栏上的 "**添加客户端 IP** " 以添加你使用的计算机的 IP 地址，然后选择 "**保存**"。 此时，系统针对当前 IP 地址创建服务器级别 IP 防火墙规则。

    ![设置服务器级 IP 防火墙规则](./media/sql-database-get-started-portal/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>从 "服务器概述" 页

服务器的 "概述" 页面随即打开。 其中显示了完全限定的服务器名称（例如*mynewserver20170403.database.windows.net*），并提供了进一步配置的选项。

1. 若要在此页中设置服务器级规则，请从左侧的 "**设置**" 菜单中选择 "**防火墙**"。

2. 选择工具栏上的 "**添加客户端 IP** " 以添加你使用的计算机的 IP 地址，然后选择 "**保存**"。 此时，系统针对当前 IP 地址创建服务器级别 IP 防火墙规则。

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>使用 Transact-sql 管理 IP 防火墙规则

| 目录视图或存储过程 | 级别 | 说明 |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |服务器 |显示当前服务器级别 IP 防火墙规则 |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |服务器 |创建或更新服务器级别 IP 防火墙规则 |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |服务器 |删除服务器级别 IP 防火墙规则 |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |数据库 |显示当前数据库级别 IP 防火墙规则 |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |数据库 |创建或更新数据库级别 IP 防火墙规则 |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |数据库 |删除数据库级别 IP 防火墙规则 |

以下示例查看现有规则，启用服务器*Contoso*上一系列 ip 地址，并删除 ip 防火墙规则：

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

接下来，添加服务器级别 IP 防火墙规则。

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

若要删除服务器级 IP 防火墙规则，请执行*sp_delete_firewall_rule*存储过程。 下面的示例删除规则*ContosoFirewallRule*：

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>使用 PowerShell 管理服务器级 IP 防火墙规则 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL 数据库仍支持 PowerShell Azure 资源管理器模块，但所有开发现在都适用于 Az .Sql 模块。 有关这些 cmdlet，请参阅[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 和 AzureRm 模块中的命令参数完全相同。

| Cmdlet | 级别 | 说明 |
| --- | --- | --- |
| [AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |服务器 |返回当前的服务器级防火墙规则 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |服务器 |新建服务器级防火墙规则 |
| [AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |服务器 |更新现有服务器级防火墙规则的属性 |
| [AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |服务器 |删除服务器级防火墙规则 |

以下示例使用 PowerShell 设置服务器级 IP 防火墙规则：

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```
> [!TIP]
> 对于 $servername 指定服务器名称而不是完全限定的 DNS 名称，例如，指定**mysqldbserver**而不是**mysqldbserver.database.windows.net**

> [!TIP]
> 有关快速入门上下文中的 PowerShell 示例，请参阅[创建 DB-PowerShell](sql-database-powershell-samples.md)和[使用 PowerShell 创建单一数据库和配置 SQL 数据库服务器级 IP 防火墙规则](scripts/sql-database-create-and-configure-database-powershell.md)。

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>使用 CLI 管理服务器级别 IP 防火墙规则

| Cmdlet | 级别 | 说明 |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|服务器|创建服务器 IP 防火墙规则|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|服务器|列出服务器上的 IP 防火墙规则|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|服务器|显示 IP 防火墙规则的详细信息|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|服务器|更新 IP 防火墙规则|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|服务器|删除 IP 防火墙规则|

以下示例使用 CLI 设置服务器级 IP 防火墙规则：

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```
> [!TIP]
> 对于 $servername 指定服务器名称而不是完全限定的 DNS 名称，例如，指定**mysqldbserver**而不是**mysqldbserver.database.windows.net**

> [!TIP]
> 有关快速入门上下文中的 CLI 示例，请参阅[创建 DB Azure CLI](sql-database-cli-samples.md)和[创建单个数据库，并使用 Azure CLI 配置 SQL 数据库 IP 防火墙规则](scripts/sql-database-create-and-configure-database-cli.md)。

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>使用 REST API 来管理服务器级别 IP 防火墙规则

| API | 级别 | 说明 |
| --- | --- | --- |
| [列出防火墙规则](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |服务器 |显示当前服务器级别 IP 防火墙规则 |
| [创建或更新防火墙规则](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |服务器 |创建或更新服务器级别 IP 防火墙规则 |
| [删除防火墙规则](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |服务器 |删除服务器级别 IP 防火墙规则 |
| [获取防火墙规则](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | 服务器 | 获取服务器级别 IP 防火墙规则 |

## <a name="troubleshoot-the-database-firewall"></a>排查数据库防火墙问题

在访问 SQL 数据库服务时，请注意以下几点。

- **本地防火墙配置：**

  在计算机可以访问 SQL 数据库之前，可能需要在计算机上为 TCP 端口1433创建防火墙例外。 若要在 Azure 云边界内建立连接，可能需要打开其他端口。 有关详细信息，请参阅[ADO.NET 4.5 和 SQL 数据库的端口1433以外的端口](sql-database-develop-direct-route-ports-adonet-v12.md)中的 "SQL 数据库：外部与内部" 部分。

- **网络地址转换：**

  由于网络地址转换（NAT），计算机用来连接到 SQL 数据库的 IP 地址可能不同于计算机 IP 配置设置中的 IP 地址。 查看计算机用于连接到 Azure 的 IP 地址：
    1. 登录到门户。
    1. 请在承载数据库的服务器上，打开 "**配置**" 选项卡。
    1. **当前客户端 Ip 地址**将显示在 "**允许的 ip 地址**" 部分中。 选择**Add** "添加 **" 以允许**此计算机访问服务器。

- **对允许列表的更改尚未生效：**

  对 SQL 数据库防火墙配置所做的更改可能需要长达五分钟的时间才能生效。

- **登录名未授权，或使用了错误的密码：**

  如果登录名没有 SQL 数据库服务器上的权限或密码不正确，则与服务器的连接将被拒绝。 创建防火墙设置仅向客户端提供尝试连接到服务器的*机会*。 客户端仍必须提供必需的安全凭据。 有关准备登录名的详细信息，请参阅[控制和授予数据库对 Sql 数据库和 Sql 数据仓库的访问权限](sql-database-manage-logins.md)。

- **动态 IP 地址：**

  如果你的 internet 连接使用动态 IP 寻址，并且在通过防火墙时遇到问题，请尝试以下解决方案之一：
  
  - 向你的 internet 服务提供商询问分配给你的客户端计算机（用于访问 SQL 数据库服务器）的 IP 地址范围。 添加该 IP 地址范围作为 IP 防火墙规则。
  - 改为获取客户端计算机的静态 IP 地址。 添加 IP 地址作为 IP 防火墙规则。

## <a name="next-steps"></a>后续步骤

- 确认你的公司网络环境允许 Azure 数据中心使用的计算 IP 地址范围（包括 SQL 范围）中的入站通信。 可能需要将这些 IP 地址添加到允许列表。 请参阅[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。  
- 有关创建服务器级 IP 防火墙规则的快速入门，请参阅[创建 AZURE SQL 数据库](sql-database-single-database-get-started.md)。
- 有关从开源或第三方应用程序连接到 Azure SQL 数据库的帮助，请参阅[SQL 数据库的客户端快速入门代码示例](https://msdn.microsoft.com/library/azure/ee336282.aspx)。
- 有关可能需要打开的其他端口的信息，请参阅[ADO.NET 4.5 和 SQL 数据库的端口1433以外的端口](sql-database-develop-direct-route-ports-adonet-v12.md)中的 "SQL 数据库：外部与内部" 部分
- 有关 Azure SQL 数据库安全性的概述，请参阅[保护数据库](sql-database-security-overview.md)。

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
