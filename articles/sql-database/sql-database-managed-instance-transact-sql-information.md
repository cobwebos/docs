---
title: Azure SQL 数据库托管实例 T-sql 差异 |Microsoft Docs
description: 本文介绍了 Azure SQL 数据库托管实例与 SQL Server 的 T-SQL 差异
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
ms.date: 08/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: 1581a62f0999cf502feaad31d2c884f4d171e770
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019659"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Azure SQL 数据库托管实例与 SQL Server 的 T-SQL 差异

本文总结并说明了 Azure SQL 数据库托管实例与本地 SQL Server 数据库引擎之间的语法和行为之间的差异。 其中讨论了以下主题：<a name="Differences"></a>

- [可用性](#availability)包括 [Always-On](#always-on-availability) 和[备份](#backup)方面的差异。
- [安全性](#security)包括[审核](#auditing)、[证书](#certificates)、[凭据](#credential)、[加密提供程序](#cryptographic-providers)、[登录名和用户名](#logins-and-users)以及[服务密钥和服务主密钥](#service-key-and-service-master-key)方面的差异。
- [配置](#configuration)包括[缓冲池扩展](#buffer-pool-extension)、[排序规则](#collation)、[兼容性级别](#compatibility-levels)、[数据库镜像](#database-mirroring)、[数据库选项](#database-options)、[SQL Server 代理](#sql-server-agent)以及[表选项](#tables)方面的差异。
- [功能](#functionalities)包括[BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc),[分布式事务](#distributed-transactions),[扩展事件](#extended-events),[外部库](#external-libraries), [filestream 和 FileTable](#filestream-and-filetable),[全文语义搜索](#full-text-semantic-search)、[链接服务器](#linked-servers)、 [PolyBase](#polybase)、[复制](#replication)、[还原](#restore-statement)、 [Service Broker](#service-broker)、[存储过程、函数和触发器](#stored-procedures-functions-and-triggers)。
- [环境设置](#Environment), 如 vnet 和子网配置。
- [在托管实例中行为不同的功能](#Changes)。
- [暂时性的限制和已知问题](#Issues)。

托管实例部署选项与本地 SQL Server 数据库引擎高度兼容。 托管实例支持大多数 SQL Server 数据库引擎功能。

![迁移](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>可用性

### <a name="always-on-availability"></a>Always On

[高可用性](sql-database-high-availability.md)内置于托管实例中, 不能由用户控制。 不支持以下语句：

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) 语句的 [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) 子句

### <a name="backup"></a>备份

托管实例包含自动备份，因此用户可以创建完整数据库 `COPY_ONLY` 备份。 不支持差异、日志和文件快照备份。

- 使用托管实例，可以只将实例数据库备份到 Azure Blob 存储帐户：
  - 仅支持 `BACKUP TO URL`。
  - 不支持 `FILE`、`TAPE` 和备份设备。
- 支持大多数常规 `WITH` 选项。
  - `COPY_ONLY` 是必需的。
  - 不支持 `FILE_SNAPSHOT`。
  - 不支持磁带选项 `REWIND`、`NOREWIND`、`UNLOAD` 和 `NOUNLOAD`。
  - 不支持日志特定的选项 `NORECOVERY`、`STANDBY` 和 `NO_TRUNCATE`。

限制： 

- 使用托管实例可将数据库备份到最多包含 32 个条带的备份，如果使用备份压缩，则这种方法对于不超过 4 TB 的数据库而言已足够。
- 在托管实例中使用 `BACKUP` 命令最大可以设置 195 GB 的备份条带大小（即最大 Blob 大小）。 在 backup 命令中增加条带数目可以减小单个条带的大小，并保持在此限制范围内。

    > [!TIP]
    > 从本地环境或虚拟机中的 SQL Server 备份数据库时，若要解决此限制，可以：
    >
    > - 备份到 `DISK` 而不是 `URL`。
    > - 将备份文件上传到 Blob 存储。
    > - 还原到托管实例。
    >
    > 托管实例中的 `Restore` 命令支持备份文件中的更大 Blob 大小，因为将使用不同的 Blob 类型来存储上传的备份文件。

有关使用 T-SQL 进行备份的信息，请参阅 [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql)。

## <a name="security"></a>安全性

### <a name="auditing"></a>审核

在审核 Azure SQL 数据库和 SQL Server 中的数据库方面，主要差异是：

- 使用 Azure SQL 数据库中的托管实例部署选项, 审核在服务器级别工作。 在 Azure Blob 存储中存储 `.xel` 日志文件。
- 使用 Azure SQL 数据库中的单一数据库和弹性池部署选项，审核是在数据库一级执行。
- 在本地 SQL Server 或虚拟机中，审核在服务器级别执行。 在文件系统或 Windows 事件日志中存储事件。
 
托管实例中的 XEvent 审核支持 Azure Blob 存储目标。 不支持文件和 Windows 日志。

Azure Blob 存储审核的主要 `CREATE AUDIT` 语法差异为：

- 提供了新语法 `TO URL`，用于指定 `.xel` 文件要放到的 Azure Blob 存储容器的 URL。
- 不支持语法 `TO FILE`，因为托管实例无法访问 Windows 文件共享。

有关详细信息，请参阅： 

- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>证书

由于托管实例无法访问文件共享和 Windows 文件夹，因此存在以下约束：

- 不支持将 `CREATE FROM`/`BACKUP TO` 文件用于证书。
- 不支持 `FILE`/`ASSEMBLY` 中的 `CREATE`/`BACKUP` 证书。 无法使用私钥文件。 

请参阅 [CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) 和 [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql)。 
 
**解决方法**：编写证书或私钥的脚本，将其存储为 .sql 文件，然后从二进制文件创建证书：

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>凭据

仅支持 Azure Key Vault 和 `SHARED ACCESS SIGNATURE` 标识。 不支持 Windows 用户。

请参阅 [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) 和 [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql)。

### <a name="cryptographic-providers"></a>加密提供程序

由于托管实例无法访问文件，因此无法创建加密提供程序：

- 不支持 `CREATE CRYPTOGRAPHIC PROVIDER`。 请参阅 [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql)。
- 不支持 `ALTER CRYPTOGRAPHIC PROVIDER`。 请参阅 [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)。

### <a name="logins-and-users"></a>登录名和用户

- 支持使用 `FROM CERTIFICATE`、`FROM ASYMMETRIC KEY` 和 `FROM SID` 创建的 SQL 登录名。 请参阅 [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql)。
- 支持使用 [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) 语法或 [CREATE USER FROM LOGIN [Azure AD 登录名]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) 语法创建的 Azure Active Directory (Azure AD) 服务器主体（登录名）（公共预览版）。 这些登录名是在服务器级别创建的。

    托管实例通过语法`CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`支持 Azure AD 数据库主体。 此功能也称为 Azure AD 包含的数据库用户。

- 不支持使用 `CREATE LOGIN ... FROM WINDOWS` 语法创建的 Windows 登录名。 使用 Azure Active Directory 登录名和用户。
- 创建实例的 Azure AD 用户具有[不受限制的管理特权](sql-database-manage-logins.md#unrestricted-administrative-accounts)。
- 可以使用 `CREATE USER ... FROM EXTERNAL PROVIDER` 语法创建非管理员 Azure AD 数据库级用户。 请参阅 [CREATE USER ...FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users)。
- Azure AD 服务器主体（登录名）仅支持一个托管实例中的 SQL 功能。 无论是在相同还是不同的 Azure AD 租户中，需要跨实例交互的功能都不支持 Azure AD 用户。 此类功能的示例包括：

  - SQL 事务复制。
  - 链接服务器。

- 不支持设置映射到作为数据库所有者的 Azure AD 组的 Azure AD 登录名。
- 支持使用其他 Azure AD 主体模拟 Azure AD 服务器级主体，例如 [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) 子句。 EXECUTE AS 限制如下:

  - 当名称不同于登录名时，EXECUTE AS USER 不支持 Azure AD 用户。 例如，如果用户是通过语法 CREATE USER [myAadUser] FROM LOGIN [john@contoso.com] 创建的，则会尝试通过 EXEC AS USER = _myAadUser_ 进行模拟。 基于 Azure AD 服务器主体（登录名）创建 **USER** 时，请指定与 **LOGIN** 中的 login_name 相同的 user_name。
  - 只有属于 `sysadmin` 角色的 SQL 服务器级主体（登录名）可以针对 Azure AD 主体执行以下操作：

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Azure AD 服务器主体（登录名）的公共预览版限制：

  - Active Directory 托管实例的管理员限制:

    - 用于设置托管实例的 Azure AD 管理员不可用于在托管实例中创建 Azure AD 服务器主体（登录名）。 必须使用充当 `sysadmin` 角色的 SQL Server 帐户创建第一个 Azure AD 服务器主体（登录名）。 Azure AD 服务器主体（登录名）的正式版推出后，即会去除这种暂时性限制。 如果尝试使用 Azure AD 管理员帐户创建登录名，将会看到以下错误：`Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - 目前，在 master 数据库中创建的第一个 Azure AD 登录名必须由充当 `sysadmin` 角色的标准 SQL Server 帐户（非 Azure AD）使用 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) FROM EXTERNAL PROVIDER 创建。 正式版推出后，将去除此限制。 然后, 可以使用托管实例的 Active Directory 管理员创建初始 Azure AD 登录名。
    - 与 SQL Server Management Studio 或 SqlPackage 配合使用的 DacFx（导出/导入）不支持 Azure AD 登录名。 Azure AD 服务器主体（登录名）的正式版推出后，即会去除这种限制。
    - 将 Azure AD 服务器主体（登录名）与 SQL Server Management Studio 配合使用：

      - 不支持为使用任何经过身份验证的登录名的 Azure AD 登录名编写脚本。
      - IntelliSense 无法识别 CREATE LOGIN FROM EXTERNAL PROVIDER 语句，将显示红色下划线。

- 只有服务器级别主体登录名由托管实例预配过程创建, 服务器角色的成员 ( `securityadmin`如或`sysadmin`) 或服务器级别的 ALTER ANY login 权限的其他登录名可以创建 Azure AD托管实例的 master 数据库中的服务器主体 (登录名)。
- 如果登录名是 SQL 主体，则只有属于 `sysadmin` 角色的登录名才能使用 create 命令来为 Azure AD 帐户创建登录名。
- Azure AD 登录名必须是用于 Azure SQL 数据库托管实例的同一目录中的 Azure AD 的成员。
- 从 SQL Server Management Studio 18.0 预览版 5 开始，Azure AD 服务器主体（登录名）将显示在对象资源管理器中。
- 允许 Azure AD 服务器主体（登录名）与 Azure AD 管理员帐户重叠。 解析主体以及将权限应用到托管实例时，Azure AD 服务器主体（登录名）优先于 Azure AD 管理员。
- 在身份验证期间，将应用以下顺序来解析身份验证主体：

    1. 如果 Azure AD 帐户存在并直接映射到 Azure AD 服务器主体（登录名）（以类型“E”的形式存在于 sys.server_principals 中），则授予访问权限并应用 Azure AD 服务器主体（登录名）的权限。
    2. 如果 Azure AD 帐户是映射到 Azure AD 服务器主体（登录名）的 Azure AD 组的成员（以类型“X”的形式存在于 sys.server_principals 中），则授予访问权限并应用 Azure AD 组登录名的权限。
    3. 如果 Azure AD 帐户为托管实例配置的特殊门户 Azure AD 管理员, 这在托管实例系统视图中不存在, 则对托管实例的 Azure AD 管理员 (旧模式) 应用特殊固定的权限。
    4. 如果 Azure AD 帐户存在并直接映射到数据库中的 Azure AD 用户（以类型“E”的形式存在于 sys.database_principals 中），则授予访问权限并应用 Azure AD 数据库用户的权限。
    5. 如果 Azure AD 帐户是映射到数据库中 Azure AD 用户的 Azure AD 组的成员（以类型“X”的形式存在于 sys.database_principals 中），则授予访问权限并应用 Azure AD 组登录名的权限。
    6. 如果某个 Azure AD 登录映射到 Azure AD 用户帐户或 Azure AD 组帐户并解析为用户身份验证，则应用此 Azure AD 登录名中的所有权限。

### <a name="service-key-and-service-master-key"></a>服务密钥和服务主密钥

- 不支持[主密钥备份](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql)（由 SQL 数据库服务管理）。
- 不支持[主密钥还原](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql)（由 SQL 数据库服务管理）。
- 不支持[服务主密钥备份](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql)（由 SQL 数据库服务管理）。
- 不支持[服务主密钥还原](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql)（由 SQL 数据库服务管理）。

## <a name="configuration"></a>配置

### <a name="buffer-pool-extension"></a>缓冲池扩展

- 不支持[缓冲池扩展](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension)。
- 不支持 `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`。 请参阅 [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql)。

### <a name="collation"></a>排序规则

默认实例排序规则为 `SQL_Latin1_General_CP1_CI_AS` 并可以被指定为创建参数。 请参阅[排序规则](https://docs.microsoft.com/sql/t-sql/statements/collations)。

### <a name="compatibility-levels"></a>兼容级别

- 支持的兼容级别：100、110、120、130 和 140。
- 不支持低于 100 的兼容级别。
- 新数据库的默认兼容级别为 140。 对于已还原的数据库，如果其兼容级别在还原之前为 100 或更高，则还原后保持不变。

请参阅 [ALTER DATABASE 兼容级别](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。

### <a name="database-mirroring"></a>数据库镜像

不支持数据库镜像。

- 不支持 `ALTER DATABASE SET PARTNER` 和 `SET WITNESS` 选项。
- 不支持 `CREATE ENDPOINT … FOR DATABASE_MIRRORING`。

有关详细信息，请参阅 [ALTER DATABASE SET PARTNER 和 SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 以及 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)。

### <a name="database-options"></a>数据库选项

- 不支持多个日志文件。
- “常规用途”服务层级不支持内存中对象。 
- 每个“常规用途”实例限制为 280 个文件，这意味着，每个数据库最多只能有 280 个文件。 “常规用途”层级中的数据文件和日志文件都会计入此限制。 [“业务关键”层级支持每个数据库 32,767 个文件](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。
- 数据库中不能有包含文件流数据的文件组。 如果 .bak 包含 `FILESTREAM` 数据，还原将会失败。 
- 每个文件都被放置在 Azure Blob 存储中。 每个文件的 IO 和吞吐量取决于每个单独文件的大小。

#### <a name="create-database-statement"></a>CREATE DATABASE 语句

以下限制适用于 `CREATE DATABASE`：

- 无法定义文件和文件组。 
- 不支持 `CONTAINMENT` 选项。 
- 不支持 `WITH` 选项。 
   > [!TIP]
   > 解决方法是在 `CREATE DATABASE` 后面使用 `ALTER DATABASE` 来设置数据库选项，以添加文件或设置包含。 

- 不支持 `FOR ATTACH` 选项。
- 不支持 `AS SNAPSHOT OF` 选项。

有关详细信息，请参阅 [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql)。

#### <a name="alter-database-statement"></a>ALTER DATABASE 语句

无法设置或更改某些文件属性：

- 无法在 `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL 语句中指定文件路径。 请从脚本中删除 `FILENAME`，因为托管实例自动放置文件。 
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

有关详细信息，请参阅 [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options)。

### <a name="sql-server-agent"></a>SQL Server 代理

- 当前在托管实例中不支持启用和禁用 SQL Server 代理。 SQL 代理始终运行。
- SQL Server 代理设置为只读。 托管实例`sp_set_agent_properties`不支持该过程。 
- 作业(Job)
  - 支持 T-SQL 作业步骤。
  - 支持以下复制作业：
    - 事务日志读取器
    - 快照
    - 分发服务器
  - 支持 SSIS 作业步骤。
  - 目前不支持其他类型的作业步骤：
    - 不支持合并复制作业步骤。 
    - 不支持队列读取器。 
    - 尚不支持命令外壳。
  - 托管实例无法访问外部资源（例如，通过 robocopy 访问网络共享）。 
  - 不支持 SQL Server Analysis Services。
- 部分支持通知。
- 支持电子邮件通知，不过需要配置数据库邮件配置文件。 SQL Server 代理只能使用一个数据库邮件配置文件，并且该配置文件必须命名为 `AzureManagedInstance_dbmail_profile`。 
  - 不支持寻呼机。
  - 不支持 NetSend。
  - 尚不支持警报。
  - 不支持代理。
- 不支持事件日志。

当前不支持以下 SQL 代理功能:

- 代理
- 针对空闲 CPU 计划作业
- 启用或禁用代理
- 警报

有关 SQL Server 代理的信息，请参阅 [SQL Server 代理](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)。

### <a name="tables"></a>表

不支持以下表类型:

- [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server)
- [外部表](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)Polybase
- [MEMORY_OPTIMIZED](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)(仅在常规用途层中不受支持)

有关如何创建和更改表的信息，请参阅 [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) 和 [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)。

## <a name="functionalities"></a>功能

### <a name="bulk-insert--openrowset"></a>Bulk insert/OPENROWSET

由于托管实例无法访问文件共享和 Windows 文件夹，必须从 Azure Blob 存储导入文件：

- 从 Azure Blob 存储导入文件时，必须在 `BULK INSERT` 命令中指定 `DATASOURCE`。 请参阅 [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql)。
- 从 Azure Blob 存储中读取文件内容时，必须在 `OPENROWSET` 函数中指定 `DATASOURCE`。 请参阅 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)。

### <a name="clr"></a>CLR

由于托管实例无法访问文件共享和 Windows 文件夹，因此存在以下约束：

- 仅支持 `CREATE ASSEMBLY FROM BINARY`。 请参阅 [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)。 
- 不支持 `CREATE ASSEMBLY FROM FILE`。 请参阅 [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)。
- `ALTER ASSEMBLY` 不能引用文件。 请参阅 [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql)。

### <a name="dbcc"></a>DBCC

托管实例不支持 SQL Server 中启用的未记录 DBCC 语句。

- 不支持 `Trace flags`。 请参阅[跟踪标志](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)。
- 不支持 `DBCC TRACEOFF`。 请参阅 [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql)。
- 不支持 `DBCC TRACEON`。 请参阅 [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql)。

### <a name="distributed-transactions"></a>分布式事务

托管实例目前不支持 MSDTC 和[弹性事务](sql-database-elastic-transactions-overview.md)。

### <a name="extended-events"></a>扩展事件

不支持对扩展事件 (XEvent) 使用某些特定于 Windows 的目标：

- 不支持 `etw_classic_sync` 目标。 在 Azure Blob 存储中存储 `.xel` 文件。 请参阅 [etw_classic_sync 目标](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)。
- 不支持 `event_file` 目标。 在 Azure Blob 存储中存储 `.xel` 文件。 请参阅 [event_file 目标](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)。

### <a name="external-libraries"></a>外部库

尚不支持数据库中的 R 和 Python 外部库。 请参阅 [SQL Server 机器学习服务](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services)。

### <a name="filestream-and-filetable"></a>文件流和文件表

- 不支持文件流数据。
- 数据库中不能有包含 `FILESTREAM` 数据的文件组。
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

- 支持的目标是托管实例、单一数据库和 SQL Server 实例。 
- 链接服务器不支持分布式可写事务 (MS DTC)。
- 不支持的目标为文件、Analysis Services 和其他 RDBMS。 尝试使用`BULK INSERT`或`OPENROWSET`作为文件导入的替代方法, 使用 Azure Blob 存储中的本机 CSV 导入。

操作

- 不支持跨实例写入事务。
- 支持使用 `sp_dropserver` 删除链接服务器。 请参阅 [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)。
- `OPENROWSET` 函数只能用于在 SQL Server 实例上执行查询。 它们可以是托管的、位于本地或位于虚拟机中。 请参阅 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)。
- `OPENDATASOURCE` 函数只能用于在 SQL Server 实例上执行查询。 它们可以是托管的、位于本地或位于虚拟机中。 仅支持将 `SQLNCLI`、`SQLNCLI11` 和 `SQLOLEDB` 值用作提供程序。 例如 `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`。 请参阅 [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)。
- 链接服务器不能用于从网络共享读取文件 (Excel、CSV)。 尝试使用从 Azure Blob 存储读取 CSV 文件[BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file)或[OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) 。 在[托管实例反馈项](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)上跟踪此请求|

### <a name="polybase"></a>PolyBase

不支持引用 HDFS 或 Azure Blob 存储中文件的外部表。 有关 PolyBase 的信息，请参阅 [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)。

### <a name="replication"></a>复制

- 支持快照和双向复制类型。 不支持合并复制、对等复制和可更新订阅。
- [事务复制](sql-database-managed-instance-transactional-replication.md)可用于托管实例上的公共预览版, 但有一些限制:
    - 所有类型的复制参与者 (发布服务器、分发服务器、请求订阅服务器和推送订阅服务器) 都可放置在托管实例上, 但不能将发布服务器和分发服务器放置在不同的实例上。
    - 托管实例可以与 SQL Server 的最新版本通信。 请在[此处](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems)查看受支持的版本。
    - 事务复制有一些[额外的网络要求](sql-database-managed-instance-transactional-replication.md#requirements)。

有关配置复制的信息, 请参阅[复制教程](replication-with-sql-database-managed-instance.md)。


如果对[故障转移组](sql-database-auto-failover-group.md)中的数据库启用了复制, 则托管实例管理员必须清除旧主副本上的所有发布, 并在发生故障转移后在新的主副本上重新配置这些发布。 在此方案中, 需要执行以下活动:

1. 停止在数据库上运行的所有复制作业 (如果存在)。
2. 通过在发布服务器数据库上运行以下脚本, 删除发布服务器上的订阅元数据:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. 删除订阅服务器上的订阅元数据。 在订阅服务器实例的订阅数据库中运行以下脚本:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. 通过在发布的数据库中运行以下脚本, 强制删除发布服务器中的所有复制对象:

   ```sql
   EXEC sp_removedbreplication
   ```

1. 强制删除原始主实例中的旧分发服务器 (如果故障回复到用于建立分发服务器的旧主实例)。 在旧的分发服务器托管实例中的 master 数据库上运行以下脚本:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

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
- 源: 
  - `FROM URL`（Azure Blob 存储）是唯一受支持的选项。
  - 不支持 `FROM DISK`/`TAPE`/备份设备。
  - 不支持备份集。
- 不支持 `WITH` 选项，例如，不允许 `DIFFERENTIAL` 或 `STATS`。
- `ASYNC RESTORE`：即使客户端连接断开，还原也会继续。 如果删除了连接，可以在 `sys.dm_operation_status` 视图中检查还原操作的状态，以及 CREATE DATABASE 和 DROP DATABASE 的状态。 请参阅 [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)。 

将设置或重写以下数据库选项，以后无法更改： 

- `NEW_BROKER`（如果未在 .bak 文件中启用代理）。 
- `ENABLE_BROKER`（如果未在 .bak 文件中启用代理）。 
- `AUTO_CLOSE=OFF`（如果 .bak 文件中的数据库采用 `AUTO_CLOSE=ON`）。 
- `RECOVERY FULL`（如果 .bak 文件中的数据库采用 `SIMPLE` 或 `BULK_LOGGED` 恢复模式）。
- 添加源 .bak 文件中不包含内存优化文件组，则会添加名为 XTP 的内存优化文件组。 
- 任何现有的内存优化文件组将重命名为 XTP。 
- `SINGLE_USER` 和 `RESTRICTED_USER` 选项将转换为 `MULTI_USER`。

限制： 

- 可能会还原损坏的数据库的备份, 具体取决于损坏的类型, 但在修复损坏之前, 不会执行自动备份。 请确保在源实例`DBCC CHECKDB`上运行, 并使用备份`WITH CHECKSUM`来避免此问题。
- 无法在托管实例还原包含本文档中所述的任何限制的数据库`FILESTREAM` `FILETABLE`文件(例如或对象)。 `.BAK`
- 无法还原包含多个备份集的 `.BAK` 文件。 
- 无法还原包含多个日志文件的 `.BAK` 文件。
- 如果备份包含的数据库大于8TB、活动内存中 OLTP 对象或280以上的文件, 则不能在常规用途实例上还原。 
- 如果备份包含的数据库大于4TB 或内存中 OLTP 对象, 但其总大小大于[资源限制](sql-database-managed-instance-resource-limits.md)中所述的大小, 则无法在业务关键实例上还原。
有关 restore 语句的信息，请参阅 [RESTORE 语句](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)。

### <a name="service-broker"></a>服务代理

不支持跨实例 Service Broker：

- `sys.routes`：作为先决条件，必须从 sys.routes 中选择地址。 该地址必须在每个路由的本地。 请参阅 [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)。
- `CREATE ROUTE`：不能将 `CREATE ROUTE` 与除 `LOCAL` 以外的 `ADDRESS` 配合使用。 请参阅 [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql)。
- `ALTER ROUTE`：不能将 `ALTER ROUTE` 与除 `LOCAL` 以外的 `ADDRESS` 配合使用。 请参阅 [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql)。 

### <a name="stored-procedures-functions-and-triggers"></a>存储过程、函数和触发器

- “常规用途”层级不支持 `NATIVE_COMPILATION`。
- 不支持以下 [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 选项： 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- 不支持 `sp_execute_external_scripts`。 请参阅 [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)。
- 不支持 `xp_cmdshell`。 请参阅 [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)。
- 不支持 `Extended stored procedures`，其中包括 `sp_addextendedproc` 和 `sp_dropextendedproc`。 请参阅[扩展存储过程](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)。
- 不支持 `sp_attach_db`、`sp_attach_single_file_db` 和 `sp_detach_db`。 请参阅 [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql)、[sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 和 [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)。

## <a name="Environment"></a>环境约束

### <a name="subnet"></a>Subnet
-  不能将任何其他资源 (例如虚拟机) 放入部署了托管实例的子网中。 使用其他子网部署这些资源。
- 子网必须具有足够数量的可用[IP 地址](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。 最小值为 16, 但建议至少在子网中包含32个 IP 地址。
- [无法将服务终结点与托管实例的子网相关联](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。 创建虚拟网络时，请务必禁用“服务终结点”选项。
- 在某个区域中, 可以部署的 Vcore 和实例类型有一些[限制和限制](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)。
- 某些[安全规则必须应用于子网](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。

### <a name="vnet"></a>VNET
- 可以使用资源模型-不支持 VNet 的经典模型来部署 VNet。
- 创建托管实例后, 不支持将托管实例或 VNet 移到另一个资源组或订阅。
- 某些服务 (如应用服务环境、逻辑应用和托管实例, 用于异地复制、事务复制或通过链接服务器) 无法访问不同区域中的托管实例, 如果它们的 Vnet 使用[global对等互连](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)。 可以通过 ExpressRoute 或 vnet 到 vnet 网关连接到这些资源。

## <a name="Changes"></a>行为更改

以下变量、函数和视图返回不同的结果：

- `SERVERPROPERTY('EngineEdition')` 返回值 8。 此属性唯一标识托管实例。 请参阅 [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)。
- `SERVERPROPERTY('InstanceName')` 返回 NULL，因为 SQL Server 存在的实例概念并不适用于托管实例。 请参阅 [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)。
- `@@SERVERNAME`返回完整 DNS "可连接" 名称, 例如, my-managed-instance.wcus17662feb9ce98.database.windows.net。 请参阅 [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql)。 
- `SYS.SERVERS`返回完整 DNS "可连接" 名称, 如`myinstance.domain.database.windows.net`属性 "name" 和 "data_source"。 请参阅 [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)。
- `@@SERVICENAME` 返回 NULL，因为 SQL Server 存在的服务概念并不适用于托管实例。 请参阅 [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql)。
- 支持 `SUSER_ID`。 如果 Azure AD 登录名不在 sys.syslogins 中，则返回 NULL。 请参阅 [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql)。 
- 不支持 `SUSER_SID`。 将返回错误数据，这是暂时性的已知问题。 请参阅 [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql)。 

## <a name="Issues"></a>已知问题和限制

### <a name="tempdb-size"></a>TEMPDB 大小

在“常规用途”层级上，`tempdb` 的最大文件大小不能超过 24 GB 每核心。 业务关键层`tempdb`上的最大大小受实例存储大小的限制。 `Tempdb`在常规用途和业务关键层上, 日志文件大小限制为 120 GB。 `tempdb` 数据库始终拆分为 12 个数据文件。 无法更改每个文件的最大大小, 并且无法将新文件添加`tempdb`到。 如果中的`tempdb`每个核心需要超过 24 gb, 或者它们产生超过 120 gb 的日志数据, 则某些查询可能会返回错误。 `Tempdb`当实例启动或故障转移时, 始终会重新创建为空数据库, 在中`tempdb`所做的任何更改都不会保留。 

### <a name="cant-restore-contained-database"></a>无法还原包含的数据库

托管实例无法还原[包含的数据库](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)。 现有包含数据库的时点还原不适用于托管实例。 同时, 我们建议您从放置在托管实例上的数据库中删除包含选项。 不要对生产数据库使用包含选项。 

### <a name="exceeding-storage-space-with-small-database-files"></a>小型数据库文件超出存储空间

`CREATE DATABASE`、`ALTER DATABASE ADD FILE` 和 `RESTORE DATABASE` 语句可能会失败，因为实例可能会达到 Azure 存储限制。

每个“常规用途”托管实例都为 Azure 高级磁盘空间保留了最多 35 TB 存储空间。 每个数据库文件放置在单独的物理磁盘上。 磁盘大小可以为 128 GB、256 GB、512 GB、1 TB 或 4 TB。 磁盘上未使用的空间不收费，但 Azure 高级磁盘大小总计不能超过 35 TB。 在某些情况下，由于内部碎片，总共不需要 8 TB 的托管实例可能会超过 35 TB 的 Azure 存储大小限制。

例如, 一个常规用途的托管实例可能有一个大文件, 大小为 1.2 TB, 大小为 4 TB 的磁盘。 它还可能具有248个文件, 每个文件都位于单独的 128 GB 磁盘上。 在本示例中：

- 分配的磁盘存储总大小为 1 x 4 TB + 248 x 128 GB = 35 TB。
- 实例上的数据库的总预留空间为 1 x 1.2 TB + 248 x 1 GB = 1.4 TB。

此示例演示在某些情况下，由于文件的具体分布，托管实例可能会出乎意料地达到为附加的 Azure 高级磁盘预留的 35 TB 存储空间大小限制。

在此示例中，只要未添加新文件，现有数据库就会继续工作并且可以毫无问题地增长。 由于没有足够的空间用于新磁盘驱动器，因此无法创建或还原新数据库，即使所有数据库的总大小未达到实例大小限制也是如此。 这种情况下返回的错误并不明确。

可以使用系统视图[识别剩余文件的数目](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)。 如果即将达到此限制，请尝试[使用 DBCC SHRINKFILE 语句清空并删除一些小型文件](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)，或者切换到[没有此限制的“业务关键”层级](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。

### <a name="tooling"></a>工具

访问托管实例时，SQL Server Management Studio 和 SQL Server Data Tools 可能会出现一些问题。

- 目前不支持将 Azure AD 服务器主体（登录名）和用户（公共预览版）与 SQL Server Data Tools 配合使用。
- Azure AD 服务器主体（登录名）和用户（公共预览版）的脚本在 SQL Server Management Studio 中不受支持。

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>在某些视图、日志和消息中，数据库名称不正确

多个系统视图、性能计数器、错误消息、XEvent 和错误日志条目显示了 GUID 数据库标识符而非实际的数据库名称。 不要依赖这些 GUID 标识符，因为它们在将来会被替换为实际的数据库名称。

### <a name="database-mail"></a>数据库邮件

[sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) 过程中的 `@query` 参数不起作用。

### <a name="database-mail-profile"></a>数据库邮件配置文件

SQL Server 代理使用的数据库邮件配置文件必须名为 `AzureManagedInstance_dbmail_profile`。 在其他数据库邮件配置文件名称方面没有限制。

### <a name="error-logs-arent-persisted"></a>不保留错误日志

托管实例中可用的错误日志不会持久保存, 它们的大小不会包括在最大存储限制内。 在发生故障转移时可能会自动清除错误日志。

### <a name="error-logs-are-verbose"></a>错误日志是详细的

托管实例在错误日志中记录详细信息，其中许多信息都不相关。 

**解决方法：** 使用自定义过程读取已筛选出某些不相关条目的错误日志。 有关详细信息, 请参阅[托管实例– sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/)。

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>跨同一实例中的两个数据库的事务范围不受支持

如果在同一事务范围中将两个查询发送到了同一实例内的两个数据库，则 .NET 中的 `TransactionScope` 类不会工作。

```csharp
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

虽然此代码处理同一实例内的数据，但它需要 MSDTC。

**解决方法：** 使用 [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) 在连接上下文中使用其他数据库，而非使用两个连接。

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR 模块和链接的服务器有时无法引用本地 IP 地址

放置在托管实例中的 CLR 模块，以及引用当前实例的链接服务器或分布式查询，有时可能无法解析本地实例的 IP。 此错误是暂时性问题。

**解决方法：** 如果可能，请在 CLR 模块中使用上下文连接。

### <a name="tde-encrypted-databases-with-a-service-managed-key-dont-support-user-initiated-backups"></a>采用服务托管密钥的 TDE 加密数据库不支持用户启动的备份

不能在使用服务托管透明数据加密 (TDE) 加密的数据库上执行 `BACKUP DATABASE ... WITH COPY_ONLY`。 服务托管的 TDE 强制使用内部 TDE 密钥对备份进行加密。 无法导出该密钥，因此无法还原备份。

**解决方法：** 使用自动备份和时间点还原，或者改用[客户托管 (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key)。 也可以在数据库上禁用加密。

## <a name="next-steps"></a>后续步骤

- 有关托管实例的详细信息，请参阅[什么是托管实例？](sql-database-managed-instance.md)
- 有关功能和比较列表，请参阅 [Azure SQL 数据库功能比较](sql-database-features.md)。
- 有关如何新建托管实例的快速入门，请参阅[创建托管实例](sql-database-managed-instance-get-started.md)。
