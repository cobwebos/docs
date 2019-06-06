---
title: 管理和监视由 Azure 备份备份 Azure VM 上的 SQL Server 数据库 |Microsoft Docs
description: 本文介绍如何管理和监视 Azure VM 运行的 SQL Server 数据库。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: raynew
ms.openlocfilehash: d850cb222b0028f862cdba302140ce12af8576db
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66492742"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>管理和监视已备份的 SQL Server 数据库

本指南介绍了常见任务的管理和监视 SQL Server 数据库的 Azure 虚拟机 (VM) 上运行，并且，备份到 Azure 备份恢复服务保管库[Azure 备份](backup-overview.md)服务。 您将了解如何监视作业和警报、 停止和恢复数据库的保护、 运行备份作业，并取消注册从备份 VM。

如果你尚未配置备份的 SQL Server 数据库，请参阅[备份 Azure Vm 上的 SQL Server 数据库](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>监视在门户中的手动备份作业

Azure 备份显示中的所有手动触发的作业**备份作业**门户。 请参阅此门户包含数据库发现和注册，以及备份和还原操作的作业。

![备份作业门户](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> **备份作业**门户不显示计划的备份作业。 可按下一部分所述，使用 SQL Server Management Studio 来监视计划的备份作业。
>

有关监视方案的详细信息，请转到[在 Azure 门户中监视](backup-azure-monitoring-built-in-monitor.md)并[监视使用 Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)。  


## <a name="view-backup-alerts"></a>查看备份警报

由于日志备份每隔 15 分钟就会发生一次，因此，监视备份作业可能很繁琐。 Azure 备份中可以简化监视通过发送电子邮件警报。 电子邮件警报包括：

- 为所有备份失败触发。
- 在数据库级别合并错误代码。
- 仅针对数据库的第一次备份失败发送。

若要监视数据库的备份警报：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在保管库仪表板中，选择“警报和事件”。 

   ![选择“警报和事件”](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. 在“警报和事件”  中，选择“备份警报”  。

   ![选择“备份警报”](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>停止 SQL Server 数据库的保护

可以通过两种方式来停止备份 SQL Server 数据库：

* 停止所有将来的备份作业，并删除所有恢复点。
* 停止所有将来的备份作业，并保留恢复点保持不变。

如果选择保留恢复点，请记住这些详细信息：

* 所有恢复点不限次数将都保持不变，所有修剪应都停止在都停止保护保留数据。
* 你将为受保护的实例占用的存储空间付费。 有关详细信息，请参阅 [Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)。
* 如果在不停止备份的情况下删除数据源，则新备份将会失败。

停止数据库的保护：

1. 在保管库仪表板中，选择**备份项**。

2. 下**备份管理类型**，选择**在 Azure VM 中的 SQL**。

    ![选择“Azure VM 中 SQL”](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. 选择要停止保护的数据库。

    ![选择要停止保护的数据库](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. 在数据库菜单中，选择“停止备份”  。

    ![选择“停止备份”](./media/backup-azure-sql-database/stop-db-button.png)


5. 上**停止备份**菜单中，选择是保留还是删除数据。 如果你想提供的原因和注释。

    ![保留或删除在停止备份菜单上的数据](./media/backup-azure-sql-database/stop-backup-button.png)

6. 选择**停止备份**。

>
> [!NOTE]
请参阅下面的删除数据选项的详细信息的常见问题： <br/>
* [如果从 autoprotected 实例中删除数据库，则会发生什么情况，备份？](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)<br/>
* [如果我执行停止 autoprotected 数据库的备份操作将其行为的内容？](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>


## <a name="resume-protection-for-a-sql-database"></a>恢复 SQL 数据库的保护

何时停止对 SQL 数据库的保护，如果您选择**保留备份数据**选项，您可以继续保护。 如果不保留备份数据，则不能恢复保护。

若要恢复对 SQL 数据库的保护：

1. 打开备份项，然后选择**恢复备份**。

    ![选择“恢复备份”以恢复数据库保护](./media/backup-azure-sql-database/resume-backup-button.png)

2. 在“备份策略”菜单中选择策略，然后选择“保存”。  

## <a name="run-an-on-demand-backup"></a>运行按需备份

你可以运行不同类型的按需备份：

* 完整备份
* 仅复制完整备份
* 差异备份
* 日志备份

尽管您需要指定仅复制完整备份的保留持续时间，其他备份类型的保留期范围是自动设置为 30 天内从当前时间。 <br/>
有关详细信息，请参阅[SQL Server 的备份类型](backup-architecture.md#sql-server-backup-types)。

## <a name="unregister-a-sql-server-instance"></a>取消注册 SQL Server 实例

禁用保护之后，但之前删除该保管库撤消注册 SQL Server 实例：

1. 在保管库仪表板下**管理**，选择**备份基础结构**。  

   ![选择“备份基础结构”。](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. 在“管理服务器”下，选择“受保护的服务器”。  

   ![选择“受保护的服务器”](./media/backup-azure-sql-database/protected-servers.png)

3. 在“受保护的服务器”中，选择要取消注册的服务器。  若要删除保管库，必须取消注册所有服务器。

4. 右键单击受保护的服务器，然后选择**注销**。

   ![选择“删除”](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>SQL Server VM 上重新注册扩展

有时，在 VM 上的工作负荷扩展可能会受到影响的原因之一或其他。 在这种情况下，在 VM 上触发的所有操作将都开始失败。 然后，您可能需要重新注册该扩展在 VM 上。 **重新注册**操作操作继续在 VM 上重新安装工作负荷备份扩展。  <br>

建议使用此选项时要注意;具有已正常扩展的 VM 上触发，此操作将导致要获取重新启动的扩展。 这可能会导致所有正在进行中作业失败。 请检查为一个或多个[症状](backup-sql-server-azure-troubleshoot.md#symptoms)之前触发重新注册操作。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[进行故障排除 SQL Server 数据库上的备份](backup-sql-server-azure-troubleshoot.md)。
