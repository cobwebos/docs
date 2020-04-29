---
title: 常见问题解答 - 备份 Azure VM 上的 SQL Server 数据库
description: 查找有关使用 Azure 备份在 Azure VM 上备份 SQL Server 数据库的常见问题的解答。
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: a973761bf16e2d271d718e4a8b29e08624276987
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79247704"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>有关备份 Azure VM 上运行的 SQL Server 数据库的常见问题解答

本文将会解答有关备份 Azure 虚拟机 (VM) 上运行的 SQL Server 数据库以及使用 [Azure 备份](backup-overview.md)服务的常见问题。

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>是否可以对同一台计算机上的 IaaS VM 和 SQL Server 使用 Azure 备份？

是，可以同时在同一 VM 上进行 VM 备份和 SQL 备份。 对于这种情况，在内部，我们将在 VM 上触发仅限复制的完整备份，以避免截断日志。

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>解决方案是重试还是自动修复备份？

在某些情况下，Azure 备份服务会触发补救备份。 如果存在下述六种条件中的任何一种，可进行自动修复：

- 如果日志备份或差异备份因 LSN 验证错误而失败，则下一次日志备份或差异备份将转换为完整备份。
- 如果在执行日志备份或差异备份之前未执行完整备份，该日志备份或差异备份将转换为完整备份。
- 如果最新完整备份的时间点超过 15 天，则下一次日志备份或差异备份将转换为完整备份。
- 由于扩展升级而取消的所有备份作业将在升级完成后重新触发，并且扩展将会启动。
- 如果在还原期间选择覆盖数据库，则下一次日志/差异备份将会失败，并改为触发完整备份。
- 如果由于数据库恢复模式发生更改而需要使用完整备份来重置日志链，则会在下一个计划时间自动触发完整备份。

默认已为所有用户启用自动修复功能；但是，如果你要禁用它，请执行以下操作：

- 在 SQL Server 实例上的 *C:\Program Files\Azure Workload Backup\bin* 文件夹中，创建或编辑 **ExtensionSettingsOverrides.json** 文件。
- 在 **ExtensionSettingsOverrides.json** 中，设置 *{"EnableAutoHealer": false}* 。
- 保存更改并关闭该文件。
- 在 SQL Server 实例上打开“管理任务”，然后重启 **AzureWLBackupCoordinatorSvc** 服务。 

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>是否可以控制 SQL Server 上运行的并发备份数？

是的。 可以限制备份策略的运行速率，以尽量减少对 SQL Server 实例的影响。 若要更改设置，请执行以下操作：

1. 在 SQL Server 实例上的 *C:\Program Files\Azure Workload Backup\bin* 文件夹中，创建 *ExtensionSettingsOverrides.json* 文件。
2. 在 *ExtensionSettingsOverrides.json* 文件中，将 **DefaultBackupTasksThreshold** 设置更改为较小的值（例如 5）。 <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
DefaultBackupTasksThreshold 的默认值为 **20**。

3. 保存更改并关闭该文件。
4. 在 SQL Server 实例上，打开“任务管理器”。  重启 **AzureWLBackupCoordinatorSvc** 服务。<br/> <br/>
 尽管在备份应用程序消耗大量资源时此方法有所帮助，但使用 SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) 可通过更常规的方式来指定传入应用程序请求可以使用的 CPU、物理 IO 和内存量限制。

> [!NOTE]
> 在 UX 中，仍可随时继续计划任意数量的备份，但是，这些备份将按某个滑动窗口进行处理，例如，在上述示例中，滑动窗口为 5。

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>是否可以从次要副本运行完整备份？

根据 SQL 限制，只能针对次要副本运行“仅限复制的完整备份”，而不允许“完整备份”。

## <a name="can-i-protect-availability-groups-on-premises"></a>是否可以保护本地的可用性组？

不是。 Azure 备份可以保护 Azure 中运行的 SQL Server 数据库。 如果可用性组 (AG) 分散在 Azure 与本地计算机之间，则仅当主要副本在 Azure 中运行时，才可以保护 AG。 此外，Azure 备份只能保护恢复服务保管库所在的同一 Azure 区域中运行的节点。

## <a name="can-i-protect-availability-groups-across-regions"></a>是否可跨区域保护可用性组？

Azure 备份恢复服务保管库可以检测并保护保管库所在的同一区域中的所有节点。 如果 SQL Server Always On 可用性组跨多个 Azure 区域，请从包含主要节点的区域设置备份。 Azure 备份可根据备份首选项检测并保护可用性组中的所有数据库。 如果不符合备份优先顺序，备份将会失败，并出现失败警报。

## <a name="do-successful-backup-jobs-create-alerts"></a>成功的备份作业是否会创建警报？

不是。 成功的备份作业不会生成警报。 仅针对失败的备份作业发送警报。 [此文](backup-azure-monitoring-built-in-monitor.md)介绍了门户警报的详细行为。 但是，如果你希望同时收到已成功作业的警报，可以[使用 Azure Monitor 进行监视](backup-azure-monitoring-use-azuremonitor.md)。

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>“备份作业”菜单中是否会显示计划的备份作业？

“备份作业”菜单只显示按需备份作业。  对于计划的作业，请[使用 Azure Monitor 进行监视](backup-azure-monitoring-use-azuremonitor.md)。

## <a name="are-future-databases-automatically-added-for-backup"></a>未来的数据库会自动添加备份吗？

是，可以通过[自动保护](backup-sql-server-database-azure-vms.md#enable-auto-protection)实现此功能。  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>如果从自动保护的实例中删除数据库，备份会发生什么情况？

如果从自动保护的实例中删除某个数据库，仍会尝试数据库备份。 这意味着，已删除的数据库会开始在“备份项”下面显示为不正常状态，但它仍受保护。 

停止保护此数据库的正确方法是针对此数据库执行“停止备份”并**删除数据**。   

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>如果停止受保护数据库的备份操作，将出现怎样的备份行为？

如果**停止备份但保留数据**，则将来的备份不会发生，现有的恢复点将保留不变。 数据库仍被视为受保护，并显示在“备份项”下。 

如果**停止备份并删除数据**，则将来的备份不会发生，现有的恢复点也会一并删除。 该数据库被视为不受保护，并显示在“配置备份”中的实例下。 但是，与其他可以手动选择或者可以自动保护的受保护数据库不同，此数据库将会灰显，并且不可选择。 重新保护此数据库的唯一方法是对该实例禁用自动保护。 接下来可以选择此数据库并对其配置保护，或者对该实例重新启用自动保护。

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>如果在保护数据库后更改其名称，会出现怎样的行为？

已重命名的数据库被视为新数据库。 因此，服务将此情况视为找不到数据库，同时会使备份失败。

可以选择现在已重命名的数据库并对其配置保护。 如果对实例启用了自动保护，则会自动检测并保护已重命名的数据库。

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>为什么不显示自动保护实例的已添加数据库？

[添加到自动保护实例的数据库](backup-sql-server-database-azure-vms.md#enable-auto-protection)可能不会立即显示在“受保护的项”下。 这是因为，发现功能通常每隔 8 小时运行一次。 但是，如果按下图所示选择“重新发现数据库”来手动运行发现，则可以立即发现并保护新的数据库： 

  ![手动发现新添加的数据库](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>后续步骤

了解如何[备份 Azure VM 上运行的 SQL Server 数据库](backup-azure-sql-database.md)。
