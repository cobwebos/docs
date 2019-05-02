---
title: Azure SQL 数据库托管实例 T-SQL 差异 |Microsoft Docs
description: 本文介绍了 Azure SQL 数据库托管实例与 SQL Server 的 T-SQL 差异
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
manager: craigg
ms.date: 03/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 08920a25fc7213a773ef0d76a5daddbab3f765c2
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866869"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Azure SQL 数据库托管实例与 SQL Server 之间的 T-SQL 差异

本文汇总并解释了 Azure SQL 数据库托管实例与本地 SQL Server 数据库引擎之间的语法和行为差异。 讨论了以下主题： <a name="Differences"></a>

- [可用性](#availability)包括在差异[Alwayson](#always-on-availability)并[备份](#backup)。
- [安全](#security)包括在差异[审核](#auditing)，[证书](#certificates)，[凭据](#credential)，[加密提供程序](#cryptographic-providers)，[登录名和用户](#logins-and-users)，并[服务密钥和服务主密钥](#service-key-and-service-master-key)。
- [配置](#configuration)包括在差异[缓冲池扩展](#buffer-pool-extension)，[排序规则](#collation)，[兼容性级别](#compatibility-levels)，[数据库镜像](#database-mirroring)，[数据库选项](#database-options)， [SQL Server 代理](#sql-server-agent)，并且[表选项](#tables)。
- [功能](#functionalities)包括[大容量插入/OPENROWSET](#bulk-insert--openrowset)， [CLR](#clr)， [DBCC](#dbcc)，[分布式事务](#distributed-transactions)， [扩展事件](#extended-events)，[外部库](#external-libraries)， [filestream 和 FileTable](#filestream-and-filetable)，[全文语义搜索](#full-text-semantic-search)， [链接服务器](#linked-servers)， [PolyBase](#polybase)，[复制](#replication)，[还原](#restore-statement)， [Service Broker](#service-broker)， [存储过程、 函数和触发器](#stored-procedures-functions-and-triggers)。
- [功能中具有不同的行为的托管实例](#Changes)。
- [临时限制和已知的问题](#Issues)。

托管实例部署选项与本地 SQL Server 数据库引擎高度兼容。 托管实例支持大多数 SQL Server 数据库引擎功能。

![迁移](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>可用性

### <a name="always-on-availability"></a>始终可用

[高可用性](sql-database-high-availability.md)内置在托管实例和用户不能控制。 不支持以下语句：

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr)子句[ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)语句

### <a name="backup"></a>备份

托管的实例有自动备份，因此，用户可以创建完整数据库`COPY_ONLY`备份。 不支持差异、 日志和文件快照备份。

- 使用托管实例，您可以备份实例数据库仅对 Azure Blob 存储帐户：
  - 仅支持 `BACKUP TO URL`。
  - `FILE``TAPE`，和不支持备份设备。
- 大多数常规`WITH`支持选项。
  - `COPY_ONLY` 是必需的。
  - 不支持 `FILE_SNAPSHOT`。
  - 合盿匡兜： `REWIND`， `NOREWIND`， `UNLOAD`，和`NOUNLOAD`不受支持。
  - 特定于日志的选项： `NORECOVERY`， `STANDBY`，和`NO_TRUNCATE`不受支持。

限制： 

- 使用托管实例，您可以备份实例数据库到包含最多 32 个带状线，这是足够的数据库的备份最多 4 TB，如果使用备份压缩。
- 通过使用的最大备份条带大小`BACKUP`托管实例中的命令为 195 GB，这是最大 blob 大小。 在 backup 命令中增加条带数目可以减小单个条带的大小，并保持在此限制范围内。

    > [!TIP]
    > 若要解决此限制，在从 SQL Server 的本地环境中或在虚拟机中备份数据库时，您可以：
    >
    > - 备份到`DISK`而不是备份到`URL`。
    > - 备份文件上传到 Blob 存储。
    > - 还原到托管实例。
    >
    > `Restore`托管实例中的命令在因为不同的 blob 类型用于上传备份文件存储在备份文件中支持更大 blob 大小。

有关使用 T-SQL 进行备份的信息，请参阅 [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql)。

## <a name="security"></a>安全

### <a name="auditing"></a>审核

在审核 Azure SQL 数据库和 SQL Server 中的数据库方面，主要差异是：

- 使用 Azure SQL 数据库托管实例部署选项时，审核在服务器级别的工作原理。 `.xel`日志文件存储在 Azure Blob 存储。
- 使用 Azure SQL 数据库中的单一数据库和弹性池部署选项，审核是在数据库一级执行。
- 在 SQL Server 的本地或虚拟机中，审核在服务器级别的工作原理。 事件存储在文件系统或 Windows 事件日志。
 
托管实例中的 XEvent 审核支持 Azure Blob 存储目标。 不支持文件和 Windows 日志。

Azure Blob 存储审核的主要 `CREATE AUDIT` 语法差异为：

- 一种新语法`TO URL`前提可用于指定 Azure Blob 存储容器的 URL 是其中`.xel`文件放置。
- 语法`TO FILE`不受支持，因为托管的实例无法访问 Windows 文件共享。

有关详细信息，请参阅： 

- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>证书

托管的实例无法访问文件共享和 Windows 文件夹，因此存在以下限制：

- `CREATE FROM` / `BACKUP TO`文件不受支持的证书。
- `CREATE` / `BACKUP`证书从`FILE` / `ASSEMBLY`不受支持。 无法使用私钥文件。 

请参阅 [CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) 和 [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql)。 
 
**解决方法**：证书或私钥编写脚本，将存储为.sql 文件，并创建从二进制文件：

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>凭据

仅支持 Azure Key Vault 和 `SHARED ACCESS SIGNATURE` 标识。 不支持 Windows 用户。

请参阅 [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) 和 [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql)。

### <a name="cryptographic-providers"></a>加密提供程序

托管的实例无法访问文件，因此无法创建加密提供程序：

- 不支持 `CREATE CRYPTOGRAPHIC PROVIDER`。 请参阅 [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql)。
- 不支持 `ALTER CRYPTOGRAPHIC PROVIDER`。 请参阅 [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)。

### <a name="logins-and-users"></a>登录名和用户

- 通过使用创建的 SQL 登录名`FROM CERTIFICATE`， `FROM ASYMMETRIC KEY`，和`FROM SID`支持。 请参阅 [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql)。
- Azure Active Directory (Azure AD) 服务器创建的主体 （登录名） 与[CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)语法或[创建用户从登录名 [Azure AD 登录]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current)语法支持 （公共预览版）。 在服务器级别创建这些登录名。

    托管实例支持使用语法 `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` 的 Azure AD 数据库主体。 此功能也称为是 Azure AD 包含的数据库用户。

- 使用创建的 Windows 登录名`CREATE LOGIN ... FROM WINDOWS`语法不受支持。 使用 Azure Active Directory 登录名和用户。
- 创建实例的 Azure AD 用户已[不受限制的管理员权限](sql-database-manage-logins.md#unrestricted-administrative-accounts)。
- 非管理员 Azure AD 数据库级别的用户可以通过创建`CREATE USER ... FROM EXTERNAL PROVIDER`语法。 请参阅 [CREATE USER ...FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users)。
- Azure AD 服务器主体 （登录名） 支持仅限一个托管实例中的 SQL 功能。 Azure AD 用户不支持需要跨实例交互，无论是否它们是相同的 Azure AD 中租户，还是不同租户的功能。 此类功能的示例包括：

  - SQL 事务复制。
  - 链接服务器。

- 不支持设置映射到作为数据库所有者的 Azure AD 组的 Azure AD 登录名。
- 支持的 Azure AD 使用其他 Azure AD 主体服务器级别主体的模拟，如[EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql)子句。 EXECUTE AS 限制是：

  - EXECUTE AS USER 不支持 Azure AD 用户时的名称不同于登录名。 例如，通过语法创建用户 [myAadUser] 从登录名创建用户时 [john@contoso.com] 和模拟尝试通过 EXEC AS USER = _myAadUser_。 当您创建**用户**从 Azure AD 服务器主体 （登录名），为从相同的 login_name 指定 user_name**登录名**。
  - 仅 SQL 服务器级主体 （登录名） 属于`sysadmin`角色可以执行以下操作的目标 Azure AD 主体：

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- 公共预览版的 Azure AD 服务器主体 （登录名） 的限制：

  - 托管实例的 active Directory 管理员限制：

    - Azure AD 管理员用于设置托管实例不能用于创建 Azure AD 内的服务器主体 （登录名） 的托管实例。 您也必须使用 SQL Server 帐户创建第一个 Azure AD 服务器主体 （登录名） 的`sysadmin`角色。 Azure AD 服务器主体 （登录名） 公开上市后，将删除此临时限制。 如果你尝试使用 Azure AD 管理员帐户创建登录名，您将看到以下错误： `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - 标准的 SQL Server 帐户 (非 Azure AD) 创建 master 数据库中创建的第一个 Azure AD 登录名的当前，这`sysadmin`通过使用角色[CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)从外部提供程序。 公开发布后，将会去除此限制。 然后可以创建一个初始的托管实例使用 Active Directory 管理员的 Azure AD 登录名。
    - Azure AD 登录名不支持用于 SQL Server Management Studio 或 SqlPackage 的 DacFx （导出/导入）。 Azure AD 服务器主体 （登录名） 公开上市后，将取消此限制。
    - SQL Server Management Studio 中使用 Azure AD 服务器主体 （登录名）：

      - 不支持脚本编写使用任何经过身份验证的登录名的 Azure AD 登录名。
      - IntelliSense 无法识别创建的登录从外部提供程序语句，并显示一条红色下划线。

- 只有服务器级别主体登录名，就由设置过程中，服务器角色的成员，如托管实例创建`securityadmin`或`sysadmin`，或者具有 ALTER ANY LOGIN 权限在服务器级别的其他登录名可以创建 Azure AD托管实例的 master 数据库中的服务器主体 （登录名）。
- 如果登录名的 SQL 主体，只有登录名属于`sysadmin`角色可用于 Azure AD 帐户创建登录名的创建命令。
- Azure AD 登录名必须是 Azure AD 为 Azure SQL 数据库托管实例使用的相同目录中的成员。
- Azure AD 服务器主体 （登录名） 将显示在对象资源管理器从 SQL Server Management Studio 18.0 preview 5 开始。
- 允许 Azure AD 服务器主体（登录名）与 Azure AD 管理员帐户重叠。 解决主体并将权限应用到托管实例时，azure AD 服务器主体 （登录名） 优先于 Azure AD 管理员。
- 在身份验证，过程按以下顺序应用以解决身份验证的主体：

    1. 如果 Azure AD 帐户为直接存在映射到的 Azure AD 服务器主体 （登录名），其中存在 sys.server_principals 中为类型"E"，则授予访问权限，应用的 Azure AD 服务器主体 （登录名） 的权限。
    2. 如果 Azure AD 帐户映射到的 Azure AD 服务器主体 （登录名），其中 sys.server_principals 键入"X"中存在，则 Azure AD 组的成员授予访问权限和应用的 Azure AD 组登录名的权限。
    3. 如果 Azure AD 帐户是特殊的门户配置为托管实例，托管实例的系统视图中不存在，Azure AD 管理员应用的 Azure AD 管理员的托管实例 （旧模式） 的特殊固定的权限。
    4. 如果作为直接映射到 Azure AD 用户在数据库中，它为类型"E"是在 sys.database_principals 中存在，存在的 Azure AD 帐户授予访问权限和应用的 Azure AD 数据库用户的权限。
    5. 如果 Azure AD 帐户是映射到在数据库中，它为类型"X"是在 sys.database_principals 中存在，Azure AD 用户的 Azure AD 组的成员授予访问权限和应用的 Azure AD 组登录名的权限。
    6. 如果没有 Azure AD 登录映射到的 Azure AD 用户帐户或 Azure AD 组帐户，其解析为的用户进行身份验证，应用此 Azure AD 登录名的所有权限。

### <a name="service-key-and-service-master-key"></a>服务密钥和服务主密钥

- [主密钥备份](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql)不受支持 （由 SQL 数据库服务管理）。
- [主密钥还原](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql)不受支持 （由 SQL 数据库服务管理）。
- [服务主密钥备份](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql)不受支持 （由 SQL 数据库服务管理）。
- [服务主密钥还原](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql)不受支持 （由 SQL 数据库服务管理）。

## <a name="configuration"></a>配置

### <a name="buffer-pool-extension"></a>缓冲池扩展

- [缓冲池扩展](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension)不受支持。
- 不支持 `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`。 请参阅 [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql)。

### <a name="collation"></a>Collation

默认实例排序规则为 `SQL_Latin1_General_CP1_CI_AS` 并可以被指定为创建参数。 请参阅[排序规则](https://docs.microsoft.com/sql/t-sql/statements/collations)。

### <a name="compatibility-levels"></a>兼容级别

- 支持的兼容级别为 100、 110、 120、 130 和 140。
- 不支持低于 100 的兼容级别。
- 新数据库的默认兼容级别为 140。 还原数据库的兼容性级别保持不变，如果它是 100 及更高版本。

请参阅 [ALTER DATABASE 兼容级别](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。

### <a name="database-mirroring"></a>数据库镜像

不支持数据库镜像。

- 不支持 `ALTER DATABASE SET PARTNER` 和 `SET WITNESS` 选项。
- 不支持 `CREATE ENDPOINT … FOR DATABASE_MIRRORING`。

有关详细信息，请参阅 [ALTER DATABASE SET PARTNER 和 SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 以及 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)。

### <a name="database-options"></a>数据库选项

- 不支持多个日志文件。
- “常规用途”服务层级不支持内存中对象。 
- 不存在的每个常规用途实例，这意味着 280 个文件，每个数据库最多 280 文件限制。 常规用途层中的数据和日志文件都将计入此限制。 [业务关键层支持每个数据库的 32,767 文件](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。
- 数据库不能包含 filestream 数据文件组。 还原将失败，如果.bak 包含`FILESTREAM`数据。 
- 每个文件都被放置在 Azure Blob 存储中。 每个文件的 IO 和吞吐量取决于每个单独文件的大小。

#### <a name="create-database-statement"></a>CREATE DATABASE 语句

以下限制适用于`CREATE DATABASE`:

- 无法定义文件和文件组。 
- `CONTAINMENT`选项不受支持。 
- `WITH` 不支持选项。 
   > [!TIP]
   > 作为一种解决方法，使用`ALTER DATABASE`后`CREATE DATABASE`设置数据库选项以添加文件或设置包含。 

- `FOR ATTACH`选项不受支持。
- `AS SNAPSHOT OF`选项不受支持。

有关详细信息，请参阅 [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql)。

#### <a name="alter-database-statement"></a>ALTER DATABASE 语句

无法设置或更改某些文件属性：

- 文件路径不能指定`ALTER DATABASE ADD FILE (FILENAME='path')`T-SQL 的语句。 请从脚本中删除 `FILENAME`，因为托管实例自动放置文件。 
- 不能使用更改文件名称`ALTER DATABASE`语句。

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

有关详细信息，请参阅 [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options)。

### <a name="sql-server-agent"></a>SQL Server 代理

- SQL Server 代理设置是只读的。 该过程`sp_set_agent_properties`托管实例中不受支持。 
- 作业
  - 支持 T-SQL 作业步骤。
  - 支持以下复制作业：
    - 事务日志读取器
    - 快照
    - 分发服务器
  - 支持 SSIS 作业步骤。
  - 目前不支持其他类型的作业步骤：
    - 不支持合并复制作业步骤。 
    - 不支持队列读取器。 
    - 尚不支持命令行界面。
  - 托管的实例无法访问外部资源，例如，网络共享通过 robocopy。 
  - SQL Server Analysis Services 不支持。
- 部分支持通知。
- 支持电子邮件通知，尽管它要求配置数据库邮件配置文件。 SQL Server 代理可以使用只有一个数据库邮件配置文件，并且必须在调用`AzureManagedInstance_dbmail_profile`。 
  - 不支持寻呼机。 
  - 不支持 NetSend。
  - 目前还不支持警报。
  - 不支持代理。 
- 事件日志不受支持。

以下功能目前不受支持，但将在将来启用：

- 代理
- 针对空闲 CPU 计划作业
- 启用或禁用代理
- 警报

有关 SQL Server 代理的信息，请参阅 [SQL Server 代理](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)。

### <a name="tables"></a>表

不支持以下表：

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED` 

有关如何创建和更改的表的信息，请参阅[CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql)并[ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)。

## <a name="functionalities"></a>功能

### <a name="bulk-insert--openrowset"></a>批量插入/openrowset

托管的实例无法访问文件共享和 Windows 文件夹，因此必须从 Azure Blob 存储导入文件：

- `DATASOURCE` 在所需`BULK INSERT`命令，同时从 Azure Blob 存储导入文件。 请参阅 [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql)。
- `DATASOURCE` 在所需`OPENROWSET`函数时从 Azure Blob 存储中读取文件的内容。 请参阅 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)。

### <a name="clr"></a>CLR

托管的实例无法访问文件共享和 Windows 文件夹，因此存在以下限制：

- 仅支持 `CREATE ASSEMBLY FROM BINARY`。 请参阅 [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)。 
- 不支持 `CREATE ASSEMBLY FROM FILE`。 请参阅 [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)。
- `ALTER ASSEMBLY` 不能引用文件。 请参阅 [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql)。

### <a name="dbcc"></a>DBCC

未记录在 SQL Server 中启用的 DBCC 语句不支持在托管实例中。

- 不支持 `Trace flags`。 请参阅[跟踪标志](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)。
- 不支持 `DBCC TRACEOFF`。 请参阅 [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql)。
- 不支持 `DBCC TRACEON`。 请参阅 [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql)。

### <a name="distributed-transactions"></a>分布式事务

MSDTC 和[弹性事务](sql-database-elastic-transactions-overview.md)目前不支持在托管实例中。

### <a name="extended-events"></a>扩展事件

不支持扩展事件 (XEvents) 的一些特定于 Windows 的目标：

- `etw_classic_sync`目标不受支持。 应用商店`.xel`Azure Blob 存储中的文件。 请参阅 [etw_classic_sync 目标](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)。
- `event_file`目标不受支持。 应用商店`.xel`Azure Blob 存储中的文件。 请参阅 [event_file 目标](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)。

### <a name="external-libraries"></a>外部库

数据库内 R 和 Python 外部库不是尚不支持。 请参阅 [SQL Server 机器学习服务](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services)。

### <a name="filestream-and-filetable"></a>Filestream 和 FileTable

- 不支持 filestream 数据。
- 数据库不能包含使用文件组`FILESTREAM`数据。
- 不支持 `FILETABLE`。
- 表不能采用 `FILESTREAM` 类型。
- 不支持以下函数：
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

有关详细信息，请参阅 [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) 和[文件表](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server)。

### <a name="full-text-semantic-search"></a>全文语义搜索

不支持[语义搜索](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server)。

### <a name="linked-servers"></a>链接的服务器

托管实例中的链接服务器支持的目标数有限：

- 支持的目标是 SQL Server 和 SQL 数据库。
- 不受支持的目标是文件、 Analysis Services 和其他 RDBMS。

操作

- 不支持跨实例写入事务。
- 支持使用 `sp_dropserver` 删除链接服务器。 请参阅 [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)。
- `OPENROWSET`函数可用于仅在 SQL Server 实例上执行查询。 它们可以是托管的、 在本地，或在虚拟机中。 请参阅 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)。
- `OPENDATASOURCE`函数可用于仅在 SQL Server 实例上执行查询。 它们可以是托管的、 在本地，或在虚拟机中。 仅`SQLNCLI`， `SQLNCLI11`，和`SQLOLEDB`为提供程序支持的值。 例如 `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`。 请参阅 [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)。

### <a name="polybase"></a>PolyBase

外部表的引用 HDFS 或 Azure Blob 存储中的文件不受支持。 有关 PolyBase 的信息，请参阅[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)。

### <a name="replication"></a>复制

复制功能适用于托管实例公共预览版。 有关复制的信息，请参阅[SQL Server 复制](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance)。

### <a name="restore-statement"></a>RESTORE 语句 

- 支持的语法：
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- 不支持的语法：
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- 源： 
  - `FROM URL` （azure Blob 存储） 是唯一支持的选项。
  - 不支持 `FROM DISK`/`TAPE`/备份设备。
  - 不支持备份集。
- `WITH` 不支持选项，如否`DIFFERENTIAL`或`STATS`。
- `ASYNC RESTORE`：即使客户端连接中断，将继续还原。 如果删除了连接，你可以检查`sys.dm_operation_status`视图状态的还原操作，并为 CREATE 和 DROP 数据库。 请参阅 [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)。 

以下数据库选项设置或重写和不能在以后对其进行更改： 

- `NEW_BROKER` 如果.bak 文件中未启用中转站。 
- `ENABLE_BROKER` 如果.bak 文件中未启用中转站。 
- `AUTO_CLOSE=OFF` 如果.bak 文件中的数据库具有`AUTO_CLOSE=ON`。 
- `RECOVERY FULL` 如果.bak 文件中的数据库具有`SIMPLE`或`BULK_LOGGED`恢复模式。
- 内存优化文件组被添加，如果源.bak 文件中没有调用 XTP。 
- 任何现有的内存优化文件组已重命名为 XTP。 
- `SINGLE_USER` 并`RESTRICTED_USER`选项将转换为`MULTI_USER`。

限制： 

- `.BAK` 无法还原包含多个备份集的文件。 
- `.BAK` 无法还原包含多个日志文件的文件。
- 还原将失败，如果.bak 包含`FILESTREAM`数据。
- 包含具有活动内存中对象的数据库的备份无法还原的常规用途实例上。 有关还原语句的信息，请参阅[RESTORE 语句](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)。

### <a name="service-broker"></a>服务代理

不支持跨实例 Service Broker：

- `sys.routes`：作为先决条件，必须从 sys.routes 中选择的地址。 在每个路由，该地址必须为本地。 请参阅 [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)。
- `CREATE ROUTE`：不能使用`CREATE ROUTE`与`ADDRESS`而不`LOCAL`。 请参阅 [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql)。
- `ALTER ROUTE`：不能使用`ALTER ROUTE`与`ADDRESS`而不`LOCAL`。 请参阅 [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql)。 

### <a name="stored-procedures-functions-and-triggers"></a>存储的过程、 函数和触发器

- `NATIVE_COMPILATION` 不支持在常规用途层。
- 不支持以下 [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 选项： 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- 不支持 `sp_execute_external_scripts`。 请参阅 [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)。
- 不支持 `xp_cmdshell`。 请参阅 [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)。
- `Extended stored procedures` 不受支持，其中包括`sp_addextendedproc` 和`sp_dropextendedproc`。 请参阅[扩展存储的过程](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)。
- 不支持 `sp_attach_db`、`sp_attach_single_file_db` 和 `sp_detach_db`。 请参阅 [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql)、[sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 和 [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)。

## <a name="Changes"></a>行为更改

以下变量、函数和视图返回不同的结果：

- `SERVERPROPERTY('EngineEdition')` 返回值 8。 此属性唯一标识托管实例。 请参阅 [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)。
- `SERVERPROPERTY('InstanceName')` 因为 SQL Server 不能应用于的托管实例的实例作为其概念存在，则返回 NULL。 请参阅 [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)。
- `@@SERVERNAME` 返回为完整的 DNS"可连接"名称，例如，我管理 instance.wcus17662feb9ce98.database.windows.net。 请参阅 [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql)。 
- `SYS.SERVERS` 返回为完整的 DNS"可连接"名称，如`myinstance.domain.database.windows.net`属性"name"和"data_source。" 请参阅 [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)。
- `@@SERVICENAME` 返回 NULL，因为它与服务的概念存在 SQL Server 不会应用到的托管实例。 请参阅 [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql)。
- 支持 `SUSER_ID`。 如果 Azure AD 登录名不在 sys.syslogins 中，则返回 NULL。 请参阅 [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql)。 
- 不支持 `SUSER_SID`。 返回了错误的数据，这是临时的已知问题。 请参阅 [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql)。 

## <a name="Issues"></a>已知问题和限制

### <a name="tempdb-size"></a>TEMPDB 大小

最大文件大小`tempdb`不能超过每个常规用途层上的核心 24 GB。 最大值`tempdb`业务关键层上的大小是与实例存储大小。 `tempdb`数据库始终拆分为 12 个数据文件。 无法更改此最大大小，每个文件，并可以将新文件添加到`tempdb`。 某些查询可能会返回错误，如果它们需要 24 GB 以上每个核心中`tempdb`。

### <a name="cant-restore-contained-database"></a>无法还原包含的数据库

无法还原托管的实例[包含的数据库](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)。 托管实例的现有包含数据库的时点还原不起作用。 将很快解决此问题。 在此期间，我们建议从数据库放置在托管实例上删除的包含关系选项。 不要使用用于生产数据库的包含关系选项。 

### <a name="exceeding-storage-space-with-small-database-files"></a>小型数据库文件超出存储空间

`CREATE DATABASE`、`ALTER DATABASE ADD FILE` 和 `RESTORE DATABASE` 语句可能会失败，因为实例可能会达到 Azure 存储限制。

每个常规用途的托管的实例都有最多 35 TB 的存储空间保留 Azure 高级磁盘空间。 每个数据库文件放置在单独的物理磁盘上。 磁盘大小可以为 128 GB、256 GB、512 GB、1 TB 或 4 TB。 未使用的磁盘空间不收费，但 Azure 高级磁盘大小的总和不能超过 35 TB。 在某些情况下，不会总共需要 8 TB 的托管的实例可能会超过 35 TB Azure 由于内部碎片的存储大小限制。

例如，常规用途的托管的实例可能会有一个放置在一个 4 TB 的磁盘上大小为 1.2 TB 的文件。 它还可能有 248 文件的放置在单独的 128-GB 磁盘的每个 1 GB 的大小。 在本示例中：

- 分配的磁盘存储总大小为 1 x 4 TB + 248 x 128 GB = 35 TB。
- 实例上的数据库的总预留空间为 1 x 1.2 TB + 248 x 1 GB = 1.4 TB。

此示例说明，在某些情况下，由于文件的特定分发的托管的实例可能会达到时可能会出乎意料地为已连接的 Azure 高级磁盘保留 35 TB 限制。

在此示例中，现有数据库继续工作，只要不添加新的文件可增长而无需任何问题。 无法创建新的数据库，或将其还原，因为没有足够的空间用于新磁盘驱动器，即使所有数据库的总大小未达到实例大小限制。 这种情况下返回的错误并不明确。

你可以[确定剩余的文件数量](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)通过使用系统视图。 如果达到此限制，请尝试[的空和删除一些较小的文件使用 DBCC SHRINKFILE 语句](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)或切换到[业务关键层中，其不具有此限制](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。

### <a name="incorrect-configuration-of-the-sas-key-during-database-restore"></a>还原数据库期间配置不正确的 SAS 密钥

`RESTORE DATABASE` 读取.bak 文件可能会不断重试读取.bak 文件，并返回错误长的一段时间后，如果中的共享的访问签名`CREDENTIAL`不正确。 执行 RESTORE HEADERONLY，然后才能还原数据库以确保 SAS 密钥正确。
请确保删除前导`?`通过使用 Azure 门户生成的 SAS 密钥中。

### <a name="tooling"></a>工具

SQL Server Management Studio 和 SQL Server Data Tools 可能会遇到一些问题，而他们访问的托管的实例。

- 使用 Azure AD 服务器主体 （登录名） 和用户 （公共预览版） 与 SQL Server 数据工具当前不受支持。
- Azure AD 服务器主体 （登录名） 和用户 （公共预览版） 的脚本不支持在 SQL Server Management Studio 中。

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>在某些视图、日志和消息中，数据库名称不正确

多个系统视图、性能计数器、错误消息、XEvent 和错误日志条目显示了 GUID 数据库标识符而非实际的数据库名称。 不要依赖这些 GUID 标识符，因为它们在将来替换实际的数据库名称。

### <a name="database-mail"></a>数据库邮件

`@query`中的参数[sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql)过程不起作用。

### <a name="database-mail-profile"></a>数据库邮件配置文件

SQL Server 代理使用的数据库邮件配置文件必须在调用`AzureManagedInstance_dbmail_profile`。 没有任何限制的其他数据库邮件配置文件名称。

### <a name="error-logs-arent-persisted"></a>不保留的错误日志

错误日志，可在托管实例中不会保持不变，并且其大小不包括在最大存储限制。 如果发生故障转移，可能会自动清除错误日志。

### <a name="error-logs-are-verbose"></a>错误日志是详细的

托管的实例都置于错误日志的详细信息，其中的许多不相关。 将在将来降低错误日志中的信息量。

**解决方法：** 使用自定义过程中读取的错误日志，筛选出不相关的某些项。 有关详细信息，请参阅[托管实例 – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/)。

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>不支持对同一个实例中的两个数据库的事务作用域

`TransactionScope`类在.NET 中的不起作用，如果两个查询发送到同一个事务范围内的同一实例内的两个数据库：

```C#
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

尽管此代码适用于同一个实例中的数据，但它要求 MSDTC。

**解决方法：** 使用[SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase)而不是使用两个连接的连接上下文中使用的另一个数据库。

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR 模块和链接的服务器有时不能引用本地 IP 地址

在托管的实例和链接的服务器或有时会引用当前实例的分布式的查询中放置的 CLR 模块无法解析的本地实例的 IP。 此错误是暂时性问题。

**解决方法：** 如有可能在 CLR 模块中使用上下文连接。

### <a name="tde-encrypted-databases-with-a-service-managed-key-dont-support-user-initiated-backups"></a>使用服务托管密钥的 TDE 加密数据库不支持用户启动的备份

无法执行`BACKUP DATABASE ... WITH COPY_ONLY`上使用服务托管透明数据加密 (TDE) 加密的数据库。 服务托管 TDE 强制使用内部的 TDE 密钥进行加密的备份。 无法导出密钥，因此无法还原备份。

**解决方法：** 使用自动备份和时间点还原，或者使用[客户托管 (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key)相反。 此外可以禁用对数据库进行加密。

## <a name="next-steps"></a>后续步骤

- 有关托管实例的详细信息，请参阅[什么是托管的实例？](sql-database-managed-instance.md)
- 有关功能和比较列表，请参阅[Azure SQL 数据库功能比较](sql-database-features.md)。
- 有关演示如何创建新的托管的实例的快速入门，请参阅[创建的托管的实例](sql-database-managed-instance-get-started.md)。
