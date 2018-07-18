---
title: SQL Server VM 的 Azure 备份故障排除指南 | Microsoft Docs
description: 有关将 SQL Server VM 备份到 Azure 的故障排除信息。
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 1c87382c2aae70b022fb391f80f7c75b0a4e5fe6
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296954"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>排查在 Azure 上备份 SQL Server 的问题

本文提供有关在 Azure 上保护 SQL Server VM（预览版）的故障排除信息。

## <a name="public-preview-limitations"></a>公共预览版的限制

若要查看公共预览版的限制，请参阅[在 Azure 中备份 SQL Server 数据库](backup-azure-sql-database.md#public-preview-limitations)一文。

## <a name="sql-server-permissions"></a>SQL Server 权限

若要在虚拟机上为 SQL Server 数据库配置保护，必须在该虚拟机上安装 **AzureBackupWindowsWorkload** 扩展。 如果收到错误 **UserErrorSQLNoSysadminMembership**，则表示 SQL 实例没有所需的备份权限。 若要解决此错误，请遵循[为非市场 SQL VM 设置权限](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms)中的步骤。

## <a name="troubleshooting-errors"></a>排查错误

使用下表中的信息来排查在 Azure 中保护 SQL Server 时遇到的问题和错误。

## <a name="backup-failures"></a>备份失败

下表按错误代码进行组织。

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 此 SQL 数据库不支持所请求的备份类型。 | 当数据库恢复模式不允许所请求的备份类型时，会发生此错误。 在以下情况下，可能会发生此错误： <br/><ul><li>使用简单恢复模式的数据库不允许日志备份。</li><li>不允许对 Master 数据库执行差异备份和日志备份。</li></ul>有关详细信息，请参阅 [SQL 恢复模式](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server)文档。 | 如果采用简单恢复模式的数据库的日志备份失败，请尝试以下选项之一：<ul><li>如果数据库处于简单恢复模式，请禁用日志备份。</li><li>使用 [SQL 文档](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server)将数据库恢复模式更改为“完整”或“批量日志记录”。 </li><li> 如果不想要更改恢复模式，并使用标准策略来备份无法更改的多个数据库，请忽略此错误。 完整备份和差异备份会按计划进行。 在这种情况下，预期会跳过日志备份。</li></ul>如果备份的是 Master 数据库，并且已配置差异备份或日志备份，请使用以下任一步骤：<ul><li>使用门户将 Master 数据库的备份策略计划更改为“完整”。</li><li>如果使用标准策略来备份无法更改的多个数据库，请忽略此错误。 完整备份会按计划进行。 在这种情况下，预期不会发生差异备份或日志备份。</li></ul> |
| 操作将被取消，因为已对同一个数据库运行了某个有冲突的操作。 | 请参阅[有关并行运行备份和还原时存在的限制的博客文章](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database)。| [使用 SQL Server Management Studio (SSMS) 监视备份作业。](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms) 有冲突的操作失败后，重启该操作。|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| SQL 数据库不存在。 | 该数据库已被删除或重命名。 | <ul><li>检查是否意外删除或重命名了该数据库。</li><li>如果意外删除了该数据库，若要继续备份，请将该数据库还原到原始位置。</li><li>如果删除了该数据库，且将来不需要备份，请在恢复服务保管库中单击“停止备份 [和“删除/保留数据](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms) 。</li>|

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 日志链已中断。 | 数据库或 VM 是使用其他备份解决方案备份的，该解决方案截断了日志链。|<ul><li>检查是否正在使用其他备份解决方案或脚本。 如果是，请停止其他备份解决方案。 </li><li>如果备份是临时日志备份，请触发完整备份来启动新的日志链。 对于计划的日志备份，不需要执行任何操作，因为 Azure 备份服务会自动触发完整备份来解决此问题。</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| Azure 备份无法连接到 SQL 实例。 | Azure 备份无法连接到 SQL 实例。 | 使用 Azure 门户上错误菜单中的其他详细信息缩小根本原因的范围。 请参阅 [SQL 备份故障排除](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)来解决错误。<br/><ul><li>如果默认 SQL 设置不允许远程连接，请更改设置。 参阅以下链接来更改设置。<ul><li>[https://msdn.microsoft.com/library/bb326495.aspx](https://msdn.microsoft.com/library/bb326495.aspx)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>如果出现登录问题，请参阅以下链接来解决问题：<ul><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 此数据源缺少首次完整备份。 | 数据库缺少首次完整备份。 日志备份和差异备份基于完整备份，因此，在触发差异备份或日志备份之前，必须先创建完整备份。 | 触发临时完整备份。   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| 错误消息 | 可能的原因 | 建议的操作 |
|---|---|---|
| 由于数据源的事务日志已满，无法创建备份。 | 数据库事务日志空间已满。 | 若要解决此问题，请参阅 [SQL 文档](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)。 |
| 此 SQL 数据库不支持所请求的备份类型。 | Always On AG 次要副本不支持完整备份和差异备份。 | <ul><li>如果触发了临时备份，请在主节点上触发备份。</li><li>如果备份是由策略计划的，请确保已注册主节点。 若要注册节点，[请遵循发现 SQL Server 数据库的步骤](backup-azure-sql-database.md#discover-sql-server-databases)。</li></ul> | 

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

## <a name="next-steps"></a>后续步骤

有 SQL Server VM 的 Azure 备份（公共预览版）的详细信息，请参阅 [SQL VM 的 Azure 备份（公共预览版）](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup)。