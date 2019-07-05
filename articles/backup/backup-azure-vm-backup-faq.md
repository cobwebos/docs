---
title: 有关使用 Azure 备份来备份 Azure VM 的常见问题
description: 有关使用 Azure 备份来备份 Azure VM 的常见问题的解答。
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: sogup
ms.openlocfilehash: 0248e169f5d502cce8723f594f438b87ab088f3a
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551598"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>常见问题 - 备份 Azure VM

本文解答有关使用 [Azure 备份](backup-introduction-to-azure-backup.md)服务备份 Azure VM 的常见问题。


## <a name="backup"></a>备份

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>哪些 VM 映像可以在创建时启用备份功能？
在创建 VM 时，可以运行的 Vm 启用备份[受支持的操作系统](backup-support-matrix-iaas.md#supported-backup-actions)

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>备份成本包含在 VM 成本内吗？

不。 备份成本独立于 VM 的成本。 详细了解 [Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)。

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>为 VM 启用备份需要哪些权限？

如果你是 VM 参与者，便可以在 VM 上启用备份。 如果你使用的是自定义角色，则需要具有以下权限才可在 VM 上启用备份：

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

如果恢复服务保管库和 VM 的资源组不同，请确保具有恢复服务保管库资源组的写入权限。  


### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>按需备份作业是否使用与计划备份相同的保留计划？
否。 为按需备份作业指定保留期。 默认情况下，从门户触发时，该作业会保留 30 天。

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>我最近在一些 VM 上启用了 Azure 磁盘加密。 我的备份是否继续有效？
提供 Azure 备份访问 Key Vault 所需的权限。 按照 [Azure 备份 PowerShell](backup-azure-vms-automation.md) 文档中的“启用备份”部分所述，在 PowerShell 中指定权限  。

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>我将 VM 磁盘迁移到了托管磁盘。 我的备份是否继续有效？
是的，备份可以顺利工作。 没有必要重新配置任何内容。

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>为什么在“配置备份”向导中看不到我的 VM？
该向导仅列出与 Vault 相同区域中的 VM，这些 VM 尚未备份。

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>我的 VM 已关闭。 按需备份或计划备份会运行吗？
是的。 计算机关闭时运行备份。 将恢复点标记为崩溃一致。

### <a name="can-i-cancel-an-in-progress-backup-job"></a>可以取消正在进行的备份作业吗？
是的。 可在“拍摄快照”状态中取消备份作业  。 如果此作业正在从快照传输数据，则无法取消。

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprggeonumber-will-my-backups-continue-to-work"></a>我在由 Azure 备份服务创建的资源组（即 `AzureBackupRG_<geo>_<number>`）上启用了锁定，我的备份是否继续有效？
如果锁定 Azure 备份服务创建的资源组，则由于还原点的最大数目限制为 18 个，备份会开始失败。

用户需要删除锁，然后清除该资源组中的还原点集合以使将来的备份成功，[请按照下列步骤](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal)删除还原点集合。


### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Azure 备份是否支持标准 SSD 托管磁盘？
Azure 备份支持[标准 SSD 托管磁盘](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/)。 SSD 托管磁盘的 Azure Vm 提供一种新型的持久存储。 [即时还原](backup-instant-restore-capability.md)中提供了对 SSD 托管磁盘的支持。

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>可使用支持写入加速器 (WA) 的磁盘备份 VM 吗？
无法在已启用 WA 的磁盘上拍摄快照。 但是，Azure 备份服务可以从备份中排除已启用 WA 的磁盘。

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>我有一个安装了写入加速器 (WA) 磁盘和 SAP HANA 的 VM。 我该如何备份？
Azure 备份无法备份已启用 WA 的磁盘，但可以将其从备份中排除。 但是，备份不会提供数据库一致性，因为未备份已启用 WA 的磁盘上的信息。 如果需要备份操作系统磁盘和备份未启用 WA 的磁盘，则可以使用此配置备份磁盘。

我们正在与 15 分钟 RPO 运行 SAP HANA 备份的专用预览版。 它以与 SQL 数据库备份类似的方式构建，并将 backInt 接口用于 SAP HANA 认证的第三方解决方案。 如果您感兴趣，发送电子邮件至`AskAzureBackupTeam@microsoft.com`与该主题**注册的 Azure Vm 中的 SAP HANA 备份的专用预览版**。

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>什么是我可以从我已经在我的 VM 备份策略中设置的计划备份时间的备份开始时间在预期的最大延迟？
在 2 小时内的计划的备份时间，将触发计划的备份。 有关示例。 如果 100 个 Vm 有备份开始时间安排在凌晨 2:00，然后按最大凌晨 4:00 点所有 100VMs 都必须备份作业正在进行中。 如果由于服务中断和恢复/重试已暂停计划的备份然后此计划的 2 小时的时段之外，可以开始备份。

### <a name="what-is-the-minimum-allowed-retention-range-for-daily-backup-point"></a>每日备份点的最小的允许保留期范围是什么？
Azure 虚拟机备份策略到 9999 天支持启动的最小保持期为 7 天。 向现有 VM 备份策略与 7 天内的任何修改都要求更新以满足最小保持期为 7 天。

## <a name="restore"></a>还原

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>如何确定仅还原磁盘还是完整 VM？
将 VM 还原视为 Azure VM 的快速创建选项。 此选项可更改磁盘名称，磁盘、 公共 IP 地址和网络接口名称使用的容器。 创建 VM 时，更改将维护唯一资源。 未将 VM 添加到可用性集。

若要执行以下操作，可使用还原磁盘选项：
  * 自定义创建的 VM。 例如，更改大小。
  * 添加备份时不存在的配置设置。
  * 控制创建的资源的命名约定。
  * 将 VM 添加到可用性集。
  * 添加必须使用 PowerShell 或模板配置的任何其他设置。

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>升级到托管磁盘后，是否可以还原非托管 VM 磁盘的备份？
是的，可使用将磁盘从非托管迁移到托管之前创建的备份。
- 默认情况下，还原 VM 作业将创建非托管 VM。
- 但是，可以还原磁盘并用其来创建托管 VM。

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>将 VM 迁移到托管磁盘之前，如何将 VM 还原至还原点？
默认情况下，还原 VM 作业将创建具有非托管磁盘的 VM。 若要使用托管磁盘创建 VM，请执行以下操作：
1. [还原到非托管磁盘](tutorial-restore-disk.md#restore-a-vm-disk)。
2. [将还原的磁盘转换为托管磁盘](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk)。
3. [使用托管磁盘创建 VM](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk)。

在 PowerShell 中[详细了解](backup-azure-vms-automation.md#restore-an-azure-vm)此操作。

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>可以还原已删除的 VM 吗？
是的。 即使删除了 VM，也可以转到保管库中的相应备份项并从恢复点还原。

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>如何将 VM 还原到相同的可用性集？
对于托管磁盘 Azure VM，通过在还原为托管磁盘时在模板中提供选项来启用还原到可用性集。 此模板具有名为“可用性集”的输入参数  。

### <a name="how-do-we-get-faster-restore-performances"></a>如何获得更快的还原性能？
为了获得更快的还原性能，我们正在转到[即时还原](backup-instant-restore-capability.md)功能。

## <a name="manage-vm-backups"></a>管理 VM 备份

### <a name="what-happens-if-i-modify-a-backup-policy"></a>如果修改备份策略会发生什么情况？
在修改后的策略或新策略中使用计划和保留期设置备份 VM。

- 如果延长保留期，则会根据新策略标记并保留现有恢复点。
- 如果缩短保留期，则会将恢复点标记为在下一清理作业中删除，随后会将其删除。

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>如何将 Azure 备份所备份的 VM 移动到其他资源组？

1. 暂时停止备份，并保留备份数据。
2. 将 VM 移动到目标资源组。
3. 在相同或新的保管库中重新启用备份。

可以从在移动操作之前创建的可用还原点还原 VM。

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy"></a>是否有可以与相同的备份策略相关联的 Vm 数量限制？
是的可以关联到相同的备份策略在门户中的 100 个 Vm 的限制。 我们建议为超过 100 个 Vm，使用相同的计划或不同的计划创建多个备份策略。
