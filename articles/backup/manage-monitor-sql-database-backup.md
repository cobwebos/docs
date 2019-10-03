---
title: 使用 Azure 备份在 Azure VM 上管理和监视 SQL Server 数据库
description: 本文介绍如何管理和监视在 Azure VM 上运行的 SQL Server 数据库。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: dacurwin
ms.openlocfilehash: 5ef4ca3f6cbf45ac67bad6531926a7de54cd2012
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934772"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>管理和监视已备份的 SQL Server 数据库

本文介绍了在 Azure 虚拟机（VM）上运行的管理和监视 SQL Server 数据库的常见任务，以及由[Azure 备份](backup-overview.md)服务备份到 Azure 备份恢复服务保管库的数据库。 你将了解如何监视作业和警报，停止和恢复数据库保护，运行备份作业，以及从备份中取消注册 VM。

如果尚未为 SQL Server 数据库配置备份，请参阅[在 Azure vm 上备份 SQL Server 数据库](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>在门户中监视手动备份作业

Azure 备份在**备份作业**门户中显示所有手动触发的作业。 在此门户中看到的作业包括数据库发现和注册以及备份和还原操作。

![备份作业门户](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> **备份作业**门户不显示计划的备份作业。 可按下一部分所述，使用 SQL Server Management Studio 来监视计划的备份作业。
>

有关监视方案的详细信息，请参阅[Azure 门户中的 "监视" 和 "](backup-azure-monitoring-built-in-monitor.md) [使用 Azure Monitor 进行监视](backup-azure-monitoring-use-azuremonitor.md)"。  


## <a name="view-backup-alerts"></a>查看备份警报

由于日志备份每隔 15 分钟就会发生一次，因此，监视备份作业可能很繁琐。 Azure 备份通过发送电子邮件警报简化了监视。 电子邮件警报：

- 触发所有备份失败。
- 按错误代码在数据库级别合并。
- 仅在数据库首次备份失败时发送。

监视数据库备份警报：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在保管库仪表板中，选择“警报和事件”。

   ![选择“警报和事件”](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. 在“警报和事件”中，选择“备份警报”。

   ![选择“备份警报”](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>停止 SQL Server 数据库的保护

可以通过两种方式来停止备份 SQL Server 数据库：

* 停止所有将来的备份作业，并删除所有恢复点。
* 停止所有将来的备份作业，并将恢复点保留原样。

如果选择保留恢复点，请记住以下详细信息：

* 所有恢复点都将永久保持原样，所有修剪都应在停止保护时停止，并保留数据。
* 将为受保护的实例和使用的存储付费。 有关详细信息，请参阅 [Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)。
* 如果在不停止备份的情况下删除数据源，则新备份将会失败。

停止数据库的保护：

1. 在保管库仪表板上，选择 "**备份项**"。

2. 在 "**备份管理类型**" 下，选择 " **SQL in Azure VM**"。

    ![选择“Azure VM 中 SQL”](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. 选择要停止保护的数据库。

    ![选择要停止保护的数据库](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. 在数据库菜单中，选择“停止备份”。

    ![选择“停止备份”](./media/backup-azure-sql-database/stop-db-button.png)


5. 在 "**停止备份**" 菜单中，选择是保留还是删除数据。 如果需要，请提供原因和注释。

    ![保留或删除 "停止备份" 菜单上的数据](./media/backup-azure-sql-database/stop-backup-button.png)

6. 选择 "**停止备份**"。


> [!NOTE]
>
>有关 "删除数据" 选项的详细信息，请参阅下面的常见问题解答：
>* [如果从 autoprotected 实例中删除数据库，备份会发生什么情况呢？](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>* [如果我确实停止了 autoprotected 数据库的备份操作，会出现什么情况？](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>


## <a name="resume-protection-for-a-sql-database"></a>恢复 SQL 数据库的保护

停止保护 SQL 数据库时，如果选择 "**保留备份数据**" 选项，则可以在以后恢复保护。 如果不保留备份数据，则无法继续保护。

继续保护 SQL 数据库：

1. 打开备份项，然后选择 "**恢复备份**"。

    ![选择“恢复备份”以恢复数据库保护](./media/backup-azure-sql-database/resume-backup-button.png)

2. 在“备份策略”菜单中选择策略，然后选择“保存”。

## <a name="run-an-on-demand-backup"></a>运行按需备份

你可以运行不同类型的按需备份：

* 完整备份
* 仅复制完整备份
* 差异备份
* 日志备份

尽管需要指定仅复制完整备份的保持期，但临时完整备份的保持期将从当前时间自动设置为45天。 <br/>
有关详细信息，请参阅[SQL Server 备份类型](backup-architecture.md#sql-server-backup-types)。

## <a name="unregister-a-sql-server-instance"></a>取消注册 SQL Server 实例

在禁用保护之后但在删除保管库之前取消注册 SQL Server 实例：

1. 在保管库仪表板上的 "**管理**" 下，选择 "**备份基础结构**"。  

   ![选择“备份基础结构”。](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. 在“管理服务器”下，选择“受保护的服务器”。

   ![选择“受保护的服务器”](./media/backup-azure-sql-database/protected-servers.png)

3. 在“受保护的服务器”中，选择要取消注册的服务器。 若要删除保管库，必须取消注册所有服务器。

4. 右键单击受保护的服务器，然后选择 "**注销**"。

   ![选择“删除”](./media/backup-azure-sql-database/delete-protected-server.jpg)


## <a name="modify-policy"></a>修改策略
修改策略以更改备份频率或保持期。

> [!NOTE]
> 保留期内的任何更改都将应用于所有旧恢复点（新的恢复点除外）。

在保管库仪表板中，单击 "**管理** > **备份策略**"，然后选择要编辑的策略。

  ![管理备份策略](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![修改备份策略](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

策略修改将影响所有关联的备份项，并触发相应的**配置保护**作业。 

#### <a name="inconsistent-policy"></a>策略不一致 

有时，修改策略操作可能导致某些备份项的策略版本**不一致**。 如果在执行修改策略操作后，备份项的相应**配置保护**作业失败，则会发生这种情况。 它在 "备份项" 视图中显示为以下内容：
 
  ![不一致的策略](./media/backup-azure-sql-database/inconsistent-policy.png)

可以通过一次单击来修复所有受影响的项的策略版本：

  ![修复不一致的策略](./media/backup-azure-sql-database/fix-inconsistent-policy.png)
 

## <a name="re-register-extension-on-the-sql-server-vm"></a>重新注册 SQL Server VM 上的扩展

有时，VM 上的工作负荷扩展可能会因为某种原因而受到影响。 在这种情况下，在 VM 上触发的所有操作都将开始失败。 然后，你可能需要在 VM 上重新注册该扩展。 **重新注册**操作会重新安装 VM 上的工作负荷备份扩展，以便继续操作。  <br>

请谨慎使用此选项;当在具有正常扩展的 VM 上触发时，此操作会导致扩展重新启动。 这可能会导致所有正在进行的作业失败。 请在触发重新注册操作之前检查是否有一个或多个[症状](backup-sql-server-azure-troubleshoot.md#re-registration-failures)。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[排查 SQL Server 数据库上的备份问题](backup-sql-server-azure-troubleshoot.md)。
