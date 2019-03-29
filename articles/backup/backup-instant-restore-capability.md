---
title: Azure 即时还原功能
description: Azure 即时还原功能以及有关 VM 备份堆栈、资源管理器部署模型的常见问题解答
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: sogup
ms.openlocfilehash: 21aa01ec8382341de34cca743b9e088598872659
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578894"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>使用 Azure 备份即时还原功能获得更高的备份和还原性能

> [!NOTE]
> 根据用户的反馈，我们正在将“VM 备份堆栈 V2”重命名为“即时还原”，以减少与 Azure Stack 功能的混淆。

即时还原的新模型提供以下功能增强：

* 可以使用执行备份作业期间创建的用于恢复的快照，而无需等待完成将数据传输到保管库的操作。 它缩短了快照在触发还原之前复制到保管库的等待时间。
* 通过在本地保留快照，缩短备份和还原时间，默认可在本地保留两天。 此默认保管库可配置为 1 到 5 天之间的任何值。
* 最大支持 4 TB 的磁盘。
* 支持标准 SSD 磁盘，以及标准 HDD 磁盘和高级 SSD 磁盘。
*   还原时可以使用非托管 VM 的原始存储帐户（按磁盘）。 即使 VM 的磁盘跨存储帐户进行分布，也具备此能力。 这可以加快各种 VM 配置的还原操作。


## <a name="whats-new-in-this-feature"></a>功能亮点

目前，备份作业包括两个阶段：

1.  获取 VM 快照。
2.  将 VM 快照传输到 Azure 恢复服务保管库。

只有在完成阶段 1 和 2 之后，才认为已创建恢复点。 在执行此项升级的过程中，完成快照后会立即创建恢复点，可以在相同的还原流中，使用此快照恢复点类型执行还原。 你可以通过使用"快照"作为恢复点类型，确定在 Azure 门户中的此恢复点和快照传输到保管库后，恢复点类型更改为"快照和保管库"。

![VM 备份堆栈资源管理器部署模型中的备份作业 - 存储和保管库](./media/backup-azure-vms/instant-rp-flow.png)

默认情况下，快照将保留两天。 此功能允许从这些快照存在的还原操作通过降低恢复时间。 它减少了转换和将数据复制回到保管库所需的时间。

## <a name="feature-considerations"></a>功能注意事项

* 快照将连同磁盘一起存储，以提高恢复点的创建速度并加快还原操作。 因此，可以查看 7 天内创建的快照的相应存储成本。
* 增量快照作为页 blob 存储。 使用非托管磁盘的所有用户需要为其本地存储帐户中存储的快照付费。 由于托管 VM 备份使用的还原点集合在基础存储级别使用 Blob 快照，因此对于托管磁盘，你将看到与 Blob 快照定价对应的成本，并且成本是递增的。
* 对于高级存储帐户，即时恢复点计数至 10 TB 限制的所用的快照已分配空间。
* 获得根据还原需要配置快照保留期的能力。 根据要求，可以在备份策略边栏选项卡中将快照保留期设置为最少一天，如下所述。 如果不经常执行还原，这可帮助节省保留快照的成本。
* 这是一个方向升级，即时还原到升级后，您不能再返回。

>[!NOTE]
>使用此即时还原升级，所有客户（**包含新客户和现有客户**）的快照保留期都将设置为两天的默认值。 但是，可以根据需要将持续时间设置为 1 到 5 天之间的任意值。

## <a name="cost-impact"></a>成本影响

增量快照存储在 VM 的存储帐户中，用于即时恢复。 使用增量快照意味着快照占用的空间等于创建该快照后写入的页面所占用的空间。 费用仍按快照占用的空间按 GB 计算，每 GB 价格与[定价页](https://azure.microsoft.com/pricing/details/managed-disks/)中所述的价格相同。

>[!NOTE]
> 快照保留期固定为 5 天的每周的策略。

## <a name="configure-snapshot-retention-using-the-azure-portal"></a>配置使用 Azure 门户的快照保留期

**所有 Azure 备份用户现在已升级到即时还原**。

在 Azure 门户中，您可以看到在中添加字段**VM 备份策略**边栏选项卡下的**即时还原**部分。 可以从“VM 备份策略”边栏选项卡为与特定备份策略关联的所有 VM 更改快照保留期。

![即时还原功能](./media/backup-azure-vms/instant-restore-capability.png)

若要配置快照保留使用 Powershell，请参阅[本文档](backup-azure-vms-automation.md#configuring-instant-restore-snapshot-retention)。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="what-are-the-cost-implications-of-instant-restore"></a>哪些因素影响即时还原的成本？
快照将连同磁盘一起存储，以加速恢复点的创建和还原操作。 因此，你会看到与 VM 备份策略中选择的快照保留期相对应的存储成本。

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>在高级存储帐户中，即时恢复点创建的快照占用 10 TB 快照限制？
是的高级存储帐户的即时恢复点创建的快照占用 10 TB 的快照已分配的空间。

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>在 5 天期限内，快照保留的工作方式是怎样的？
如果每天创建一个新快照，则有 5 个单独的增量快照。 在大多数情况下大约 2%-7%的数据变动量取决于快照的大小。

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>即时还原快照是增量快照还是完整快照？
即时还原功能创建的快照是增量快照。

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>如何计算即时还原功能大约增加的成本？
这取决于 VM 的变动率。 在稳定状态下，可以假设增加的成本 = 快照保留期 * 每个 VM 的每日变动率 * 每 GB 存储成本。

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>如果还原点的恢复类型是“快照和保管库”，而我执行了还原操作，那么，将使用哪种恢复类型？
如果恢复类型是“快照和保管库”，则会从本地快照自动执行还原，与从保管库执行还原相比，其速度要快得多。

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>如果选择的还原点（第 2 层）保留期小于快照（第 1 层）保留期，会发生什么情况？
除非删除快照（第 1 层），否则新模型不允许删除还原点（第 2 层）。 建议将还原点（第 2 层）保留期设置为大于快照保留期。

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>为何我即使在备份策略中设置了保留期，我的快照也仍然存在？
如果恢复点包含快照并且存在最新可用的 RP，则该快照会一直保留到下一次成功备份为止。 这符合当前设计的 GC 策略，该策略强制要求始终至少有一个最新的 RP，以防 VM 中的问题导致所有备份进一步出错。 正常情况下，在 RP 过期后，将在最多 24 小时内予以清理。
