---
title: 在托管实例数据库中配置复制
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
ms.date: 02/07/2019
ms.openlocfilehash: f303a363fd4d42889e7817273be5d5e5440a2293
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822589"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>在 Azure SQL 数据库托管实例数据库中配置复制

使用事务复制，可将数据从 SQL Server 数据库或其他实例数据库复制到 Azure SQL 数据库托管实例数据库中。 

还可以使用事务复制将 Azure SQL 数据库托管实例中实例数据库发生的更改推送到：

- SQL Server 数据库。
- Azure SQL 数据库中的单一数据库。
- Azure SQL 数据库弹性池中的共用数据库。
 
事务复制在 [Azure SQL 数据库托管实例](sql-database-managed-instance.md)上以公共预览版提供。 托管实例可以托管发布服务器、分发服务器和订阅服务器数据库。 有关可用配置，请参阅[事务复制配置](sql-database-managed-instance-transactional-replication.md#common-configurations)。

  > [!NOTE]
  > 本文旨在从头到尾地引导用户配置 Azure 数据库托管实例的复制，并从创建资源组开始讲起。 如果已部署托管实例，请跳到[步骤 4](#4---create-a-publisher-database) 以创建发布服务器数据库；如果你已有一个发布服务器数据库和订阅服务器数据库，并已准备好开始配置复制，请跳到[步骤 6](#6---configure-distribution)。  

## <a name="requirements"></a>要求

配置充当发布服务器和/或分发服务器的托管实例需要满足以下要求：

- 该托管实例当前未加入异地复制关系。
- 发布服务器托管实例位于分发服务器和订阅服务器所在的同一个虚拟网络中，或者已在所有三个实体的虚拟网络之间建立 [vNet 对等互连](../virtual-network/tutorial-connect-virtual-networks-powershell.md)。 
- 连接时，在复制参与者之间使用 SQL 身份验证。
- 适用于复制工作目录的 Azure 存储帐户共享。
- 需要在托管实例的 NSG 安全规则中打开端口 445（TCP 出站）才能访问 Azure 文件共享。  如果遇到错误 "无法连接到 azure 存储 \<的存储帐户名称 > 操作系统错误 53"，则需要向相应 SQL 托管实例子网的 NSG 添加出站规则。


 > [!NOTE]
 > Azure SQL 数据库中的单一数据库和共用数据库只能用作订阅服务器。 


## <a name="features"></a>功能

支持：

- 对本地实例和 Azure SQL 数据库中的托管实例混合使用事务复制和快照复制。
- 订阅服务器可以位于本地 SQL Server 数据库中、Azure SQL 数据库中的单一数据库/托管实例中，也可以位于 Azure SQL 数据库弹性池中的入池数据库中。
- 单向或双向复制。

Azure SQL 数据库中的托管实例不支持以下功能：

- [可更新订阅](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication)。
- 带事务复制的[活动异地复制](sql-database-active-geo-replication.md)。 使用[自动故障转移组](sql-database-auto-failover-group.md)而不是活动异地复制，但请注意，必须从主托管实例中[手动删除](sql-database-managed-instance-transact-sql-information.md#replication)发布，并在故障转移后在辅助托管实例上重新创建。  
 
## <a name="1---create-a-resource-group"></a>1 - 创建资源组

使用 [Azure 门户](https://portal.azure.com)创建名为 `SQLMI-Repl` 的资源组。  

## <a name="2---create-managed-instances"></a>2 - 创建托管实例

使用 [Azure 门户](https://portal.azure.com)在同一个虚拟网络和子网中创建两个[托管实例](sql-database-managed-instance-create-tutorial-portal.md)。 这两个托管实例应命名为：

- `sql-mi-pub`
- `sql-mi-sub`

还需要[将 Azure VM 配置为连接](sql-database-managed-instance-configure-vm.md)到 Azure SQL 数据库托管实例。 

## <a name="3---create-azure-storage-account"></a>3 - 创建 Azure 存储帐户

为工作目录[创建一个 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account)，然后在该存储帐户中创建一个[文件共享](../storage/files/storage-how-to-create-file-share.md)。 

复制采用 `\\storage-account-name.file.core.windows.net\file-share-name` 格式的文件共享路径

复制采用 `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net` 格式的存储访问密钥

 有关详细信息，请参阅 [查看和复制存储访问密钥](../storage/common/storage-account-manage.md#access-keys)。 

## <a name="4---create-a-publisher-database"></a>4 - 创建发布服务器数据库

使用 SQL Server Management Studio 连接到 `sql-mi-pub` 托管实例，然后运行以下 Transact-SQL (T-SQL) 代码来创建发布服务器数据库：

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

## <a name="5---create-a-subscriber-database"></a>5 - 创建订阅服务器数据库

使用 SQL Server Management Studio 连接到 `sql-mi-sub` 托管实例，然后运行以下 T-SQL 代码来创建空的订阅服务器数据库：

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

## <a name="6---configure-distribution"></a>6 - 配置分发

使用 SQL Server Management Studio 连接到 `sql-mi-pub` 托管实例，然后运行以下 T-SQL 代码来配置分发数据库。 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - 将发布服务器配置为使用分发服务器 

在发布服务器托管实例 `sql-mi-pub` 上，将查询执行更改为 [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) 模式，然后运行以下代码将新的分发服务器注册到发布服务器。 

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
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

此脚本将在托管实例上配置一个本地发布服务器，添加链接服务器，并为 SQL Server 代理创建一组作业。 

## <a name="8---create-publication-and-subscriber"></a>8 - 创建发布和订阅服务器

使用 [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) 模式运行以下 T-SQL 脚本以便为数据库启用复制，然后在发布服务器、分发服务器和订阅服务器之间配置复制。 

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

## <a name="9---modify-agent-parameters"></a>9 - 修改代理参数

目前，Azure SQL 数据库托管实例在与复制代理连接时会遇到一些后端问题。 我们正在解决此问题，暂时的解决方法是增大复制代理的登录超时值。 

在发布服务器上运行以下 T-SQL 命令以增大登录超时值： 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

如果需要，可再次运行以下 T-SQL 命令，将登录超时值重置为默认值：

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

重启所有三个代理以应用这些更改。 

## <a name="10---test-replication"></a>10 - 测试复制

配置复制后，可以通过以下方式测试复制：在发布服务器上插入新项，然后观察更改是否会传播到订阅服务器。 

运行以下 T-SQL 代码片段以查看订阅服务器上的行：

```sql
select * from dbo.ReplTest
```

运行以下 T-SQL 代码片段以在发布服务器上插入附加的行，然后再次在订阅服务器上检查这些行。 

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

若要从数据库中删除复制选项，请运行以下 T-SQL 命令：

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

可以通过[从资源组中删除托管实例资源](../azure-resource-manager/manage-resources-portal.md#delete-resources)，然后删除资源组 `SQLMI-Repl`，来清理 Azure 资源。 

   
## <a name="see-also"></a>另请参阅

- [事务复制](sql-database-managed-instance-transactional-replication.md)
- [什么是托管实例？](sql-database-managed-instance.md)
