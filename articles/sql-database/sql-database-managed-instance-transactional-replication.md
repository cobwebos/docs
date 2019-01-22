---
title: Azure SQL 逻辑服务器和 Azure SQL 托管实例的事务复制 | Microsoft Docs
description: 了解如何对 Azure SQL 数据库逻辑服务器和 SQL 托管实例使用 SQL Server 事务复制。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 01/08/2019
ms.openlocfilehash: d94173f9b1940613c26451658b90c956c71876fb
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353237"
---
# <a name="transactional-replication-with-azure-sql-logical-server-and-azure-sql-managed-instance"></a>Azure SQL 逻辑服务器和 Azure SQL 托管实例的事务复制

事务复制是 Azure SQL 数据库、托管实例和 SQL Server 的一项功能，用于将 Azure SQL 数据库或 SQL Server 的表中的数据复制到远程数据库中的表。 使用此功能可以同步不同数据库中的多个表。

## <a name="when-to-use-transactional-replication"></a>何时使用事务复制

在以下情况下，事务复制非常有用：
- 发布数据库的一个或多个表中所做的更改，并将其分发到订阅了更改的一个或多个 SQL Server 或 Azure SQL 数据库。
- 将多个分布式数据库保持同步状态。
- 通过持续发布更改，将数据库从一个 SQL Server 或托管实例迁移到另一个数据库。

## <a name="overview"></a>概述 
下图显示了事务复制的关键组件：  

![SQL 数据库的复制](media/replication-to-sql-database/replication-to-sql-database.png)


**发布服务器**是通过将更新发送到分发服务器，来发布某些表（项目）所发生的更改的实例或服务器。 以下版本的 SQL Server 支持从本地 SQL Server 发布到 Azure SQL 数据库
    - SQL Server 2019（预览版）
    - SQL Server 2016 到 SQL 2017
    - SQL Server 2014 SP1 CU3 或更高版本 (12.00.4427)
    - SQL Server 2014 RTM CU10 (12.00.2556)
    - SQL Server 2012 SP3 或更高版本 (11.0.6020)
    - SQL Server 2012 SP2 CU8 (11.0.5634.0)
    - 对于不支持发布到 Azure 中的对象的其他 SQL Server 版本，可以利用[重新发布数据](https://docs.microsoft.com/sql/relational-databases/replication/republish-data)方法将数据转移到较新版本的 SQL Server。 

**分发服务器**是从发布服务器收集项目中的更改，并将其分发到订阅服务器的实例或服务器。 分发服务器可以是 Azure SQL 数据库托管实例或 SQL Server（可以采用等于或高于发布服务器版本的任何版本）。 

**订阅服务器**是接收发布服务器上发生的更改的实例或服务器。 订阅服务器可以是 Azure SQL 数据库逻辑服务器/托管实例或 SQL Server。 必须将逻辑服务器上的订阅服务器配置为推送-订阅服务器。 

| 角色 | 逻辑服务器 | 托管实例 |
| :----| :------------- | :--------------- |
| **发布者** | 否 | 是 | 
| **分发服务器** | 否 | 是|
| **提取订阅服务器** | 否 | 是|
| **推送订阅服务器**| 是 | 是|
| &nbsp; | &nbsp; | &nbsp; |

有不同的[复制类型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication?view=sql-server-2017)：


| 复制 | 逻辑服务器 | 托管实例 |
| :----| :------------- | :--------------- |
| [**事务**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | 是（仅用作订阅服务器） | 是 | 
| [**快照**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | 是（仅用作订阅服务器） | 是|
| [**合并复制**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | 否 | 否|
| [**对等**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | 否 | 否|
| **单向** | 是 | 是|
| [**双向**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | 否 | 是|
| [**可更新订阅**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | 否 | 否|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - 尝试使用旧版本来配置复制可能导致错误 MSSQL_REPL20084（进程无法连接到订阅服务器）和 MSSQ_REPL40532（无法打开登录名所请求的服务器 <name>。 登录失败。）
  > - 若要使用 Azure SQL 数据库的所有功能，必须使用最新版本的 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) 和 [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)。

## <a name="requirements"></a>要求
- 连接时，在复制参与者之间使用 SQL 身份验证。 
- 复制功能使用的工作目录的 Azure 存储帐户共享。 
- 需要在托管实例子网的安全规则中打开端口 445（TCP 出站）才能访问 Azure 文件共享。 
- 如果发布服务器/分发服务器位于托管实例上，而订阅服务器位于本地，则需要打开端口 1433（TCP 出站）。 

## <a name="common-configurations"></a>常用配置
一般情况下，发布服务器和分发服务器必须都在云中，或者都在本地。 支持以下配置： 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>带本地分发服务器的发布服务器位于托管实例上

![用作发布服务器和分发服务器的单个实例 ](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

发布服务器和分发服务器在单个托管实例中配置，并将更改分发到本地的其他托管实例、单一数据库或 SQL Server。 在此配置中，不能使用[异地复制和自动故障转移组](sql-database-auto-failover-group.md)来配置发布服务器/分发服务器托管实例。

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>带远程分发服务器的发布服务器位于托管实例上

在此配置中，由一个托管实例将更改发布到能够为许多源托管实例提供服务的另一个托管实例上的分发服务器，并将更改分发到托管实例、单一数据库或 SQL Server 上的一个或多个目标。

![发布服务器和分发服务器的独立实例](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

在两个托管实例上配置发布服务器和分发服务器。 在此配置中

- 两个托管实例位于同一 vNet 中。
- 两个托管实例位于同一位置。
- 不能[使用自动故障转移组异地复制](sql-database-auto-failover-group.md)正在托管发布和分发服务器数据库的托管实例。

### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-managed-instance-or-logical-server"></a>发布服务器和分发服务器位于本地，订阅服务器位于托管实例或逻辑服务器上 

![Azure SQL DB 用作订阅服务器](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
在此配置中，Azure SQL 数据库（托管实例或逻辑服务器）是订阅服务器。 此配置支持从本地迁移到 Azure。 如果订阅服务器位于逻辑服务器上，则它必须处于推送模式。  

## <a name="next-steps"></a>后续步骤
1. [为托管实例配置事务复制](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance#configure-publishing-and-distribution-example)。 
1. [创建发布](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)。
1. 使用 Azure SQL 数据库逻辑服务器名称作为订阅服务器（例如 `N'azuresqldbdns.database.windows.net`）并使用 Azure SQL 数据库名称作为目标数据库（例如 **Adventureworks**）来[创建推送订阅](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription)。 )


## <a name="see-also"></a>另请参阅  

- [复制到 SQL 数据库](replication-to-sql-database.md)
- [复制到托管实例](replication-with-sql-database-managed-instance.md)
- [创建发布](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [创建推送订阅](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [复制类型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [监视（复制）](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [初始化订阅](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
