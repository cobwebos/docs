---
title: 配置托管实例之间的复制
titleSuffix: Azure SQL Managed Instance
description: 本教程介绍如何在 Azure SQL 托管实例发布服务器/分发服务器与 SQL 托管实例订阅服务器之间配置事务复制。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: ac701b70a9db860e2f839ab30fb575133703c142
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708469"
---
# <a name="tutorial-configure-replication-between-two-managed-instances"></a>教程：在两个托管实例之间配置复制

[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

通过事务复制，可以将数据从一个数据库复制到 SQL Server 或[AZURE SQL 托管实例](sql-managed-instance-paas-overview.md)上托管的另一个数据库（公共预览版）。 SQL 托管实例可以是复制拓扑中的发布服务器、分发服务器或订阅服务器。 有关可用配置，请参阅[事务复制配置](replication-transactional-overview.md#common-configurations)。

> [!NOTE]
> 本文介绍了如何在 Azure SQL 托管实例中使用[事务复制](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication)。 它与[故障转移组](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group)无关，后者是一项 Azure SQL 托管实例功能，可用于创建单个实例的完整可读副本。

本教程将指导你将一个托管实例配置为发布服务器和分发服务器，然后将另一个托管实例配置为订阅服务器。  

![在两个托管实例之间复制](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

  > [!NOTE]
  > - 本文旨在指导高级用户从端到端配置与 SQL 托管实例的复制，开始创建资源组。 如果已部署了托管实例，请跳到[步骤 4](#4---create-a-publisher-database)以创建发布服务器数据库[，或者，](#6---configure-distribution)如果已有发布服务器和订阅服务器数据库，并且已准备好开始配置复制，请跳到步骤4。  
  > - 本文在相同的托管实例上配置发布服务器和分发服务器。 若要将分发服务器置于单独的托管实例上，请参阅教程在[AZURE SQL 托管实例和 SQL Server 之间配置事务复制](replication-two-instances-and-sql-server-configure-tutorial.md)。 

## <a name="requirements"></a>要求

将 SQL 托管实例配置为充当发布服务器和/或分发服务器的功能需要：

- 发布服务器托管实例与分发服务器和订阅服务器位于同一虚拟网络中，或者在所有三个实体的虚拟网络之间配置了[虚拟网络对等互连](../../virtual-network/tutorial-connect-virtual-networks-powershell.md)。 
- 连接时，在复制参与者之间使用 SQL 身份验证。
- 复制工作目录的 Azure 存储帐户共享。
- 需要在托管实例的 NSG 安全规则中打开端口 445（TCP 出站）才能访问 Azure 文件共享。  如果遇到错误 `failed to connect to azure storage \<storage account name> with os error 53` ，你将需要向相应 SQL 托管实例子网的 NSG 添加出站规则。

## <a name="1---create-a-resource-group"></a>1 - 创建资源组

使用 [Azure 门户](https://portal.azure.com)创建名为 `SQLMI-Repl` 的资源组。  

## <a name="2---create-managed-instances"></a>2 - 创建托管实例

使用[Azure 门户](https://portal.azure.com)在同一个虚拟网络和子网中创建两个[SQL 托管实例](instance-create-quickstart.md)。 例如，将两个托管实例命名为：

- `sql-mi-pub`（以及一些用于随机化的字符）
- `sql-mi-sub`（以及一些用于随机化的字符）

还需要[配置 AZURE VM 以连接](connect-vm-instance-configure.md)到托管实例。 

## <a name="3---create-an-azure-storage-account"></a>3-创建 Azure 存储帐户

为工作目录[创建 Azure 存储帐户](/azure/storage/common/storage-create-storage-account#create-a-storage-account)，并在存储帐户中创建[文件共享](../../storage/files/storage-how-to-create-file-share.md)。 

按以下格式复制文件共享路径：`\\storage-account-name.file.core.windows.net\file-share-name`

示例： `\\replstorage.file.core.windows.net\replshare`

复制采用 `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net` 格式的存储访问密钥

示例： `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

有关详细信息，请参阅[管理存储帐户访问密钥](../../storage/common/storage-account-keys-manage.md)。 

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

在发布服务器 SQL 托管实例上 `sql-mi-pub` ，将查询执行更改为[SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor)模式，并运行以下代码以向发布服务器注册新的分发服务器。

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
   > 请确保对 file_storage 参数仅使用反斜杠 (`\`)。 在连接到文件共享时使用正斜杠（`/`）可能会导致错误。

此脚本在托管实例上配置本地发布服务器，添加链接服务器，并为 SQL Server 代理创建一组作业。

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


-- Configure your log reader agent
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

## <a name="9---modify-agent-parameters"></a>9 - 修改代理参数

Azure SQL 托管实例当前在与复制代理连接时遇到一些后端问题。 虽然正在解决此问题，但解决方法是增加复制代理的登录超时值。

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

可以通过[从资源组中删除 SQL 托管实例资源](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources)，然后删除资源组来清理 Azure 资源 `SQLMI-Repl` 。 

## <a name="next-steps"></a>后续步骤

你还可以[通过 AZURE sql 托管实例了解有关事务复制的](replication-transactional-overview.md)详细信息，或了解如何[在 sql 托管实例发布服务器/分发服务器与 azure VM 订阅服务器上的 sql](replication-two-instances-and-sql-server-configure-tutorial.md)之间配置复制。 
