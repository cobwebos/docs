---
title: 备份 Azure 文件常见问题解答
description: 本文介绍有关如何使用 Azure 备份服务保护 Azure 文件共享的常见问题解答。
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: 1be509f3b82cece3afb1e728a19da4c4d9526195
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836101"
---
# <a name="questions-about-backing-up-azure-files"></a>有关如何备份 Azure 文件的问题

本文回答了有关如何备份 Azure 文件的常见问题。 某些答案提供内含全面信息的文章的链接。 也可以在[供讨论的 Microsoft Q&A 问题页面](https://docs.microsoft.com/answers/topics/azure-backup.html)中发布有关 Azure 备份服务的问题。

若要快速浏览本文的各个部分，请使用右侧“本文内容”下的链接。

## <a name="configuring-the-backup-job-for-azure-files"></a>为 Azure 文件配置备份作业

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>为什么我看不到需要保护的部分存储帐户？这些帐户中包含有效的 Azure 文件共享。

请参阅 [Azure 文件共享备份的支持矩阵](azure-file-share-support-matrix.md)，确保存储帐户属于受支持的存储帐户类型之一。 还有一种可能是，要查找的存储帐户已受保护或已注册到另一保管库中。 从保管库中[注销存储帐户](manage-afs-backup.md#unregister-a-storage-account)可发现其他保管库中要保护的存储帐户。

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>尝试配置备份时，为什么看不到存储帐户中的部分 Azure 文件共享？

检查是否已在同一恢复服务保管库中对 Azure 文件共享进行保护，或者已在最近将其删除。

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>是否可以保护已连接到 Azure 文件同步中的某个同步组的文件共享？

是的。 保护连接到同步组的 Azure 文件共享这一功能已启用。

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>我在尝试备份文件共享时，单击了某个存储帐户，看能否发现其中的文件共享。 但是，我没能对其进行保护。 如何使用其他保管库来保护这些文件共享？

尝试进行备份时，如果选择一个要发现其中的文件共享的存储帐户，则会将该存储帐户注册到在其中执行此操作的保管库。 如果选择使用其他保管库来保护文件共享，则请从该保管库[注销](manage-afs-backup.md#unregister-a-storage-account)所选存储帐户。

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>是否可以更改将文件共享备份到的保管库？

是的。 但是，需要先在连接的保管库中[停止对文件共享的保护](manage-afs-backup.md#stop-protection-on-a-file-share)，[注销](manage-afs-backup.md#unregister-a-storage-account)此存储帐户，然后在另一保管库中对其进行保护。

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>可以在保管库中保护多少 Azure 文件共享？

一个保管库中最多可以保护 50 个存储帐户的 Azure 文件共享。 也可在单个保管库中保护多达 200 个 Azure 文件共享。

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>是否可以在不同的保管库中对同一存储帐户中的两个不同的文件共享进行保护？

不是。 只能由同一保管库对某个存储帐户中的所有文件共享进行保护。

## <a name="backup"></a>备份

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>如果因达到最大限制的错误而无法进行备份，我该怎么办？

在任何时间点，最多可以有一个文件共享的 200 个快照。 此限制包括由 Azure 备份根据策略的定义创建的快照。 如果在达到此限制后无法进行备份，请删除按需快照，以便将来能够成功地进行备份。

## <a name="restore"></a>还原

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>能否从已删除的 Azure 文件共享进行恢复？

删除 Azure 文件共享时，系统会显示将删除的备份列表，并会要求你进行确认。 目前，无法还原已删除的 Azure 文件共享。

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>在停止对 Azure 文件共享进行保护的情况下，是否能从备份还原？

是的。 如果在停止保护时选择了“保留备份数据”，则可从所有现有的还原点还原。

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>如果取消正在进行的还原作业，会发生什么情况？

如果取消正在进行的还原作业，则还原过程将停止，并且在取消之前还原的所有文件将保留在已配置的目标位置（原始位置或备用位置），而不进行任何回退。

## <a name="manage-backup"></a>管理备份

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>是否可以使用 PowerShell 配置/管理/还原 Azure 文件共享的备份？

是的。 请参阅[此处](backup-azure-afs-automation.md)的详细文档。

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>能否访问 Azure 备份生成的快照并装载它们？

可以访问 Azure 备份生成的所有快照，只需在门户、PowerShell 或 CLI 中查看快照即可。 若要详细了解 Azure 文件共享快照，请参阅 [Azure 文件的共享快照概述](../storage/files/storage-snapshots-files.md)。

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>可以为备份配置的最长保留期是多长？

有关最长保留期的详细信息，请参阅[支持矩阵](azure-file-share-support-matrix.md)。 在配置备份策略时输入保留期值后，Azure 备份会实时计算快照数。 一旦与定义的保留期值对应的快照数超过 200，门户就会显示一条警告，要求你调整保留期值。 因此，在任何时间点对于任何文件共享，都不会超过 Azure 文件所支持的最大快照数限制。

### <a name="what-is-the-impact-on-existing-recovery-points-and-snapshots-when-i-modify-the-backup-policy-for-an-azure-file-share-to-switch-from-daily-policy-to-gfs-policy"></a>修改 Azure 文件共享的备份策略以从“每日策略”切换到“GFS 策略”时，对现有恢复点和快照有什么影响？

将每日备份策略修改为 GFS 策略（添加每周/每月/每年保留）时，行为如下所示：

- 保留：如果要在修改策略的过程中添加每周/每月/每年保留，那么作为计划备份的一部分创建的所有未来恢复点都将根据新策略进行标记。 所有现有恢复点仍将被视为每日恢复点，因此不会标记为每周/每月/每年。

- 快照和恢复点清理：

  - 如果延长每日保留期，则会根据新策略中配置的每日保留期值更新现有恢复点的到期日期。
  - 如果缩短每日保留期，则会根据新策略中配置的每日保留期值将现有恢复点和快照标记为在下一次清理运行作业中删除，之后就会将其删除。

下面用一个示例说明上述工作原理：

#### <a name="existing-policy-p1"></a>现有策略 [P1]

|保留类型 |计划 |保留  |
|---------|---------|---------|
|每日    |    每天晚上 8 点    |  100 天       |

#### <a name="new-policy-modified-p1"></a>新策略 [已修改 P1]

| 保留类型 | 计划                       | 保留 |
| -------------- | ------------------------------ | --------- |
| 每日          | 每天晚上 9 点              | 50 天   |
| 每周         | 星期日晚上 9 点              | 3 周   |
| 每月        | 上个星期一晚上 9 点         | 1 个月   |
| 每年         | 一月的第三个星期日晚上 9 点 | 4 年   |

#### <a name="impact"></a>影响

1. 现有恢复点的到期日期将根据新策略的每日保留期值（即 50 天）进行调整。 因此，超过 50 天的任何恢复点都将标记为要删除。

2. 不会基于新策略将现有恢复点标记为每周/每月/每年。

3. 所有将来备份都会根据新计划（即，晚上 9 点）触发。

4. 所有将来恢复点的到期日期都会与新策略一致。

>[!NOTE]
>策略更改只会影响在计划备份作业运行的过程中创建的恢复点。 对于按需备份，保留期由执行备份时指定的“保留截止日期”值确定。

### <a name="what-is-the-impact-on-existing-recovery-points-when-i-modify-an-existing-gfs-policy"></a>修改现有 GFS 策略时，对现有恢复点有什么影响？

在文件共享上应用新策略时，将根据修改后策略中配置的计划执行所有将来的计划备份。  所有现有恢复点的保留期将根据配置的新保留期值进行调整。 因此，如果延长保留期，则会将现有恢复点标记为根据新策略保留。 如果缩短保留期，则会将其标记为在下一次清理作业中删除，之后就会将其删除。

下面用一个示例说明上述工作原理：

#### <a name="existing-policy-p2"></a>现有策略 [P2]

| 保留类型 | 计划           | 保留 |
| -------------- | ------------------ | --------- |
| 每日          | 每天晚上 8 点 | 50 天   |
| 每周         | 星期一晚上 8 点  | 3 周   |

#### <a name="new-policy-modified-p2"></a>新策略 [已修改 P2]

| 保留类型 | 计划               | 保留 |
| -------------- | ---------------------- | --------- |
| 每日          | 每天晚上 9 点     | 10 天   |
| 每周         | 星期一晚上 9 点      | 2 周   |
| 每月        | 上个星期一晚上 9 点 | 2 个月  |

#### <a name="impact-of-change"></a>更改的影响

1. 现有每日恢复点的到期日期将根据新的每日保留期值（即 10 天）进行调整。 因此，任何超过 10 天的每日恢复点都将被删除。

2. 现有每周恢复点的到期日期将根据新的每周保留期值（即 2 周）进行调整。 因此，任何超过 2 周的每周恢复点都将被删除。

3. 每月恢复点只会基于新策略配置在将来备份时创建。

4. 所有将来恢复点的到期日期都会与新策略一致。

>[!NOTE]
>策略更改只会影响在计划备份的过程中创建的恢复点。 对于按需备份，保留期由执行备份时指定的“保留截止日期”值确定。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 备份的其他领域，请选择性地参阅下面的其他备份常见问题解答：

- [恢复服务保管库常见问题解答](backup-azure-backup-faq.md)
- [Azure VM 备份常见问题解答](backup-azure-vm-backup-faq.md)
- [Azure 备份代理常见问题解答](backup-azure-file-folder-backup-faq.md)
