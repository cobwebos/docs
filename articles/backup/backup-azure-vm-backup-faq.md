---
title: 常见问题解答 - 备份 Azure VM
description: 本文解答有关使用 Azure 备份服务备份 Azure VM 的常见问题。
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: accfc57055f70254814c889de875f5360878bcd9
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757469"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>常见问题 - 备份 Azure VM

本文解答有关使用 [Azure 备份](backup-introduction-to-azure-backup.md)服务备份 Azure VM 的常见问题。

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>哪些 VM 映像可以在创建时启用备份功能？

创建 VM 时，可以为运行[受支持的操作系统的](backup-support-matrix-iaas.md#supported-backup-actions)VM 启用备份。

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>备份成本包含在 VM 成本内吗？

否。 备份成本独立于 VM 的成本。 详细了解 [Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)。

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

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>按需备份作业是否与计划的备份使用相同的保留计划？

否。 为按需备份作业指定保留期。 默认情况下，从门户触发时，该作业会保留 30 天。

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>我最近在一些 VM 上启用了 Azure 磁盘加密。 我的备份是否继续有效？

为 Azure 备份提供访问密钥保管库的权限。 请根据 [Azure 备份 PowerShell](backup-azure-vms-automation.md) 文档的“启用备份”部分中所述，在 PowerShell 中指定权限。 

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>我将 VM 磁盘迁移到了托管磁盘。 我的备份是否继续有效？

是的，备份可以顺利工作。 无需重新配置任何设置。

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>为何在配置备份向导中看不到我的 VM？

该向导只会列出保管库所在的同一区域中的 VM，以及不在备份的 VM。

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>我的 VM 已关闭。 按需备份或计划备份会运行吗？

是的。 计算机关闭时，将运行备份。 恢复点标记为崩溃一致。

### <a name="can-i-cancel-an-in-progress-backup-job"></a>可以取消正在进行的备份作业吗？

是的。 您可以在 **"拍摄快照"** 状态下取消备份作业。 如果作业正在从快照传输数据，则不能将它取消。

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>我启用了 Azure 备份服务创建的资源组的锁（例如。 `AzureBackupRG_<geo>_<number>` 我的备份是否继续有效？

如果锁定 Azure 备份服务创建的资源组，备份将开始失败，因为最大限制为 18 个还原点。

删除锁，并从该资源组清除还原点集合，以使将来的备份成功。 [按照以下步骤](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal)删除还原点集合。

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Azure 备份是否支持标准 SSD 托管磁盘？

是的，Azure 备份支持[标准 SSD 托管磁盘](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/)。

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>可使用支持写入加速器 (WA) 的磁盘备份 VM 吗？

无法在已启用 WA 的磁盘上拍摄快照。 但是，Azure 备份服务可以从备份中排除已启用 WA 的磁盘。

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>我有一个安装了写入加速器 (WA) 磁盘和 SAP HANA 的 VM。 我该如何备份？

Azure 备份无法备份已启用 WA 的磁盘，但可以将其从备份中排除。 但是，备份不会提供数据库一致性，因为未备份已启用 WA 的磁盘上的信息。 如果需要备份操作系统磁盘和备份未启用 WA 的磁盘，则可以使用此配置备份磁盘。

Azure 备份为 SAP HANA 数据库提供了 15 分钟的流式处理备份解决方案。 它由 SAP 认证，可利用 SAP HANA 的本机 API 提供本机备份支持。 [了解有关在 Azure VM 中备份 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/sap-hana-db-about)的信息。

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>从我在 VM 备份策略中设置的计划备份时间开始，我可以预期的备份开始时间的最大延迟是多少？

计划备份将在计划备份时间的 2 小时内触发。 例如，如果 100 个 VM 的备份开始时间安排在凌晨 2：00，那么最晚 4：00 时，所有 100 个 VM 都将进行备份作业。 如果计划备份因中断而暂停并恢复或重试，则备份可以在计划两小时窗口之外启动。

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>每日备份点的最小允许保留范围是多少？

Azure 虚拟机备份策略支持从 7 天到 9999 天的最低保留范围。 对少于 7 天的现有 VM 备份策略进行任何修改都需要进行更新，以满足 7 天的最小保留期限。

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>如果我更改 VM 或 VM 资源组的名称，会发生什么情况？

如果将 VM 或 VM 资源组的情况（更改为上部或下部），则备份项名称的情况不会更改。 但是，这是预期的 Azure 备份行为。 案例更改不会显示在备份项中，但在后端更新。

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>我可以备份或还原连接到 VM 的选择性磁盘吗？

Azure 备份现在支持使用 Azure 虚拟机备份解决方案进行选择性磁盘备份和还原。

如今，Azure 备份支持使用虚拟机备份解决方案一起备份 VM 中的所有磁盘（操作系统和数据）。 使用排除磁盘功能，您可以选择从 VM 中的许多数据磁盘备份一个或多个数据磁盘。 这为备份和恢复需求提供了高效且经济高效的解决方案。 每个恢复点都包含备份操作中包含的磁盘的数据，这进一步允许您在还原操作期间从给定恢复点还原磁盘子集。 这适用于从快照和保管库还原。

要注册预览版，请写信给我们：AskAzureBackupTeam@microsoft.com

## <a name="restore"></a>还原

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>如何确定仅还原磁盘还是完整 VM？

将 VM 还原视为 Azure VM 的快速创建选项。 此选项会更改磁盘名称、磁盘使用的容器、公共 IP 地址和网络接口名称。 创建 VM 时，更改将维护唯一资源。 未将 VM 添加到可用性集。

若要执行以下操作，可使用还原磁盘选项：

- 自定义创建的 VM。 例如，更改大小。
- 添加备份时不存在的配置设置。
- 控制创建的资源的命名约定。
- 将 VM 添加到可用性集。
- 添加必须使用 PowerShell 或模板配置的任何其他设置。

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>升级到托管磁盘后，是否可以还原非托管 VM 磁盘的备份？

是的，可使用将磁盘从非托管迁移到托管之前创建的备份。

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>将 VM 迁移到托管磁盘之前，如何将 VM 还原至还原点？

还原过程保持不变。 如果恢复点是 VM 具有非托管磁盘时的时间点，则可以[将磁盘还原为非托管](tutorial-restore-disk.md#unmanaged-disks-restore)磁盘。 如果 VM 具有托管磁盘，则可以[将磁盘还原为托管磁盘](tutorial-restore-disk.md#managed-disk-restore)。 然后，可以从[这些磁盘创建 VM。](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk)

在 PowerShell 中[详细了解](backup-azure-vms-automation.md#restore-an-azure-vm)此操作。

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>可以还原已删除的 VM 吗？

是的。 即使您删除了 VM，也可以转到保管库中的相应备份项并从恢复点进行还原。

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>如何将 VM 还原到相同的可用性集？

对于托管磁盘 Azure VM，通过在模板中提供一个选项，同时还原为托管磁盘，从而启用还原到可用性集。 此模板具有名为“可用性集”的输入参数****。

### <a name="how-do-we-get-faster-restore-performances"></a>如何获得更快的还原性能？

[即时还原](backup-instant-restore-capability.md)功能有助于加快备份速度，并快速从快照进行恢复。

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>更改已加密 VM 的密钥保管库设置时会发生什么情况？

更改加密 VM 的密钥保管库设置后，备份将继续使用新的详细信息集。 但是，在更改之前从恢复点还原后，您必须还原密钥保管库中的秘密，然后才能从该密钥保管库中创建 VM。 有关详细信息，请参阅[本文](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret)。

机密/密钥滚动等操作不需要此步骤，并且还原后可以使用相同的 KeyVault。

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>在还原后我是否由于 VM 与域控制器的关系被破坏而可以访问 VM？

可以，由于 VM 与域控制器的关系被破坏，因此在还原后可以访问 VM。 有关详细信息，请参阅[本文](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps)

## <a name="manage-vm-backups"></a>管理 VM 备份

### <a name="what-happens-if-i-modify-a-backup-policy"></a>如果修改备份策略会发生什么情况？

在修改后的策略或新策略中使用计划和保留期设置备份 VM。

- 如果延长保留期，则会根据新策略标记并保留现有恢复点。
- 如果缩短保留期，则会将恢复点标记为在下一清理作业中删除，随后会将其删除。

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>如何将 Azure 备份所备份的 VM 移动到其他资源组？

1. 暂时停止备份，并保留备份数据。
2. 将 VM 移动到目标资源组。
3. 在同一保管库中重新启用备份。

可以从在移动操作之前创建的可用还原点还原 VM。

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy"></a>可以与同一备份策略关联的 VM 数量是否有限制？

是的，有 100 个 VM 的限制可以从门户连接到同一备份策略。 我们建议，如果 VM 数超过 100 个，请创建具有相同计划或不同计划的多个备份策略。
