---
title: 托管实例 T-sql 差异
description: 本文介绍了 Azure SQL 数据库托管实例与 SQL Server 的 T-SQL 差异
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
ms.date: 11/04/2019
ms.custom: seoapril2019
ms.openlocfilehash: 636fd5fd17838c729cdbc9e2a322c1f991d93948
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186430"
---
# <a name="managed-instance-t-sql-differences-limitations-and-known-issues"></a>托管实例的 T-SQL 差异、限制和已知问题

本文汇总并解释了 Azure SQL 数据库托管实例与本地 SQL Server 数据库引擎之间的语法和行为差异。 托管实例部署选项与本地 SQL Server 数据库引擎高度兼容。 托管实例支持大多数 SQL Server 数据库引擎功能。

![迁移](./media/sql-database-managed-instance/migration.png)

与 SQL Server 相比，托管实例中引入了一些 PaaS 限制，并且在行为方面有一些变化。 这些差异划分为以下几个类别：<a name="Differences"></a>

- [可用性](#availability)包括 [Always-On](#always-on-availability) 和[备份](#backup)方面的差异。
- [安全性](#security)包括[审核](#auditing)、[证书](#certificates)、[凭据](#credential)、[加密提供程序](#cryptographic-providers)、[登录名和用户名](#logins-and-users)以及[服务密钥和服务主密钥](#service-key-and-service-master-key)方面的差异。
- [配置](#configuration)包括[缓冲池扩展](#buffer-pool-extension)、[排序规则](#collation)、[兼容性级别](#compatibility-levels)、[数据库镜像](#database-mirroring)、[数据库选项](#database-options)、[SQL Server 代理](#sql-server-agent)以及[表选项](#tables)方面的差异。
- [功能](#functionalities)包括 [BULK INSERT/OPENROWSET](#bulk-insert--openrowset)、[CLR](#clr)、[DBCC](#dbcc)、[分布式事务](#distributed-transactions)、[已扩展事件](#extended-events)、[外部库](#external-libraries)、[文件流和文件表](#filestream-and-filetable)、[全文语义搜索](#full-text-semantic-search)、[链接服务器](#linked-servers)、[Polybase](#polybase)、[复制](#replication)、[还原](#restore-statement)、[Service Broker](#service-broker)、[存储过程、函数和触发器](#stored-procedures-functions-and-triggers)。
- [环境设置](#Environment)，例如 VNet 和子网配置。

其中的大多数功能都是体系结构约束，代表服务功能。

本页还描述了在托管实例中发现的[暂时性已知问题](#Issues)，这些问题将来会得到解决。

## <a name="availability"></a>可用性

### <a name="always-on-availability"></a>Always On

[高可用性](sql-database-high-availability.md)内置在托管实例中，用户无法控制。 不支持以下语句：

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) 语句的 [SET HADR](/sql/t-sql/statements/alter-database-transact-sql) 子句

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
- 不能在使用服务托管透明数据加密 (TDE) 加密的数据库上执行 `BACKUP DATABASE ... WITH COPY_ONLY`。 服务托管的 TDE 强制使用内部 TDE 密钥对备份进行加密。 无法导出该密钥，因此无法还原备份。 使用自动备份和时间点还原，或者改用[客户托管 (BYOK) TDE](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key)。 也可以在数据库上禁用加密。
- 在托管实例中使用 `BACKUP` 命令最大可以设置 195 GB 的备份条带大小（即最大 Blob 大小）。 在 backup 命令中增加条带数目可以减小单个条带的大小，并保持在此限制范围内。

    > [!TIP]
    > 从本地环境或虚拟机中的 SQL Server 备份数据库时，若要解决此限制，可以：
    >
    > - 备份到 `DISK` 而不是 `URL`。
    > - 将备份文件上传到 Blob 存储。
    > - 还原到托管实例。
    >
    > 托管实例中的 `Restore` 命令支持备份文件中的更大 Blob 大小，因为将使用不同的 Blob 类型来存储上传的备份文件。

有关使用 T-SQL 进行备份的信息，请参阅 [BACKUP](/sql/t-sql/statements/backup-transact-sql)。

## <a name="security"></a>安全性

### <a name="auditing"></a>审核

在审核 Azure SQL 数据库和 SQL Server 中的数据库方面，主要差异是：

- 使用 Azure SQL 数据库中的托管实例部署选项时，审核将在服务器级别执行。 在 Azure Blob 存储中存储 `.xel` 日志文件。
- 使用 Azure SQL 数据库中的单一数据库和弹性池部署选项，审核是在数据库一级执行。
- 在本地 SQL Server 或虚拟机中，审核在服务器级别执行。 在文件系统或 Windows 事件日志中存储事件。
 
托管实例中的 XEvent 审核支持 Azure Blob 存储目标。 不支持文件和 Windows 日志。

Azure Blob 存储审核的主要 `CREATE AUDIT` 语法差异为：

- 提供了新语法 `TO URL`，用于指定 `.xel` 文件要放到的 Azure Blob 存储容器的 URL。
- 不支持语法 `TO FILE`，因为托管实例无法访问 Windows 文件共享。

有关详细信息，请参阅： 

- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [审核](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>证书

由于托管实例无法访问文件共享和 Windows 文件夹，因此存在以下约束：

- 不支持将 `CREATE FROM`/`BACKUP TO` 文件用于证书。
- 不支持 `CREATE`/`BACKUP` 中的 `FILE`/`ASSEMBLY` 证书。 无法使用私钥文件。 

请参阅 [CREATE CERTIFICATE](/sql/t-sql/statements/create-certificate-transact-sql) 和 [BACKUP CERTIFICATE](/sql/t-sql/statements/backup-certificate-transact-sql)。 
 
**解决方法**：创建证书[二进制内容和私钥，并将其存储为 .sql 文件，并从二进制文件创建](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)，而不是创建证书备份和还原备份。

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>凭据

仅支持 Azure Key Vault 和 `SHARED ACCESS SIGNATURE` 标识。 不支持 Windows 用户。

请参阅 [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql) 和 [ALTER CREDENTIAL](/sql/t-sql/statements/alter-credential-transact-sql)。

### <a name="cryptographic-providers"></a>加密提供程序

由于托管实例无法访问文件，因此无法创建加密提供程序：

- 不支持 `CREATE CRYPTOGRAPHIC PROVIDER`。 请参阅 [CREATE CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/create-cryptographic-provider-transact-sql)。
- 不支持 `ALTER CRYPTOGRAPHIC PROVIDER`。 请参阅 [ALTER CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)。

### <a name="logins-and-users"></a>登录名和用户

- 支持使用 `FROM CERTIFICATE`、`FROM ASYMMETRIC KEY` 和 `FROM SID` 创建的 SQL 登录名。 请参阅 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql)。
- 支持 Azure Active Directory （Azure AD）通过[创建登录](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)语法创建的服务器主体（登录名）或 "[从登录名创建用户 [Azure AD 登录名]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) " 语法。 这些登录名是在服务器级别创建的。

    托管实例支持使用语法 `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` 的 Azure AD 数据库主体。 此功能也称为 Azure AD 包含的数据库用户。

- 不支持使用 `CREATE LOGIN ... FROM WINDOWS` 语法创建的 Windows 登录名。 使用 Azure Active Directory 登录名和用户。
- 创建实例的 Azure AD 用户具有[不受限制的管理特权](sql-database-manage-logins.md#unrestricted-administrative-accounts)。
- 可以使用 `CREATE USER ... FROM EXTERNAL PROVIDER` 语法创建非管理员 Azure AD 数据库级用户。 请参阅[创建用户 .。。来自外部提供程序](sql-database-manage-logins.md#non-administrator-users)。
- Azure AD 服务器主体（登录名）仅支持一个托管实例中的 SQL 功能。 无论是在相同还是不同的 Azure AD 租户中，需要跨实例交互的功能都不支持 Azure AD 用户。 此类功能的示例包括：

  - SQL 事务复制。
  - 链接服务器。

- 不支持设置映射到作为数据库所有者的 Azure AD 组的 Azure AD 登录名。
- 支持使用其他 Azure AD 主体模拟 Azure AD 服务器级主体，例如 [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) 子句。 EXECUTE AS 限制如下：

  - 当名称不同于登录名时，EXECUTE AS USER 不支持 Azure AD 用户。 例如，如果用户是通过语法 CREATE USER [myAadUser] FROM LOGIN [john@contoso.com] 创建的，则会尝试通过 EXEC AS USER = _myAadUser_ 进行模拟。 基于 Azure AD 服务器主体（登录名）创建 **USER** 时，请指定与 **LOGIN** 中的 login_name 相同的 user_name。
  - 只有属于 `sysadmin` 角色的 SQL 服务器级主体（登录名）可以针对 Azure AD 主体执行以下操作：

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- 使用[SSMS v 18.4 或更高版本](/sql/ssms/download-sql-server-management-studio-ssms)或[SQLPackage](/sql/tools/sqlpackage-download)，托管实例中的 Azure AD 用户支持使用 bacpac 文件进行数据库导出/导入。
  - 使用数据库 bacpac 文件支持以下配置： 
    - 在同一 Azure AD 域中的不同管理实例之间导出/导入数据库。
    - 从托管实例中导出数据库，并将其导入到同一个 Azure AD 域中的 SQL 数据库。 
    - 从 SQL 数据库中导出数据库，并将其导入到同一个 Azure AD 域中的托管实例。
    - 从托管实例中导出数据库，并将其导入 SQL Server （2012版或更高版本）。
      - 在此配置中，所有 Azure AD 用户都作为不带登录名的 SQL 数据库主体（用户）创建。 用户的类型列为 SQL （在 sys. database_principals 中显示为 "SQL_USER"）。 它们的权限和角色保留在 SQL Server 数据库元数据中，并且可用于模拟。 但是，它们不能用于访问 SQL Server 使用其凭据进行登录。

- 只有服务器级主体登录名（由托管实例预配进程创建）、服务器角色的成员（例如 `securityadmin` 或 `sysadmin`）或者在服务器级别拥有 ALTER ANY LOGIN 权限的其他登录名可以在托管实例的 master 数据库中创建 Azure AD 服务器主体（登录名）。
- 如果登录名是 SQL 主体，则只有属于 `sysadmin` 角色的登录名才能使用 create 命令来为 Azure AD 帐户创建登录名。
- Azure AD 登录名必须是用于 Azure SQL 数据库托管实例的同一目录中的 Azure AD 成员。
- 从 SQL Server Management Studio 18.0 预览版 5 开始，Azure AD 服务器主体（登录名）将显示在对象资源管理器中。
- 允许 Azure AD 服务器主体（登录名）与 Azure AD 管理员帐户重叠。 解析主体以及将权限应用到托管实例时，Azure AD 服务器主体（登录名）优先于 Azure AD 管理员。
- 在身份验证期间，将应用以下顺序来解析身份验证主体：

    1. 如果 Azure AD 帐户存在并直接映射到 Azure AD 服务器主体（登录名）（以类型“E”的形式存在于 sys.server_principals 中），则授予访问权限并应用 Azure AD 服务器主体（登录名）的权限。
    2. 如果 Azure AD 帐户是映射到 Azure AD 服务器主体（登录名）的 Azure AD 组的成员（以类型“X”的形式存在于 sys.server_principals 中），则授予访问权限并应用 Azure AD 组登录名的权限。
    3. 如果 Azure AD 帐户是在门户中配置的、托管实例的特殊 Azure AD 管理员（不存在于托管实例系统视图中），则应用托管实例的 Azure AD 管理员的特殊固定权限（传统模式）。
    4. 如果 Azure AD 帐户存在并直接映射到数据库中的 Azure AD 用户（以类型“E”的形式存在于 sys.database_principals 中），则授予访问权限并应用 Azure AD 数据库用户的权限。
    5. 如果 Azure AD 帐户是映射到数据库中 Azure AD 用户的 Azure AD 组的成员（以类型“X”的形式存在于 sys.database_principals 中），则授予访问权限并应用 Azure AD 组登录名的权限。
    6. 如果某个 Azure AD 登录映射到 Azure AD 用户帐户或 Azure AD 组帐户并解析为用户身份验证，则应用此 Azure AD 登录名中的所有权限。

### <a name="service-key-and-service-master-key"></a>服务密钥和服务主密钥

- 不支持[主密钥备份](/sql/t-sql/statements/backup-master-key-transact-sql)（由 SQL 数据库服务管理）。
- 不支持[主密钥还原](/sql/t-sql/statements/restore-master-key-transact-sql)（由 SQL 数据库服务管理）。
- 不支持[服务主密钥备份](/sql/t-sql/statements/backup-service-master-key-transact-sql)（由 SQL 数据库服务管理）。
- 不支持[服务主密钥还原](/sql/t-sql/statements/restore-service-master-key-transact-sql)（由 SQL 数据库服务管理）。

## <a name="configuration"></a>配置

### <a name="buffer-pool-extension"></a>缓冲池扩展

- 不支持[缓冲池扩展](/sql/database-engine/configure-windows/buffer-pool-extension)。
- 不支持 `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`。 请参阅 [ALTER SERVER CONFIGURATION](/sql/t-sql/statements/alter-server-configuration-transact-sql)。

### <a name="collation"></a>Collation

默认实例排序规则为 `SQL_Latin1_General_CP1_CI_AS` 并可以被指定为创建参数。 请参阅[排序规则](/sql/t-sql/statements/collations)。

### <a name="compatibility-levels"></a>兼容级别

- 支持的兼容级别：100、110、120、130、140 和 150。
- 不支持低于 100 的兼容级别。
- 新数据库的默认兼容级别为 140。 对于已还原的数据库，如果其兼容级别在还原之前为 100 或更高，则还原后保持不变。

请参阅 [ALTER DATABASE 兼容级别](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。

### <a name="database-mirroring"></a>数据库镜像

不支持数据库镜像。

- 不支持 `ALTER DATABASE SET PARTNER` 和 `SET WITNESS` 选项。
- 不支持 `CREATE ENDPOINT … FOR DATABASE_MIRRORING`。

有关详细信息，请参阅 [ALTER DATABASE SET PARTNER 和 SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 以及 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)。

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
   > 解决方法是在 `ALTER DATABASE` 后面使用 `CREATE DATABASE` 来设置数据库选项，以添加文件或设置包含。 

- 不支持 `FOR ATTACH` 选项。
- 不支持 `AS SNAPSHOT OF` 选项。

有关详细信息，请参阅 [CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql)。

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

有关详细信息，请参阅 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options)。

### <a name="sql-server-agent"></a>SQL Server 代理

- 目前，托管实例不支持启用和禁用 SQL Server 代理。 SQL 代理始终运行。
- SQL Server 代理设置为只读。 托管实例不支持过程 `sp_set_agent_properties`。 
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

目前不支持以下 SQL 代理功能：

- 代理
- 针对空闲 CPU 计划作业
- 启用或禁用代理
- 警报

有关 SQL Server 代理的信息，请参阅 [SQL Server 代理](/sql/ssms/agent/sql-server-agent)。

### <a name="tables"></a>表

不支持以下表类型：

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) (Polybase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)（仅在“常规用途”层级中不受支持）

有关如何创建和更改表的信息，请参阅 [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) 和 [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql)。

## <a name="functionalities"></a>功能

### <a name="bulk-insert--openrowset"></a>BULK INSERT/OPENROWSET

由于托管实例无法访问文件共享和 Windows 文件夹，必须从 Azure Blob 存储导入文件：

- 从 Azure Blob 存储导入文件时，必须在 `DATASOURCE` 命令中指定 `BULK INSERT`。 请参阅 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql)。
- 从 Azure Blob 存储中读取文件内容时，必须在 `DATASOURCE` 函数中指定 `OPENROWSET`。 请参阅 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)。
- `OPENROWSET` 可以用来从其他 Azure SQL 单一数据库、托管实例或 SQL Server 实例读取数据。 其他资源（例如 Oracle 数据库或 Excel 文件）不受支持。

### <a name="clr"></a>CLR

由于托管实例无法访问文件共享和 Windows 文件夹，因此存在以下约束：

- 仅支持 `CREATE ASSEMBLY FROM BINARY`。 请参阅 [CREATE ASSEM BLY FROM BINARY](/sql/t-sql/statements/create-assembly-transact-sql)。 
- 不支持 `CREATE ASSEMBLY FROM FILE`。 请参阅 [CREATE ASSEMBLY FROM FILE](/sql/t-sql/statements/create-assembly-transact-sql)。
- `ALTER ASSEMBLY` 不能引用文件。 请参阅 [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql)。

### <a name="database-mail-db_mail"></a>数据库邮件 (db_mail)
 - `sp_send_dbmail` 无法使用 @file_attachments 参数发送附件。 在此过程中无法访问本地文件系统和外部共享或 Azure Blob 存储。
 - 请参阅与 `@query` 参数和身份验证相关的已知问题。
 
### <a name="dbcc"></a>DBCC

托管实例不支持 SQL Server 中启用的未记录 DBCC 语句。

- 仅支持有限数量的全局跟踪标志。 不支持会话级 `Trace flags`。 请参阅[跟踪标志](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)。
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) 和 [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) 使用有限数量的全局跟踪标志。
- 无法使用带有 REPAIR_ALLOW_DATA_LOSS、REPAIR_FAST 和 REPAIR_REBUILD 选项的 [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql)，因为无法在 `SINGLE_USER` 模式中设置数据库 - 请参阅 [ALTER DATABASE 的差异](#alter-database-statement)。 潜在的数据库损坏将由 Azure 支持团队处理。 如果你注意到应予以修复的数据库损坏，请联系 Azure 支持部门。

### <a name="distributed-transactions"></a>分布式事务

托管实例目前不支持 MSDTC 和[弹性事务](sql-database-elastic-transactions-overview.md)。

### <a name="extended-events"></a>扩展事件

不支持对扩展事件 (XEvent) 使用某些特定于 Windows 的目标：

- 不支持 `etw_classic_sync` 目标。 在 Azure Blob 存储中存储 `.xel` 文件。 请参阅 [etw_classic_sync 目标](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)。
- 不支持 `event_file` 目标。 在 Azure Blob 存储中存储 `.xel` 文件。 请参阅 [event_file 目标](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)。

### <a name="external-libraries"></a>外部库

尚不支持数据库中的 R 和 Python 外部库。 请参阅 [SQL Server 机器学习服务](/sql/advanced-analytics/r/sql-server-r-services)。

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

有关详细信息，请参阅 [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) 和[文件表](/sql/relational-databases/blob/filetables-sql-server)。

### <a name="full-text-semantic-search"></a>全文语义搜索

不支持[语义搜索](/sql/relational-databases/search/semantic-search-sql-server)。

### <a name="linked-servers"></a>链接的服务器

托管实例中的链接服务器支持的目标数有限：

- 支持的目标为托管实例、单一数据库和 SQL Server 实例。 
- 链接服务器不支持分布式可写事务 (MS DTC)。
- 不支持的目标为文件、Analysis Services 和其他 RDBMS。 请尝试使用从 Azure Blob 存储进行本机 CSV 导入（使用 `BULK INSERT` 或 `OPENROWSET`）来代替文件导入操作。

操作

- 不支持跨实例写入事务。
- 支持使用 `sp_dropserver` 删除链接服务器。 请参阅 [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)。
- `OPENROWSET` 函数只能用于在 SQL Server 实例上执行查询。 它们可以是托管的、位于本地或位于虚拟机中。 请参阅 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)。
- `OPENDATASOURCE` 函数只能用于在 SQL Server 实例上执行查询。 它们可以是托管的、位于本地或位于虚拟机中。 仅支持将 `SQLNCLI`、`SQLNCLI11` 和 `SQLOLEDB` 值用作提供程序。 例如 `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`。 请参阅 [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql)。
- 不能使用链接服务器从网络共享读取文件（Excel、CSV）。 请尝试使用从 Azure Blob 存储读取 CSV 文件的 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) 或 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file)。 在[托管实例反馈项](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)| 上跟踪此请求

### <a name="polybase"></a>PolyBase

不支持引用 HDFS 或 Azure Blob 存储中文件的外部表。 有关 PolyBase 的信息，请参阅 [PolyBase](/sql/relational-databases/polybase/polybase-guide)。

### <a name="replication"></a>复制

- 支持快照和双向复制类型。 不支持合并复制、对等复制和可更新订阅。
- [事务复制](sql-database-managed-instance-transactional-replication.md)在托管实例上为公共预览版，但存在一些约束：
    - 所有类型的复制参与者（发布服务器、分发服务器、拉取订阅服务器和推送订阅服务器）都可以放置在托管实例上，但发布服务器和分发服务器必须同时在云中或同时在本地。
    - 托管实例可以与最新版 SQL Server 通信。 请在[此处](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems)查看支持的版本。
    - 事务复制有一些[其他的网络要求](sql-database-managed-instance-transactional-replication.md#requirements)。

若要了解如何配置复制，请参阅[复制教程](replication-with-sql-database-managed-instance.md)。


如果对[故障转移组](sql-database-auto-failover-group.md)中的数据库启用了复制，则托管实例管理员必须清理旧的主节点上的所有发布内容，然后在故障转移后，在新的主节点上重新配置这些发布内容。 在此方案中，需要执行以下活动：

1. 停止数据库上运行的所有复制作业（如果有）。
2. 通过在发布服务器数据库上运行以下脚本，删除发布服务器中的订阅元数据：

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. 删除订阅服务器中的订阅元数据。 在订阅服务器实例的订阅数据库中运行以下脚本：

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. 通过在已发布的数据库中运行以下脚本，强制删除发布服务器中的所有复制对象：

   ```sql
   EXEC sp_removedbreplication
   ```

1. 强制删除原始主实例中的旧分发服务器（如果故障回复到曾经具有分发服务器的旧主实例）。 在旧的分发服务器托管实例中的 master 数据库上运行以下脚本：

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
- 源： 
  - `FROM URL`（Azure Blob 存储）是唯一受支持的选项。
  - 不支持 `FROM DISK`/`TAPE`/备份设备。
  - 不支持备份集。
- 不支持 `WITH` 选项，例如，不允许 `DIFFERENTIAL` 或 `STATS`。
- `ASYNC RESTORE`：即使客户端连接中断，还原仍将继续。 如果删除了连接，可以在 `sys.dm_operation_status` 视图中检查还原操作的状态，以及 CREATE DATABASE 和 DROP DATABASE 的状态。 请参阅 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)。 

将设置或重写以下数据库选项，以后无法更改： 

- `NEW_BROKER`（如果未在 .bak 文件中启用代理）。 
- `ENABLE_BROKER`（如果未在 .bak 文件中启用代理）。 
- `AUTO_CLOSE=OFF`（如果 .bak 文件中的数据库采用 `AUTO_CLOSE=ON`）。 
- `RECOVERY FULL`（如果 .bak 文件中的数据库采用 `SIMPLE` 或 `BULK_LOGGED` 恢复模式）。
- 添加源 .bak 文件中不包含内存优化文件组，则会添加名为 XTP 的内存优化文件组。 
- 任何现有的内存优化文件组将重命名为 XTP。 
- `SINGLE_USER` 和 `RESTRICTED_USER` 选项将转换为 `MULTI_USER`。

限制： 

- 根据损坏类型，有时可以还原已损坏的数据库的备份，但在修复损坏之前，不会创建自动备份。 确保在源实例上运行 `DBCC CHECKDB`，并使用备份 `WITH CHECKSUM` 来避免此问题。
- 无法在托管实例上还原包含本文档所述的任何限制的数据库的 `.BAK` 文件（例如 `FILESTREAM` 或 `FILETABLE` 对象）。
- 无法还原包含多个备份集的 `.BAK` 文件。 
- 无法还原包含多个日志文件的 `.BAK` 文件。
- 在“常规用途”实例上，无法还原包含 8 TB 以上的数据库、活动的内存中 OLTP 对象或每个实例有 280 个以上的文件的备份。 
- 在“业务关键”实例上，无法还原包含 4 TB 以上的数据库或内存中 OLTP 对象，且总大小超过[资源限制](sql-database-managed-instance-resource-limits.md)中所述大小的备份。
有关 restore 语句的信息，请参阅 [RESTORE 语句](/sql/t-sql/statements/restore-statements-transact-sql)。

 > [!IMPORTANT]
 > 这些限制同样适用于内置的时间点还原操作。 例如，在“业务关键”实例上，无法还原大于 4 TB 的“常规用途”数据库。 在“常规用途”实例上，无法还原包含内存中 OLTP 文件或 280 个以上的文件的“业务关键”数据库。

### <a name="service-broker"></a>服务代理

不支持跨实例 Service Broker：

- `sys.routes`：作为先决条件，你必须从 sys.databases 中选择地址。 该地址必须在每个路由的本地。 请参阅 [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)。
- `CREATE ROUTE`：不能使用除 `LOCAL`以外 `ADDRESS` `CREATE ROUTE`。 请参阅 [CREATE ROUTE](/sql/t-sql/statements/create-route-transact-sql)。
- `ALTER ROUTE`：不能使用除 `LOCAL`以外 `ADDRESS` `ALTER ROUTE`。 请参阅 [ALTER ROUTE](/sql/t-sql/statements/alter-route-transact-sql)。 

### <a name="stored-procedures-functions-and-triggers"></a>存储过程、函数和触发器

- “常规用途”层级不支持 `NATIVE_COMPILATION`。
- 不支持以下 [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 选项： 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- 不支持 `sp_execute_external_scripts`。 请参阅 [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)。
- 不支持 `xp_cmdshell`。 请参阅 [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)。
- 不支持 `Extended stored procedures`，其中包括 `sp_addextendedproc` 和 `sp_dropextendedproc`。 请参阅[扩展存储过程](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)。
- 不支持 `sp_attach_db`、`sp_attach_single_file_db` 和 `sp_detach_db`。 请参阅 [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql)、[sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 和 [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)。

### <a name="system-functions-and-variables"></a>系统函数和变量

以下变量、函数和视图返回不同的结果：

- `SERVERPROPERTY('EngineEdition')` 返回值 8。 此属性唯一标识托管实例。 请参阅 [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql)。
- `SERVERPROPERTY('InstanceName')` 返回 NULL，因为 SQL Server 存在的实例概念并不适用于托管实例。 请参阅 [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql)。
- `@@SERVERNAME` 返回完整的 DNS "可连接" 名称，例如 my-managed-instance.wcus17662feb9ce98.database.windows.net。 请参阅 [@@SERVERNAME](/sql/t-sql/functions/servername-transact-sql)。 
- `SYS.SERVERS` 返回完整的 DNS“可连接”名称，例如，为属性“name”和“data_source”返回 `myinstance.domain.database.windows.net`。 请参阅 [SYS.SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)。
- `@@SERVICENAME` 返回 NULL，因为 SQL Server 存在的服务概念并不适用于托管实例。 请参阅 [@@SERVICENAME](/sql/t-sql/functions/servicename-transact-sql)。
- 支持 `SUSER_ID`。 如果 Azure AD 登录名不在 sys.syslogins 中，则返回 NULL。 请参阅 [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql)。 
- 不支持 `SUSER_SID`。 将返回错误数据，这是暂时性的已知问题。 请参阅 [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql)。 

## <a name="Environment"></a>环境约束

### <a name="subnet"></a>子网
-  在部署托管实例的子网中，无法放置任何其他资源（例如虚拟机）。 请使用其他子网部署这些资源。
- 子网必须有足够数量的可用 [IP 地址](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。 至少为 16 个，但建议在子网中至少有 32 个 IP 地址。
- [不能将服务终结点与托管实例的子网相关联](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。 创建虚拟网络时，请务必禁用“服务终结点”选项。
- 可以在某个区域部署的 vCore 数和实例类型存在一些[约束和限制](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)。
- 存在一些[必须在子网上应用的安全规则](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。

### <a name="vnet"></a>VNET
- VNet 可以使用资源模型进行部署 - 不支持适用于 VNet 的经典模型。
- 创建托管实例后，不支持将托管实例或 VNet 移到另一个资源组或订阅。
- 应用服务环境、逻辑应用和托管实例之类的某些服务（用于异地复制、事务复制，或者通过链接服务器来使用）在其 VNet 是通过[全局对等互连](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)进行连接的情况下不能访问不同区域中的托管实例。 可以通过 VNet 网关经由 ExpressRoute 或 VNet-to-VNet 连接到这些资源。

### <a name="tempdb"></a>TEMPDB

在“常规用途”层级上，`tempdb` 的最大文件大小不能超过 24 GB 每核心。 在“业务关键”层级上，最大 `tempdb` 大小根据实例存储大小受到限制。 在“常规用途”层级上，`Tempdb` 日志文件大小限制为 120 GB。 如果某些查询需要在 `tempdb` 中为每个核心提供 24 GB 以上的空间，或者生成 120 GB 以上的日志数据，则这些查询可能会返回错误。

### <a name="error-logs"></a>错误日志

托管实例将详细信息放在错误日志中。 有很多内部系统事件记录在错误日志中。 使用自定义过程读取已筛选出某些不相关条目的错误日志。 有关详细信息，请参阅[托管实例–](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) Azure Data Studio 的 sp_readmierrorlog 或[托管实例扩展（预览版）](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) 。

## <a name="Issues"></a> 已知问题

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>内存中 OLTP 内存限制不适用

**日期：** Oct 2019

在某些情况下，业务关键型服务层级不会正确应用[内存优化对象的最大内存限制](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space)。 托管实例可使工作负荷对内存中 OLTP 操作使用更多内存，这可能会影响实例的可用性和稳定性。 达到限制的内存中 OLTP 查询可能不会立即失败。 此问题即将得到解决。 使用较多内存中 OLTP 内存的查询在达到[限制](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space)的情况下会更快地失败。

**解决方法：** 使用[SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) [监视内存中 OLTP 存储使用情况](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring)，确保工作负荷不会使用超过可用内存。 提高基于 vCore 数的内存限制，或者优化工作负荷，让其使用较少的内存。

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>尝试删除不为空的文件时，返回了错误的错误

**日期：** Oct 2019

SQL Server/托管实例[不允许用户删除不为空的文件](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites)。 如果尝试使用 `ALTER DATABASE REMOVE FILE` 语句删除非空数据文件，系统会立即返回 `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` 错误。 托管实例会继续尝试删除该文件，操作会在 30 分钟后失败并显示`Internal server error`。

**解决方法**：使用 `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` 命令删除文件内容。 如果这是文件组中的唯一文件，则需从与此文件组关联的表或分区中删除数据，然后才能收缩文件并选择将该数据加载到另一表/分区中。

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>更改服务层级和创建实例的操作会被正在进行的数据库还原操作阻止

**日期：** 09月2019

正在运行的 `RESTORE` 语句、数据迁移服务的迁移过程以及内置的时间点还原都会阻止对服务层的更新操作或者对现有实例的重设大小操作以及创建新实例的操作，直至还原过程完成为止。 还原过程会阻止其运行时所在的子网的托管实例和实例池中的这些操作。 实例池中的实例不受影响。 创建或更改服务层级的操作不会失败或超时 - 一旦还原过程完成或取消，它们就会继续。

**解决方法**：等待还原过程完成，如果创建或更新服务层操作的优先级较高，则取消还原过程。

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>故障转移后，可能需要重新配置“业务关键”服务层级上的 Resource Governor

**日期：** 09月2019

[Resource Governor](/sql/relational-databases/resource-governor/resource-governor) 功能可让你限制分配给用户工作负荷的资源。在故障转移或者完成用户发起的服务层级更改（例如，更改最大 vCore 数或最大实例存储大小）后，Resource Governor 可能会错误地分类某些用户工作负荷。

**解决方法**：如果你使用的是[Resource Governor](/sql/relational-databases/resource-governor/resource-governor)，则在启动实例时，定期或作为 sql 代理作业的一部分运行 `ALTER RESOURCE GOVERNOR RECONFIGURE`。

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>升级服务层级后必须重新初始化跨数据库 Service Broker 对话

**日期：** 2019年8月

完成更改服务层级的操作后，跨数据库 Service Broker 对话会停止向其他数据库中的服务传递消息。 消息并未**丢失**，可在发送方队列中找到它们。 在托管实例中对 vCore 或实例存储大小进行任何更改都会导致 `service_broke_guid`sys.databases[ 视图中所有数据库的 ](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) 值发生更改。 使用 `DIALOG`BEGIN DIALOG[ 语句创建的、引用其他数据库中的 Service Broker 的任何 ](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) 将停止向目标服务传递消息。

**解决方法：** 先停止使用跨数据库 Service Broker 对话会话的任何活动，然后再更新服务层并在之后重新初始化它们。 如果在更改服务层级后存在任何未传递的剩余消息，请从源队列中读取消息，然后将其重新发送到目标队列。

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>不支持 Azure AD 登录类型的 Impersonification

**日期：** 2019年7月

不支持使用以下 AAD 主体的 `EXECUTE AS USER` 或 `EXECUTE AS LOGIN` 进行模拟：
-   带别名的 AAD 的用户。 在这种情况下，将返回以下错误：`15517`。
- 基于 AAD 应用程序或服务主体的 AAD 登录名和用户。 在这种情况下，将返回以下错误：`15517` 和 `15406`。

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>sp_send_db_mail 中不支持 @query 参数

**日期：** 2019年4月

`@query`sp_send_db_mail[ 过程中的 ](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) 参数不起作用。

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>异地故障转移之后，必须重新配置事务复制

**日期：** 三月2019

如果对自动故障转移组中的数据库启用了事务复制，则托管实例管理员必须清理旧的主节点上的所有发布内容，然后在故障转移到另一个区域后，在新的主节点上重新配置这些发布内容。 有关更多详细信息，请参阅[复制](#replication)。

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>SSDT 中不支持 AAD 登录名和用户

**日期：** 11月2019

SQL Server Data Tools 不完全支持 Azure Active directory 登录名和用户。

### <a name="temporary-database-is-used-during-restore-operation"></a>在还原操作过程中使用临时数据库

在托管实例上还原某个数据库时，还原服务首先会使用所需的名称创建一个空数据库，以便在实例上分配该名称。 一段时间后，将会删除此数据库，并启动实际数据库的还原。 处于“正在还原”状态的数据库将临时使用随机 GUID 值而不是名称。 还原过程完成后，临时名称将更改为 `RESTORE` 语句中指定的所需名称。 在初始阶段，用户可以访问空数据库，甚至可以在此数据库中创建表或加载数据。 当还原服务启动第二个阶段时，将删除此临时数据库。

**解决方法**：在你看到还原完成之前，不要访问你要还原的数据库。

### <a name="tempdb-structure-and-content-is-re-created"></a>将重新创建 TEMPDB 结构和内容

`tempdb` 数据库始终拆分为 12 个数据文件，文件结构不可更改。 无法更改每个文件的最大大小，并且无法将新文件添加到 `tempdb`。 实例启动或故障转移时，`Tempdb` 始终会重新创建为空数据库；在 `tempdb` 中所做的任何更改不会保留。

### <a name="exceeding-storage-space-with-small-database-files"></a>小型数据库文件超出存储空间

`CREATE DATABASE`、`ALTER DATABASE ADD FILE` 和 `RESTORE DATABASE` 语句可能会失败，因为实例可能会达到 Azure 存储限制。

每个“常规用途”托管实例都为 Azure 高级磁盘空间保留了最多 35 TB 存储空间。 每个数据库文件放置在单独的物理磁盘上。 磁盘大小可以为 128 GB、256 GB、512 GB、1 TB 或 4 TB。 磁盘上未使用的空间不收费，但 Azure 高级磁盘大小总计不能超过 35 TB。 在某些情况下，由于内部碎片，总共不需要 8 TB 的托管实例可能会超过 35 TB 的 Azure 存储大小限制。

例如，“常规用途”托管实例可将一个大小为 1.2 TB 的大文件放在 4 TB 的磁盘上。 它还可以将 248 个文件（每个大小为 1 GB）放在单独的 128 GB 磁盘上。 在本示例中：

- 分配的磁盘存储总大小为 1 x 4 TB + 248 x 128 GB = 35 TB。
- 实例上的数据库的总预留空间为 1 x 1.2 TB + 248 x 1 GB = 1.4 TB。

此示例演示在某些情况下，由于文件的具体分布，托管实例可能会出乎意料地达到为附加的 Azure 高级磁盘预留的 35 TB 存储空间大小限制。

在此示例中，只要未添加新文件，现有数据库就会继续工作并且可以毫无问题地增长。 由于没有足够的空间用于新磁盘驱动器，因此无法创建或还原新数据库，即使所有数据库的总大小未达到实例大小限制也是如此。 这种情况下返回的错误并不明确。

可以使用系统视图[识别剩余文件的数目](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)。 如果即将达到此限制，请尝试[使用 DBCC SHRINKFILE 语句清空并删除一些小型文件](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)，或者切换到[没有此限制的“业务关键”层级](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。

### <a name="guid-values-shown-instead-of-database-names"></a>显示 GUID 值而不是数据库名称

多个系统视图、性能计数器、错误消息、XEvent 和错误日志条目显示了 GUID 数据库标识符而非实际的数据库名称。 不要依赖这些 GUID 标识符，因为它们在将来会被替换为实际的数据库名称。

### <a name="error-logs-arent-persisted"></a>不保留错误日志

托管实例中可用的错误日志不会持久保留，并且它们的大小不包括在最大存储限制中。 在发生故障转移时可能会自动清除错误日志。 错误日志历史记录中可能存在差距，因为托管实例在多个虚拟机上转移了多次。

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

**解决方法：** 使用[ChangeDatabase （String）](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase)在连接上下文中使用其他数据库，而不是使用两个连接。

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR 模块和链接的服务器有时无法引用本地 IP 地址

放置在托管实例中的 CLR 模块，以及引用当前实例的链接服务器或分布式查询，有时可能无法解析本地实例的 IP。 此错误是暂时性问题。

**解决方法：** 如果可能，请在 CLR 模块中使用上下文连接。

## <a name="next-steps"></a>后续步骤

- 有关托管实例的详细信息，请参阅[什么是托管实例？](sql-database-managed-instance.md)
- 有关功能和比较列表，请参阅 [Azure SQL 数据库功能比较](sql-database-features.md)。
- 有关如何新建托管实例的快速入门，请参阅[创建托管实例](sql-database-managed-instance-get-started.md)。
