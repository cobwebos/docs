---
title: 有关使用 Azure 备份在 Azure VM 上备份 SQL Server 数据库的常见问题
description: 查找有关使用 Azure 备份的 Azure Vm 上备份 SQL Server 数据库的常见问题的解答。
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: sachdevaswati
ms.openlocfilehash: 48a0400a471e06f65c1d548b7c1c419a1cb198bd
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58284572"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>有关在 Azure VM 备份运行的 SQL Server 数据库的常见问题解答

本文解答有关 SQL Server 数据库备份 Azure 虚拟机 (Vm) 上的运行并使用[Azure 备份](backup-overview.md)服务。

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>该解决方案是否重试或自动修复备份？

在某些情况下，Azure 备份服务就会触发补救备份。 自动修复可能发生的任何下面所述的六个条件：

  - 如果日志备份或差异备份由于 LSN 验证错误而失败下, 一个日志备份或差异备份而转换为完整备份。
  - 如果在日志或差异备份之前不发生了任何完全备份，该日志备份或差异备份而转换为完整备份。
  - 如果超过 15 天的最新完整备份的时间点下, 一个日志备份或差异备份而转换为完整备份。
  - 升级已完成并启动该扩展后，会重新触发以便取消操作由于扩展升级的所有备份作业。
  - 如果你选择覆盖数据库在还原期间下, 一次日志/差异备份将失败，并改为触发完整备份。
  - 在完整备份需要重置到期的日志链中的数据库恢复模型更改的情况下，完整获取按下一步计划自动触发。

自动修复为一项功能已启用的所有用户默认设置。但是如果你选择退出它，然后执行下面：

  * 在 SQL Server 实例中*C:\Program Files\Azure 工作负荷 Backup\bin*文件夹中，创建或编辑**ExtensionSettingsOverrides.json**文件。
  * 在中 **ExtensionSettingsOverrides.json**，请设置 *{"EnableAutoHealer": false}*。
  * 保存所做的更改并关闭该文件。
  * 在 SQL Server 实例中，打开**管理任务**，然后重新启动**AzureWLBackupCoordinatorSvc**服务。  

## <a name="can-i-control-as-to-how-many-concurrent-backups-run-on-the-sql-server"></a>可以在 SQL server 上运行的并发备份的数量进行控制

是的。 可以限制备份策略的运行速率，以尽量减少对 SQL Server 实例的影响。 若要更改设置，请执行以下操作：
1. 在 SQL Server 实例中*C:\Program Files\Azure 工作负荷 Backup\bin*文件夹中，创建*ExtensionSettingsOverrides.json*文件。
2. 在中*ExtensionSettingsOverrides.json*文件中，将**DefaultBackupTasksThreshold**将设置为较低的值 (例如，5)。 <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. 保存所做的更改并关闭该文件。
4. 在 SQL Server 实例上，打开“任务管理器”。 重启 **AzureWLBackupCoordinatorSvc** 服务。

> [!NOTE]
> 在用户体验仍可以继续，并在任何给定时间计划的备份数一样，但是它们将处理在说，5，根据上面的示例中的滑动窗口中。

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>是否可以从次要副本运行完整备份？
根据 SQL 限制可以运行复制仅完整备份，对辅助副本;但是，不允许完整备份。

## <a name="can-i-protect-availability-groups-on-premises"></a>可以保护可用性组的本地？
不是。 Azure 备份来保护在 Azure 中运行的 SQL Server 数据库。 如果某一可用性组 (AG) 跨 Azure 和本地计算机之间，仅当主副本运行在 Azure 中，可以保护可用性组。 此外，Azure 备份会保护仅在恢复服务保管库所在的同一 Azure 区域中运行的节点。

## <a name="can-i-protect-availability-groups-across-regions"></a>可以跨区域保护可用性组？
Azure 备份恢复服务保管库可以检测并保护在与保管库位于同一区域中的所有节点。 如果 SQL Server Always On 可用性组跨多个 Azure 区域，将备份从主节点的区域设置。 Azure 备份可以检测并保护备份首选项根据可用性组中的所有数据库。 当不满足您的备份首选项时，备份将失败，并获取故障警报。

## <a name="do-successful-backup-jobs-create-alerts"></a>成功的备份作业是否会创建警报？
不是。 成功的备份作业不会生成警报。 仅针对失败的备份作业发送警报。 记录有关门户警报的详细的行为[此处](backup-azure-monitoring-built-in-monitor.md)。 但是，感兴趣的情况下是否有警报甚至对于成功的作业，你可以使用[使用 Azure Monitor 监视](backup-azure-monitoring-use-azuremonitor.md)。

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>可以看到备份作业菜单中的计划备份作业？
**备份作业**菜单将只显示即席备份作业。 对于计划的作业使用[使用 Azure Monitor 监视](backup-azure-monitoring-use-azuremonitor.md)。

## <a name="are-future-databases-automatically-added-for-backup"></a>未来的数据库会自动添加备份吗？
是的则可以使用此功能 [自动保护](backup-sql-server-database-azure-vms.md#enable-auto-protection)。  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>如果从 autoprotected 实例中删除数据库，则会发生什么情况，备份？
如果从 autoprotected 实例删除数据库，数据库备份仍尝试。 这意味着已删除的数据库开始才会显示在为不正常**备份项**和仍会受到保护。

若要停止保护此数据库的正确方法将采取的操作 **停止备份**与**删除数据**此数据库上。  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>如果我执行停止 autoprotected 数据库的备份操作将其行为的内容？
如果这样做**数据停止备份使用保留**、 任何将来的备份会发生和现有的恢复点将保持不变。 在数据库仍将被视为为受保护，下方显示**备份项**。

如果这样做**通过删除数据停止备份**、 任何将来的备份会发生和现有的恢复点也将被删除。 数据库将被视为未受到保护，并显示在中配置备份的实例。 但是，最多保护与其他数据库不同，可以手动选择，或者，可以获取 autoprotected，此数据库变灰并且不能选择。 重新保护此数据库的唯一方法是禁用自动保护的实例上。 现在可以选择此数据库并在其上配置保护，或再次重新启用自动保护的实例上。

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>如果受保护后更改的名称，，是数据库的怎样的行为？
重命名的数据库视为新的数据库。 因此，该服务会将这种情况下，像数据库找不到，并且对于失败的备份。

您可以选择数据库，它现重命名，并在其上配置保护。 如果在实例上启用了自动保护，重命名的数据库将会自动检测和受保护的。

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>为何看不到 autoprotected 实例添加的数据库？
一个数据库[将添加到 autoprotected 实例](backup-sql-server-database-azure-vms.md#enable-auto-protection)可能不会立即显示在受保护的项。 这是因为，发现功能通常每隔 8 小时运行一次。 但是，可以发现并立即保护新的数据库，如果你选择手动运行发现**恢复数据库**下, 图中所示。

  ![手动发现新添加的数据库](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>后续步骤

了解如何[备份 SQL Server 数据库](backup-azure-sql-database.md)Azure VM 上运行。
