---
title: 在 Azure SQL 数据库托管的实例数据库中配置复制 |Microsoft Docs
description: 了解如何在 Azure SQL 数据库托管实例数据库中配置事务复制
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: c72c4d21f948d6d6c4d1d4598efa0e13de9705a6
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926200"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>在 Azure SQL 数据库托管实例数据库中配置复制

使用事务复制，可将数据从 SQL Server 数据库或其他实例数据库复制到 Azure SQL 数据库托管实例数据库中。 

事务复制还可用于将推送到 Azure SQL 数据库托管实例中实例数据库中所做的更改：

- SQL Server 数据库。
- Azure SQL 数据库中的单个数据库。
- 入池的 Azure SQL 数据库弹性池中数据库。
 
事务复制处于公共预览状态上[Azure SQL 数据库托管实例](sql-database-managed-instance.md)。 托管实例可以托管发布服务器、分发服务器和订阅服务器数据库。 有关可用配置，请参阅[事务复制配置](sql-database-managed-instance-transactional-replication.md#common-configurations)。

  > [!NOTE]
  > 本文旨在指导中的 Azure 数据库配置复制的用户托管实例端到端，从开始创建资源组。 如果已有管理部署的实例，跳到[步骤 4](#4---create-a-publisher-database)创建发布服务器数据库，或[步骤 6](#6---configure-distribution)如果你已有一个发布服务器和订阅服务器上的数据库，并准备好开始配置复制。  

## <a name="requirements"></a>要求

配置为充当发布服务器和/或分发服务器的托管的实例需要：

- 该托管实例当前未加入异地复制关系。
- 发布服务器上托管实例位于同一虚拟网络作为分发服务器和订阅服务器，或[vNet 对等互连](../virtual-network/tutorial-connect-virtual-networks-powershell.md)所有三个实体的虚拟网络之间建立。 
- 连接时，在复制参与者之间使用 SQL 身份验证。
- 适用于复制工作目录的 Azure 存储帐户共享。
- 在 NSG 的托管实例，用于访问 Azure 文件共享的安全规则中打开端口 445(tcp 出站)。 


 > [!NOTE]
 > Azure SQL 数据库中的单一数据库和入池数据库只能用作订阅服务器。 


## <a name="features"></a>功能

支持：

- 对本地实例和 Azure SQL 数据库中的托管实例混合使用事务复制和快照复制。
- 订阅服务器可以位于本地 SQL Server 数据库中、Azure SQL 数据库中的单一数据库/托管实例中，也可以位于 Azure SQL 数据库弹性池中的入池数据库中。
- 单向或双向复制。

Azure SQL 数据库中的托管实例不支持以下功能：

- [可更新订阅](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication)。
- 如果配置了事务复制，则不应使用[活动异地复制](sql-database-active-geo-replication.md)和[自动故障转移组](sql-database-auto-failover-group.md)。
 
## <a name="1---create-a-resource-group"></a>1-创建资源组

使用[Azure 门户](https://portal.azure.com)若要创建的资源组名称`SQLMI-Repl`。  

## <a name="2---create-managed-instances"></a>2-创建托管的实例

使用[Azure 门户](https://portal.azure.com)若要创建两个[托管实例](sql-database-managed-instance-create-tutorial-portal.md)上的同一虚拟网络和子网。 应命名为两个托管的实例：

- `sql-mi-pub`
- `sql-mi-sub`

您还需要[配置 Azure VM 连接](sql-database-managed-instance-configure-vm.md)到 Azure SQL 数据库托管实例。 

## <a name="3---create-azure-storage-account"></a>3-创建 Azure 存储帐户

[创建 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account)为工作目录，然后创建[文件共享](../storage/files/storage-how-to-create-file-share.md)存储帐户中。 

中的格式复制文件共享路径： `\\storage-account-name.file.core.windows.net\file-share-name`

格式中的复制存储访问密钥： `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

 有关详细信息，请参阅 [查看和复制存储访问密钥](../storage/common/storage-account-manage.md#access-keys)。 

## <a name="4---create-a-publisher-database"></a>4-创建发布服务器数据库

连接到你`sql-mi-pub`托管实例使用 SQL Server Management Studio 并运行下面的 TRANSACT-SQL (T-SQL) 代码，以创建你的发布服务器数据库：

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5-创建订阅服务器数据库

连接到你`sql-mi-sub`托管实例使用 SQL Server Management Studio 并运行以下 T-SQL 代码以创建空的订阅服务器数据库：

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6-配置分发

连接到你`sql-mi-pub`托管实例使用 SQL Server Management Studio 并运行下面的 T-SQL 代码，以配置您的分发数据库。 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7-配置发布服务器使用分发服务器 

发布服务器上托管实例`sql-mi-pub`，更改到查询执行[SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor)模式并运行以下代码以将注册你的发布服务器的新的分发服务器。 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"


USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)';
```

此脚本将本地发布服务器配置的托管实例上添加链接的服务器，并为 SQL Server 代理创建一组的作业。 

## <a name="8---create-publication-and-subscriber"></a>8-创建发布和订阅服务器

使用[SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor)模式下，运行以下 T-SQL 脚本以启用复制数据库，并配置发布服务器、 分发服务器和订阅服务器之间的复制。 

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reaer agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle 
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(target_username)',
  @job_password = N'$(target_password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9-修改代理参数

Azure SQL 数据库托管的实例具有与复制代理连接当前遇到的一些后端问题。 此问题时正在解决问题得到解决，若要增加复制代理的登录超时值的解决方法。 

若要提高登录超时值在发布服务器上运行以下 T-SQL 命令： 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

运行下面的 T-SQL 命令，再次以将登录超时值设置回默认值，应需要执行此操作：

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

重新启动所有三个代理来应用这些更改。 

## <a name="10---test-replication"></a>10-测试复制

一旦配置了复制，可以通过插入新项在发布服务器上的和观看所做的更改传播到订阅服务器上对它进行测试。 

运行以下 T-SQL 代码片段，查看在订阅服务器上的行：

```sql
select * from dbo.ReplTest
```

运行以下 T-SQL 代码片段插入在发布服务器上的附加行，然后检查再次在订阅服务器上的行。 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>清理资源

若要删除发布，请运行以下 T-SQL 命令：

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

若要从数据库删除复制选项，请运行以下 T-SQL 命令：

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

若要禁用发布和分发，请运行以下 T-SQL 命令：

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

你可以清理由 Azure 资源[从资源组中删除托管的实例资源](../azure-resource-manager/manage-resources-portal.md#delete-resources)，然后在删除资源组`SQLMI-Repl`。 

   
## <a name="see-also"></a>另请参阅

- [事务复制](sql-database-managed-instance-transactional-replication.md)
- [什么是托管实例？](sql-database-managed-instance.md)
