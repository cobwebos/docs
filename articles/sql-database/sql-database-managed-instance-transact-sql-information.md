---
title: "Azure SQL 数据库托管实例的 T-SQL 差异 | Microsoft Docs"
description: "本文讨论 Azure SQL 数据库托管实例与 SQL Server 之间的 T-SQL 差异。"
services: sql-database
author: jovanpop-msft
ms.reviewer: carlrab, bonova
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 03/09/2018
ms.author: jovanpop
manager: cguyer
ms.openlocfilehash: 27be2b9a5f2b9aaf2d4464a6c927ec2a1694131a
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Azure SQL 数据库托管实例与 SQL Server 之间的 T-SQL 差异 

Azure SQL 数据库托管实例（预览版）与本地 SQL Server 数据库引擎高度兼容。 托管实例支持大多数 SQL Server 数据库引擎功能。 由于两者的语法和行为仍有一些差异，本文汇总并解释了这些差异。
 - [T-SQL 差异和不支持的功能](#Differences)
 - [在托管实例中具有不同行为的功能](#Changes)
 - [暂时的限制和已知问题](#Issues)

## <a name="Differences"></a>与SQL Server 之间的 T-SQL 差异 

本部分汇总了托管实例与本地 SQL Server 数据库引擎之间的主要 T-SQL 语法和行为差异，以及不支持的功能。

### <a name="always-on-availability"></a>Always-On 可用性

[高可用性](sql-database-high-availability.md)内置在托管实例中，不能由用户控制。 不支持以下语句：
 - [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql.md)
 - [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql.md)
 - [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql.md)
 - [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql.md)
 - [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) 语句的 [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr.md) 子句

### <a name="auditing"></a>审核 
 
托管实例、Azure SQL 数据库与本地 SQL Server 之间的主要 SQL 审核差异为：
- 在托管实例中，SQL 审核在服务器级别工作，在 Azure Blob 存储帐户中存储 `.xel` 文件。  
- 在 Azure SQL 数据库中，SQL 审核在数据库级别工作。
- 在本地 SQL Server/虚拟机中，SQL 审核在服务器级别工作，但在文件系统/Windows 事件日志中存储事件。  
  
托管实例中的 XEvent 审核支持 Azure Blob 存储目标。 不支持文件和 Windows 日志。    
 
Azure Blob 存储审核的主要 `CREATE AUDIT` 语法差异为：
- 提供新语法 `TO URL`，允许指定用于放置 `.xel` 文件的 Azure Blob 存储容器的 URL 
- 不支持语法 `TO FILE`，因为托管实例无法访问 Windows 文件共享。 
 
有关详细信息，请参阅：  
- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql) 
- [审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)     

### <a name="backup"></a>备份 

托管实例提供自动备份，允许用户创建完整的数据库 `COPY_ONLY` 备份。 不支持差异、日志和文件快照备份。  
- 托管实例只能将某个数据库备份到 Azure Blob 存储帐户： 
 - 仅支持 `BACKUP TO URL` 
 - 不支持 `FILE`、`TAPE` 和备份设备  
- 支持大多数常规 `WITH` 选项 
 - `COPY_ONLY` 是必需的
 - 不支持 `FILE_SNAPSHOT`  
 - 不支持磁带选项 `REWIND`、`NOREWIND`、`UNLOAD` 和 `NOUNLOAD` 
 - 不支持日志特定的选项 `NORECOVERY`、`STANDBY` 和 `NO_TRUNCATE` 
 
限制：  
- 托管实例可将数据库备份到最多包含 32 个条带的备份，如果使用备份压缩，则这种方法对于不超过 4 TB 的数据库而言已足够。
- 最大备份条带大小为 195 GB（最大 Blob 大小）。 在 backup 命令中增加条带数目可以减小单个条带的大小，并保持在此限制范围内。 

> [!TIP]
> 若要在本地解决此限制，请备份到 `DISK` 而不是 `URL`，将备份文件上传到 Blob，然后还原。 由于使用了不同的 Blob 类型，因此还原操作支持较大的文件。  

有关使用 T-SQL 进行备份的信息，请参阅 [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql)。

### <a name="buffer-pool-extension"></a>缓冲池扩展 
 
- 不支持[缓冲池扩展](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension)。
- 不支持 `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`。 请参阅 [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql)。 
 
### <a name="bulk-insert--openrowset"></a>批量插入/openrowset

托管实例无法访问文件共享和 Windows 文件夹，因此必须从 Azure Blob 存储导入文件。
- 从 Azure Blob 存储导入文件时，必须在 `BULK INSERT` 命令中指定 `DATASOURCE`。 请参阅 [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql)。
- 从 Azure Blob 存储中读取文件内容时，必须在 `OPENROWSET` 函数中指定 `DATASOURCE`。 请参阅 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)。
 
### <a name="certificates"></a>证书 

托管实例无法访问文件共享和 Windows 文件夹，因此存在以下约束： 
- 不支持将 `CREATE FROM`/`BACKUP TO` 文件用于证书
- 不支持 `FILE`/`ASSEMBLY` 中的 `CREATE`/`BACKUP` 证书。 无法使用私钥文件。  
 
请参阅 [CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) 和 [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql)。  
  
> [!TIP]
> 解决方法：编写证书/私钥的脚本，存储为 .sql 文件，然后从二进制文件创建证书： 
> 
> ``` 
CREATE CERTIFICATE  
 FROM BINARY = asn_encoded_certificate    
WITH PRIVATE KEY ( <private_key_options> ) 
>```   
 
### <a name="clr"></a>CLR 

托管实例无法访问文件共享和 Windows 文件夹，因此存在以下约束： 
- 仅支持 `CREATE ASSEMBLY FROM BINARY`。 请参阅 [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)。  
- 不支持 `CREATE ASSEMBLY FROM FILE`。 请参阅 [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)。
- `ALTER ASSEMBLY` 不能引用文件。 请参阅 [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql)。
 
### <a name="compatibility-levels"></a>兼容级别 
 
- 支持的兼容级别：100、110、120、130、140  
- 不支持低于 100 的兼容级别。 
- 新数据库的默认兼容级别为 140。 对于已还原的数据库，如果其兼容级别在还原之前为 100 或更高，则还原后保持不变。

请参阅 [ALTER DATABASE 兼容级别](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。
 
### <a name="credential"></a>凭据 
 
仅支持 Azure Key Vault 和 `SHARED ACCESS SIGNATURE` 标识。 不支持 Windows 用户。
 
请参阅 [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) 和 [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql)。 
 
### <a name="cryptographic-providers"></a>加密提供程序

托管实例无法访问文件，因此无法创建加密提供程序：
- 不支持 `CREATE CRYPTOGRAPHIC PROVIDER`。 请参阅 [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql)。
- 不支持 `ALTER CRYPTOGRAPHIC PROVIDER`。 请参阅 [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)。 

### <a name="collation"></a>Collation 
 
服务器排序规则为 `SQL_Latin1_General_CP1_CI_AS`，不可更改。 请参阅[排序规则](https://docs.microsoft.com/sql/t-sql/statements/collations)。
 
### <a name="database-options"></a>数据库选项 
 
- 不支持多个日志文件。 
- 常规用途服务层不支持内存中对象。  
- 每个实例限制为 280 个文件，这意味着，每个数据库最多只能有 280 个文件。 数据文件和日志文件都会计入此限制。  
- 数据库中不能有包含文件流数据的文件组。  如果 .bak 包含 `FILESTREAM` 数据，则还原将会失败。  
- 每个文件被放置在 Azure 高级存储中。 与 Azure 高级存储磁盘中的文件一样，每个文件的 IO 和吞吐量取决于每个文件的大小。 请参阅 [Azure 高级磁盘性能](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)  
 
#### <a name="create-database-statement"></a>CREATE DATABASE 语句

`CREATE DATABASE` 有以下限制： 
- 无法定义文件和文件组。  
- 不支持 `CONTAINMENT` 选项。  
- 不支持 `WITH` 选项。  
   > [!TIP]
   > 解决方法是在 `CREATE DATABASE` 后面使用 `ALTER DATABASE` 来设置数据库选项，以添加文件或设置包含。  

- 不支持 `FOR ATTACH` 选项 
- 不支持 `AS SNAPSHOT OF` 选项 

有关详细信息，请参阅 [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql)。

#### <a name="alter-database-statement"></a>ALTER DATABASE 语句

无法设置或更改某些文件属性：
- 无法在 `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL 语句中指定文件路径。 由于托管实例会自动放置文件，因此需从脚本中删除 `FILENAME`。  
- 无法使用 `ALTER DATABASE` 语句更改文件名。

默认会设置以下选项，无法更改这些选项： 
- `MULTI_USER` 
- `ENABLE_BROKER ON` 
- `AUTO_CLOSE OFF` 

无法修改以下选项： 
- `AUTO_CLOSE` 
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)` 
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)` 
- `DISABLE_BROKER` 
- `EMERGENCY` 
- `ENABLE_BROKER` 
- `FILESTREAM` 
- `HADR`   
- `NEW_BROKER` 
- `OFFLINE` 
- `PAGE_VERIFY` 
- `PARTNER` 
- `READ_ONLY` 
- `RECOVERY BULK_LOGGED` 
- `RECOVERY_SIMPLE` 
- `REMOTE_DATA_ARCHIVE`  
- `RESTRICTED_USER` 
- `SINGLE_USER` 
- `WITNESS`

不支持修改名称。

有关详细信息，请参阅 [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options)。

### <a name="database-mirroring"></a>数据库镜像

不支持数据库镜像。
 - 不支持 `ALTER DATABASE SET PARTNER` 和 `SET WITNESS` 选项。
 - 不支持 `CREATE ENDPOINT … FOR DATABASE_MIRRORING`。

有关详细信息，请参阅 [ALTER DATABASE SET PARTNER 和 SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 以及 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)。

### <a name="dbcc"></a>DBCC 
 
托管实例不支持 SQL Server 中启用的、未阐述的 DBCC 语句。
- 不支持 `Trace Flags`。 请参阅[跟踪标志](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)。
- 不支持 `DBCC TRACEOFF`。 请参阅 [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql)。
- 不支持 `DBCC TRACEON`。 请参阅 [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql)。

### <a name="extended-events"></a>扩展事件 

不支持对 XEvents 使用某些特定于 Windows 的目标：
- 不支持 `etw_classic_sync target`。 在 Azure Blob 存储中存储 `.xel` 文件。 请参阅 [etw_classic_sync 目标](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etwclassicsynctarget-target)。 
- 不支持 `event_file target`。 在 Azure Blob 存储中存储 `.xel` 文件。 请参阅 [event_file 目标](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#eventfile-target)。

### <a name="external-libraries"></a>外部库

尚不支持数据库中 R 和 Python 外部库。 请参阅 [SQL Server 机器学习服务](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services)。

### <a name="filestream-and-filetable"></a>文件流和文件表

- 不支持文件流数据。 
- 数据库中不能有包含 `FILESTREAM` 数据的文件组
- 不支持 `FILETABLE`
- 表不能采用 `FILESTREAM` 类型
- 不支持以下函数：
 - `GetPathLocator()` 
 - `GET_FILESTREAM_TRANSACTION_CONTEXT()` 
 - `PathName()` 
 - `GetFileNamespacePath()` 
 - `FileTableRootPath()` 

有关详细信息，请参阅 [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) 和[文件表](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server)。

### <a name="full-text-semantic-search"></a>全文语义搜索

不支持[语义搜索](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server)。

### <a name="linked-servers"></a>链接的服务器
 
托管实例中的链接服务器支持有限数量的目标： 
- 支持的目标：SQL Server、SQL 数据库、托管实例，以及虚拟机上的 SQL Server。
- 不支持的目标：文件、Analysis Services 和其他 RDBMS。

操作

- 不支持跨实例写入事务。
- 支持使用 `sp_dropserver` 删除链接服务器。 请参阅 [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)。
- 使用 `OPENROWSET` 函数可以仅针对 SQL Server 实例（托管实例、本地实例或虚拟机中的实例）执行查询。 请参阅 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)。
- 使用 `OPENDATASOURCE` 函数可以仅针对 SQL Server 实例（托管实例、本地实例或虚拟机中的实例）执行查询。 仅支持将 `SQLNCLI`、`SQLNCLI11` 和 `SQLOLEDB` 值用作提供程序。 例如：`SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`。 请参阅 [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)。
 
### <a name="logins--users"></a>登录名/用户 

- 支持使用 `FROM CERTIFICATE`、`FROM ASYMMETRIC KEY` 和 `FROM SID` 创建的 SQL 登录名。 请参阅 [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql)。
- 不支持使用 `CREATE LOGIN ... FROM WINDOWS` 语法创建的 Windows 登录名。
- 创建实例的 Azure Active Directory (Azure AD) 用户具有[不受限制的管理特权](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#unrestricted-administrative-accounts)。
- 可以使用 `CREATE USER ... FROM EXTERNAL PROVIDER` 语法创建非管理员 Azure Active Directory (Azure AD) 数据库级用户。 请参阅 [CREATE USER ...FROM EXTERNAL PROVIDER](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#non-administrator-users)
 
### <a name="polybase"></a>Polybase

不支持外部表引用 HDFS 或 Azure Blob 存储中的文件。 有关 Polybase 的信息，请参阅 [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)。

### <a name="replication"></a>复制 
 
尚不支持复制。 有关复制的信息，请参阅 [SQL Server 复制](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication)。
 
### <a name="restore-statement"></a>RESTORE 语句 
 
- 支持的语法  
   - `RESTORE DATABASE` 
   - `RESTORE FILELISTONLY ONLY` 
   - `RESTORE HEADER ONLY` 
   - `RESTORE LABELONLY ONLY` 
   - `RESTORE VERIFYONLY ONLY` 
- 不支持的语法 
   - `RESTORE LOG ONLY` 
   - `RESTORE REWINDONLY ONLY`
- Source  
 - `FROM URL`（Azure Blob 存储）是唯一受支持的选项。
 - 不支持 `FROM DISK`/`TAPE`/备份设备。
 - 不支持备份集。 
- 不支持 `WITH` 选项（不允许 `DIFFERENTIAL`、`STATS` 等）。     
- `ASYNC RESTORE` - 即使客户端连接断开，还原也会继续。 如果删除了连接，可以在 `sys.dm_operation_status` 视图中检查还原操作的状态（以及 CREATE DATABASE 和 DROP DATABASE 的状态）。 请参阅 [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)。  
 
将设置/重写以下数据库选项，以后无法更改：  
- `NEW_BROKER`（如果未在 .bak 文件中启用代理）  
- `ENABLE_BROKER`（如果未在 .bak 文件中启用代理）  
- `AUTO_CLOSE=OFF`（如果 .bak 文件中的数据库采用 `AUTO_CLOSE=ON`）  
- `RECOVERY FULL`（如果 .bak 文件中的数据库采用 `SIMPLE` 或 `BULK_LOGGED` 恢复模式）
- 添加源 .bak 文件中不包含内存优化文件组，则会添加名为 XTP 的内存优化文件组  
- 任何现有的内存优化文件组将重命名为 XTP  
- `SINGLE_USER` 和 `RESTRICTED_USER` 选项将转换为 `MULTI_USER`   
限制：  
- 无法还原包含多个备份集的 `.BAK` 文件。 
- 无法还原包含多个日志文件的 `.BAK` 文件。 
- 如果 .bak 包含 `FILESTREAM` 数据，则还原将会失败。
- 目前无法还原包含数据库且这些数据库中具有活动内存中对象的备份。  
- 目前无法还原包含数据库且这些数据库的某个位置存在内存中对象的备份。   
- 目前无法还原包含采用只读模式的数据库的备份。 很快将会去除此限制。   
 
有关 Restore 语句的信息，请参阅 [RESTORE 语句](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)。

### <a name="service-broker"></a>服务代理 
 
- 不支持跨实例服务代理 
 - `sys.routes` - 先决条件：从 sys.routes 中选择地址。 地址必须在每个路由的本地。 请参阅 [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)。
 - `CREATE ROUTE` - 不能使用除 `LOCAL` 以外的 `ADDRESS` 执行 `CREATE ROUTE`。 请参阅 [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql)。
 - `ALTER ROUTE` - 不能使用除 `LOCAL` 以外的 `ADDRESS` 执行 `ALTER ROUTE`。 请参阅 [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql)。  
 
### <a name="service-key-and-service-master-key"></a>服务密钥和服务主密钥 
 
- 不支持[主密钥备份](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql)（由 SQL 数据库服务管理） 
- 不支持[主密钥还原](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql)（由 SQL 数据库服务管理） 
- 不支持[服务主密钥备份](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql)（由 SQL 数据库服务管理） 
- 不支持[服务主密钥还原](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql)（由 SQL 数据库服务管理） 
 
### <a name="stored-procedures-functions-triggers"></a>存储过程、函数和触发器 
 
- 目前不支持 `NATIVE_COMPILATION`。 
- 不支持以下 [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 选项： 
 - `allow polybase export` 
 - `allow updates` 
 - `filestream_access_level` 
 - `max text repl size` 
 - `remote data archive` 
 - `remote proc trans` 
- 不支持 `sp_execute_external_scripts`。 请参阅 [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)。
- 不支持 `xp_cmdshell`。 请参阅 [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)。
- 不支持 `Extended stored procedures`，包括 `sp_addextendedproc` 和 `sp_dropextendedproc`。 请参阅[扩展存储过程](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)
- 不支持 `sp_attach_db`、`sp_attach_single_file_db` 和 `sp_detach_db`。 请参阅 [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql)、[sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 和 [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)。
- 不支持 `sp_renamedb`。 请参阅 [sp_renamedb](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-renamedb-transact-sql)。

### <a name="sql-server-agent"></a>SQL Server 代理 
 
- SQL 代理设置为只读。 托管实例不支持过程 `sp_set_agent_properties`。  
- 作业 - 目前仅支持 T-SQL 作业步骤（在公共预览期将添加更多步骤）。
 - 尚不支持 SSIS。 
 - 尚不支持复制  
  - 尚不支持事务日志读取器。  
  - 尚不支持快照。  
  - 尚不支持分发服务器。  
  - 不支持合并。  
  - 不支持队列读取器。  
 - 尚不支持命令外壳。 
  - 托管实例无法访问外部资源（例如，通过 robocopy 访问网络共享）。  
 - 尚不支持 PowerShell。
 - 不支持 Analysis Services。  
- 部分支持通知。
 - 支持电子邮件通知，但需要配置数据库邮件配置文件。 公共预览版中只能有一个数据库邮件配置文件，并且该配置文件必须命名为 `AzureManagedInstance_dbmail_profile`（暂时性的限制）。  
 - 不支持寻呼机。  
 - 不支持 NetSend。 
 - 尚不支持警报。
 - 不支持代理。  
- 不支持事件日志。 
 
目前不支持以下功能，但以后会支持：  
- 代理
- 针对空闲 CPU 计划作业 
- 启用/禁用代理
- 警报

有关 SQL Server 代理的信息，请参阅 [SQL Server 代理](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)。
 
### <a name="tables"></a>表 

不支持以下各项： 
- `FILESTREAM` 
- `FILETABLE` 
- `EXTERNAL TABLE` 
- `MEMORY_OPTIMIZED`  

有关创建和更改表的信息，请参阅 [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) 和 [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)。
 
## <a name="Changes"></a>行为更改 
 
以下变量、函数和视图返回不同的结果：  
- `SERVERPROPERTY('EngineEdition')` 返回值 8。 此属性唯一标识托管实例。 请参阅 [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)。
- `SERVERPROPERTY('InstanceName')` 返回短实例名称，例如“myserver”。 请参阅 [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)。
- `@@SERVERNAME` 返回完整的 DNS“可连接”名称，例如 my-managed-instance.wcus17662feb9ce98.database.windows.net。 请参阅 [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql)。  
- `SYS.SERVERS` - 返回完整的 DNS“可连接”名称，例如，为属性“name”和“data_source”返回 `myinstance.domain.database.windows.net`。 请参阅 [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)。 
- `@@SERVERNAME` 返回完整的 DNS“可连接”名称，例如 `my-managed-instance.wcus17662feb9ce98.database.windows.net`。 请参阅 [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql)。  
- `SYS.SERVERS` - 返回完整的 DNS“可连接”名称，例如，为属性“name”和“data_source”返回 `myinstance.domain.database.windows.net`。 请参阅 [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)。 
- `@@SERVICENAME` 返回 NULL，因为它在托管实例环境中没有意义。 请参阅 [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql)。   
- 支持 `SUSER_ID`。 如果 AAD 登录名不在 sys.syslogins 中，则返回 NULL。 请参阅 [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql)。  
- 不支持 `SUSER_SID`。 返回错误数据（暂时性的已知问题）。 请参阅 [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql)。 
- `GETDATE()` 和其他内置日期/时间函数始终返回采用 UTC 时区的时间。 请参阅 [GETDATE](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql)。

## <a name="Issues"></a>已知问题和限制

### <a name="tempdb-size"></a>TEMPDB 大小

`tempdb` 拆分为 12 个文件，每个文件的最大大小为 14 GB。 无法更改每个文件的最大大小，并且无法将新文件添加到 `tempdb`。 很快将会去除此限制。 如果某些查询需要 `tempdb` 中 168 GB 以上的空间，这些查询可能返回错误。

### <a name="exceeding-storage-space-with-small-database-files"></a>小型数据库文件超出存储空间

每个托管实例最多提供 35 TB 的保留存储空间，每个数据库文件最初放置在 128 GB 的存储分配单元中。 包含许多小文件的数据库可能被放置在 128 GB 单元中，其总大小超过了 35 TB 限制。 在这种情况下，无法创建新数据库或还原数据库，即使所有数据库的总大小未达到实例大小限制。 在这种情况下，可能返回不明的错误。

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>在还原数据库期间不正确地配置了 SAS 密钥

如果 `CREDENTIAL` 中的共享访问签名不正确，读取 .bak 文件的 `RESTORE DATABASE` 可能会不断重试读取 .bak 文件，并在较长一段时间后返回错误。 请在还原数据库之前执行 RESTORE HEADERONLY，确保 SAS 密钥正确。
确保在使用 Azure 门户生成的 SAS 密钥中删除前导 `?`。

### <a name="tooling"></a>工具

访问托管实例时，SQL Server Management Studio 和 SQL Server Data Tools 可能会出现一些问题。 将发布正式版之前，我们将解决所有工具问题。

### <a name="incorrect-database-names"></a>错误的数据库名称

托管实例在还原期间或者在某些错误消息中可能会显示 GUID 值，而不是数据库名称。 将发布正式版之前，我们将解决这些问题。

### <a name="database-mail-profile"></a>数据库邮件配置文件
只能有一个数据库邮件配置文件，并且该配置文件必须命名为 `AzureManagedInstance_dbmail_profile`。 很快将会去除此暂时性限制。

## <a name="next-steps"></a>后续步骤

- 有关托管实例的详细信息，请参阅[什么是托管实例？](sql-database-managed-instance.md)
- 有关功能和比较列表，请参阅 [SQL 常用功能](sql-database-features.md)。
- 有关教程，请参阅[创建托管实例](sql-database-managed-instance-tutorial-portal.md)。
