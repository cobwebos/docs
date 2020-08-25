---
title: 管理和监视 Azure VM 上的 SQL Server 数据库
description: 本文介绍如何管理和监视 Azure VM 上运行的 SQL Server 数据库。
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: c9d8b9b56820182f7bf7866d38d40df8f5488a7a
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2020
ms.locfileid: "88756310"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>管理和监视已备份的 SQL Server 数据库

本文介绍对 Azure 虚拟机 (VM) 上运行的、已通过 [Azure 备份](backup-overview.md)服务备份到 Azure 备份恢复服务保管库的 SQL Server 数据库进行管理和监视所要执行的常见任务。 其中将会介绍如何监视作业和警报、停止和恢复数据库保护、运行备份作业，以及从备份中取消注册 VM。

如果尚未为 SQL Server 数据库配置备份，请参阅[备份 Azure VM 上的 SQL Server 数据库](backup-azure-sql-database.md)

## <a name="monitor-backup-jobs-in-the-portal"></a>在门户中监视备份作业

Azure 备份会在门户中的 " **备份作业** " 下显示所有计划的和按需操作，但计划的日志备份除外，因为它们可能会非常频繁。 在此门户中看到的作业包括数据库发现和注册、配置备份以及备份和还原操作。

![“备份作业”门户](./media/backup-azure-sql-database/jobs-list.png)

有关监视方案的详细信息，请参阅[在 Azure 门户中进行监视](backup-azure-monitoring-built-in-monitor.md)和[使用 Azure Monitor 进行监视](backup-azure-monitoring-use-azuremonitor.md)。  

## <a name="view-backup-alerts"></a>查看备份警报

由于日志备份每隔 15 分钟就会发生一次，因此，监视备份作业可能很繁琐。 Azure 备份通过发送电子邮件警报来简化监视工作。 电子邮件警报：

- 只要出现备份失败就会触发。
- 按错误代码在数据库级别合并的警报。
- 仅在数据库首次备份失败时发送。

若要监视数据库备份警报：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在保管库仪表板中，选择“警报和事件”。

   ![选择“警报和事件”](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. 在“警报和事件”中，选择“备份警报”。

   ![选择“备份警报”](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>停止 SQL Server 数据库的保护

可以通过两种方式来停止备份 SQL Server 数据库：

- 停止所有将来的备份作业，并删除所有恢复点。
- 停止所有将来的备份作业，但将恢复点保留不变。

如果选择保留恢复点，请注意以下细节：

- 所有恢复点都将永久保持不变，所有删除操作都应在停止保护时停止，并保留数据。
- 你将为受保护的实例和使用的存储付费。 有关详细信息，请参阅 [Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)。
- 如果在不停止备份的情况下删除数据源，则新备份将会失败。 旧恢复点将根据策略过期，但始终会保留一个最后的恢复点，直至你显式停止备份并删除数据。

停止数据库的保护：

1. 在保管库仪表板中，选择“备份项”。

2. 在“备份管理类型”下，选择“Azure VM 中的 SQL”。 

    ![选择“Azure VM 中 SQL”](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. 选择要停止保护的数据库。

    ![选择要停止保护的数据库](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. 在数据库菜单中，选择“停止备份”。

    ![选择“停止备份”](./media/backup-azure-sql-database/stop-db-button.png)

5. 在“停止备份”菜单中，选择是要保留还是删除数据。 根据需要提供原因和注释。

    ![在“停止备份”菜单中保留或删除数据](./media/backup-azure-sql-database/stop-backup-button.png)

6. 选择“停止备份”。

> [!NOTE]
>
>有关删除数据选项的详细信息，请参阅下面的常见问题解答：
>
>- [如果从自动保护的实例中删除数据库，备份会发生什么情况？](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [如果停止受保护数据库的备份操作，将出现怎样的备份行为？](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>恢复 SQL 数据库的保护

停止对 SQL 数据库的保护时，如果选择“保留备份数据”选项，以后可以恢复保护。 如果不保留备份数据，则无法恢复保护。

若要恢复 SQL 数据库的保护：

1. 打开备份项，并选择“恢复备份”。

    ![选择“恢复备份”以恢复数据库保护](./media/backup-azure-sql-database/resume-backup-button.png)

2. 在“备份策略”菜单中选择策略，然后选择“保存”。 

## <a name="run-an-on-demand-backup"></a>运行按需备份

你可以运行不同类型的按需备份：

- 完整备份
- 仅复制完整备份
- 差异备份
- 日志备份

虽然需要为仅复制完整备份指定保留期限，但按需完整备份的保留期限将自动设置为从当前时间算起 45 天。

有关详细信息，请参阅 [SQL Server 备份类型](backup-architecture.md#sql-server-backup-types)。

## <a name="modify-policy"></a>修改策略

修改策略可以更改备份频率或保留范围。

> [!NOTE]
> 在保留期内进行的任何更改将应用到新恢复点，并以追溯方式应用到所有旧恢复点。

在保管库仪表板中，转到“管理” > “备份策略”，然后选择要编辑的策略。 

  ![管理备份策略](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![修改备份策略](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

策略修改将影响所有关联的备份项，并触发相应的**配置保护**作业。

### <a name="inconsistent-policy"></a>不一致的策略

有时，修改策略操作可能导致某些备份项出现**不一致的**策略版本。 如果在触发修改策略操作后对备份项执行相应的**配置保护**作业失败，则会出现这种不一致。 它在备份项视图中显示如下：

  ![策略不一致](./media/backup-azure-sql-database/inconsistent-policy.png)

只需单击一下鼠标即可修复所有受影响项的策略版本：

  ![修复不一致的策略](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="unregister-a-sql-server-instance"></a>取消注册 SQL Server 实例

在禁用保护之后但删除保管库之前取消注册 SQL Server 实例：

1. 在保管库仪表板上，在“管理”下，选择“备份基础结构”。   

   ![选择“备份基础结构”。](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. 在“管理服务器”下，选择“受保护的服务器”。 

   ![选择“受保护的服务器”](./media/backup-azure-sql-database/protected-servers.png)

3. 在“受保护的服务器”中，选择要取消注册的服务器。 若要删除保管库，必须取消注册所有服务器。

4. 右键单击受保护的服务器并选择“取消注册”。

   ![选择“删除”](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>重新注册 SQL Server VM 上的扩展

有时，VM 上的工作负荷扩展可能会因为某种原因而受到影响。 在这些情况下，在 VM 上触发的所有操作都将开始失败。 然后，你可能需要在 VM 上重新注册该扩展。 **重新注册**操作会重新安装 VM 上的工作负荷备份扩展，以便继续操作。 可以在恢复服务保管库中的 **备份基础结构** 下找到此选项。

![备份基础结构下受保护的服务器](./media/backup-azure-sql-database/protected-servers-backup-infrastructure.png)

请谨慎使用此选项。 当在具有正常扩展的 VM 上触发时，此操作会导致扩展重新启动。 这可能会导致所有正在进行的作业失败。 在触发重新注册操作之前，请检查是否存在一种或多种[症状](backup-sql-server-azure-troubleshoot.md#re-registration-failures)。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[排查 SQL Server 数据库备份问题](backup-sql-server-azure-troubleshoot.md)。
