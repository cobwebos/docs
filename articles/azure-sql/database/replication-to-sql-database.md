---
title: Azure SQL Server 复制到 Azure SQL 数据库
description: 可以在一个单向事务或快照复制拓扑中将 Azure SQL 数据库中的数据库配置为推送订阅服务器。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 079d187f66cf77585121198df06cabafc454fea1
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362123"
---
# <a name="replication-to-azure-sql-database"></a>复制到 Azure SQL 数据库
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

可以在单向事务或快照复制拓扑中将 Azure SQL 数据库配置为推送订阅服务器。

> [!NOTE]
> 本文介绍如何在 Azure SQL 数据库中使用[事务复制](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication)。 它与[活动异地复制](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)无关，后者是一项 Azure SQL 数据库功能，可用于创建单个数据库的完全可读副本。

## <a name="supported-configurations"></a>支持的配置
  
- Azure SQL 数据库只能是 SQL Server 发布服务器和分发服务器的推送订阅服务器。  
- 充当发布服务器和/或分发服务器的 SQL Server 实例可以是[在本地运行的 SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) 的实例、[Azure SQL 托管实例](../managed-instance/instance-create-quickstart.md)，或[在云中的 Azure 虚拟机上运行的 SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md) 的实例。 
- 不能将分发数据库和复制代理置于一个 Azure SQL 数据库中的数据库上。  
- [支持快照](/sql/relational-databases/replication/snapshot-replication)和[单向事务性](/sql/relational-databases/replication/transactional/transactional-replication)复制。 不支持对等事务复制和合并复制。

### <a name="versions"></a>版本  

若要将内容成功复制到 Azure SQL 数据库中的数据库，SQL Server 发布服务器和分发服务器须至少使用以下版本之一：

以下版本的 SQL Server 支持从 SQL Server 数据库发布到任何 Azure SQL 数据库：

- SQL Server 2016 及更高版本
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) 或 [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) 或 [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> 尝试使用不受支持的版本来配置复制可能导致错误编号 MSSQL_REPL20084（进程无法连接到订阅服务器）和 MSSQL_REPL40532（无法打开登录名所请求的服务器 \<name>。 登录失败）。  

若要使用 Azure SQL 数据库的所有功能，必须使用最新版的 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 和 [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt)。  

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

  
## <a name="remarks"></a>备注

- 仅支持推送订阅 Azure SQL 数据库。  
- 若要配置复制，可以使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)，也可以在发布服务器上执行 Transact-SQL 语句。 不能使用 Azure 门户来配置复制。  
- 复制时，只能使用 SQL Server 身份验证登录来连接到 Azure SQL 数据库。
- 复制的表必须有主键。  
- 必须已经有 Azure 订阅。  
- Azure SQL 数据库订阅服务器可以位于任何区域。  
- SQL Server 上的单一发布可以支持 Azure SQL 数据库和 SQL Server（本地的以及 Azure 虚拟机中的 SQL Server）订阅服务器。  
- 必须从 SQL Server 而不是 Azure SQL 数据库中执行复制管理、监视和故障排除。  
- 在适用于 SQL 数据库的 **sp_addsubscription** 中，仅 `@subscriber_type = 0` 受支持。  
- Azure SQL 数据库不支持双向、即时、可更新或对等复制。

## <a name="replication-architecture"></a>复制体系结构  

![关系图显示了 Azure SQL 数据库的复制体系结构，其中包含不同区域的几个订阅服务器群集和本地 Azure 虚拟机，其中包含连接到远程群集的发布服务器、Logread.exe 可执行文件和分发服务器可执行文件。](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>方案  

### <a name="typical-replication-scenario"></a>典型复制方案  

1. 在 SQL Server 数据库上创建事务复制发布。  
2. 在 SQL Server 上使用**新建订阅向导**或 Transact-SQL 语句创建一个推送，以便推送到 Azure SQL 数据库的订阅。  
3. 对于 Azure SQL 数据库中的单一数据库和共用数据库，初始数据集是由快照代理创建并由分发代理分发和应用的快照。 使用 SQL 数据库托管实例发布服务器，还可以使用数据库备份来植入 Azure SQL 数据库订阅服务器。

### <a name="data-migration-scenario"></a>数据迁移方案  

1. 使用事务复制将数据从 SQL Server 数据库复制到 Azure SQL 数据库。  
2. 重定向客户端或中间层应用程序以更新数据库副本。  
3. 停止更新 SQL Server 版本的表并删除发布。  

## <a name="limitations"></a>限制

Azure SQL 数据库订阅不支持以下选项：

- 复制文件组关联关系  
- 复制表分区方案  
- 复制索引分区方案  
- 复制用户定义的统计信息  
- 复制默认绑定  
- 复制规则绑定  
- 复制全文索引  
- 复制 XML XSD  
- 复制 XML 索引  
- 复制权限  
- 复制空间索引  
- 复制筛选的索引  
- 复制数据压缩属性  
- 复制稀疏列属性  
- 将文件流转换为 MAX 数据类型  
- 将 hierarchyid 转换为 MAX 数据类型  
- 将空间转换为 MAX 数据类型  
- 复制扩展的属性  

### <a name="limitations-to-be-determined"></a>限制待定

- 复制排序规则  
- 在 SP 的序列化事务中执行  

## <a name="examples"></a>示例

创建发布和推送订阅。 有关详细信息，请参阅：
  
- [创建发布](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- 使用服务器名称作为订阅服务器 (例如**n'azuresqldbdns.database.windows.net "**) ，并使用 Azure SQL 数据库名称作为目标数据库 (例如**AdventureWorks**) ，以[创建推送订阅](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)。  

## <a name="see-also"></a>另请参阅  

- [事务复制](../managed-instance/replication-transactional-overview.md)
- [创建发布](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [创建推送订阅](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [复制类型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [监视（复制）](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [初始化订阅](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
