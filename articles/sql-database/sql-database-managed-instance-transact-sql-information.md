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
ms.openlocfilehash: d84e52878c285ddd66fd799efe8c0f3cd2fc3e31
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358443"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Azure SQL 数据库托管实例与 SQL Server 之间的 T-SQL 差异

本文总结并介绍 Azure SQL 数据库托管实例和本地 SQL Server 数据库引擎之间的语法和行为差异。 <a name="Differences"></a>

- [可用性](#availability)包括 [Always-On](#always-on-availability) 和[备份](#backup)方面的差异
- [安全性](#security)包括[审核](#auditing)、[证书](#certificates)、[凭据](#credential)、[加密提供程序](#cryptographic-providers)、[登录名/用户名](#logins--users)、[服务密钥和服务主密钥](#service-key-and-service-master-key)方面的差异
- [配置](#configuration)包括[缓冲池扩展](#buffer-pool-extension)、[排序规则](#collation)、[兼容性级别](#compatibility-levels)、[数据库镜像](#database-mirroring)、[数据库选项](#database-options)、[SQL Server 代理](#sql-server-agent)、[表选项](#tables)方面的差异
- [功能](#functionalities)包括 [BULK INSERT/OPENROWSET](#bulk-insert--openrowset)、[CLR](#clr)、[DBCC](#dbcc)、[分布式事务](#distributed-transactions)、[已扩展事件](#extended-events)、[外部库](#external-libraries)、[文件流和文件表](#filestream-and-filetable)、[全文语义搜索](#full-text-semantic-search)、[链接服务器](#linked-servers)、[Polybase](#polybase)、[复制](#replication)、[还原](#restore-statement)、[Service Broker](#service-broker)、[存储过程、函数和触发器](#stored-procedures-functions-triggers)方面的差异
- [在托管实例中具有不同行为的功能](#Changes)
- [临时限制和已知的问题](#Issues)

托管实例部署选项与本地 SQL Server 数据库引擎高度兼容。 托管实例支持大多数 SQL Server 数据库引擎功能。

![迁移](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>可用性

### <a name="always-on-availability"></a>Always-On

[高可用性](sql-database-high-availability.md)内置在托管实例中，不能由用户控制。 不支持以下语句：

- [创建终结点... FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [创建可用性组](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [删除可用性组](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) 语句的 [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) 子句

### <a name="backup"></a>备份

托管实例包含自动备份，可便于用户创建完整数据库 `COPY_ONLY` 备份。 不支持差异、日志和文件快照备份。

- 使用托管实例，可以只将实例数据库备份到 Azure Blob 存储帐户：
  - 仅支持 `BACKUP TO URL`
  - `FILE``TAPE`，和不支持备份设备  
- 支持大多数常规 `WITH` 选项
  - `COPY_ONLY` 是必需的
  - `FILE_SNAPSHOT` 不支持
  - 不支持磁带选项 `REWIND`、`NOREWIND`、`UNLOAD` 和 `NOUNLOAD`
  - 不支持日志特定的选项 `NORECOVERY`、`STANDBY` 和 `NO_TRUNCATE`

限制：  

- 使用托管实例，可以将实例数据库备份到最多包含 32 个带区的备份（如果使用备份压缩，这种方法对不超过 4TB 的数据库够用）。
- 最大备份条带大小使用`BACKUP`托管实例中的命令为 195 GB （最大 blob 大小）。 在 backup 命令中增加条带数目可以减小单个条带的大小，并保持在此限制范围内。

    > [!TIP]
    > 若要解决此限制，从 SQL Server 的本地环境中或在虚拟机中备份数据库时，请执行以下操作：
    >
    > - 备份到`DISK`而不是备份到 `URL`
    > - 备份文件上传到 Blob 存储
    > - 还原到托管实例
    >
    > `Restore`托管实例中的命令在因为不同的 blob 类型用于上传备份文件存储在备份文件中支持更大 blob 大小。

有关使用 T-SQL 进行备份的信息，请参阅 [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql)。

## <a name="security"></a>安全

### <a name="auditing"></a>审核

在审核 Azure SQL 数据库和 SQL Server 中的数据库方面，主要差异是：

- 使用 Azure SQL 数据库中的托管实例部署选项，审核在服务器级别执行，并在 Azure Blob 存储中存储 `.xel` 日志文件。
- 使用 Azure SQL 数据库中的单一数据库和弹性池部署选项，审核是在数据库一级执行。
- 在本地 SQL Server/虚拟机上的 SQL Server 中，审核是在服务器一级执行，但却在文件系统/Windows 事件日志中存储事件。
  
托管实例中的 XEvent 审核支持 Azure Blob 存储目标。 不支持文件和 Windows 日志。

Azure Blob 存储审核的主要 `CREATE AUDIT` 语法差异为：

- 提供新语法 `TO URL`，允许指定用于放置 `.xel` 文件的 Azure Blob 存储容器的 URL
- 不支持语法 `TO FILE`，因为托管实例无法访问 Windows 文件共享。

有关详细信息，请参阅：  

- [创建服务器审核](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>证书

由于托管实例无法访问文件共享和 Windows 文件夹，因此存在以下约束：

- `CREATE FROM`/`BACKUP TO` 文件不受支持的证书
- `CREATE`/`BACKUP` 从证书`FILE` / `ASSEMBLY`不受支持。 无法使用私钥文件。  

请参阅 [CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) 和 [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql)。  
  
**解决方法**：编写证书/私钥的脚本，存储为 .sql 文件，然后从二进制文件创建证书：

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

- `CREATE CRYPTOGRAPHIC PROVIDER` 不受支持。 请参阅 [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql)。
- `ALTER CRYPTOGRAPHIC PROVIDER` 不受支持。 请参阅 [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)。

### <a name="logins--users"></a>登录名/用户

- 支持使用 `FROM CERTIFICATE`、`FROM ASYMMETRIC KEY` 和 `FROM SID` 创建的 SQL 登录名。 请参阅 [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql)。
- 支持使用 [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) 语法或 [CREATE USER FROM LOGIN [Azure AD 登录名]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) 语法创建的 Azure Active Directory (Azure AD) 服务器主体（登录名）（**公共预览版**）。 这些登录名是在服务器级别创建的。

    托管实例支持使用语法 `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` 的 Azure AD 数据库主体。 这也称为 Azure AD 包含的数据库用户。

- 不支持使用 `CREATE LOGIN ... FROM WINDOWS` 语法创建的 Windows 登录名。 使用 Azure Active Directory 登录名和用户。
- 创建实例的 Azure AD 用户具有[不受限制的管理特权](sql-database-manage-logins.md#unrestricted-administrative-accounts)。
- 可以使用 `CREATE USER ... FROM EXTERNAL PROVIDER` 语法创建非管理员 Azure Active Directory (Azure AD) 数据库级用户。 请参阅 [CREATE USER ...FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users)。
- Azure AD 服务器主体（登录名）仅支持一个 MI 实例中的 SQL 功能。 无论是在相同还是不同的 Azure AD 租户中，需要跨实例交互的功能都不支持 Azure AD 用户。 此类功能的示例包括：

  - SQL 事务复制
  - 链接服务器

- 不支持设置映射到作为数据库所有者的 Azure AD 组的 Azure AD 登录名。
- 支持使用其他 Azure AD 主体模拟 Azure AD 服务器级主体，例如 [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) 子句。 EXECUTE AS 限制：

  - 当名称不同于登录名时，EXECUTE AS USER 不支持 Azure AD 用户。 例如，如果用户是通过语法 CREATE USER [myAadUser] FROM LOGIN [john@contoso.com] 创建的，则会尝试通过 EXEC AS USER = _myAadUser_ 进行模拟。 基于 Azure AD 服务器主体（登录名）创建 **USER** 时，请指定与 **LOGIN** 中的 login_name 相同的 user_name。
  - 只有属于 `sysadmin` 角色的 SQL 服务器级主体（登录名）可以针对 Azure AD 主体执行以下操作：

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Azure AD 服务器主体（登录名）的**公共预览版**限制：

  - 托管实例的 Active Directory 管理员限制：

    - 用于设置托管实例的 Azure AD 管理员不可用于在托管实例中创建 Azure AD 服务器主体（登录名）。 必须使用充当 `sysadmin` 的 SQL Server 帐户创建第一个 Azure AD 服务器主体（登录名）。 Azure AD 服务器主体（登录名）的正式版推出后，即会去除这种暂时性限制。 如果尝试使用 Azure AD 管理员帐户创建登录名，将看到以下错误： `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - 目前，在 master 数据库中创建的第一个 Azure AD 登录名必须由充当 `sysadmin` 的标准 SQL Server 帐户（非 Azure AD）使用 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) FROM EXTERNAL PROVIDER 创建。 正式版推出后，将去除此限制，初始的 Azure AD 登录名可由托管实例的 Active Directory 管理员创建。
    - 与 SQL Server Management Studio (SSMS) 或 SqlPackage 配合使用的 DacFx（导出/导入）不支持 Azure AD 登录名。 Azure AD 服务器主体（登录名）的正式版推出后，即会去除此限制。
    - 将 Azure AD 服务器主体（登录名）与 SSMS 配合使用

      - 不支持编写 Azure AD 登录名的脚本（使用任何经过身份验证的登录名）。
      - Intellisense 无法识别 **CREATE LOGIN FROM EXTERNAL PROVIDER** 语句，将显示红色下划线。

- 只有服务器级主体登录名（由托管实例预配进程创建）、服务器角色的成员（`securityadmin` 或 `sysadmin`）或者在服务器级别拥有 ALTER ANY LOGIN 权限的其他登录名可以在托管实例的 master 数据库中创建 Azure AD 服务器主体（登录名）。
- 如果登录名是 SQL 主体，则只有属于 `sysadmin` 角色的登录名才能使用 create 命令来为 Azure AD 帐户创建登录名。
- Azure AD 登录名必须是用于 Azure SQL 托管实例的同一目录中的 Azure AD 成员。
- 从 SSMS 18.0 预览版 5 开始，Azure AD 服务器主体（登录名）将显示在对象资源管理器中。
- 允许 Azure AD 服务器主体（登录名）与 Azure AD 管理员帐户重叠。 解析主体以及将权限应用到托管实例时，Azure AD 服务器主体（登录名）优先于 Azure AD 管理员。
- 在身份验证期间，将应用以下顺序来解析身份验证主体：

    1. 如果 Azure AD 帐户存在并直接映射到 Azure AD 服务器主体（登录名）（以类型“E”的形式存在于 sys.server_principals 中），则授予访问权限并应用 Azure AD 服务器主体（登录名）的权限。
    2. 如果 Azure AD 帐户是映射到 Azure AD 服务器主体（登录名）的 Azure AD 组的成员（以类型“X”的形式存在于 sys.server_principals 中），则授予访问权限并应用 Azure AD 组登录名的权限。
    3. 如果 Azure AD 帐户是在门户中配置的、托管实例的特殊 Azure AD 管理员（不存在于托管实例系统视图中），则应用托管实例的 Azure AD 管理员的特殊固定权限（传统模式）。
    4. 如果 Azure AD 帐户存在并直接映射到数据库中的 Azure AD 用户（以类型“E”的形式存在于 sys.database_principals 中），则授予访问权限并应用 Azure AD 数据库用户的权限。
    5. 如果 Azure AD 帐户是映射到数据库中 Azure AD 用户的 Azure AD 组的成员（以类型“X”的形式存在于 sys.database_principals 中），则授予访问权限并应用 Azure AD 组登录名的权限。
    6. 如果某个 Azure AD 登录映射到 Azure AD 用户帐户或 Azure AD 组帐户并解析为用户身份验证，则应用此 Azure AD 登录名中的所有权限。

### <a name="service-key-and-service-master-key"></a>服务密钥和服务主密钥

- 不支持[主密钥备份](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql)（由 SQL 数据库服务管理）
- 不支持[主密钥还原](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql)（由 SQL 数据库服务管理）
- 不支持[服务主密钥备份](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql)（由 SQL 数据库服务管理）
- 不支持[服务主密钥还原](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql)（由 SQL 数据库服务管理）

## <a name="configuration"></a>配置

### <a name="buffer-pool-extension"></a>缓冲池扩展

- 不支持[缓冲池扩展](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension)。
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` 不受支持。 请参阅 [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql)。

### <a name="collation"></a>Collation

默认实例排序规则为 `SQL_Latin1_General_CP1_CI_AS` 并可以被指定为创建参数。 请参阅[排序规则](https://docs.microsoft.com/sql/t-sql/statements/collations)。

### <a name="compatibility-levels"></a>兼容级别

- 支持的兼容级别：100、110、120、130、140  
- 不支持低于 100 的兼容级别。
- 新数据库的默认兼容级别为 140。 对于已还原的数据库，如果其兼容级别在还原之前为 100 或更高，则还原后保持不变。

请参阅 [ALTER DATABASE 兼容级别](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。

### <a name="database-mirroring"></a>数据库镜像

不支持数据库镜像。

- `ALTER DATABASE SET PARTNER` 和`SET WITNESS`选项不受支持。
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` 不受支持。

有关详细信息，请参阅 [ALTER DATABASE SET PARTNER 和 SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 以及 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)。

### <a name="database-options"></a>数据库选项

- 不支持多个日志文件。
- 常规用途服务层不支持内存中对象。  
- 没有 280 个文件每个常规用途实例，这意味着每个数据库的最大 280 文件限制。 数据和日志文件通常层都将计入此限制的目的。 [业务关键层支持每个数据库的 32,767 文件](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。
- 数据库中不能有包含文件流数据的文件组。  如果 .bak 包含 `FILESTREAM` 数据，则还原将会失败。  
- 每个文件都被放置在 Azure Blob 存储中。 每个文件的 IO 和吞吐量取决于每个单独文件的大小。  

#### <a name="create-database-statement"></a>CREATE DATABASE 语句

`CREATE DATABASE` 有以下限制：

- 无法定义文件和文件组。  
- `CONTAINMENT` 不支持选项。  
- `WITH`不支持选项。  
   > [!TIP]
   > 解决方法是在 `CREATE DATABASE` 后面使用 `ALTER DATABASE` 来设置数据库选项，以添加文件或设置包含。  

- `FOR ATTACH` 不支持选项
- `AS SNAPSHOT OF` 不支持选项

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

- SQL 代理设置为只读。 托管实例不支持过程 `sp_set_agent_properties`。  
- 作业
  - 支持 T-SQL 作业步骤。
  - 支持以下复制作业：
    - 事务日志读取器
    - 快照
    - 分发服务器
  - 支持 SSIS 作业步骤
  - 目前不支持其他类型的作业步骤，包括：
    - 不支持合并复制作业步骤。  
    - 不支持队列读取器。  
    - 尚不支持命令外壳。
  - 托管实例无法访问外部资源（例如，通过 robocopy 访问网络共享）。  
  - 不支持 Analysis Services。
- 部分支持通知。
- 支持电子邮件通知，但需要配置数据库邮件配置文件。 SQL 代理可以使用只有一个数据库邮件配置文件和必须调用`AzureManagedInstance_dbmail_profile`。  
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

## <a name="functionalities"></a>功能

### <a name="bulk-insert--openrowset"></a>批量插入/openrowset

由于托管实例无法访问文件共享和 Windows 文件夹，必须从 Azure Blob 存储导入文件：

- `DATASOURCE` 在所需`BULK INSERT`命令从 Azure Blob 存储导入文件时。 请参阅 [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql)。
- `DATASOURCE` 在所需`OPENROWSET`函数时从 Azure Blob 存储中读取文件的内容。 请参阅 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)。

### <a name="clr"></a>CLR

由于托管实例无法访问文件共享和 Windows 文件夹，因此存在以下约束：

- 仅支持 `CREATE ASSEMBLY FROM BINARY`。 请参阅 [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)。  
- `CREATE ASSEMBLY FROM FILE` 不受支持。 请参阅 [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)。
- `ALTER ASSEMBLY` 不能引用文件。 请参阅 [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql)。

### <a name="dbcc"></a>DBCC

托管实例不支持 SQL Server 中启用的未记录 DBCC 语句。

- `Trace Flags` 不受支持。 请参阅[跟踪标志](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)。
- `DBCC TRACEOFF` 不受支持。 请参阅 [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql)。
- `DBCC TRACEON` 不受支持。 请参阅 [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql)。

### <a name="distributed-transactions"></a>分布式事务

托管实例暂不支持 MSDTC 和[弹性事务](sql-database-elastic-transactions-overview.md)。

### <a name="extended-events"></a>扩展事件

不支持对 XEvents 使用某些特定于 Windows 的目标：

- `etw_classic_sync target` 不受支持。 在 Azure Blob 存储中存储 `.xel` 文件。 请参阅 [etw_classic_sync 目标](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)。
- `event_file target` 不受支持。 在 Azure Blob 存储中存储 `.xel` 文件。 请参阅 [event_file 目标](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)。

### <a name="external-libraries"></a>外部库

尚不支持数据库中 R 和 Python 外部库。 请参阅 [SQL Server 机器学习服务](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services)。

### <a name="filestream-and-filetable"></a>文件流和文件表

- 不支持文件流数据。
- 数据库中不能有包含 `FILESTREAM` 数据的文件组。
- `FILETABLE` 不受支持。
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

- 支持的目标：SQL Server 和 SQL 数据库
- 不支持的目标：文件、Analysis Services 和其他 RDBMS。

操作

- 不支持跨实例写入事务。
- `sp_dropserver` 删除链接的服务器的支持。 请参阅 [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)。
- `OPENROWSET` 函数可用于仅在 SQL Server 实例上执行查询 (或者，在本地管理，或在虚拟机中)。 请参阅 [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)。
- `OPENDATASOURCE` 函数可用于仅在 SQL Server 实例上执行查询 (或者，在本地管理，或在虚拟机中)。 仅支持将 `SQLNCLI`、`SQLNCLI11` 和 `SQLOLEDB` 值用作提供程序。 例如：`SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`。 请参阅 [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)。

### <a name="polybase"></a>Polybase

不支持引用 HDFS 或 Azure Blob 存储中文件的外部表。 有关 Polybase 的信息，请参阅 [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)。

### <a name="replication"></a>复制

复制功能适用于托管实例公共预览版。 有关复制的信息，请参阅 [SQL Server 复制](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance)。

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
- 源  
  - `FROM URL` （azure Blob 存储） 是受支持的选项。
  - `FROM DISK`/`TAPE`/ 备份设备不受支持。
  - 不支持备份集。
- `WITH` 选项不受支持 (无`DIFFERENTIAL`， `STATS`，等等。)
- `ASYNC RESTORE` 即使客户端连接中断，将继续还原。 如果删除了连接，可以在 `sys.dm_operation_status` 视图中检查还原操作的状态（以及 CREATE DATABASE 和 DROP DATABASE 的状态）。 请参阅 [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)。  

将设置/重写以下数据库选项，以后无法更改：  

- `NEW_BROKER` （如果.bak 文件中未启用中转站）  
- `ENABLE_BROKER` （如果.bak 文件中未启用中转站）  
- `AUTO_CLOSE=OFF` (如果.bak 文件中的数据库具有`AUTO_CLOSE=ON`)  
- `RECOVERY FULL` (如果.bak 文件中的数据库具有`SIMPLE`或`BULK_LOGGED`恢复模式)
- 添加源 .bak 文件中不包含内存优化文件组，则会添加名为 XTP 的内存优化文件组  
- 任何现有的内存优化文件组将重命名为 XTP  
- `SINGLE_USER` 和`RESTRICTED_USER`选项转换为 `MULTI_USER`

限制：  

- `.BAK` 无法还原包含多个备份集的文件。
- `.BAK` 无法还原包含多个日志文件的文件。
- 如果 .bak 包含 `FILESTREAM` 数据，则还原将会失败。
- 不能在常规用途实例上还原备份中包含具有活动的内存中对象的数据库备份。  
有关 Restore 语句的信息，请参阅 [RESTORE 语句](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)。

### <a name="service-broker"></a>服务代理

不支持跨实例 Service Broker：

- `sys.routes` -先决条件： 从 sys.routes 中选择地址。 地址必须在每个路由的本地。 请参阅 [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)。
- `CREATE ROUTE` -不能使用`CREATE ROUTE`与`ADDRESS`而不`LOCAL`。 请参阅 [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql)。
- `ALTER ROUTE` 不能`ALTER ROUTE`与`ADDRESS`而不`LOCAL`。 请参阅 [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql)。  

### <a name="stored-procedures-functions-triggers"></a>存储过程、函数和触发器

- `NATIVE_COMPILATION` 在常规用途层不支持。
- 不支持以下 [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 选项：
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` 不受支持。 请参阅 [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)。
- `xp_cmdshell` 不受支持。 请参阅 [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)。
- `Extended stored procedures` 不支持，包括`sp_addextendedproc` 和`sp_dropextendedproc`。 请参阅[扩展存储过程](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)
- `sp_attach_db``sp_attach_single_file_db`，和`sp_detach_db`不受支持。 请参阅 [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql)、[sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 和 [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)。

## <a name="Changes"></a>行为更改

以下变量、函数和视图返回不同的结果：

- `SERVERPROPERTY('EngineEdition')` 返回值 8。 此属性唯一标识托管实例。 请参阅 [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)。
- `SERVERPROPERTY('InstanceName')` 返回 NULL，因为 SQL Server 不能应用于托管实例存在的实例作为其概念。 请参阅 [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)。
- `@@SERVERNAME` 返回完整 DNS 可连接名称，例如，我管理 instance.wcus17662feb9ce98.database.windows.net。 请参阅 [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql)。  
- `SYS.SERVERS` -返回完整的可连接的 DNS 名称，例如`myinstance.domain.database.windows.net`属性 name 和 data_source。 请参阅 [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)。
- `@@SERVICENAME` 返回 NULL，因为它与服务的概念存在 SQL Server 不会应用到托管实例。 请参阅 [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql)。
- `SUSER_ID` 支持。 如果 Azure AD 登录名不在 sys.syslogins 中，则返回 NULL。 请参阅 [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql)。  
- `SUSER_SID` 不受支持。 返回错误数据（暂时性的已知问题）。 请参阅 [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql)。
- `GETDATE()` 和其他内置日期/时间函数始终返回采用 UTC 时区的时间。 请参阅 [GETDATE](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql)。

## <a name="Issues"></a>已知问题和限制

### <a name="tempdb-size"></a>TEMPDB 大小

最大文件大小`tempdb`不能大于 24 GB/core 常规用途层上。 最大`tempdb`业务关键层上的大小是与实例存储大小。 `tempdb` 始终为 12 个数据文件拆分。 无法更改每个文件的最大大小，并且无法将新文件添加到 `tempdb`。 某些查询可能会返回错误，如果需要超过 24 GB / 核心中`tempdb`。

### <a name="cannot-restore-contained-database"></a>无法还原包含的数据库

无法还原托管的实例[包含的数据库](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)。 托管实例无法运行现有包含数据库的时点还原。 很快将删除此问题，并在此期间，我们建议从您放置在托管实例，请不要用于生产数据库的包含关系选项的数据库中删除包含关系选项。

### <a name="exceeding-storage-space-with-small-database-files"></a>小型数据库文件超出存储空间

`CREATE DATABASE ``ALTER DATABASE ADD FILE`，和`RESTORE DATABASE`语句可能会失败，因为该实例可以访问 Azure 存储限制。

35 TB 的存储为 Azure 高级磁盘空间保留的每个常规用途托管实例会带来高达并每个数据库文件放置在单独的物理磁盘上。 磁盘大小可以为 128 GB、256 GB、512 GB、1 TB 或 4 TB。 磁盘上未使用的空间不收费，但 Azure 高级磁盘大小总计不能超过 35 TB。 在某些情况下，由于内部碎片，总共不需要 8 TB 的托管实例可能会超过 35 TB 的 Azure 存储大小限制。

例如，常规用途托管实例可以将一个文件中 4 TB 磁盘放置的大小和 248 放置在单独的 128 GB 磁盘的文件 (每个 1 GB 的大小) 1.2 TB。 在本示例中：

- 分配的磁盘存储总大小为 1 x 4 TB + 248 x 128 GB = 35 TB。
- 实例上的数据库的总预留空间为 1 x 1.2 TB + 248 x 1 GB = 1.4 TB。

这说明在某些情况下，由于文件分布很具体，托管实例可能会出乎意料地达到为附加的 Azure 高级磁盘预留的 35 TB。

在此示例中，只要未添加新文件，现有数据库就会继续工作并且可以毫无问题地增长。 但是，由于没有足够的空间用于新磁盘驱动器，因此无法创建或还原新数据库，即使所有数据库的总大小未达到实例大小限制也是如此。 这种情况下返回的错误并不明确。

你可以[确定剩余的文件的数目](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)使用系统视图。 如果您达到此限制尝试[的空和删除一些较小的文件使用 DBCC SHRINKFILE 语句](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)或切换到[不的业务关键层都有此限制](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>在还原数据库期间不正确地配置了 SAS 密钥

`RESTORE DATABASE` 它读取.bak 文件可能会不断重试长一段时间后读取.bak 文件，并返回错误，如果在共享访问签名`CREDENTIAL`不正确。 请在还原数据库之前执行 RESTORE HEADERONLY，确保 SAS 密钥正确。
确保从使用 Azure 门户生成的 SAS 密钥中删除前导 `?`。

### <a name="tooling"></a>工具

访问托管实例时，SQL Server Management Studio (SSMS) 和 SQL Server Data Tools (SSDT) 可能会出现一些问题。

- 目前不支持将 Azure AD 服务器主体（登录名）和用户（**公共预览版**）用于 SSDT。
- Azure AD 服务器主体（登录名）和用户（**公共预览版**）的脚本在 SSMS 中不受支持。

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>在某些视图、日志和消息中，数据库名称不正确

多个系统视图、性能计数器、错误消息、XEvent 和错误日志条目显示了 GUID 数据库标识符而非实际的数据库名称。 不要依赖这些 GUID 标识符，因为它们在将来会被替换为实际的数据库名称。

### <a name="database-mail"></a>数据库邮件

`@query` 中的参数[sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql)过程不起作用。

### <a name="database-mail-profile"></a>数据库邮件配置文件

SQL 代理使用的数据库邮件配置文件必须在调用`AzureManagedInstance_dbmail_profile`。 没有限制，有关其他数据库邮件配置文件名称。

### <a name="error-logs-are-not-persisted"></a>错误日志不会持久保留

托管实例中可用的错误日志不会持久保留，并且它们的大小不包括在最大存储限制中。 在发生故障转移时可能会自动清除错误日志。

### <a name="error-logs-are-verbose"></a>错误日志是详细的

托管实例在错误日志中记录详细信息，其中许多信息都不相关。 将来，错误日志中的信息量将减少。

**解决方法**：使用自定义过程来读取错误日志，以便过滤掉某些不相关的条目。 有关详细信息，请参阅[托管实例 - sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/)。

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>跨同一实例中的两个数据库的事务范围不受支持

`TransactionScope` 如果两个查询发送到同一个事务范围内的相同实例中的两个数据库，在.NET 中的类不起作用：

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

虽然此代码处理同一实例内的数据，但它需要 MSDTC。

**解决方法**：使用 [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) 在连接上下文中使用其他数据库，而非使用两个连接。

### <a name="clr-modules-and-linked-servers-sometime-cant-reference-local-ip-address"></a>CLR 模块和链接的服务器有时无法引用本地 IP 地址

放置在托管实例中的 CLR 模块，以及引用当前实例的链接服务器/分布式查询，有时可能无法解析本地实例的 IP。 此错误是暂时性问题。

**解决方法**：如果可能，请在 CLR 模块中使用上下文连接。

### <a name="tde-encrypted-databases-dont-support-user-initiated-backups"></a>TDE 加密数据库不支持用户启动的备份

不能在使用透明数据加密 (TDE) 加密的数据库上执行 `BACKUP DATABASE ... WITH COPY_ONLY`。 TDE 强制使用内部 TDE 密钥对备份进行加密，并且该密钥无法导出，因此将无法还原备份。

**解决方法**：使用自动备份和时点还原，或在数据库上禁用加密。

## <a name="next-steps"></a>后续步骤

- 若要详细了解托管实例，请参阅[什么是托管实例？](sql-database-managed-instance.md)
- 有关功能和比较列表，请参阅 [SQL 常用功能](sql-database-features.md)。
- 有关演示了如何新建托管实例的快速入门，请参阅[创建托管实例](sql-database-managed-instance-get-started.md)。
