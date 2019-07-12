---
title: 使用 Azure 备份 SQL Server 数据库备份进行故障排除 |Microsoft Docs
description: 有关使用 Azure 备份来备份在 Azure VM 上运行的 SQL Server 数据库的故障排除信息。
services: backup
author: anuragm
manager: sivan
ms.service: backup
ms.topic: article
ms.date: 06/18/2019
ms.author: anuragm
ms.openlocfilehash: b2822a3c7dfa23065f2cbd35ef4e506efae026f2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704847"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>使用 Azure 备份 SQL Server 数据库备份进行故障排除

本文提供 Azure 虚拟机上运行的 SQL Server 数据库的故障排除信息。

有关备份过程和限制的详细信息，请参阅[在 Azure Vm 中的有关 SQL Server 备份](backup-azure-sql-database.md#feature-consideration-and-limitations)。

## <a name="sql-server-permissions"></a>SQL Server 权限

若要配置的虚拟机上的 SQL Server 数据库的保护，必须安装**AzureBackupWindowsWorkload**该虚拟机上的扩展。 如果收到错误**UserErrorSQLNoSysadminMembership**，这意味着您的 SQL Server 实例不具有所需的备份权限。 若要修复此错误，请按照中的步骤[设置 VM 权限](backup-azure-sql-database.md#set-vm-permissions)。

## <a name="error-messages"></a>错误消息

### <a name="backup-type-unsupported"></a>不受支持的备份类型

| Severity | 描述 | 可能的原因 | 建议的操作 |
|---|---|---|---|
| 警告 | 为此数据库的当前设置不支持某些关联的策略中存在的备份类型。 | <li>仅完整数据库备份操作可以对 master 数据库执行。 差异备份和事务日志备份都不可能。 </li> <li>简单恢复模式中的任何数据库不允许的事务日志备份。</li> | 将数据库设置修改为支持策略中的所有备份类型。 或者，更改当前的策略，以包含仅支持的备份类型。 否则为将在计划备份期间跳过不受支持的备份类型或备份作业的即席备份将失败。


### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 此 SQL 数据库不支持所请求的备份类型。 | 当数据库恢复模式不允许所请求的备份类型时，会发生此错误。 在以下情况下，可能会发生此错误： <br/><ul><li>使用简单恢复模式的数据库不允许日志备份。</li><li>Master 数据库不允许差异和日志备份。</li></ul>有关更多详细信息，请参阅[SQL Server 恢复模式](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server)文档。 | 如果在简单恢复模式的数据库的日志备份失败，请尝试以下选项之一：<ul><li>如果数据库处于简单恢复模式，请禁用日志备份。</li><li>使用[SQL Server 文档](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server)更改为完整的数据库恢复模式或大容量日志记录。 </li><li> 如果不想要更改恢复模式，并使用标准策略来备份无法更改的多个数据库，请忽略此错误。 完整备份和差异备份会按计划进行。 在这种情况下，预期会跳过日志备份。</li></ul>如果它是主数据库，并且已配置差异或日志备份，使用以下步骤：<ul><li>使用门户更改为完整的 master 数据库的备份策略计划。</li><li>如果使用标准策略来备份无法更改的多个数据库，请忽略此错误。 完整备份会按计划进行。 在这种情况下，预期不会发生差异备份或日志备份。</li></ul> |
| 操作将被取消，因为已对同一个数据库运行了某个有冲突的操作。 | 请参阅[有关备份和还原的限制的博客文章](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database)同时运行。| [使用 SQL Server Management Studio (SSMS) 来监视备份作业](manage-monitor-sql-database-backup.md)。 冲突的操作失败后，重新启动该操作。|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| SQL 数据库不存在。 | 该数据库已被删除或重命名。 | 检查是否意外删除或重命名了该数据库。<br/><br/> 如果意外删除了该数据库，若要继续备份，请将该数据库还原到原始位置。<br/><br/> 如果你删除了该数据库而不需要将来的备份，然后在恢复服务保管库中，选择**停止备份**与**保留备份数据**或**删除备份数据**。 有关详细信息，请参阅[管理和监视备份 SQL Server 数据库](manage-monitor-sql-database-backup.md)。

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 日志链已中断。 | 数据库或 VM 通过另一个备份解决方案，将截断日志链备份。|<ul><li>检查是否正在使用其他备份解决方案或脚本。 如果是，请停止其他备份解决方案。 </li><li>如果备份是临时的日志备份，请触发完整备份以启动新的日志链。 对于计划的日志备份，不需要执行任何操作，因为 Azure 备份服务会自动触发完整备份以解决此问题。</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| Azure 备份无法连接到 SQL 实例。 | Azure 备份无法连接到 SQL Server 实例。 | 使用 Azure 门户错误菜单上的其他详细信息以缩小的根本原因。 请参阅 [SQL 备份故障排除](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)来解决错误。<br/><ul><li>如果默认 SQL 设置不允许远程连接，更改的设置。 请参阅以下文章，了解有关更改的设置的信息：<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>如果登录问题，使用这些链接可以解决这些问题：<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 此数据源缺少首次完整备份。 | 数据库缺少首次完整备份。 日志和差异备份父项的完整备份，因此请确保在触发之前差异进行完整备份或日志备份。 | 触发即席完整备份。   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 由于数据源的事务日志已满，无法创建备份。 | 数据库事务日志空间已满。 | 若要解决此问题，请参阅[SQL Server 文档](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)。 |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 目标位置已存在同名的数据库 | 目标还原目标中已存在具有相同名称的数据库。  | <ul><li>更改目标数据库名称。</li><li>或者，使用还原页上的强制覆盖选项。</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 由于无法将数据库脱机，还原失败。 | 而要执行还原，目标数据库必须处于脱机状态。 Azure 备份不能将此数据脱机。 | 使用 Azure 门户错误菜单上的其他详细信息以缩小的根本原因。 有关详细信息，请参阅[SQL Server 文档](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms)。 |

###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 在目标上找不到包含指纹的服务器证书。 | 在目标实例上的 master 数据库不存在有效的加密指纹。 | 导入源实例，对目标实例上使用有效的证书指纹。 |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 用于恢复的日志备份包含批量记录的更改。 它不能用来按照 SQL 准则随时停止。 | 如果数据库是在大容量日志恢复模式下，大容量日志事务和下一个日志事务之间的数据无法恢复。 | 选择一个不同点的恢复时间。 [了解详细信息](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105))。


### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 由于可用性组的某些节点未注册，无法满足 SQL Always On 可用性组的备份首选项。 | 执行备份所需的节点未注册或不可访问。 | <ul><li>请确保执行此数据库的备份所需的所有节点已注册并运行正常，，然后重试该操作。</li><li>更改 SQL Server Always On 可用性组的备份首选项。</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| SQL Server VM 已关闭，或者无法让 Azure 备份服务访问。 | 关闭 VM。 | 请确保正在运行的 SQL Server 实例。 |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| Azure 备份服务使用 Azure VM 来宾代理执行备份，但来宾代理在目标服务器上不可用。 | 来宾代理未启用或不正常。 | 手动[安装 VM 来宾代理](../virtual-machines/extensions/agent-windows.md)。 |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 自动保护意向被删除或不再有效。 | 启用自动保护 SQL Server 实例上时**配置备份**作业运行的该实例中的所有数据库。 如果在作业运行时禁用自动保护，则会使用此错误代码取消**正在进行的**作业。 | 启用自动保护再一次来帮助保护所有剩余的数据库。 |

## <a name="re-registration-failures"></a>重新注册失败

触发重新注册操作之前检查一个或多个以下症状：

* 所有操作 （如备份、 还原和配置备份） 无法应用于具有以下错误代码的一个 VM:**WorkloadExtensionNotReachable**， **UserErrorWorkloadExtensionNotInstalled**， **WorkloadExtensionNotPresent**， **WorkloadExtensionDidntDequeueMsg**.
* **备份状态**显示备份项的区域**不可访问**。 排除可能会导致同一状态的所有其他原因：

  * 没有的权限来执行与备份相关的 VM 上的操作  
  * 关闭 VM，因此无法执行备份
  * 网络问题  

  !["Not reachable"状态中重新注册 VM](./media/backup-azure-sql-database/re-register-vm.png)

* 对于 Always On 可用性组，将备份开始失败更改备份首选项后，或在故障转移。

这些症状可能出现的一个或多个原因如下：

* 扩展已被删除，或从门户卸载。 
* 从已卸载扩展**Control Panel**在 VM 上**卸载或更改程序**。
* 在通过就地磁盘还原的时间已还原 VM。
* VM 已关闭长时间，以便在其上的扩展配置已过期。
* 已删除 VM，并具有相同名称和已删除 VM 所在的资源组中创建另一个 VM。
* 一个可用性组节点未收到完整的备份配置。 这可能发生时的可用性组已注册到保管库或添加新节点。

在前面的方案中，我们建议您触发在 VM 上的重新注册操作。 现在，此选项才可用只能通过 PowerShell。

## <a name="size-limit-for-files"></a>文件大小限制

文件的总字符串大小取决于不仅上的文件数，还在其名称和路径。 对于每个数据库文件，请获取逻辑文件名称和物理路径。 可以使用以下 SQL 查询：

```
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

现在排列它们采用以下格式：

```
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

以下是一个示例：

```
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

如果内容的字符串大小超过 20,000 个字节，以不同的方式存储数据库文件。 在恢复期间，你将无法设置还原的目标文件路径。 文件将还原到 SQL Server 提供的默认 SQL 路径。

### <a name="override-the-default-target-restore-file-path"></a>重写默认目标还原文件路径

通过将包含映射到目标还原路径的数据库文件的 JSON 文件，可以在还原操作期间覆盖目标还原文件路径。 创建`database_name.json`文件，并将其放在位置*C:\Program Files\Azure 工作负荷 Backup\bin\plugins\SQL*。

文件的内容应按以下格式：
```
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

以下是一个示例：

```
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

在前面的内容中，可以使用以下 SQL 查询来获取的数据库文件的逻辑名称：

```
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```


触发还原操作之前，应置于此文件。

## <a name="next-steps"></a>后续步骤

SQL Server Vm （公共预览版） 的 Azure 备份的详细信息，请参阅[适用于 SQL Vm 的 Azure 备份](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup)。
