---
title: 事务复制
titleSuffix: Azure SQL Managed Instance
description: 了解如何在 Azure SQL 托管实例 (预览) 中使用 SQL Server 事务复制。
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
ms.openlocfilehash: 95fc877636fe1d3dac466dc32fc49cee56f35185
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89290508"
---
# <a name="transactional-replication-with-azure-sql-managed-instance-preview"></a>通过 Azure SQL 托管实例 (预览版进行事务复制) 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

事务复制是 Azure SQL 托管实例和 SQL Server 的一项功能，用于将 Azure SQL 托管实例或 SQL Server 实例中表的数据复制到远程数据库中的表。 使用此功能可以同步不同数据库中的多个表。 

事务复制目前提供了用于 SQL 托管实例的公共预览版。 

## <a name="overview"></a>概述

还可以使用事务复制将 Azure SQL 托管实例中发生的更改推送到：

- SQL Server 数据库 - 在本地或在 Azure VM 上
- Azure SQL 数据库中的数据库
- Azure SQL 托管实例中的实例数据库

  > [!NOTE]
  > 若要使用 Azure SQL 托管实例的所有功能，必须使用最新版本的 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 和 [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)。

### <a name="components"></a>组件

事务复制的关键组件是“发布服务器”、“分发服务器”和“订阅服务器”，如下图所示  ：  

![SQL 数据库的复制](./media/replication-transactional-overview/replication-to-sql-database.png)

| 角色 | Azure SQL 数据库 | Azure SQL 托管实例 |
| :----| :------------- | :--------------- |
| **发布者** | 否 | 是 |
| **分发服务器** | 否 | 是|
| **提取订阅服务器** | 否 | 是|
| **推送订阅服务器**| 是 | 是|
| &nbsp; | &nbsp; | &nbsp; |

“发布服务器”通过将更新发送到分发服务器，来发布某些表（项目）所发生的更改。 发布服务器可以是 Azure SQL 托管实例或 SQL Server 实例。

“分发服务器”从发布服务器收集项目中的更改，并将其分发到订阅服务器。 分发服务器可以是 Azure SQL 托管实例或 SQL Server 实例（可以采用等于或高于发布服务器版本的任何版本）。

“订阅服务器”接收在发布服务器上发生的更改。 SQL Server 实例和 Azure SQL 托管实例都可以是推送和请求订阅服务器，但当分发服务器是 Azure SQL 托管实例而订阅服务器不是时，则请求订阅不受支持。 Azure SQL 数据库中的数据库只能是推送订阅服务器。

Azure SQL 托管实例可以支持成为以下版本的 SQL Server 的订阅服务器：

- SQL Server 2016 和更高版本
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) 或 [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) 或 [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

   > [!NOTE]
   >
   > - 对于不支持发布到 Azure 中的对象的其他 SQL Server 版本，可以利用[重新发布数据](https://docs.microsoft.com/sql/relational-databases/replication/republish-data)方法将数据转移到较新版本的 SQL Server。
   > - 尝试使用旧版本来配置复制可能导致错误 MSSQL_REPL20084（进程无法连接到订阅服务器）和 MSSQ_REPL40532（无法打开登录名所请求的服务器 \<name>。 登录失败。）

### <a name="types-of-replication"></a>复制类型

有不同的[复制类型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)：

| 复制 | Azure SQL 数据库 | Azure SQL 托管实例 |
| :----| :------------- | :--------------- |
| [**标准事务**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | 是（仅用作订阅服务器） | 是 |
| [**快照**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | 是（仅用作订阅服务器） | 是|
| [**合并复制**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | 否 | 否|
| [**对等**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | 否 | 否|
| [**双向**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | 否 | 是|
| [**可更新订阅**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | 否 | 否|
| &nbsp; | &nbsp; | &nbsp; |

### <a name="supportability-matrix"></a>可支持性矩阵

  Azure SQL 托管实例的事务复制可支持性矩阵与 SQL Server 的相同。
  
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

- 发布数据库的一个或多个表中所做的更改，并将其分发到订阅了更改的 SQL Server 实例或 Azure SQL 数据库中的一个或多个数据库。
- 将多个分布式数据库保持同步状态。
- 通过持续发布更改，将数据库从一个 SQL Server 实例或 Azure SQL 托管实例迁移到另一个数据库。

### <a name="compare-data-sync-with-transactional-replication"></a>将数据同步与事务复制进行比较

| 类别 | 数据同步 | 事务复制 |
|---|---|---|
| 优点 | - 主动-主动支持<br/>- 在本地和 Azure SQL 数据库之间双向同步 | - 更低的延迟<br/>- 事务一致性<br/>- 迁移后重用现有拓扑 |
| 缺点 | - 5 分钟或更长的延迟<br/>- 无事务一致性<br/>- 更高的性能影响 | -无法从 Azure SQL 数据库发布 <br/>- 维护成本高 |

## <a name="common-configurations"></a>常用配置

一般情况下，发布服务器和分发服务器必须都在云中，或者都在本地。 支持以下配置：

### <a name="publisher-with-local-distributor-on-sql-managed-instance"></a>带本地分发服务器的发布服务器位于 SQL 托管实例上

![用作发布服务器和分发服务器的单个实例](./media/replication-transactional-overview/01-single-instance-asdbmi-pubdist.png)

发布服务器和分发服务器在单个 SQL 托管实例中配置，并将更改分发到其他 SQL 托管实例、SQL 数据库或 SQL Server 实例。

### <a name="publisher-with-remote-distributor-on-sql-managed-instance"></a>带远程分发服务器的发布服务器位于 SQL 托管实例上

在此配置中，由一个托管实例将更改发布到能够为许多源 SQL 托管实例提供服务的另一个 SQL 托管实例上的分发服务器，并将更改分发到 Azure SQL 数据库、Azure SQL 托管实例或 SQL Server 上的一个或多个目标。

![发布服务器和分发服务器的独立实例](./media/replication-transactional-overview/02-separate-instances-asdbmi-pubdist.png)

在两个托管实例上配置发布服务器和分发服务器。 此配置存在一些约束：

- 两个托管实例位于同一 vNet 中。
- 两个托管实例都位于同一位置。

### <a name="on-premises-publisherdistributor-with-remote-subscriber"></a>本地发布服务器/分发服务器与远程订阅服务器

![Azure SQL 数据库作为订阅服务器](./media/replication-transactional-overview/03-azure-sql-db-subscriber.png)

在此配置中，Azure SQL 数据库或 Azure SQL 托管实例中的数据库是订阅服务器。 此配置支持从本地迁移到 Azure。 如果订阅服务器是 Azure SQL 数据库中的数据库，则它必须处于推送模式。  

## <a name="requirements"></a>要求

- 使用 SQL 身份验证实现复制参与者之间的连接。
- 对复制功能使用的工作目录使用 Azure 存储帐户共享。
- 在子网安全规则中打开 TCP 出站端口 445 以访问 Azure 文件共享。
- 当 SQL 托管实例是发布服务器/分发服务器，而订阅服务器不是时，请打开 TCP 出站端口 1433。 对于端口 1433 目标服务标记，你可能还需要将 `allow_linkedserver_outbound` 的 SQL 托管实例 NSG 出站安全规则从 `virtualnetwork` 更改为 `internet`。
- 将发布服务器和分发服务器都置于云中，或都置于本地。
- 如果虚拟网络不同，请配置复制参与者的虚拟网络之间的 VPN 对等互连。

> [!NOTE]
> 当分发服务器为 Azure SQL 托管实例数据库且订阅服务器位于本地时，如果阻止出站网络安全组 (NSG) 端口 445，则会在连接到 Azure 存储文件时遇到错误 53。 [更新 vNet NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) 以解决此问题。

## <a name="with-failover-groups"></a>使用故障转移组

使用事务复制的 SQL 托管实例不支持[活动异地复制](../database/active-geo-replication-overview.md)。 使用[自动故障转移组](../database/auto-failover-group-overview.md)而不是活动异地复制，但请注意，必须从主托管实例中[手动删除](transact-sql-tsql-differences-sql-server.md#replication)发布，并在故障转移后在辅助 SQL 托管实例上重新创建。

如果对[故障转移组](../database/auto-failover-group-overview.md)中的“发布服务器”或“分发服务器”SQL 托管实例启用了异地复制，则 SQL 托管实例管理员必须清理旧的主节点上的所有发布内容，然后在故障转移后，在新的主节点上重新配置这些发布内容 。 在此方案中，需要执行以下活动：

1. 停止数据库上运行的所有复制作业（如果有）。
1. 通过在发布服务器数据库上运行以下脚本，删除发布服务器中的订阅元数据：

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```

1. 删除订阅服务器中的订阅元数据。 对订阅服务器 SQL 托管实例上的订阅数据库运行以下脚本：

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

1. 强制删除原始主 SQL 托管实例中的旧分发服务器（如果故障回复到曾经具有分发服务器的旧主实例）。 在旧的分发服务器 SQL 托管实例中的 master 数据库上运行以下脚本：

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

如果对故障转移组中的**订阅服务器**实例启用了异地复制，则应将发布配置为连接到订阅服务器托管实例的故障转移组侦听器终结点。 发生故障转移时，托管实例管理员执行的后续操作取决于发生的故障转移类型：

- 如果在不丢失数据的情况下进行故障转移，则故障转移后复制将继续工作。
- 如果在丢失数据的情况下进行故障转移，复制也能正常工作。 它会再次复制丢失的更改。
- 如果在丢失数据的情况下进行故障转移，但数据丢失发生在分发数据库保留期以外，则 SQL 托管实例管理员需要重新初始化订阅数据库。

## <a name="next-steps"></a>后续步骤

有关配置事务复制的详细信息，请参阅以下教程：

- [配置 SQL 托管实例发布服务器与订阅服务器之间的复制](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [配置 SQL 托管实例发布服务器、SQL 托管实例分发服务器与 SQL Server 订阅服务器之间的复制](../managed-instance/replication-two-instances-and-sql-server-configure-tutorial.md)
- [创建发布](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)。
- 使用服务器名称作为订阅服务器（例如 `N'azuresqldbdns.database.windows.net`）并使用 Azure SQL 数据库中的数据库名称作为目标数据库（例如 Adventureworks）来[创建推送订阅](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription)。 )

## <a name="see-also"></a>另请参阅  

- [使用 SQL 托管实例和故障转移组进行复制](transact-sql-tsql-differences-sql-server.md#replication)
- [复制到 SQL 数据库](../database/replication-to-sql-database.md)
- [复制到托管实例](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [创建发布](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [创建推送订阅](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [复制类型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [监视（复制）](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [初始化订阅](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
