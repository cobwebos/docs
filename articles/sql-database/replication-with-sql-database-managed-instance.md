---
title: 在托管实例数据库中配置复制
description: 了解如何在 Azure SQL 数据库托管实例发布服务器/分发服务器和托管实例订阅服务器之间配置事务复制。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 02/07/2019
ms.openlocfilehash: 9af7b471210ca3cc69428e68aef4aafaee159344
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299067"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>在 Azure SQL 数据库托管实例数据库中配置复制

使用事务复制，可将数据从 SQL Server 数据库或其他实例数据库复制到 Azure SQL 数据库托管实例数据库中。 

本文介绍如何在托管实例发布服务器/分发服务器和托管实例订阅服务器之间配置复制。 

![在两个托管实例之间复制](media/replication-with-sql-database-managed-instance/sqlmi-sqlmi-repl.png)

你还可以使用事务复制将 Azure SQL 数据库托管实例中的实例数据库中所做的更改推送到：

- SQL Server 的数据库。
- Azure SQL 数据库中的单个数据库。
- Azure SQL 数据库弹性池中的池数据库。
 
在[AZURE SQL 数据库托管实例](sql-database-managed-instance.md)上，事务复制是公共预览。 托管实例可以托管发布服务器、分发服务器和订阅服务器数据库。 有关可用配置，请参阅[事务复制配置](sql-database-managed-instance-transactional-replication.md#common-configurations)。

  > [!NOTE]
  > - 本文旨在指导用户在配置复制时，从端到端配置 Azure 数据库托管实例，从创建资源组开始。 如果已部署了托管实例，请跳到[步骤 4](#4---create-a-publisher-database)以创建发布服务器数据库[，或者，](#6---configure-distribution)如果已有发布服务器和订阅服务器数据库，并且已准备好开始配置复制，请跳到步骤4。  
  > - 本文在相同的托管实例上配置发布服务器和分发服务器。 若要将分发服务器置于单独的托管实例上，请参阅教程在[mi 发布服务器和 mi 分发服务器之间配置复制](sql-database-managed-instance-configure-replication-tutorial.md)。 

## <a name="requirements"></a>要求

将托管实例配置为充当发布服务器和/或分发服务器的功能需要：

- 发布服务器托管实例与分发服务器和订阅服务器位于同一虚拟网络上，或已在所有三个实体的虚拟网络之间建立[vNet 对等互连](../virtual-network/tutorial-connect-virtual-networks-powershell.md)。 
- 连接时，在复制参与者之间使用 SQL 身份验证。
- 适用于复制工作目录的 Azure 存储帐户共享。
- 在 NSG 的安全规则中打开端口445（TCP 出站）以访问 Azure 文件共享。  如果遇到错误 "无法连接到 azure 存储 \<的存储帐户名称 > 操作系统错误 53"，则需要向相应 SQL 托管实例子网的 NSG 添加出站规则。


 > [!NOTE]
 > Azure SQL 数据库中的单一数据库和共用数据库只能用作订阅服务器。 


## <a name="features"></a>功能

支持：

- 对本地实例和 Azure SQL 数据库中的托管实例混合使用事务复制和快照复制。
- 订阅服务器可以在本地 SQL Server 数据库、Azure SQL 数据库中的单个数据库/托管实例或 Azure SQL 数据库弹性池中的入池数据库。
- 单向或双向复制。

Azure SQL 数据库中的托管实例不支持以下功能：

- [可更新订阅](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication)。
- 具有事务复制的[活动异地复制](sql-database-active-geo-replication.md)。 请不要使用活动异地复制，而应使用[自动故障转移组](sql-database-auto-failover-group.md)，但请注意，必须从主托管实例中[手动删除](sql-database-managed-instance-transact-sql-information.md#replication)发布，并在故障转移后在辅助托管实例上重新创建发布。  
 
## <a name="1---create-a-resource-group"></a>1-创建资源组

使用[Azure 门户](https://portal.azure.com)创建名称 `SQLMI-Repl`的资源组。  

## <a name="2---create-managed-instances"></a>2-创建托管实例

使用[Azure 门户](https://portal.azure.com)在同一个虚拟网络和子网中创建两个[托管实例](sql-database-managed-instance-create-tutorial-portal.md)。 例如，将两个托管实例命名为：

- `sql-mi-pub` （以及用于随机化的一些字符）
- `sql-mi-sub` （以及用于随机化的一些字符）

还需要将[AZURE VM 配置为连接](sql-database-managed-instance-configure-vm.md)到 Azure SQL 数据库托管实例。 

## <a name="3---create-azure-storage-account"></a>3-创建 Azure 存储帐户

为工作目录[创建 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account)，并在存储帐户中创建[文件共享](../storage/files/storage-how-to-create-file-share.md)。 

按以下格式复制文件共享路径：`\\storage-account-name.file.core.windows.net\file-share-name`

示例： `\\replstorage.file.core.windows.net\replshare`

按以下格式复制存储访问密钥： `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

示例： `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

有关详细信息，请参阅[管理存储帐户访问密钥](../storage/common/storage-account-keys-manage.md)。 

## <a name="4---create-a-publisher-database"></a>4-创建发布服务器数据库

使用 SQL Server Management Studio 连接到 `sql-mi-pub` 托管实例，并运行以下 Transact-sql （T-sql）代码以创建发布服务器数据库：

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

使用 SQL Server Management Studio 连接到 `sql-mi-sub` 托管实例，并运行以下 T-sql 代码以创建您的空订阅服务器数据库：

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

使用 SQL Server Management Studio 连接到 `sql-mi-pub` 托管实例，并运行以下 T-sql 代码来配置分发数据库。 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7-配置发布服务器以使用分发服务器 

在发布服务器托管实例 `sql-mi-pub`上，将查询执行更改为[SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor)模式，并运行以下代码以向发布服务器注册新的分发服务器。 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

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

   > [!NOTE]
   > 请确保 file_storage 参数只使用反斜杠（`\`）。 在连接到文件共享时使用正斜杠（`/`）可能会导致错误。 

此脚本在托管实例上配置本地发布服务器，添加链接服务器，并为 SQL Server 代理创建一组作业。 

## <a name="8---create-publication-and-subscriber"></a>8-创建发布和订阅服务器

使用[SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor)模式，运行以下 t-sql 脚本，为数据库启用复制，并在发布服务器、分发服务器和订阅服务器之间配置复制。 

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
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9-修改代理参数

Azure SQL 数据库托管实例当前在与复制代理连接时遇到一些后端问题。 尽管此问题已解决，但解决该问题的方法是增加复制代理的登录超时值。 

在发布服务器上运行以下 T-sql 命令以增加登录超时值： 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

如果需要这样做，请再次运行以下 T-sql 命令，将登录超时设置回默认值：

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

重新启动所有三个代理以应用这些更改。 

## <a name="10---test-replication"></a>10-测试复制

配置复制后，可对其进行测试，方法是：在发布服务器上插入新项并监视更改传播到订阅服务器。 

运行以下 T-SQL 代码片段可查看订阅服务器上的行：

```sql
select * from dbo.ReplTest
```

运行以下 T-SQL 代码片段可在发布服务器上插入更多行，然后再次检查订阅服务器上的行。 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>清理资源

若要删除发布，请运行以下 T-sql 命令：

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

若要从数据库中删除复制选项，请运行以下 T-sql 命令：

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

若要禁用发布和分发，请运行以下 T-sql 命令：

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

可以通过[删除资源组中的托管实例资源](../azure-resource-manager/management/manage-resources-portal.md#delete-resources)，然后删除资源组 `SQLMI-Repl`来清理 Azure 资源。 

   
## <a name="see-also"></a>另请参阅

- [事务复制](sql-database-managed-instance-transactional-replication.md)
- [教程：在 MI 发行者与 SQL Server 订阅服务器之间配置事务复制](sql-database-managed-instance-configure-replication-tutorial.md)
- [什么是托管实例？](sql-database-managed-instance.md)
