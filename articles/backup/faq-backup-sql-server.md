---
title: 有关使用 Azure 备份在 Azure VM 上备份 SQL Server 数据库的常见问题
description: 提供有关使用 Azure 备份在 Azure VM 上备份 SQL Server 数据库的常见问题的解答。
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: sogup
ms.openlocfilehash: a14406733ff60d53d4bf7792ff0c9a015c57d9b3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430921"
---
# <a name="faq-on-sql-server-running-on-azure-vm-backup"></a>关于在 Azure VM 备份上运行的 SQL Server 的常见问题解答

本文解答有关使用 [Azure 备份](backup-overview.md)服务备份在 Azure VM 上运行的 SQL Server 数据库的常见问题。

> [!NOTE]
> 此功能目前处于公开预览状态。



## <a name="can-i-throttle-the-backup-speed"></a>可以限制备份速度吗？

是的。 可以限制备份策略的运行速率，以尽量减少对 SQL Server 实例的影响。 若要更改设置，请执行以下操作：
1. 在 SQL Server 实例上的 *C:\Program Files\Azure Workload Backup\bin* 文件夹中，创建 **ExtensionSettingsOverrides.json** 文件。
2. 在 **ExtensionSettingsOverrides.json** 文件中，将 **DefaultBackupTasksThreshold** 设置更改为较小的值（例如 5） <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. 保存所做更改。 关闭该文件。
4. 在 SQL Server 实例上，打开“任务管理器”。 重启 **AzureWLBackupCoordinatorSvc** 服务。

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>是否可以从次要副本运行完整备份？
不是。 不支持此功能。

## <a name="do-successful-backup-jobs-create-alerts"></a>成功的备份作业是否会创建警报？

不是。 成功的备份作业不会生成警报。 仅针对失败的备份作业发送警报。

## <a name="can-i-see-scheduled-backup-jobs-in-the-jobs-menu"></a>“作业”菜单中是否会显示计划的备份作业？

不是。 “备份作业”菜单显示按需作业的详细信息，但不显示计划的备份作业。 如果有任何计划的备份作业失败，失败的作业警报中会提供详细信息。 若要监视所有计划的备份作业和临时备份作业，请使用 [SQL Server Management Studio](manage-monitor-sql-database-backup.md)。

## <a name="are-future-databases-automatically-added-for-backup"></a>未来的数据库会自动添加备份吗？

不是。 为 SQL Server 实例配置保护时，如果选择服务器级别选项，则会添加所有数据库。 如果在配置保护后将数据库添加到 SQL Server 实例，则必须手动添加新数据库才能保护它们。 已配置的保护不会自动包含数据库。

##  <a name="how-do-i-restart-protection-after-changing-recovery-type"></a>更改恢复类型后如何重启保护？

触发完整备份。 日志备份将按预期开始。

## <a name="can-i-protect-availability-groups-on-premises"></a>可以在本地保护可用性组吗？

不是。 Azure 备份保护 Azure 中运行的 SQL Server。 如果可用性组 (AG) 分散在 Azure 与本地计算机之间，则仅当主副本在 Azure 中运行时，才可以保护 AG。 此外，Azure 备份仅保护恢复服务保管库所在的同一 Azure 区域中运行的节点。

## <a name="can-i-protect-availability-groups-across-regions"></a>可以跨区域保护可用性组吗？

Azure 备份恢复服务保管库可以检测并保护与恢复服务保管库位于同一区域中的所有节点。 如果有跨多个 Azure 区域的 SQL Always On 可用性组，则需要从具有主要节点的区域配置备份。 Azure 备份将能够根据备份首选项检测并保护可用性组中的所有数据库。 如果不满足备份首选项，则备份将失败，你将收到故障警报。

## <a name="can-i-exclude-databases-with-auto-protection-enabled"></a>可以排除启用了自动保护的数据库吗？

不可以，[自动保护](backup-azure-sql-database.md#enable-auto-protection)将应用到整个实例。 不能使用自动保护来选择地保护实例中的数据库。

## <a name="can-i-have-different-policies-in-an-auto-protected-instance"></a>是否可在自动保护的实例中使用不同的策略？

如果实例中已有一些受保护的数据库，则即使**启用**[自动保护](backup-azure-sql-database.md#enable-auto-protection)选项，这些数据库也会继续受其相应策略的保护。 但是，所有不受保护的数据库以及将来要添加的数据库只会应用单个策略，该策略是选择数据库后在“配置备份”下面定义的。 事实上，与其他受保护数据库不同，你甚至无法更改自动保护的实例中数据库的策略。
若要这样做，唯一的方法是暂时针对该实例禁用自动保护，然后更改该数据库的策略。 现在，可为此实例重新启用自动保护。

## <a name="if-i-delete-a-database-from-auto-protection-will-backups-stop"></a>从自动保护中删除数据库，备份会停止吗？

不会，如果从自动保护的实例中删除某个数据库，仍会尝试该数据库的备份。 这意味着，已删除的数据库会开始在“备份项”下面显示为不正常状态，但仍将其视为受保护。

停止保护此数据库的唯一方法是暂时针对该实例禁用[自动保护](backup-azure-sql-database.md#enable-auto-protection)，然后在该数据库对应的“备份项”下面选择“停止备份”选项。 现在，可为此实例重新启用自动保护。

##  <a name="why-cant-i-see-an-added-database-for-an-auto-protected-instance"></a>为什么不显示自动保护实例的已添加数据库？

最近添加到[自动保护](backup-azure-sql-database.md#enable-auto-protection)实例的数据库可能不会在受保护项下立即显示。 这是因为，发现功能通常每隔 8 小时运行一次。 但是，用户可以使用“恢复数据库”选项运行手动发现，以立即发现并保护新数据库，如下图所示：

  ![查看最近添加的数据库](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>后续步骤

[了解如何](backup-azure-sql-database.md)备份在 Azure VM 上运行的 SQL Server 数据库。
