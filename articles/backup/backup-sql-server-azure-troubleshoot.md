---
title: 排查通过 Azure 备份进行 SQL Server数据库备份的问题 | Microsoft Docs
description: 有关使用 Azure 备份来备份在 Azure VM 上运行的 SQL Server 数据库的故障排除信息。
services: backup
author: anuragm
manager: shivamg
ms.service: backup
ms.topic: article
ms.date: 03/13/2019
ms.author: anuragm
ms.openlocfilehash: d8cbae679552cce8df29410ad8a477801abd4ff1
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58847457"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>排查在 Azure 上备份 SQL Server 的问题

本文提供有关在 Azure 上保护 SQL Server VM（预览版）的故障排除信息。

## <a name="feature-consideration-and-limitations"></a>功能注意事项和限制

若要查看需要考虑的功能，请参阅文章[在 Azure Vm 中的有关 SQL Server 备份](backup-azure-sql-database.md#feature-consideration-and-limitations)。

## <a name="sql-server-permissions"></a>SQL Server 权限

若要在虚拟机上为 SQL Server 数据库配置保护，必须在该虚拟机上安装 **AzureBackupWindowsWorkload** 扩展。 如果收到错误 **UserErrorSQLNoSysadminMembership**，则表示 SQL 实例没有所需的备份权限。 若要解决此错误，请遵循[为非市场 SQL VM 设置权限](backup-azure-sql-database.md#fix-sql-sysadmin-permissions)中的步骤。

## <a name="troubleshooting-errors"></a>排查错误

使用下表中的信息来排查在 Azure 中保护 SQL Server 时遇到的问题和错误。

## <a name="alerts"></a>警报

### <a name="backup-type-unsupported"></a>不受支持的备份类型

| 严重性 | 描述 | 可能的原因 | 建议的操作 |
|---|---|---|---|
| 警告 | 此数据库的当前设置不支持关联策略中的特定备份类型。 | <li>**Master DB**：仅完整数据库备份操作可以执行对 master 数据库;既不**差异**备份，也事务**日志**的备份。 </li> <li>简单恢复模式中的任何数据库都不允许进行事务日志备份。</li> | 将数据库设置修改为支持策略中的所有备份类型。 或者，将当前策略更改为只包含受支持的备份类型。 否则为将在计划备份期间跳过不受支持的备份类型或备份作业的即席备份将失败。


## <a name="backup-failures"></a>备份失败

下表按错误代码进行组织。

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 此 SQL 数据库不支持所请求的备份类型。 | 当数据库恢复模式不允许所请求的备份类型时，会发生此错误。 在以下情况下，可能会发生此错误： <br/><ul><li>使用简单恢复模式的数据库不允许日志备份。</li><li>不允许对 Master 数据库执行差异备份和日志备份。</li></ul>有关详细信息，请参阅 [SQL 恢复模式](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server)文档。 | 如果采用简单恢复模式的数据库的日志备份失败，请尝试以下选项之一：<ul><li>如果数据库处于简单恢复模式，请禁用日志备份。</li><li>使用 [SQL 文档](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server)将数据库恢复模式更改为“完整”或“批量日志记录”。 </li><li> 如果不想要更改恢复模式，并使用标准策略来备份无法更改的多个数据库，请忽略此错误。 完整备份和差异备份会按计划进行。 在这种情况下，预期会跳过日志备份。</li></ul>如果备份的是 Master 数据库，并且已配置差异备份或日志备份，请使用以下任一步骤：<ul><li>使用门户将 Master 数据库的备份策略计划更改为“完整”。</li><li>如果使用标准策略来备份无法更改的多个数据库，请忽略此错误。 完整备份会按计划进行。 在这种情况下，预期不会发生差异备份或日志备份。</li></ul> |
| 操作将被取消，因为已对同一个数据库运行了某个有冲突的操作。 | 请参阅[有关并行运行备份和还原时存在的限制的博客文章](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database)。| [使用 SQL Server Management Studio (SSMS) 监视备份作业。](manage-monitor-sql-database-backup.md) 有冲突的操作失败后，重启该操作。|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| SQL 数据库不存在。 | 该数据库已被删除或重命名。 | 检查是否意外删除或重命名了该数据库。<br/><br/> 如果意外删除了该数据库，若要继续备份，请将该数据库还原到原始位置。<br/><br/> 如果删除了该数据库，且将来不需要备份，请在恢复服务保管库中单击“停止备份 [和“删除/保留数据](manage-monitor-sql-database-backup.md) 。

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 日志链已中断。 | 数据库或 VM 是使用其他备份解决方案备份的，该解决方案截断了日志链。|<ul><li>检查是否正在使用其他备份解决方案或脚本。 如果是，请停止其他备份解决方案。 </li><li>如果备份是临时的日志备份，请触发完整备份以启动新的日志链。 对于计划的日志备份，不需要执行任何操作，因为 Azure 备份服务会自动触发完整备份来解决此问题。</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| Azure 备份无法连接到 SQL 实例。 | Azure 备份无法连接到 SQL 实例。 | 使用 Azure 门户上错误菜单中的其他详细信息缩小根本原因的范围。 请参阅 [SQL 备份故障排除](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)来解决错误。<br/><ul><li>如果默认 SQL 设置不允许远程连接，请更改设置。 参阅以下链接来更改设置。<ul><li>[https://msdn.microsoft.com/library/bb326495.aspx](https://msdn.microsoft.com/library/bb326495.aspx)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>如果出现登录问题，请参阅以下链接来解决问题：<ul><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 此数据源缺少首次完整备份。 | 数据库缺少首次完整备份。 日志备份和差异备份基于完整备份，因此，在触发差异备份或日志备份之前，必须先创建完整备份。 | 触发即席完整备份。   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 由于数据源的事务日志已满，无法创建备份。 | 数据库事务日志空间已满。 | 若要解决此问题，请参阅 [SQL 文档](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)。 |
| 此 SQL 数据库不支持所请求的备份类型。 | Always On AG 次要副本不支持完整备份和差异备份。 | <ul><li>如果触发即席备份，会触发在主节点上的备份。</li><li>如果备份是由策略计划的，请确保已注册主节点。 若要注册节点，[请遵循发现 SQL Server 数据库的步骤](backup-sql-server-database-azure-vms.md#discover-sql-server-databases)。</li></ul> |

## <a name="restore-failures"></a>还原失败

还原作业失败时显示以下错误代码。

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 目标位置已存在同名的数据库 | 目标还原位置已存在同名的数据库。  | <ul><li>更改目标数据库名称</li><li>或在还原页中使用强制覆盖选项</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 由于无法将数据库脱机，还原失败。 | 执行还原时，需将目标数据库脱机。 Azure 备份无法将此数据脱机。 | 使用 Azure 门户上错误菜单中的其他详细信息缩小根本原因的范围。 有关详细信息，请参阅 [SQL 文档](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms)。 |

###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 在目标上找不到包含指纹的服务器证书。 | 目标实例上的 Master 数据库没有有效的加密指纹。 | 将源实例上使用的有效证书指纹导入目标实例。 |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 用于恢复的日志备份包含大容量日志更改。 它不能用于在任意点根据 SQL 指导原则时间停止。 | 如果数据库是在大容量日志的恢复模式下，大容量日志事务和下一个日志事务之间的数据无法恢复。 | 请中的时间进行恢复，选择一个不同点。 [了解详细信息](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105))


## <a name="registration-failures"></a>注册失败

以下错误代码表示注册失败。

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 由于可用性组的某些节点未注册，无法满足 SQL Always On 可用性组的备份首选项。 | 执行备份所需的节点未注册或不可访问。 | <ul><li>确保对此数据库执行备份所需的所有节点已注册且正常，然后重试操作。</li><li>更改 SQL Always On 可用性组备份首选项。</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| SQL Server VM 已关闭，或者无法让 Azure 备份服务访问。 | VM 已关闭 | 确保 SQL Server 正在运行。 |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| Azure 备份服务使用 Azure VM 来宾代理执行备份，但来宾代理在目标服务器上不可用。 | 来宾代理未启用或不正常 | 手动[安装 VM 来宾代理](../virtual-machines/extensions/agent-windows.md)。 |

## <a name="configure-backup-failures"></a>配置备份失败

以下错误代码用于配置备份失败。

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 自动保护意向被删除或不再有效。 | 在 SQL 实例上启用自动保护时，将为该实例中的所有数据库运行“配置备份”作业。 如果在作业运行时禁用自动保护，则会使用此错误代码取消**正在进行的**作业。 | 重新启用自动保护可保护所有剩余的数据库。 |

## <a name="re-registration-failures"></a>重新注册失败

检查一个或多个[症状](#symptoms)之前触发重新注册操作。

### <a name="symptoms"></a>症状

* 所有操作，如备份、 还原和配置备份无法应用于具有以下错误代码的一个 VM:**WorkloadExtensionNotReachable**， **UserErrorWorkloadExtensionNotInstalled**， **WorkloadExtensionNotPresent**， **WorkloadExtensionDidntDequeueMsg**
* **备份状态**项显示备份**不可访问**。 尽管必须排除所有其他原因，还可能会导致同一状态：

  * 没有的权限执行备份相关的 VM 上的操作  
  * VM 已关闭造成无法执行备份
  * 网络问题  

    ![重新注册 VM](./media/backup-azure-sql-database/re-register-vm.png)

* Alwayson 可用性组中，如果备份开始失败更改备份首选项后或故障转移时

### <a name="causes"></a>原因
这些症状可能会出现由于一个或多个原因如下：

  * 已删除或从门户卸载扩展 
  * 从已卸载扩展**Control Panel**下的 vm**卸载或更改程序**UI
  * 在使用就地磁盘还原的时间已还原 VM
  * 由于该扩展配置在其上的已过期很长一段已关闭 VM
  * VM 已被删除，具有相同名称和已删除 VM 所在的资源组中创建另一个 VM
  * 其中一个 AG 节点未收到完整的备份配置，这可能是在可用性组注册到保管库时，或添加新节点获取  <br>
    在上述情况下，建议触发在 VM 上的重新注册操作。 此选项仅可通过 PowerShell，并且很快就会在 Azure 门户中可用。


## <a name="next-steps"></a>后续步骤

有 SQL Server VM 的 Azure 备份（公共预览版）的详细信息，请参阅 [SQL VM 的 Azure 备份（公共预览版）](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup)。
