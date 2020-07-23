---
title: 事务复制
titleSuffix: Azure SQL Managed Instance
description: 了解如何使用 Azure SQL 托管实例 SQL Server 事务复制。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 04/20/2020
ms.openlocfilehash: ee481067a3904c208061607b7109fcba0f3faaa7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86504061"
---
# <a name="transactional-replication-with-azure-sql-managed-instance"></a>通过 Azure SQL 托管实例进行事务复制
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

事务复制是 Azure SQL 托管实例和 SQL Server 的一项功能，它使你能够将数据从 Azure SQL SQL Server 托管实例中的表复制到位于远程数据库的表中。 使用此功能可以同步不同数据库中的多个表。

## <a name="overview"></a>概述

可以使用事务复制将 Azure SQL 托管实例中所做的更改推送到：

- SQL Server 本地或 Azure VM 上的数据库
- Azure SQL 数据库中的数据库
- Azure SQL 中的实例数据库托管实例

  > [!NOTE]
  > 若要使用 Azure SQL 托管实例的所有功能，必须使用最新版本的[SQL Server Management Studio （SSMS）](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)和[SQL Server Data Tools （SSDT）](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)。

### <a name="components"></a>组件

事务复制中的关键组件包括**发布服务器**、**分发**服务器和**订阅服务器**，如下图所示：  

![SQL 数据库的复制](./media/replication-transactional-overview/replication-to-sql-database.png)

| 角色 | Azure SQL Database | Azure SQL 托管实例 |
| :----| :------------- | :--------------- |
| **发布者** | No | 是 |
| **分发服务器** | No | 是|
| **提取订阅服务器** | No | 是|
| **推送订阅服务器**| 是 | 是|
| &nbsp; | &nbsp; | &nbsp; |

**发布服务器**通过将更新发送到分发服务器来发布对某些表（项目）所做的更改。 发布者可以是 Azure SQL 托管实例或 SQL Server 实例。

**分发服务器**在发布服务器上收集项目中的更改并将其分发给订阅服务器。 分发服务器可以是 Azure SQL 托管实例或 SQL Server 实例（任何版本，只要它与发布服务器版本相同或更高）。

**订阅服务器**接收在发布服务器上所做的更改。 SQL Server 实例和 Azure SQL 托管实例可以是推送和请求订阅服务器，但当分发服务器是 Azure SQL 托管实例而订阅服务器不受支持时，请求订阅不受支持。 Azure SQL 数据库中的数据库只能是推送订阅服务器。

Azure SQL 托管实例可以通过以下版本的 SQL Server 来支持订阅服务器：

- SQL Server 2016 及更高版本
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) 或 [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) 或 [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

   > [!NOTE]
   >
   > - 对于不支持发布到 Azure 中的对象的其他 SQL Server 版本，可以利用[重新发布数据](https://docs.microsoft.com/sql/relational-databases/replication/republish-data)方法将数据转移到较新版本的 SQL Server。
   > - 尝试使用旧版本来配置复制可能导致错误 MSSQL_REPL20084（进程无法连接到订阅服务器）和 MSSQ_REPL40532（无法打开登录名所请求的服务器 \<name>。 登录失败。）

### <a name="types-of-replication"></a>复制类型

有不同的[复制类型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)：

| 复制 | Azure SQL Database | Azure SQL 托管实例 |
| :----| :------------- | :--------------- |
| [**标准事务**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | 是（仅用作订阅服务器） | 是 |
| [**快照**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | 是（仅用作订阅服务器） | 是|
| [**合并复制**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | 否 | 否|
| [**对等**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | 否 | 否|
| [**双向**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | No | 是|
| [**可更新订阅**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | 否 | 否|
| &nbsp; | &nbsp; | &nbsp; |

### <a name="supportability-matrix"></a>可支持性矩阵

  适用于 Azure SQL 托管实例的事务复制可支持性矩阵与 SQL Server 相同。
  
| **发布者**   | **分发服务器** | **订阅服务器** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="when-to-use"></a>何时使用

在以下情况下，事务复制非常有用：

- 发布数据库中的一个或多个表中所做的更改，并将其分发到 SQL Server 实例或 Azure SQL 数据库中订阅了更改的一个或多个数据库。
- 将多个分布式数据库保持同步状态。
- 通过持续发布所做的更改，将数据库从一个 SQL Server 实例或 Azure SQL 托管实例迁移到其他数据库。

### <a name="compare-data-sync-with-transactional-replication"></a>将数据同步与事务复制进行比较

| 类别 | 数据同步 | 事务复制 |
|---|---|---|
| 优点 | - 主动-主动支持<br/>- 在本地和 Azure SQL 数据库之间双向同步 | - 更低的延迟<br/>- 事务一致性<br/>- 迁移后重用现有拓扑 |
| 缺点 | - 5 分钟或更长的延迟<br/>- 无事务一致性<br/>- 更高的性能影响 | -无法从 Azure SQL 数据库发布 <br/>- 维护成本高 |

## <a name="common-configurations"></a>常用配置

一般情况下，发布服务器和分发服务器必须都在云中，或者都在本地。 支持以下配置：

### <a name="publisher-with-local-distributor-on-sql-managed-instance"></a>SQL 托管实例上具有本地分发服务器的发布服务器

![用作发布服务器和分发服务器的单个实例](./media/replication-transactional-overview/01-single-instance-asdbmi-pubdist.png)

发布服务器和分发服务器在单个 SQL 托管实例中进行配置，并将更改分发到另一个 SQL 托管实例、SQL 数据库或 SQL Server 实例中。

### <a name="publisher-with-remote-distributor-on-sql-managed-instance"></a>SQL 托管实例上的带远程分发服务器的发布服务器

在此配置中，一个托管实例将更改发布到位于另一个 SQL 托管实例上的分发服务器，该服务器可以为多个源 SQL 托管实例提供服务，并将更改分发到 Azure SQL 数据库、Azure SQL 托管实例或 SQL Server 上的一个或多个目标。

![发布服务器和分发服务器的独立实例](./media/replication-transactional-overview/02-separate-instances-asdbmi-pubdist.png)

在两个托管实例上配置发布服务器和分发服务器。 此配置存在一些约束：

- 两个托管实例位于同一 vNet 中。
- 两个托管实例都位于同一位置。

### <a name="on-premises-publisherdistributor-with-remote-subscriber"></a>具有远程订阅服务器的本地发布服务器/分发服务器

![作为订阅服务器的 Azure SQL 数据库](./media/replication-transactional-overview/03-azure-sql-db-subscriber.png)

在此配置中，Azure SQL 数据库或 Azure SQL 托管实例中的数据库是一个订阅服务器。 此配置支持从本地迁移到 Azure。 如果订阅服务器是 Azure SQL 数据库中的数据库，则该数据库必须处于推送模式。  

## <a name="requirements"></a>要求

- 使用 SQL 身份验证在复制参与者之间建立连接。
- 将 Azure 存储帐户共享用于复制所使用的工作目录。
- 打开子网安全规则中的 TCP 出站端口445以访问 Azure 文件共享。
- 当 SQL 托管实例为发布服务器/分发服务器并且订阅服务器不是时，打开 TCP 出站端口1433。 你可能还需要将 `allow_linkedserver_outbound` 端口 1433**目标服务标记**的 SQL 托管实例 NSG 出站安全规则更改为 `virtualnetwork` `internet` 。
- 将发布服务器和分发服务器置于云中，或同时置于本地。
- 如果虚拟网络不同，请在复制参与者的虚拟网络之间配置 VPN 对等互连。

> [!NOTE]
> 连接到 Azure 存储文件时，如果出站网络安全组（NSG）端口445被阻止（当分发服务器是 Azure SQL 托管实例数据库，而订阅服务器在本地）时，则可能会遇到错误53。 [更新 vNet NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) 以解决此问题。

## <a name="with-failover-groups"></a>使用故障转移组

使用事务复制的 SQL 托管实例不支持[活动异地复制](../database/active-geo-replication-overview.md)。 请不要使用活动异地复制，而应使用[自动故障转移组](../database/auto-failover-group-overview.md)，但请注意，必须从主托管实例中[手动删除](transact-sql-tsql-differences-sql-server.md#replication)发布，并在故障转移后在辅助 SQL 托管实例上重新创建发布。

如果在[故障转移组](../database/auto-failover-group-overview.md)中的**发布服务器**或**分发服务器**SQL 托管实例上启用了异地复制，则 SQL 托管实例管理员必须清除旧主副本上的所有发布，并在发生故障转移后在新的主副本上重新配置这些发布。 在此方案中，需要执行以下活动：

1. 停止数据库上运行的所有复制作业（如果有）。
1. 通过在发布服务器数据库上运行以下脚本，删除发布服务器中的订阅元数据：

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```

1. 删除订阅服务器中的订阅元数据。 在订阅服务器 SQL 托管实例上的订阅数据库上运行以下脚本：

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>',
      @publisher_db = N'<publisher database>',
      @publication = N'<name of publication>';
   ```

1. 通过在已发布的数据库中运行以下脚本，强制删除发布服务器中的所有复制对象：

   ```sql
   EXEC sp_removedbreplication
   ```

1. 从原始的主 SQL 托管实例强制删除旧的分发服务器（如果故障回复到使用了分发服务器的旧主服务器）。 在旧分发服务器 SQL 托管实例中的 master 数据库上运行以下脚本：

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

如果对故障转移组中的**订阅服务器**实例启用了异地复制，则应将发布配置为连接到订阅服务器托管实例的故障转移组侦听器终结点。 发生故障转移时，托管实例管理员执行的后续操作取决于发生的故障转移类型：

- 如果在不丢失数据的情况下进行故障转移，则故障转移后复制将继续工作。
- 如果在丢失数据的情况下进行故障转移，复制也能正常工作。 它会再次复制丢失的更改。
- 对于数据丢失的故障转移，但数据丢失超出了分发数据库的保持期，SQL 托管实例管理员将需要重新初始化订阅数据库。

## <a name="next-steps"></a>后续步骤

有关配置事务复制的详细信息，请参阅以下教程：

- [配置 SQL 托管实例发布服务器与订阅服务器之间的复制](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [配置 SQL 托管实例发布服务器、SQL 托管实例分发服务器和 SQL Server 订阅服务器之间的复制](../managed-instance/replication-two-instances-and-sql-server-configure-tutorial.md)
- [创建发布](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)。
- [Create a push subscription](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription)使用服务器名称（例如， `N'azuresqldbdns.database.windows.net` Azure SQL 数据库名称中的数据库作为目标数据库，例如**Adventureworks**）来创建推送订阅。 )

## <a name="see-also"></a>另请参阅  

- [使用 SQL 托管实例和故障转移组进行复制](transact-sql-tsql-differences-sql-server.md#replication)
- [复制到 SQL 数据库](../database/replication-to-sql-database.md)
- [复制到托管实例](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [创建发布](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [创建推送订阅](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [复制类型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [监视（复制）](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [初始化订阅](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
