---
title: 复制
description: 了解如何对 Azure SQL 数据库的单个数据库和弹性池中的数据库使用 SQL Server 复制
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: f718bc17b987926f4324635f096d5983acdb63fc
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997269"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>复制到 SQL 数据库的单一数据库和共用数据库

可以对 SQL Server 复制进行配置，以便复制到 Azure SQL 数据库中 [SQL 数据库服务器](sql-database-servers.md)上的单一数据库和共用数据库。  

## <a name="supported-configurations"></a>**支持的配置：**
  
- SQL Server 可以是在本地运行的 SQL Server 实例，也可以是在云端的 Azure 虚拟机中运行的 SQL Server 实例。 有关详细信息，请参阅 [Azure 虚拟机上的 SQL Server 概述](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/)。  
- Azure SQL 数据库必须是 SQL Server 发布服务器的推送订阅者。  
- 不能将分发数据库和复制代理置于一个 Azure SQL 数据库上。  
- 支持快照和单向事务复制。 不支持对等事务复制和合并复制。
- Azure SQL 数据库托管实例上的公共预览版可以使用复制。 托管实例可以托管发布服务器、分发服务器和订阅服务器数据库。 有关详细信息，请参阅[通过 SQL 数据库托管实例进行复制](replication-with-sql-database-managed-instance.md)。

## <a name="versions"></a>版本  

本地 SQL Server 发布服务器和分发服务器必须至少使用以下版本之一：  

- SQL Server 2016 及更高版本
- SQL Server 2014 [RTM CU10 （12.0.4427.24）](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014)或[SP1 CU3 （12.0.2556.4）](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 （11.0.5634.1）](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2)或[SP3 （11.0.6020.0）](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> 尝试使用不受支持的版本配置复制可能导致错误号 MSSQL_REPL20084 （进程无法连接到订阅服务器。）和 MSSQL_REPL40532 （无法打开登录名 > 请求的服务器 \<名称。 登录失败）。  

若要使用 Azure SQL 数据库的所有功能，必须使用最新版的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 和 [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)。  

  
## <a name="remarks"></a>备注

- 若要配置复制，可以使用 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)，也可以在发布服务器上执行 Transact-SQL 语句。 不能使用 Azure 门户来配置复制。  
- 复制时，只能使用 SQL Server 身份验证登录来连接到 Azure SQL 数据库。
- 复制的表必须有主键。  
- 必须已经有 Azure 订阅。  
- Azure SQL 数据库订阅服务器可以位于任何区域。  
- SQL Server 上的单一发布可以支持 Azure SQL 数据库和 SQL Server（本地的以及 Azure 虚拟机中的 SQL Server）订阅服务器。  
- 复制管理、监视和故障排除必须从本地 SQL Server 执行。  
- 仅支持推送订阅 Azure SQL 数据库。  
- 在适用于 SQL 数据库的 **sp_addsubscription** 中，仅 `@subscriber_type = 0` 受支持。  
- Azure SQL 数据库不支持双向、即时、可更新或对等复制。

## <a name="replication-architecture"></a>复制体系结构  

![replication-to-sql-database](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>方案  

### <a name="typical-replication-scenario"></a>典型复制方案  

1. 在本地 SQL Server 数据库上创建事务复制发布。  
2. 在本地 SQL Server 上使用**新建订阅向导**或 Transact-SQL 语句创建一个推送，以便推送到 Azure SQL 数据库的订阅。  
3. 对于 Azure SQL 数据库中的单一数据库和共用数据库，初始数据集是由快照代理创建并由分发代理分发和应用的快照。 使用托管实例数据库，还可以使用数据库备份来植入订阅服务器数据库。

### <a name="data-migration-scenario"></a>数据迁移方案  

1. 使用事务复制将数据从本地 SQL Server 数据库复制到 Azure SQL 数据库。  
2. 重定向客户端或中间层应用程序，以便更新 Azure SQL 数据库副本。  
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
- 复制权限  

### <a name="limitations-to-be-determined"></a>限制待定

- 复制排序规则  
- 在 SP 的序列化事务中执行  

## <a name="examples"></a>示例

创建发布和推送订阅。 有关详细信息，请参阅：
  
- [创建发布](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [创建推送订阅](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)，方法是将 Azure SQL 数据库服务器名称用作订阅服务器（例如 N'azuresqldbdns.database.windows.net'），将 Azure SQL 数据库名称用作目标数据库（例如 AdventureWorks）。  

## <a name="see-also"></a>另请参阅  

- [事务复制](sql-database-managed-instance-transactional-replication.md)
- [创建发布](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [创建推送订阅](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [复制类型](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [监视（复制）](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [初始化订阅](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
