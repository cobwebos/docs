---
title: SQL Server 数据库备份的疑难解答
description: 有关使用 Azure 备份来备份在 Azure VM 上运行的 SQL Server 数据库的故障排除信息。
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: d49843e8fd96df29a7359ec639e42d312ad584e2
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659247"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>使用 Azure 备份排查 SQL Server 数据库备份问题

本文提供了有关在 Azure 虚拟机上运行的 SQL Server 数据库的疑难解答信息。

有关备份过程和限制的详细信息，请参阅[关于在 Azure vm 中备份 SQL Server](backup-azure-sql-database.md#feature-consideration-and-limitations)。

## <a name="sql-server-permissions"></a>SQL Server 权限

若要为虚拟机上的 SQL Server 数据库配置保护，必须在该虚拟机上安装**AzureBackupWindowsWorkload**扩展。 如果收到错误**UserErrorSQLNoSysadminMembership**，则表示 SQL Server 实例没有所需的备份权限。 若要修复此错误，请按照[设置 VM 权限](backup-azure-sql-database.md#set-vm-permissions)中的步骤操作。

## <a name="troubleshoot-discover-and-configure-issues"></a>发现和配置问题疑难解答
创建和配置恢复服务保管库后，发现数据库和配置备份的过程分为两个步骤。<br>

![sql](./media/backup-azure-sql-database/sql.png)

在备份配置过程中，如果 SQL VM 及其实例在**vm 的发现**数据库中不可见并**配置备份**（请参阅上图），请确保：

### <a name="step-1-discovery-dbs-in-vms"></a>步骤1： Vm 中的发现数据库

- 如果 VM 未列在 "已发现的 VM" 列表中，也未在其他保管库中为 SQL 备份注册，请按照[发现 SQL Server 备份](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#discover-sql-server-databases)步骤操作。

### <a name="step-2-configure-backup"></a>步骤2：配置备份

- 如果在用于保护数据库的同一个保管库中注册了 SQL VM 的保管库，请按照[配置备份](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#configure-backup)步骤操作。

如果需要在新保管库中注册 SQL VM，则必须将其从旧保管库中注销。  从保管库中取消注册 SQL VM 需要停止保护所有受保护的数据源，然后可以删除已备份的数据。 删除备份的数据是一种破坏性操作。  查看并采取取消注册 SQL VM 的所有预防措施后，请将此同一 VM 注册到新的保管库，然后重试备份操作。



## <a name="error-messages"></a>错误消息

### <a name="backup-type-unsupported"></a>不支持的备份类型

| 严重性 | Description | 可能的原因 | 建议的操作 |
|---|---|---|---|
| 警告 | 此数据库的当前设置不支持关联策略中存在特定的备份类型。 | <li>只能对 master 数据库执行完整数据库备份操作。 不能进行差异备份和事务日志备份。 </li> <li>简单恢复模式中的任何数据库都不允许备份事务日志。</li> | 将数据库设置修改为支持策略中的所有备份类型。 或者，将当前策略更改为仅包括支持的备份类型。 否则，在计划的备份过程中将跳过不支持的备份类型，否则备份作业将失败。

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 此 SQL 数据库不支持所请求的备份类型。 | 当数据库恢复模式不允许所请求的备份类型时，会发生此错误。 在以下情况下，可能会发生此错误： <br/><ul><li>使用简单恢复模式的数据库不允许日志备份。</li><li>Master 数据库不允许进行差异备份和日志备份。</li></ul>有关更多详细信息，请参阅[SQL Server 恢复模式](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server)文档。 | 如果简单恢复模式中的数据库的日志备份失败，请尝试下列选项之一：<ul><li>如果数据库处于简单恢复模式，请禁用日志备份。</li><li>使用[SQL Server 文档](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server)将数据库恢复模式更改为完整或大容量日志记录。 </li><li> 如果不想要更改恢复模式，并使用标准策略来备份无法更改的多个数据库，请忽略此错误。 完整备份和差异备份会按计划进行。 在这种情况下，预期会跳过日志备份。</li></ul>如果它是 master 数据库并且已配置了差异备份或日志备份，请使用以下步骤之一：<ul><li>使用门户将 master 数据库的备份策略计划更改为 "完整"。</li><li>如果使用标准策略来备份无法更改的多个数据库，请忽略此错误。 完整备份会按计划进行。 在这种情况下，预期不会发生差异备份或日志备份。</li></ul> |
| 操作将被取消，因为已对同一个数据库运行了某个有冲突的操作。 | 查看有关并发运行的[备份和还原限制的博客文章](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database)。| [使用 SQL Server Management Studio （SSMS）来监视备份作业](manage-monitor-sql-database-backup.md)。 冲突操作失败后，请重新启动操作。|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| SQL 数据库不存在。 | 该数据库已被删除或重命名。 | 检查是否意外删除或重命名了该数据库。<br/><br/> 如果意外删除了该数据库，若要继续备份，请将该数据库还原到原始位置。<br/><br/> 如果已删除数据库，但不需要将来进行备份，则在恢复服务保管库中，选择 "**停止备份**并**保留备份数据**" 或 "**删除备份数据**"。 有关详细信息，请参阅[管理和监视备份的 SQL Server 数据库](manage-monitor-sql-database-backup.md)。

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 日志链已中断。 | 数据库或 VM 通过其他备份解决方案进行备份，该解决方案将截断日志链。|<ul><li>检查是否正在使用其他备份解决方案或脚本。 如果是，请停止其他备份解决方案。 </li><li>如果备份是按需日志备份，请触发完整备份以启动新的日志链。 对于计划日志备份，无需执行任何操作，因为 Azure 备份服务会自动触发完整备份以解决此问题。</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| Azure 备份无法连接到 SQL 实例。 | Azure 备份无法连接到 SQL Server 实例。 | 使用 Azure 门户错误 "菜单上的其他详细信息来缩小根本原因。 请参阅 [SQL 备份故障排除](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)来解决错误。<br/><ul><li>如果默认的 SQL 设置不允许远程连接，请更改设置。 有关更改设置的信息，请参阅以下文章：<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>如果有登录问题，请使用以下链接修复这些问题：<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 此数据源缺少首次完整备份。 | 数据库缺少首次完整备份。 日志备份和差异备份是完整备份的父项，因此请确保在触发差异备份或日志备份之前执行完整备份。 | 触发按需完整备份。   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 由于数据源的事务日志已满，无法创建备份。 | 数据库事务日志空间已满。 | 若要解决此问题，请参阅[SQL Server 文档](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)。 |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 目标位置已存在同名的数据库 | 目标还原目标已具有同名的数据库。  | <ul><li>更改目标数据库名称。</li><li>或者，使用 "还原" 页上的 "强制覆盖" 选项。</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 由于无法将数据库脱机，还原失败。 | 执行还原时，目标数据库需要处于脱机状态。 Azure 备份不能使此数据脱机。 | 使用 Azure 门户错误 "菜单上的其他详细信息来缩小根本原因。 有关详细信息，请参阅 [SQL Server 文档](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms)。 |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 在目标上找不到包含指纹的服务器证书。 | 目标实例上的 master 数据库没有有效的加密指纹。 | 将源实例上使用的有效证书指纹导入到目标实例。 |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 用于恢复的日志备份包含批量记录的更改。 它不能用来按照 SQL 准则随时停止。 | 当数据库处于大容量日志恢复模式时，大容量日志事务和下一日志事务之间的数据将无法恢复。 | 为恢复选择其他时间点。 [了解详细信息](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105))。

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 由于可用性组的某些节点未注册，无法满足 SQL Always On 可用性组的备份首选项。 | 执行备份所需的节点未注册或不可访问。 | <ul><li>请确保执行此数据库备份所需的所有节点均已注册并运行正常，然后重试该操作。</li><li>更改 SQL Server Always On 可用性组的备份首选项。</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| SQL Server VM 已关闭，或者无法让 Azure 备份服务访问。 | VM 已关闭。 | 确保 SQL Server 实例正在运行。 |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| Azure 备份服务使用 Azure VM 来宾代理执行备份，但来宾代理在目标服务器上不可用。 | 来宾代理未启用或不正常。 | 手动[安装 VM 来宾代理](../virtual-machines/extensions/agent-windows.md)。 |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 自动保护意向被删除或不再有效。 | 启用 SQL Server 实例的自动保护时，为该实例中的所有数据库**配置备份**作业运行。 如果在作业运行时禁用自动保护，则会使用此错误代码取消**正在进行的**作业。 | 再次启用自动保护，以帮助保护所有剩余的数据库。 |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
操作被阻止，因为你已达到24小时内允许的操作数量限制。 | 如果已达到24小时内操作的最大允许限制，则会出现此错误。 <br> 例如：如果达到了每天可触发的配置备份作业数的限制，并且你尝试在新项上配置备份，你将看到此错误。 | 通常，在24小时后重试操作可解决此问题。 但是，如果问题仍然存在，可以联系 Microsoft 支持部门以获得帮助。

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
操作被阻止，因为保管库已达到范围24小时内允许的此类操作的最大限制。 | 如果已达到24小时内操作的最大允许限制，则会出现此错误。 当存在大规模操作（如修改策略或自动保护）时，通常会出现此错误。 与 CloudDosAbsoluteLimitReached 不同的是，为了解决这种状态，实际上，Azure 备份服务将为相关的所有项目重试操作。<br> 例如：如果你有大量使用策略保护的数据源，并且你尝试修改该策略，则它将为每个受保护的项触发配置保护作业，有时可能会达到每日此类操作允许的最大限制。| Azure 备份服务会在24小时后自动重试此操作。

### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
由于 internet 连接问题，VM 无法联系 Azure 备份服务。 | VM 需要与 Azure 备份服务、Azure 存储或 Azure Active Directory 服务建立出站连接。| -如果你使用 NSG 来限制连接，则应使用 AzureBackup service 标记，以允许对 azure 备份服务、Azure 存储或 Azure Active Directory 服务的出站访问。 请按照以下[步骤](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#allow-access-using-nsg-tags)授予访问权限。<br>-确保 DNS 正在解析 Azure 终结点。<br>-检查 VM 是否在阻止访问 internet 的负载均衡器后面。 通过向 Vm 分配公共 IP，发现将起作用。<br>-验证没有防火墙/防病毒/代理正在阻止调用上述三个目标服务。


## <a name="re-registration-failures"></a>重新注册失败

在触发重新注册操作之前，请检查是否有以下一个或多个症状：

* 所有操作（例如备份、还原和配置备份）在 VM 上失败，并出现以下错误代码之一： **WorkloadExtensionNotReachable**、 **UserErrorWorkloadExtensionNotInstalled**、 **WorkloadExtensionNotPresent**、 **WorkloadExtensionDidntDequeueMsg**。
* 如果备份项的**备份状态**区域显示为 "**无法访问**"，请排除可能导致相同状态的所有其他原因：

  * 缺少对 VM 执行与备份相关的操作的权限。
  * 关闭 VM，因此无法进行备份。
  * 网络问题。

   ![正在重新注册 VM](./media/backup-azure-sql-database/re-register-vm.png)



* 如果是 Always On 可用性组，则在更改备份首选项或故障转移后，备份会失败。

由于以下一个或多个原因，可能会出现这些症状：

* 已从门户中删除或卸载扩展。
* 在**卸载或更改程序**下，已从 VM 上的 "**控制面板**" 中卸载扩展。
* VM 通过就地磁盘还原后恢复。
* VM 关闭的时间过长，因此它的扩展配置已过期。
* VM 已被删除，并且创建了另一个具有相同名称的 VM，并在与已删除 VM 相同的资源组中。
* 某个可用性组节点未接收到完整的备份配置。 当可用性组注册到保管库或添加新节点时，可能会发生这种情况。

在上述方案中，我们建议你在 VM 上触发重新注册操作。 有关如何在 PowerShell 中执行此任务的说明，请参阅[此处](https://docs.microsoft.com/azure/backup/backup-azure-sql-automation#enable-backup)。

## <a name="size-limit-for-files"></a>文件大小限制

文件的总字符串大小不仅取决于文件的数量，还取决于文件的名称和路径。 对于每个数据库文件，获取逻辑文件名和物理路径。 您可以使用以下 SQL 查询：

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

现在，按以下格式排列它们：

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

下面是一个示例：

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

如果内容的字符串大小超过20000字节，则以不同的方式存储数据库文件。 在恢复期间，你将无法设置还原的目标文件路径。 文件将还原到 SQL Server 提供的默认 SQL 路径。

### <a name="override-the-default-target-restore-file-path"></a>替代默认目标还原文件路径

在还原操作过程中，你可以通过将包含数据库文件映射的 JSON 文件放置到目标还原路径，来覆盖目标还原文件路径。 创建 `database_name.json` 文件，并将其放置在*C:\Program Files\Azure Backup\bin\plugins\SQL*中的位置。

文件的内容应采用以下格式：

```json
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

下面是一个示例：

```json
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

在上述内容中，可以使用以下 SQL 查询获取数据库文件的逻辑名称：

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```

此文件应在触发还原操作之前放置。

## <a name="next-steps"></a>后续步骤

有关 SQL Server Vm （公共预览版）的 Azure 备份的详细信息，请参阅[适用于 SQL vm 的 Azure 备份](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup)。
