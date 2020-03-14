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
ms.reviewer: sstein, carlrab, bonova, danil
ms.date: 03/11/2020
ms.custom: seoapril2019
ms.openlocfilehash: 8c995a40e621f7155ad0741004d10b1146523489
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256050"
---
# <a name="managed-instance-t-sql-differences-limitations-and-known-issues"></a>托管实例 T-sql 差异、限制和已知问题

本文总结并说明了 Azure SQL 数据库托管实例与本地 SQL Server 数据库引擎之间的语法和行为之间的差异。 托管实例部署选项与本地 SQL Server 数据库引擎高度兼容。 托管实例支持大多数 SQL Server 数据库引擎功能。

![迁移](./media/sql-database-managed-instance/migration.png)

托管实例中引入了一些 PaaS 限制，与 SQL Server 相比，某些行为发生了更改。 这些差异分为以下几类：<a name="Differences"></a>

- [可用性](#availability)包括[Always On 可用性组](#always-on-availability-groups)和[备份](#backup)中的差异。
- [安全性](#security)包括[审核](#auditing)、[证书](#certificates)、[凭据](#credential)、[加密提供程序](#cryptographic-providers)、[登录名和用户](#logins-and-users)以及[服务密钥和服务主密钥](#service-key-and-service-master-key)之间的差异。
- [配置](#configuration)包括[缓冲池扩展](#buffer-pool-extension)、[排序规则](#collation)、[兼容级别](#compatibility-levels)、[数据库镜像](#database-mirroring)、[数据库选项](#database-options)、 [SQL Server 代理](#sql-server-agent)和[表选项](#tables)之间的差异。
- [功能](#functionalities)包括[BULK INSERT/OPENROWSET](#bulk-insert--openrowset)、 [CLR](#clr)、 [DBCC](#dbcc)、[分布式事务](#distributed-transactions)、[扩展事件](#extended-events)、[外部库](#external-libraries)、 [filestream 和 FileTable](#filestream-and-filetable)、[全文语义搜索](#full-text-semantic-search)、[链接服务器](#linked-servers)、 [PolyBase](#polybase)、[复制](#replication)、[还原](#restore-statement)、 [Service Broker](#service-broker)、[存储过程、函数和触发器](#stored-procedures-functions-and-triggers)。
- [环境设置](#Environment)，如 vnet 和子网配置。

其中大多数功能都是体系结构约束，表示服务功能。

此页还介绍了在托管实例中发现的[临时已知问题](#Issues)，这些问题将在以后解决。

## <a name="availability"></a>可用性

### <a name="always-on-availability-groups"></a>Always On 可用性组

[高可用性](sql-database-high-availability.md)内置于托管实例中，不能由用户控制。 不支持以下语句：

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)语句的[SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr)子句

### <a name="backup"></a>备份

托管实例具有自动备份，因此，用户可以创建 `COPY_ONLY` 备份的完整数据库。 不支持差异备份、日志备份和文件快照备份。

- 对于托管实例，可以仅将实例数据库备份到 Azure Blob 存储帐户：
  - 仅支持 `BACKUP TO URL`。
  - 不支持 `FILE`、`TAPE`和备份设备。
- 大多数常规 `WITH` 选项都受支持。
  - `COPY_ONLY` 是必需的。
  - 不支持 `FILE_SNAPSHOT`。
  - 磁带选项：不支持 `REWIND`、`NOREWIND`、`UNLOAD`和 `NOUNLOAD`。
  - 特定于日志的选项：不支持 `NORECOVERY`、`STANDBY`和 `NO_TRUNCATE`。

限制： 

- 对于托管实例，可以将实例数据库备份到最多32条带化的备份，如果使用备份压缩，这对于最大为 4 TB 的数据库来说就足够了。
- 不能对使用服务托管透明数据加密（TDE）加密的数据库执行 `BACKUP DATABASE ... WITH COPY_ONLY`。 服务托管的 TDE 强制使用内部 TDE 密钥对备份进行加密。 无法导出密钥，因此无法还原备份。 使用自动备份和时间点还原，或改为使用[客户托管（BYOK） TDE](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key) 。 您还可以在数据库上禁用加密。
- 使用托管实例中的 `BACKUP` 命令的最大备份条带大小为 195 GB，这是最大 blob 大小。 增加备份命令中的带状线数量以缩小单个带状线大小，将其保持在限制范围内。

    > [!TIP]
    > 若要解决此限制，请在本地环境或虚拟机中的 SQL Server 备份数据库时，可以：
    >
    > - 备份到 `DISK`，而不是备份到 `URL`。
    > - 将备份文件上传到 Blob 存储。
    > - 还原到托管实例。
    >
    > 托管实例中的 `Restore` 命令在备份文件中支持更大的 blob 大小，因为在存储已上传的备份文件时，使用不同的 blob 类型。

有关使用 T-SQL 进行备份的信息，请参阅 [BACKUP](/sql/t-sql/statements/backup-transact-sql)。

## <a name="security"></a>安全性

### <a name="auditing"></a>审核

在审核 Azure SQL 数据库和 SQL Server 中的数据库方面，主要差异是：

- 使用 Azure SQL 数据库中的托管实例部署选项，审核在服务器级别工作。 `.xel` 日志文件存储在 Azure Blob 存储中。
- 使用 Azure SQL 数据库中的单一数据库和弹性池部署选项，审核是在数据库一级执行。
- 在 SQL Server 本地计算机或虚拟机上，审核在服务器级别工作。 事件存储在文件系统或 Windows 事件日志中。
 
托管实例中的 XEvent 审核支持 Azure Blob 存储目标。 不支持文件和 Windows 日志。

Azure Blob 存储审核的主要 `CREATE AUDIT` 语法差异为：

- 提供了一个新的语法 `TO URL`，你可以使用它来指定放置 `.xel` 文件的 Azure Blob 存储容器的 URL。
- 由于托管实例无法访问 Windows 文件共享，因此不支持语法 `TO FILE`。

有关详细信息，请参阅： 

- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [审核](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>证书

托管实例无法访问文件共享和 Windows 文件夹，因此以下约束适用：

- 证书不支持 `BACKUP TO` 文件 /`CREATE FROM`。
- `FILE`/`ASSEMBLY` `CREATE`/`BACKUP` 证书不受支持。 无法使用私钥文件。 

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

托管实例无法访问文件，因此无法创建加密提供程序：

- 不支持 `CREATE CRYPTOGRAPHIC PROVIDER`。 请参阅 [CREATE CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/create-cryptographic-provider-transact-sql)。
- 不支持 `ALTER CRYPTOGRAPHIC PROVIDER`。 请参阅 [ALTER CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)。

### <a name="logins-and-users"></a>登录名和用户

- 支持使用 `FROM CERTIFICATE`、`FROM ASYMMETRIC KEY`和 `FROM SID` 创建的 SQL 登录名。 请参阅 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql)。
- 支持 Azure Active Directory （Azure AD）通过[创建登录](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)语法创建的服务器主体（登录名）或 "[从登录名创建用户 [Azure AD 登录名]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) " 语法。 这些登录名在服务器级别创建。

    托管实例支持 Azure AD 具有语法 `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`的数据库主体。 此功能也称为 Azure AD 包含的数据库用户。

- 不支持用 `CREATE LOGIN ... FROM WINDOWS` 语法创建的 Windows 登录名。 使用 Azure Active Directory 登录名和用户。
- 创建实例的 Azure AD 用户具有不[受限制的管理员权限](sql-database-manage-logins.md)。
- 非管理员 Azure AD 可以使用 `CREATE USER ... FROM EXTERNAL PROVIDER` 语法创建数据库级别的用户。 请参阅[创建用户 .。。来自外部提供程序](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)。
- Azure AD 服务器主体（登录）仅支持一个托管实例中的 SQL 功能。 需要跨实例交互的功能，无论它们是在同一 Azure AD 租户还是不同租户中，不支持 Azure AD 用户。 此类功能的示例包括：

  - SQL 事务复制。
  - 链接服务器。

- 不支持设置映射到作为数据库所有者的 Azure AD 组的 Azure AD 登录名。
- 支持通过使用其他 Azure AD 主体来模拟 Azure AD 服务器级主体，例如[EXECUTE as](/sql/t-sql/statements/execute-as-transact-sql)子句。 EXECUTE AS 限制如下：

  - 如果 Azure AD 用户的名称不同于登录名，则该用户不支持 EXECUTE AS USER。 例如，用户是通过登录 [john@contoso.com] 中的语法 CREATE USER [myAadUser] 创建的，并且通过 EXEC 尝试通过 EXEC 作为 USER = _myAadUser_。 从 Azure AD 服务器主体（登录名）创建**用户**时，请将 user_name 指定为**登录名**相同的 login_name。
  - 只有作为 `sysadmin` 角色的一部分的 SQL Server 级别主体（登录名）才能执行面向 Azure AD 主体的以下操作：

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- 使用[SSMS v 18.4 或更高版本](/sql/ssms/download-sql-server-management-studio-ssms)或[SQLPackage](/sql/tools/sqlpackage-download)，托管实例中的 Azure AD 用户支持使用 bacpac 文件进行数据库导出/导入。
  - 使用数据库 bacpac 文件支持以下配置： 
    - 在同一 Azure AD 域中的不同管理实例之间导出/导入数据库。
    - 从托管实例中导出数据库，并将其导入到同一个 Azure AD 域中的 SQL 数据库。 
    - 从 SQL 数据库中导出数据库，并将其导入到同一个 Azure AD 域中的托管实例。
    - 从托管实例中导出数据库，并将其导入 SQL Server （2012版或更高版本）。
      - 在此配置中，所有 Azure AD 用户都作为不带登录名的 SQL 数据库主体（用户）创建。 用户的类型列为 SQL （在 sys. database_principals 中显示为 "SQL_USER"）。 它们的权限和角色保留在 SQL Server 数据库元数据中，并且可用于模拟。 但是，它们不能用于访问 SQL Server 使用其凭据进行登录。

- 只有服务器级别主体登录名由托管实例预配过程创建，服务器角色的成员（例如 `securityadmin` 或 `sysadmin`）或服务器级别上具有 ALTER ANY LOGIN 权限的其他登录名可以在托管实例的 master 数据库中创建 Azure AD 服务器主体（登录名）。
- 如果该登录名是 SQL 主体，则只有属于 `sysadmin` 角色的登录名才能使用 create 命令为 Azure AD 帐户创建登录名。
- Azure AD 登录名必须是用于 Azure SQL 数据库托管实例的同一目录中的 Azure AD 的成员。
- 从 SQL Server Management Studio 18.0 preview 5 开始对象资源管理器中显示 Azure AD 服务器主体（登录名）。
- 允许 Azure AD 服务器主体（登录名）与 Azure AD 管理员帐户重叠。 解析主体并将权限应用到托管实例时，Azure AD 服务器主体（登录名）优先于 Azure AD 管理员。
- 在身份验证过程中，将应用以下序列来解析身份验证主体：

    1. 如果 Azure AD 帐户直接映射到 Azure AD 服务器主体（登录名），而该主体存在于 sys. server_principals 类型 "E" 中，则授予访问权限并应用 Azure AD 服务器主体（登录名）的权限。
    2. 如果 Azure AD 帐户是映射到 Azure AD 服务器主体（登录名）的 Azure AD 组的成员，而该主体在 sys. server_principals 类型为 "X"，则授予访问权限并应用 Azure AD 组登录名的权限。
    3. 如果 Azure AD 帐户为托管实例配置的特殊门户 Azure AD 管理员，这在托管实例系统视图中不存在，则对托管实例的 Azure AD 管理员（旧模式）应用特殊固定的权限。
    4. 如果 Azure AD 帐户存在直接映射到数据库中的 Azure AD 用户，则该数据库中存在的 database_principals 类型为 "E"，则授予访问权限并应用 Azure AD 数据库用户的权限。
    5. 如果 Azure AD 帐户是映射到数据库中的 Azure AD 用户的 Azure AD 组的成员，而该数据库在 sys. database_principals 类型为 "X"，则授予访问权限并应用 Azure AD 组登录的权限。
    6. 如果有 Azure AD 登录名映射到 Azure AD 用户帐户或 Azure AD 组帐户，而该帐户解析为正在进行身份验证的用户，则会应用此 Azure AD 登录名的所有权限。

### <a name="service-key-and-service-master-key"></a>服务密钥和服务主密钥

- 不支持[主密钥备份](/sql/t-sql/statements/backup-master-key-transact-sql)（由 SQL 数据库服务管理）。
- 不支持[主密钥还原](/sql/t-sql/statements/restore-master-key-transact-sql)（由 SQL 数据库服务管理）。
- 不支持[服务主密钥备份](/sql/t-sql/statements/backup-service-master-key-transact-sql)（由 SQL 数据库服务管理）。
- 不支持[服务主密钥还原](/sql/t-sql/statements/restore-service-master-key-transact-sql)（由 SQL 数据库服务管理）。

## <a name="configuration"></a>配置

### <a name="buffer-pool-extension"></a>缓冲池扩展

- [缓冲池扩展](/sql/database-engine/configure-windows/buffer-pool-extension)不受支持。
- 不支持 `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`。 请参阅 [ALTER SERVER CONFIGURATION](/sql/t-sql/statements/alter-server-configuration-transact-sql)。

### <a name="collation"></a>排序规则

默认实例排序规则为 `SQL_Latin1_General_CP1_CI_AS` 并可以被指定为创建参数。 请参阅[排序规则](/sql/t-sql/statements/collations)。

### <a name="compatibility-levels"></a>兼容级别

- 支持的兼容级别为100、110、120、130、140和150。
- 不支持低于 100 的兼容级别。
- 新数据库的默认兼容级别为 140。 对于还原的数据库，如果兼容级别为100及更高版本，则兼容级别将保持不变。

请参阅 [ALTER DATABASE 兼容级别](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。

### <a name="database-mirroring"></a>数据库镜像

不支持数据库镜像。

- 不支持 `ALTER DATABASE SET PARTNER` 和 `SET WITNESS` 选项。
- 不支持 `CREATE ENDPOINT … FOR DATABASE_MIRRORING`。

有关详细信息，请参阅 [ALTER DATABASE SET PARTNER 和 SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 以及 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)。

### <a name="database-options"></a>数据库选项

- 不支持多个日志文件。
- “常规用途”服务层级不支持内存中对象。 
- 每个常规用途实例的文件数限制为280，这意味着每个数据库最多可包含280个文件。 常规用途层中的数据文件和日志文件均计入此限制。 [业务关键层支持每个数据库32767个文件](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。
- 该数据库不能包含包含 filestream 数据的文件组。 如果 .bak 包含 `FILESTREAM` 数据，则还原将失败。 
- 每个文件都被放置在 Azure Blob 存储中。 每个文件的 IO 和吞吐量取决于每个单独文件的大小。

#### <a name="create-database-statement"></a>CREATE DATABASE 语句

以下限制适用于 `CREATE DATABASE`：

- 无法定义文件和文件组。 
- 不支持 `CONTAINMENT` 选项。 
- 不支持 `WITH` 选项。 
   > [!TIP]
   > 一种解决方法是在 `CREATE DATABASE` 后使用 `ALTER DATABASE` 来设置数据库选项，以添加文件或设置包含。 

- 不支持 `FOR ATTACH` 选项。
- 不支持 `AS SNAPSHOT OF` 选项。

有关详细信息，请参阅 [CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql)。

#### <a name="alter-database-statement"></a>ALTER DATABASE 语句

无法设置或更改某些文件属性：

- 不能在 `ALTER DATABASE ADD FILE (FILENAME='path')` T-sql 语句中指定文件路径。 请从脚本中删除 `FILENAME`，因为托管实例自动放置文件。 
- 不能使用 `ALTER DATABASE` 语句来更改文件名。

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

- 当前在托管实例中不支持启用和禁用 SQL Server 代理。 SQL 代理始终运行。
- SQL Server 代理设置是只读的。 托管实例不支持过程 `sp_set_agent_properties`。 
- 作业
  - 支持 T-SQL 作业步骤。
  - 支持以下复制作业：
    - 事务日志读取器
    - 快照
    - 分发服务器
  - 支持 SSIS 作业步骤。
  - 当前不支持其他类型的作业步骤：
    - 不支持合并复制作业步骤。 
    - 不支持队列读取器。 
    - 目前尚不支持命令行界面。
  - 托管实例无法访问外部资源，例如通过 robocopy 的网络共享。 
  - 不支持 SQL Server Analysis Services。
- 部分支持通知。
- 支持电子邮件通知，但需要配置数据库邮件配置文件。 SQL Server 代理只能使用一个数据库邮件配置文件，并且必须 `AzureManagedInstance_dbmail_profile`调用它。 
  - 不支持寻呼机。
  - 不支持 NetSend。
  - 尚不支持警报。
  - 不支持代理。
- 不支持 EventLog。

当前不支持以下 SQL 代理功能：

- 代理
- 计划空闲 CPU 上的作业
- 启用或禁用代理
- 警报

有关 SQL Server 代理的信息，请参阅 [SQL Server 代理](/sql/ssms/agent/sql-server-agent)。

### <a name="tables"></a>表

不支持以下表类型：

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [外部表](/sql/t-sql/statements/create-external-table-transact-sql)（Polybase）
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) （仅常规用途层中不支持）

有关如何创建和更改表的信息，请参阅[CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql)和[alter TABLE](/sql/t-sql/statements/alter-table-transact-sql)。

## <a name="functionalities"></a>功能

### <a name="bulk-insert--openrowset"></a>Bulk insert/OPENROWSET

托管实例无法访问文件共享和 Windows 文件夹，因此必须从 Azure Blob 存储导入文件：

- 导入 Azure Blob 存储中的文件时，在 `BULK INSERT` 命令中需要 `DATASOURCE`。 请参阅 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql)。
- 从 Azure Blob 存储读取文件内容时，`OPENROWSET` 函数中需要 `DATASOURCE`。 请参阅 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)。
- `OPENROWSET` 可用于读取其他 Azure SQL 单一数据库、托管实例或 SQL Server 实例中的数据。 其他源（如 Oracle 数据库或 Excel 文件）不受支持。

### <a name="clr"></a>CLR

托管实例无法访问文件共享和 Windows 文件夹，因此以下约束适用：

- 仅支持 `CREATE ASSEMBLY FROM BINARY`。 请参阅[CREATE ASSEM 通过 FROM BINARY](/sql/t-sql/statements/create-assembly-transact-sql)。 
- 不支持 `CREATE ASSEMBLY FROM FILE`。 请参阅 [CREATE ASSEMBLY FROM FILE](/sql/t-sql/statements/create-assembly-transact-sql)。
- `ALTER ASSEMBLY` 不能引用文件。 请参阅 [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql)。

### <a name="database-mail-db_mail"></a>数据库邮件（db_mail）
 - `sp_send_dbmail` 无法使用 @file_attachments 参数发送附件。 不能从此过程访问本地文件系统和外部共享或 Azure Blob 存储。
 - 请参阅与 `@query` 参数和身份验证相关的已知问题。
 
### <a name="dbcc"></a>DBCC

托管实例不支持在 SQL Server 中启用的未记录的 DBCC 语句。

- 仅支持有限数量的全局跟踪标志。 不支持会话级 `Trace flags`。 请参阅[跟踪标志](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)。
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql)和[dbcc TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql)使用有限数量的全局跟踪标志。
- 不能使用具有选项 REPAIR_ALLOW_DATA_LOSS、REPAIR_FAST 和 REPAIR_REBUILD 的[DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) ，因为无法在 `SINGLE_USER` 模式下设置数据库-请参阅[ALTER database 差异](#alter-database-statement)。 潜在的数据库损坏由 Azure 支持团队处理。 如果你注意到应修复的数据库损坏，请联系 Azure 支持。

### <a name="distributed-transactions"></a>分布式事务

托管实例当前不支持 MSDTC 和[弹性事务](sql-database-elastic-transactions-overview.md)。

### <a name="extended-events"></a>扩展事件

对于扩展事件（XEvents），某些特定于 Windows 的目标不受支持：

- 不支持 `etw_classic_sync` 目标。 在 Azure Blob 存储中存储 `.xel` 文件。 请参阅 [etw_classic_sync 目标](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)。
- 不支持 `event_file` 目标。 在 Azure Blob 存储中存储 `.xel` 文件。 请参阅 [event_file 目标](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)。

### <a name="external-libraries"></a>外部库

数据库中的 R 和 Python 目前尚不支持外部库。 请参阅 [SQL Server 机器学习服务](/sql/advanced-analytics/r/sql-server-r-services)。

### <a name="filestream-and-filetable"></a>文件流和 FileTable

- 不支持 Filestream 数据。
- 该数据库不能包含具有 `FILESTREAM` 数据的文件组。
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

### <a name="linked-servers"></a>链接服务器

托管实例中的链接服务器支持的目标数有限：

- 支持的目标是托管实例、单一数据库和 SQL Server 实例。 
- 链接服务器不支持分布式可写事务（MS DTC）。
- 不受支持的目标是文件、Analysis Services 和其他 RDBMS。 尝试使用 `BULK INSERT` 或 `OPENROWSET` 作为文件导入的替代方法，使用 Azure Blob 存储中的本机 CSV 导入。

操作

- 不支持跨实例写入事务。
- 支持使用 `sp_dropserver` 删除链接服务器。 请参阅 [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)。
- `OPENROWSET` 函数可用于仅对 SQL Server 实例执行查询。 它们可以是托管、在本地或虚拟机中。 请参阅 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)。
- `OPENDATASOURCE` 函数可用于仅对 SQL Server 实例执行查询。 它们可以是托管、在本地或虚拟机中。 仅支持 `SQLNCLI`、`SQLNCLI11`和 `SQLOLEDB` 值作为提供程序。 示例为 `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`。 请参阅 [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql)。
- 链接服务器不能用于从网络共享读取文件（Excel、CSV）。 尝试使用从 Azure Blob 存储读取 CSV 文件[BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file)或[OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) 。 在[托管实例反馈项](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)| 上跟踪此请求

### <a name="polybase"></a>PolyBase

不支持引用 HDFS 或 Azure Blob 存储中的文件的外部表。 有关 PolyBase 的信息，请参阅[polybase](/sql/relational-databases/polybase/polybase-guide)。

### <a name="replication"></a>复制

- 支持快照和双向复制类型。 不支持合并复制、对等复制和可更新订阅。
- [事务复制](sql-database-managed-instance-transactional-replication.md)可用于托管实例上的公共预览版，但有一些限制：
    - 所有类型的复制参与者（发布服务器、分发服务器、请求订阅服务器和推送订阅服务器）都可放置在托管实例上，但发布服务器和分发服务器必须都在云中或同时位于本地。
    - 托管实例可以与 SQL Server 的最新版本通信。 有关详细信息，请参阅[支持的版本矩阵](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems)。
    - 事务复制有一些[额外的网络要求](sql-database-managed-instance-transactional-replication.md#requirements)。

有关配置事务复制的详细信息，请参阅以下教程：
- [MI 发布服务器与订阅服务器之间的复制](replication-with-sql-database-managed-instance.md)
- [MI 发布服务器、MI 分发服务器与 SQL Server 订阅服务器之间的复制](sql-database-managed-instance-configure-replication-tutorial.md)

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
  - `FROM URL` （Azure Blob 存储）是唯一受支持的选项。
  - 不支持 `FROM DISK`/`TAPE`/备份设备。
  - 不支持备份集。
- 不支持 `WITH` 选项，如 no `DIFFERENTIAL` 或 `STATS`。
- `ASYNC RESTORE`：即使客户端连接中断，还原仍将继续。 如果断开连接，则可以在 "`sys.dm_operation_status`" 视图中检查还原操作的状态，以及 "创建和删除数据库" 的状态。 请参阅 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)。 

以下数据库选项已设置或重写，以后无法更改： 

- `NEW_BROKER` 如果 .bak 文件中未启用 broker，则为。 
- `ENABLE_BROKER` 如果 .bak 文件中未启用 broker，则为。 
- 如果 .bak 文件中的数据库已 `AUTO_CLOSE=ON`，则 `AUTO_CLOSE=OFF`。 
- 如果 .bak 文件中的数据库具有 `SIMPLE` 或 `BULK_LOGGED` 恢复模式，则 `RECOVERY FULL`。
- 如果内存优化的文件组不在源 .bak 文件中，则会将其添加并被称为 XTP。 
- 任何现有的内存优化文件组都将重命名为 XTP。 
- `SINGLE_USER` 和 `RESTRICTED_USER` 选项将转换为 `MULTI_USER`。

限制： 

- 可能会还原损坏的数据库的备份，具体取决于损坏的类型，但在修复损坏之前，不会执行自动备份。 请确保在源实例上运行 `DBCC CHECKDB`，并使用备份 `WITH CHECKSUM` 来避免此问题。
- 在托管实例上，不能还原包含本文档所述的任何限制的数据库 `.BAK` 文件的还原（例如 `FILESTREAM` 或 `FILETABLE` 对象）。
- 不能还原包含多个备份集的 `.BAK` 文件。 
- 不能还原包含多个日志文件 `.BAK` 文件。
- 对于包含大于 8 TB 的数据库的备份、活动的内存中 OLTP 对象或每个实例超过280个文件的文件数，无法在常规用途实例上还原。 
- 如果备份包含大于 4 TB 的数据库或内存中 OLTP 对象，但其总大小大于[资源限制](sql-database-managed-instance-resource-limits.md)中描述的大小，则无法在业务关键实例上还原。
有关 restore 语句的信息，请参阅[restore 语句](/sql/t-sql/statements/restore-statements-transact-sql)。

 > [!IMPORTANT]
 > 此限制适用于内置的时间点还原操作。 例如，无法在业务关键实例上还原常规用途大于 4 TB 的数据库。 无法在常规用途实例上还原包含内存中 OLTP 文件或280个以上文件的业务关键数据库。

### <a name="service-broker"></a>服务代理

不支持跨实例 Service Broker：

- `sys.routes`：作为先决条件，你必须从 sys.databases 中选择地址。 地址必须在每个路由上都是本地的。 请参阅 [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)。
- `CREATE ROUTE`：不能使用除 `LOCAL`以外 `ADDRESS` `CREATE ROUTE`。 请参阅 [CREATE ROUTE](/sql/t-sql/statements/create-route-transact-sql)。
- `ALTER ROUTE`：不能使用除 `LOCAL`以外 `ADDRESS` `ALTER ROUTE`。 请参阅 [ALTER ROUTE](/sql/t-sql/statements/alter-route-transact-sql)。 

### <a name="stored-procedures-functions-and-triggers"></a>存储过程、函数和触发器

- 常规用途层不支持 `NATIVE_COMPILATION`。
- 不支持以下 [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 选项： 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- 不支持 `sp_execute_external_scripts`。 请参阅 [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)。
- 不支持 `xp_cmdshell`。 请参阅 [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)。
- `Extended stored procedures` 不受支持，其中包括 `sp_addextendedproc` 和 `sp_dropextendedproc`。 请参阅[扩展存储过程](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)。
- 不支持 `sp_attach_db`、`sp_attach_single_file_db` 和 `sp_detach_db`。 请参阅 [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql)、[sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 和 [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)。

### <a name="system-functions-and-variables"></a>系统函数和变量

以下变量、函数和视图返回不同的结果：

- `SERVERPROPERTY('EngineEdition')` 返回值8。 此属性唯一标识托管实例。 请参阅 [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql)。
- `SERVERPROPERTY('InstanceName')` 返回 NULL，因为 SQL Server 的实例的概念对托管实例不适用。 请参阅 [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql)。
- `@@SERVERNAME` 返回完整的 DNS "可连接" 名称，例如 my-managed-instance.wcus17662feb9ce98.database.windows.net。 请参阅 [@@SERVERNAME](/sql/t-sql/functions/servername-transact-sql)。 
- `SYS.SERVERS` 返回完整的 DNS "可连接" 名称，如属性 "name" 和 "data_source" 的 `myinstance.domain.database.windows.net`。 请参阅 [SYS.SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)。
- `@@SERVICENAME` 返回 NULL，因为 SQL Server 存在的服务概念对托管实例不适用。 请参阅 [@@SERVICENAME](/sql/t-sql/functions/servicename-transact-sql)。
- 支持 `SUSER_ID`。 如果 Azure AD 登录名不在 syslogins 中，它将返回 NULL。 请参阅 [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql)。 
- 不支持 `SUSER_SID`。 返回错误的数据，这是一个临时已知问题。 请参阅 [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql)。 

## <a name="Environment"></a>环境约束

### <a name="subnet"></a>子网
-  不能将任何其他资源（例如虚拟机）放入部署了托管实例的子网中。 使用其他子网部署这些资源。
- 子网必须具有足够数量的可用[IP 地址](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。 最小值为16，但建议至少在子网中包含32个 IP 地址。
- [无法将服务终结点与托管实例的子网相关联](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。 请确保在创建虚拟网络时禁用了 "服务终结点" 选项。
- 在某个区域中，可以部署的 Vcore 和实例类型有一些[限制和限制](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)。
- 某些[安全规则必须应用于子网](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。

### <a name="vnet"></a>VNET
- 可以使用资源模型-不支持 VNet 的经典模型来部署 VNet。
- 创建托管实例后，不支持将托管实例或 VNet 移到另一个资源组或订阅。
- 某些服务（如应用服务环境、逻辑应用和托管实例，用于异地复制、事务复制或通过链接服务器）无法访问不同区域中的托管[实例。](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) 可以通过 ExpressRoute 或 vnet 到 vnet 网关连接到这些资源。

### <a name="tempdb"></a>TEMPDB

`tempdb` 的最大文件大小不能大于常规用途层上每个核心的 24 GB。 业务关键层上的最大 `tempdb` 大小受实例存储大小的限制。 常规用途层上 `Tempdb` 日志文件大小限制为 120 GB。 如果 `tempdb` 中的每个核心需要超过 24 GB，或者它们产生超过 120 GB 的日志数据，则某些查询可能会返回错误。

### <a name="msdb"></a>MSDB

托管实例中的以下 MSDB 架构必须由其各自的预定义角色拥有：

- 常规角色
  - TargetServersRole
- [固定数据库角色](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [数据库邮件角色](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile)：
  - DatabaseMailUserRole
- [Integration services 角色](https://docs.microsoft.com/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15)：
  - msdb
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> 更改用户的预定义角色名称、架构名称和架构所有者将会影响服务的正常运行。 对它们所做的任何更改都将还原到预定义的值，一旦检测到，或在最新的下一次服务更新中，以确保正常服务操作。

### <a name="error-logs"></a>错误日志

托管实例将详细信息放在错误日志中。 错误日志中记录了很多内部系统事件。 使用自定义过程来读取用于筛选出某些不相关项的错误日志。 有关详细信息，请参阅[托管实例–](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) Azure Data Studio 的 sp_readmierrorlog 或[托管实例扩展（预览版）](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) 。

## <a name="Issues"></a>已知问题


### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>通过门户为故障转移组进行手动故障转移的限制

**日期：** Jan 2020

如果故障转移组跨不同 Azure 订阅或资源组中的实例，则无法从故障转移组中的主实例启动手动故障转移。

**解决方法**：通过门户从异地辅助实例启动故障转移。

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL 代理角色需要对非 sysadmin 登录名的显式执行权限

**日期：** Dec 2019

如果将非 sysadmin 登录名添加到[SQL 代理固定数据库角色](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)中的任何一个，则会出现一个问题，需要对这些登录名的主存储过程授予显式执行权限才能正常工作。 如果遇到此问题，将显示错误消息 "对象 < 上的 EXECUTE 权限被拒绝 object_name > （Microsoft SQL Server，错误：229）"。

**解决方法**：将登录名添加到 SQL 代理固定数据库角色之一： SQLAgentUserRole、SQLAgentReaderRole 或 SQLAgentOperatorRole 后，对于每个添加到这些角色的登录名，请执行以下 t-sql 脚本，将 execute 权限显式授予列出的存储过程。

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>SQL 代理作业可能会在代理进程重新启动后中断

**日期：** Dec 2019

SQL 代理在每次启动作业时都会创建一个新会话，这逐渐增加了内存消耗。 若要避免出现会阻止执行计划作业的内部内存限制，则在内存占用达到阈值时，将重新启动代理进程。 这可能会导致在重新启动时中断运行的作业。

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>不应用内存中 OLTP 内存限制

**日期：** Oct 2019

在某些情况下，业务关键服务层将无法正确应用[内存优化对象的最大内存限制](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space)。 托管实例可使工作负荷对内存中 OLTP 操作使用更多内存，这可能会影响实例的可用性和稳定性。 达到限制的内存中 OLTP 查询可能不会立即失败。 此问题即将解决。 如果使用内存中 OLTP 内存更多的查询达到[限制](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space)，则会更快地失败。

**解决方法：** 使用[SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) [监视内存中 OLTP 存储使用情况](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring)，确保工作负荷不会使用超过可用内存。 增加依赖于 Vcore 数量的内存限制，或优化工作负荷以使用更少的内存。

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>尝试删除不为空的文件时返回错误错误

**日期：** Oct 2019

SQL Server/托管实例[不允许用户删除不为空的文件](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites)。 如果尝试使用 `ALTER DATABASE REMOVE FILE` 语句删除非空的数据文件，则不会立即返回 `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` 错误。 托管实例将继续尝试删除该文件，并在与 `Internal server error`30 分钟后，操作将失败。

**解决方法**：使用 `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` 命令删除文件内容。 如果这是文件组中的唯一文件，则需要在收缩文件之前删除与此文件组关联的表或分区中的数据，并选择性地将这些数据加载到另一个表/分区。

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>更改服务层和创建实例操作被正在进行的数据库还原操作阻止

**日期：** 09月2019

正在进行的 `RESTORE` 语句、数据迁移服务迁移过程和内置时间点还原将阻止更新服务层或调整现有实例的大小，并创建新实例，直到还原过程完成。 还原过程将在运行还原过程的同一子网中阻止这些操作。 实例池中的实例不受影响。 创建或更改服务层操作不会失败或超时-还原过程完成或取消后，它们将继续。

**解决方法**：等待还原过程完成，如果创建或更新服务层操作的优先级较高，则取消还原过程。

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>在故障转移后，可能需要重新配置业务关键服务层上的 Resource Governor

**日期：** 09月2019

[Resource Governor](/sql/relational-databases/resource-governor/resource-governor)功能，使你能够限制分配给用户工作负荷的资源可能会在故障转移后错误地分类某些用户工作负荷，或者在用户启动的服务层更改时（例如，更改 max vCore 或 max 实例存储大小）。

**解决方法**：如果你使用的是[Resource Governor](/sql/relational-databases/resource-governor/resource-governor)，则在启动实例时，定期或作为 sql 代理作业的一部分运行 `ALTER RESOURCE GOVERNOR RECONFIGURE`。

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>跨数据库 Service Broker 对话框必须在服务层升级后重新初始化

**日期：** 2019年8月

更改服务层操作后，跨数据库 Service Broker 对话框将停止向其他数据库中的服务传递消息。 消息不会**丢失**，并且可以在发送方队列中找到它们。 在托管实例中更改 Vcore 或实例存储大小将导致为所有数据库更改[sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql)视图中 `service_broke_guid` 值。 使用[BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql)语句创建的任何 `DIALOG` 引用其他数据库中的 Service broker，都将停止向目标服务传递消息。

**解决方法：** 先停止使用跨数据库 Service Broker 对话会话的任何活动，然后再更新服务层并在之后重新初始化它们。 如果存在服务层更改后未传递的剩余消息，请从源队列中读取消息，并将其重新发送到目标队列。

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>不支持 Azure AD 登录类型的 Impersonification

**日期：** 2019年7月

不支持使用以下 AAD 主体 `EXECUTE AS USER` 或 `EXECUTE AS LOGIN` 的模拟：
-   化名为 AAD 的用户。 在此示例中，将返回以下错误 `15517`。
- 基于 AAD 应用程序或服务主体的 AAD 登录名和用户。 在这种情况下，将返回以下错误 `15517` 和 `15406`。

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>sp_send_db_mail 不支持 @query 参数

**日期：** 2019年4月

[Sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql)过程中的 `@query` 参数不起作用。

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>在异地故障转移之后，必须重新配置事务复制

**日期：** 三月2019

如果对自动故障转移组中的数据库启用了事务复制，则托管实例管理员必须清除旧主副本上的所有发布，并在故障转移到另一个区域后将它们重新配置到新的主副本。 有关更多详细信息，请参阅[复制](#replication)。

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>SSDT 中不支持 AAD 登录名和用户

**日期：** 11月2019

SQL Server Data Tools 不完全支持 Azure Active directory 登录名和用户。

### <a name="temporary-database-is-used-during-restore-operation"></a>在还原操作过程中使用临时数据库

在托管实例上还原数据库时，还原服务将首先创建一个具有所需名称的空数据库，以在实例上分配该名称。 经过一段时间后，将删除此数据库并启动实际数据库的还原。 正在*还原*状态的数据库将临时具有一个随机 GUID 值而不是名称。 还原过程完成后，临时名称将更改为 `RESTORE` 语句中指定的所需名称。 在初始阶段，用户可以访问空数据库，甚至可以在此数据库中创建表或加载数据。 当还原服务启动第二个阶段时，将删除此临时数据库。

**解决方法**：在你看到还原完成之前，不要访问你要还原的数据库。

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB 结构和内容是重新创建的

`tempdb` 数据库将始终拆分为12个数据文件，并且无法更改文件结构。 无法更改每个文件的最大大小，并且无法将新文件添加到 `tempdb`。 当实例启动或故障转移时，始终会将 `Tempdb` 重新创建为空数据库，且不会保留在 `tempdb` 中所做的任何更改。

### <a name="exceeding-storage-space-with-small-database-files"></a>小型数据库文件超出存储空间

`CREATE DATABASE`、`ALTER DATABASE ADD FILE`和 `RESTORE DATABASE` 语句可能会失败，因为实例可以达到 Azure 存储限制。

对于 Azure 高级磁盘空间，每个常规用途托管实例最多可保留 35 TB 的存储。 每个数据库文件都放置在单独的物理磁盘上。 磁盘大小可以为 128 GB、256 GB、512 GB、1 TB 或 4 TB。 磁盘上未使用的空间未收费，但 Azure 高级磁盘大小的总大小不能超过 35 TB。 在某些情况下，由于内部碎片，不需要 8 TB 的托管实例可能会超过 35 TB 的 Azure 存储大小限制。

例如，一个常规用途的托管实例可能有一个大小为 1.2 TB 的大型文件，大小为 4 TB 的磁盘。 它还可能具有每个大小为 1 GB 的248文件，每个文件放在单独的 128 GB 磁盘上。 在本示例中：

- 分配的磁盘存储总大小为 1 x 4 TB + 248 x 128 GB = 35 TB。
- 实例上的数据库的总预留空间为 1 x 1.2 TB + 248 x 1 GB = 1.4 TB。

此示例说明，在某些情况下，由于文件的特定分发，托管实例可能会在你可能不想要的情况下达到为附加的 Azure 高级磁盘保留的 35 TB 限制。

在此示例中，只要未添加新文件，现有数据库就可以继续工作，并且无需任何问题就能增长。 由于新磁盘驱动器的空间不足，因此无法创建或还原新数据库，即使所有数据库的总大小都不会达到实例大小限制。 在这种情况下返回的错误并不清楚。

您可以通过使用系统视图来[确定剩余文件的数目](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)。 如果达到此限制，请尝试[使用 DBCC SHRINKFILE 语句为空并删除一些较小的文件](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)，或切换到[没有此限制的业务关键层](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。

### <a name="guid-values-shown-instead-of-database-names"></a>显示的 GUID 值而不是数据库名称

多个系统视图、性能计数器、错误消息、XEvent 和错误日志条目显示了 GUID 数据库标识符而非实际的数据库名称。 不要依赖这些 GUID 标识符，因为将来会将其替换为实际的数据库名称。

### <a name="error-logs-arent-persisted"></a>不保留错误日志

托管实例中可用的错误日志不会持久保存，它们的大小不会包括在最大存储限制内。 发生故障转移时，可能会自动清除错误日志。 错误日志历史记录中可能存在间隔，因为在多个虚拟机上多次移动了托管实例。

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>同一实例中的两个数据库上的事务范围不受支持

如果两个查询发送到相同事务范围下同一实例中的两个数据库，则 .NET 中的 `TransactionScope` 类不起作用：

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

尽管此代码适用于同一实例中的数据，但它需要 MSDTC。

**解决方法：** 使用[ChangeDatabase （String）](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase)在连接上下文中使用其他数据库，而不是使用两个连接。

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR 模块和链接服务器有时无法引用本地 IP 地址

放置在托管实例中的 CLR 模块和引用当前实例的链接服务器或分布式查询有时无法解析本地实例的 IP。 此错误是暂时性问题。

**解决方法：** 如果可能，请在 CLR 模块中使用上下文连接。

## <a name="next-steps"></a>后续步骤

- 有关托管实例的详细信息，请参阅[什么是托管实例？](sql-database-managed-instance.md)
- 有关功能和比较列表，请参阅[AZURE SQL 数据库功能比较](sql-database-features.md)。
- 有关演示如何创建新的托管实例的快速入门，请参阅[创建托管实例](sql-database-managed-instance-get-started.md)。
