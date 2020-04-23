---
title: 备份 Azure 文件常见问题解答
description: 本文介绍有关如何使用 Azure 备份服务保护 Azure 文件共享的常见问题解答。
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: d7b19fd11e6784a188a18f6a613eef5ff4f77764
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82105635"
---
# <a name="questions-about-backing-up-azure-files"></a>有关如何备份 Azure 文件的问题

本文回答了有关如何备份 Azure 文件的常见问题。 某些答案提供内含全面信息的文章的链接。 也可以在 [论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)中发布有关 Azure 备份服务的问题。

若要快速浏览本文的各个部分，请使用右侧“本文内容”**** 下的链接。

## <a name="configuring-the-backup-job-for-azure-files"></a>为 Azure 文件配置备份作业

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>为什么我看不到我要保护的某些存储帐户，其中包含有效的 Azure 文件共享？

请参阅[Azure 文件共享备份的支持矩阵](azure-file-share-support-matrix.md)，以确保存储帐户属于受支持的存储帐户类型之一。 还可能是你要查找的存储帐户已受保护，或已注册到另一个保管库。 从保管库中[取消注册存储帐户](manage-afs-backup.md#unregister-a-storage-account)，以便在其他保管库中发现存储帐户以进行保护。

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>尝试配置备份时，为什么看不到存储帐户中的部分 Azure 文件共享？

检查是否已在同一恢复服务保管库中对 Azure 文件共享进行保护，或者已在最近将其删除。

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>是否可以保护已连接到 Azure 文件同步中的某个同步组的文件共享？

是的。 支持对连接到同步组的 Azure 文件共享进行保护。

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>我在尝试备份文件共享时，单击了某个存储帐户，看能否发现其中的文件共享。 但是，我并没有对其进行保护。 如何实现通过任何其他保管库保护这些文件共享？

尝试备份时，选择存储帐户以发现其中的文件共享时，会将存储帐户注册到完成此操作的保管库。 如果选择使用其他保管库来保护文件共享，请从该保管库中[取消注册](manage-afs-backup.md#unregister-a-storage-account)所选存储帐户。

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>是否可以更改将文件共享备份到的保管库？

是的。 但是，你需要从已连接的保管库中[停止对文件共享的保护](manage-afs-backup.md#stop-protection-on-a-file-share)，[取消注册](manage-afs-backup.md#unregister-a-storage-account)此存储帐户，然后从其他保管库中对其进行保护。

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>可以在保管库中保护多少 Azure 文件共享？

你可以保护每个保管库最多50个存储帐户的 Azure 文件共享。 也可在单个保管库中保护多达 200 个 Azure 文件共享。

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>是否可以在不同的保管库中对同一存储帐户中的两个不同的文件共享进行保护？

不是。 只能由同一保管库对某个存储帐户中的所有文件共享进行保护。

## <a name="backup"></a>备份

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>如果备份由于达到最大限制错误而开始失败，我该怎么办？

在任何时间点，最多可以有一个文件共享的 200 个快照。 此限制包括由 Azure 备份根据策略的定义创建的快照。 如果在达到此限制后，备份开始失败，请删除按需快照以获得成功的备份。

## <a name="restore"></a>还原

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>能否从已删除的 Azure 文件共享进行恢复？

删除 Azure 文件共享时，会显示要删除的备份的列表并请求确认。 目前无法还原已删除的 Azure 文件共享。

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>在停止对 Azure 文件共享进行保护的情况下，是否能从备份还原？

是的。 如果在停止保护时选择了“保留备份数据”，则可从所有现有的还原点还原。****

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>如果取消正在进行的还原作业，会发生什么情况？

如果取消正在进行的还原作业，则还原过程将停止，并且在取消之前还原的所有文件将保留在已配置的目标位置（原始位置或备用位置），而不进行任何回退。

## <a name="manage-backup"></a>管理备份

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>是否可以使用 PowerShell 配置/管理/还原 Azure 文件共享的备份？

是的。 请参阅[此处](backup-azure-afs-automation.md)的详细文档。

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>能否访问 Azure 备份创建的快照并装载它们？

可以通过在门户、PowerShell 或 CLI 中查看快照来访问 Azure 备份所采用的所有快照。 若要了解有关 Azure 文件共享快照的详细信息，请参阅[Azure 文件的共享快照概述](../storage/files/storage-snapshots-files.md)。

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>可以为备份配置的最长保留期是多长？

有关最大保留期的详细信息，请参阅[支持矩阵](azure-file-share-support-matrix.md)。 当你在配置备份策略时输入保留值时，Azure 备份会实时计算快照数。 一旦与定义的保留值相对应的快照数超过200，门户会显示一条警告，要求你调整保留值。 这是因为，在任何时间点，你都不会超出 Azure 文件的 Azure 文件所支持的最大快照数限制。

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>更改 Azure 文件共享的备份策略时，会发生什么情况？

在文件共享上应用新策略时，将遵循新策略的计划和保留期。 如果延长保留期，则会对现有的恢复点进行标记，按新策略要求保留它们。 如果缩短保留期，则会将其标记为在下一清理作业中删除，然后就会将其删除。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 备份的其他领域，请选择性地参阅下面的其他备份常见问题解答：

- [恢复服务保管库常见问题解答](backup-azure-backup-faq.md)
- [Azure VM 备份常见问题解答](backup-azure-vm-backup-faq.md)
- [Azure 备份代理常见问题解答](backup-azure-file-folder-backup-faq.md)
