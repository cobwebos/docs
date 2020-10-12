---
title: 意外删除 Azure 文件共享的保护
description: 了解如何通过软删除来防止 Azure 文件共享被意外删除。
ms.topic: conceptual
ms.date: 02/02/2020
ms.custom: references_regions
ms.openlocfilehash: 52a116320e07c25d4ee7f00b8063ca15faeb8560
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89179906"
---
# <a name="accidental-delete-protection-for-azure-file-shares-using-azure-backup"></a>使用 Azure 备份对 Azure 文件共享的意外删除保护

若要针对网络攻击或意外删除提供保护，在为各自的存储帐户中的任何文件共享配置备份时，会为存储帐户中的所有文件共享启用 [软删除](../storage/files/storage-files-prevent-file-share-deletion.md) 。 使用软删除时，即使恶意执行组件删除文件共享，文件共享的内容和恢复点 (快照) 至少会保留14天的时间，从而允许在不丢失数据的情况下恢复文件共享。  标准存储帐户和高级存储帐户支持软删除，Azure 备份会为托管备份文件共享的所有存储帐户启用软删除。

以下流程图显示了在为存储帐户中的文件共享启用软删除时备份项的不同步骤和状态：

 ![软删除流程图](./media/soft-delete-afs/soft-delete-flow-chart.png)

## <a name="frequently-asked-questions"></a>常见问题解答

### <a name="when-will-soft-delete-be-enabled-for-file-shares-in-my-storage-account"></a>何时将对存储帐户中的文件共享启用软删除？

当你首次为存储帐户中的任何文件共享配置备份时，Azure 备份服务会对各自存储帐户中的所有文件共享启用软删除。

### <a name="can-i-configure-the-number-of-days-for-which-my-snapshots-and-restore-points-will-be-retained-in-soft-deleted-state-after-i-delete-the-file-share"></a>删除文件共享后，是否可以将快照和还原点将以软删除状态保留的天数

是的，你可以根据要求设置保持期。 [本文档](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal) 介绍配置保留期的步骤。 对于具有已备份文件共享的存储帐户，最小保持期设置应为14天。

### <a name="does-azure-backup-reset-my-retention-setting-because-i-configured-it-to-less-than-14-days"></a>Azure 备份是否重置保留设置，因为已将其配置为小于14天？

从安全角度来看，我们建议使用已备份文件共享的存储帐户至少保留14天。 因此，在每个备份作业运行时，如果 Azure 备份将该设置标识为少于14天，则会将其重置为14天。

### <a name="what-is-the-cost-incurred-during-the-retention-period"></a>在保留期内产生的成本是多少？

在软删除期间，受保护的实例成本和快照存储成本将保持不变。  此外，还将按标准文件共享的标准费率以及针对高级文件共享的快照存储费率向你收费。

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-deleted-state"></a>如果数据处于软删除状态，是否可以执行还原操作？

首先需要撤消删除软删除的文件共享才能执行还原操作。 取消删除操作会使文件共享进入已备份状态，你可以在其中还原到任何时间点。 若要了解如何撤消删除文件共享，请访问 [此链接](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) 或查看 [删除文件共享脚本](./scripts/backup-powershell-script-undelete-file-share.md)。

### <a name="how-can-i-purge-the-data-of-a-file-share-in-a-storage-account-that-has-at-least-one-protected-file-share"></a>如何清除至少具有一个受保护文件共享的存储帐户中的文件共享的数据？

如果在存储帐户中至少有一个受保护的文件共享，这意味着会为该帐户中的所有文件共享启用软删除，并且在删除操作完成后，你的数据将保留14天。 但是，如果想要立即清除数据，而不希望保留数据，请执行以下步骤：

1. 如果已在启用软删除的情况下删除了文件共享，请先从 [文件门户](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) 或使用 " [撤消删除文件共享" 脚本](./scripts/backup-powershell-script-undelete-file-share.md)删除文件共享。
2. 按照 [本文档](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#disable-soft-delete)中所述的步骤操作，对存储帐户中的文件共享禁用软删除。
3. 现在删除要立即清除其内容的文件共享。

>[!NOTE]
>在对存储帐户中受保护的文件共享运行下一个计划的备份作业之前，应执行步骤2。 因为当备份作业运行时，它将为存储帐户中的所有文件共享重新启用软删除。

>[!WARNING]
>在步骤2中禁用软删除后，对文件共享执行的任何删除操作都是永久删除操作。 因此，如果在禁用软删除后意外删除了备份文件共享，则会丢失所有快照，并且无法恢复数据。

### <a name="in-the-context-of-a-file-shares-soft-delete-setting-what-changes-does-azure-backup-do-when-i-unregister-a-storage-account"></a>在文件共享的软删除设置上下文中，当我注销存储帐户时，Azure 备份会进行哪些更改？

注销时，Azure 备份会检查文件共享的保留期设置，如果超过14天或少于14天，则保留原样。 但是，如果保留期为14天，则会将其视为由 Azure 备份启用，因此我们在注销过程中禁用软删除。 如果要在保持保留设置为 "原样" 时注销存储帐户，请在完成注销后从 "存储帐户" 窗格中再次启用它。 有关配置步骤，请参阅 [此链接](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) 。

## <a name="next-steps"></a>后续步骤

了解如何[从 Azure 门户备份 Azure 文件共享](backup-afs.md)
