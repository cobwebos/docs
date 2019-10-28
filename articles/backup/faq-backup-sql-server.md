---
title: 常见问题解答-通过 Azure 备份在 Azure Vm 上备份 SQL Server 数据库
description: 查找有关在 azure Vm 上备份 SQL Server 数据库的常见问题的解答。
ms.reviewer: vijayts
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dacurwin
ms.openlocfilehash: 27f416d4ed16de0277952f82c3f7dbac607890d6
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750217"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>有关在 Azure VM 备份上运行的 SQL Server 数据库的常见问题解答

本文解答了有关备份 Azure 虚拟机（Vm）上运行的 SQL Server 数据库和使用[Azure 备份](backup-overview.md)服务的常见问题。

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>是否可以在同一台计算机上为 IaaS VM 和 SQL Server 使用 Azure 备份？

是的，你可以在同一 VM 上同时拥有 VM 备份和 SQL 备份。 在这种情况下，我们在 VM 上内部触发仅复制完整备份，而不截断日志。

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>解决方案是重试还是自动修复备份？

在某些情况下，Azure 备份服务会触发补救备份。 对于下列六个条件中的任何一种，都可以进行自动修复：

- 如果日志备份或差异备份因 LSN 验证错误而失败，则下一个日志备份或差异备份将转换为完整备份。
- 如果在日志备份或差异备份之前未进行完整备份，则该日志备份或差异备份将转换为完整备份。
- 如果最新完整备份的时间点早于15天，则下一个日志备份或差异备份将转换为完整备份。
- 升级完成并且启动扩展后，将重新触发由于扩展升级而取消的所有备份作业。
- 如果在还原期间选择覆盖数据库，则下一个日志/差异备份将失败并改为触发完整备份。
- 如果在数据库恢复模式发生更改的情况下需要进行完整备份来重置日志链，则会在下一个计划中自动触发完整备份。

默认情况下，为所有用户启用了自动修复功能;但是，如果选择退出它，请执行以下操作：

- 在 SQL Server 实例上的*C:\Program Files\Azure 工作负荷 Backup\bin*文件夹中，创建或编辑**ExtensionSettingsOverrides**文件。
- 在**ExtensionSettingsOverrides**中，将 *{"EnableAutoHealer"： false}* 设置为。
- 保存更改并关闭该文件。
- 在 SQL Server 实例上，打开 "**管理**"，然后重新启动**AzureWLBackupCoordinatorSvc**服务。

## <a name="can-i-control-as-to-how-many-concurrent-backups-run-on-the-sql-server"></a>能否控制 SQL server 上运行的并发备份的数量？

可以。 可以限制备份策略的运行速率，以尽量减少对 SQL Server 实例的影响。 若要更改设置，请执行以下操作：

1. 在 SQL Server 实例的*C:\Program Files\Azure 工作负荷 Backup\bin*文件夹中，创建*ExtensionSettingsOverrides*文件。
2. 在*ExtensionSettingsOverrides*文件中，将**DefaultBackupTasksThreshold**设置更改为较低的值（例如5）。 <br>
  `{"DefaultBackupTasksThreshold": 5}`

3. 保存更改并关闭该文件。
4. 在 SQL Server 实例上，打开“任务管理器”。 重启 **AzureWLBackupCoordinatorSvc** 服务。<br/> <br/>
 尽管此方法可帮助备份应用程序消耗大量资源，但 SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017)是一种更通用的方法来指定传入应用程序请求可以使用的 CPU、物理 IO 和内存量的限制。

> [!NOTE]
> 在 UX 中，你仍然可以在任何给定时间计划任意数量的备份，但它们会在如5的滑动窗口中进行处理，如上面的示例所示。

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>是否可以从次要副本运行完整备份？

根据 SQL 限制，你可以只在辅助副本上运行 "复制完整备份";但不允许完整备份。

## <a name="can-i-protect-availability-groups-on-premises"></a>能否在本地保护可用性组？

不。 Azure 备份可保护在 Azure 中运行的 SQL Server 数据库。 如果在 Azure 和本地计算机之间分配可用性组（AG），则仅当主要副本在 Azure 中运行时，才可以保护 AG。 此外，Azure 备份仅保护在与恢复服务保管库相同的 Azure 区域中运行的节点。

## <a name="can-i-protect-availability-groups-across-regions"></a>是否可以跨区域保护可用性组？

Azure 备份恢复服务保管库可以检测和保护与保管库位于同一区域中的所有节点。 如果 SQL Server Always On 可用性组跨多个 Azure 区域，请从具有主节点的区域设置备份。 Azure 备份可根据备份首选项检测并保护可用性组中的所有数据库。 如果不满足备份首选项，备份将失败，并且你会收到失败警报。

## <a name="do-successful-backup-jobs-create-alerts"></a>成功的备份作业是否会创建警报？

不。 成功的备份作业不会生成警报。 仅针对失败的备份作业发送警报。 [此处](backup-azure-monitoring-built-in-monitor.md)介绍了门户警报的详细行为。 但是，如果您对成功的作业有警报，您可以使用[Azure Monitor 进行监视](backup-azure-monitoring-use-azuremonitor.md)。

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>是否可以在 "备份作业" 菜单中看到计划的备份作业？

"**备份作业**" 菜单将仅显示即席备份作业。 对于计划作业，请使用[Azure Monitor 监视](backup-azure-monitoring-use-azuremonitor.md)。

## <a name="are-future-databases-automatically-added-for-backup"></a>未来的数据库会自动添加备份吗？

是的，可以通过[自动保护](backup-sql-server-database-azure-vms.md#enable-auto-protection)实现此功能。  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>如果从 autoprotected 实例中删除数据库，备份会发生什么情况呢？

如果从 autoprotected 实例中删除数据库，则仍将尝试数据库备份。 这意味着，已删除的数据库在 "**备份项**" 下开始显示为 "不正常"，并且仍受保护。

停止保护此数据库的正确方法是在此数据库上**停止备份**并**删除数据**。  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>如果我确实停止了 autoprotected 数据库的备份操作，会出现什么情况？

如果你确实**停止备份并保留数据**，则不会进行将来的备份，现有的恢复点将保持不变。 数据库仍将被视为受保护，并显示在 "**备份项**" 下。

如果你**使用 "删除数据" 停止备份**，则不会进行将来的备份，也将删除现有恢复点。 该数据库将被视为未受保护，并显示在 "配置备份" 中的实例下。 但是，与其他可手动选择或可以获取 autoprotected 的受保护的数据库不同，此数据库将显示为灰色，并且无法选择。 重新保护此数据库的唯一方法是对该实例禁用自动保护。 你现在可以选择此数据库并在其上配置保护，也可以重新启用对实例的自动保护。

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>如果在保护数据库后更改其名称，会出现什么情况？

已重命名的数据库被视为新数据库。 因此，该服务将处理此情况，就好像找不到数据库，备份失败。

你可以选择现在已重命名的数据库并对其进行配置。 如果对实例启用了自动保护，则会自动检测并保护重命名的数据库。

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>为什么看不到 autoprotected 实例的已添加数据库？

[添加到 autoprotected 实例](backup-sql-server-database-azure-vms.md#enable-auto-protection)的数据库可能不会立即出现在 "受保护的项" 下。 这是因为，发现功能通常每隔 8 小时运行一次。 但是，如果您**通过选择 "重新发现数据库**" 手动运行发现，则可以立即发现和保护新数据库，如下图所示：

  ![手动发现新添加的数据库](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>后续步骤

了解如何备份在 Azure VM 上运行的[SQL Server 数据库](backup-azure-sql-database.md)。
