---
title: 通过 Azure Site Recovery 将 Azure Vm 重新保护到主要区域 |Microsoft Docs
description: 介绍如何使用 Azure Site Recovery 在故障转移后重新保护 Azure Vm （辅助区域到主要区域）。
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 818c053c22cfa47cac0f4f6a19349cf239d3cdec
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368586"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>重新保护已故障转移到主要区域的 Azure VM

使用[Azure Site Recovery](site-recovery-overview.md)将 Azure vm 从一个区域[故障转移](site-recovery-failover.md)到另一个区域时，将在次要区域中启动 vm，并处于不**受保护**状态。 如果要将 Vm 故障回复到主要区域，请执行以下任务：

1. 在次要区域中重新保护 VM，以便这些 VM 开始复制到主要区域。
1. 重新保护完成并且 Vm 正在复制后，可以从次要区域故障转移到主要区域。

## <a name="prerequisites"></a>必备条件

- 必须提交将 VM 从主要区域故障转移到次要区域的操作。
- 主要目标站点应该可用，并且应该能够在该区域中访问或创建资源。

## <a name="reprotect-a-vm"></a>重新保护 VM

1. 在“保管库” **“复制的项”中，右键单击已故障转移的 VM，并选择“重新保护”。**  >  重新保护方向应该显示为从次要区域到主要区域。

   ![重新保护](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

1. 查看资源组、网络、存储和可用性集。 然后单击“确定”。 如果有任何资源标记为 "新"，则会将其作为重新保护进程的一部分创建。
1. 重新保护作业使用最新数据植入目标站点。 作业完成后，将发生增量复制。 然后，可以故障转移回到主要站点。 可以使用自定义选项，选择要在重新保护期间使用的存储帐户或网络。

   ![自定义选项](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>自定义重新保护设置

可以在重新保护期间自定义目标 VM 的以下属性。

![自定义](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|properties |说明  |
|---------|---------|
|目标资源组 | 修改创建 VM 的目标资源组。 在重新保护的过程中，将删除目标 VM。 可以选择在故障转移后要在其下创建 VM 的新资源组。 |
|目标虚拟网络 | 执行重新保护作业期间，无法更改目标网络。 若要更改网络，请重做网络映射。 |
|目标存储（辅助 VM 不使用托管磁盘） | 可以更改故障转移后 VM 使用的存储帐户。 |
|副本托管磁盘（辅助 VM 使用托管磁盘） | Site Recovery 在主要区域中创建副本托管磁盘以监视辅助 VM 的托管磁盘。 |
|缓存存储 | 可以指定复制期间要使用的缓存存储帐户。 默认情况下，将创建一个新的缓存存储帐户（如果该帐户不存在）。 |
|可用性集 | 如果次要区域中的 VM 属于可用性集，则可为主要区域中的目标 VM 选择可用性集。 默认情况下，Site Recovery 会尝试在主要区域中查找并使用现有的可用性集。 在自定义期间，可以指定新的可用性集。 |

### <a name="what-happens-during-reprotection"></a>重新保护期间会发生什么情况？

默认情况下，会发生以下情况：

1. 在运行故障转移的 VM 的区域中创建一个缓存存储帐户。
1. 如果目标存储帐户（主要区域中的原始存储帐户）不存在，则会创建一个新帐户。 分配的存储帐户名称是辅助 VM 使用的存储帐户的名称，带有后缀 `asr`。
1. 如果 VM 使用托管磁盘，则主要区域中会创建副本托管磁盘以存储从辅助 VM 磁盘中复制的数据。
1. 如果目标可用性集不存在，则在重新保护作业中创建一个新的目标可用性集（如有必要）。 如果已自定义重新保护设置，则使用所选集。

触发重新保护作业时，如果目标 VM 存在，则会发生以下情况：

1. 关闭正在运行的目标端 VM。
1. 如果 VM 使用托管磁盘，则使用 `-ASRReplica` 后缀创建原始磁盘的副本。 原始磁盘将被删除。 `-ASRReplica` 副本用于复制。
1. 如果 VM 使用的是非托管磁盘，则会分离目标 VM 的数据磁盘并将其用于复制。 将会创建 OS 磁盘的一个副本并将其附加到 VM 上。 将分离原始 OS 磁盘并将其用于复制。
1. 仅同步源磁盘与目标磁盘之间的更改。 将通过比较两个磁盘来计算差异，然后传输这些差异。 查看下面的，找到完成重新保护所要完成的估计时间。
1. 同步完成后，增量复制将开始，并使用复制策略创建恢复点。

当你触发重新保护作业，且目标 VM 和磁盘不存在时，将发生以下情况：

1. 如果 VM 使用托管磁盘，则使用 `-ASRReplica` 后缀创建副本磁盘。 `-ASRReplica` 副本用于复制。
1. 如果 VM 使用的是非托管磁盘，则会在目标存储帐户中创建副本磁盘。
1. 将全部磁盘从故障转移的区域复制到新的目标区域。
1. 同步完成后，增量复制将开始，并使用复制策略创建恢复点。

#### <a name="estimated-time-to-do-the-reprotection"></a>完成重新保护的估计时间

在大多数情况下，Azure Site Recovery 不会将完整的数据复制到源区域。
以下条件确定复制的数据量：

1. 如果由于某种原因（例如资源组更改/删除）删除、损坏或无法访问源 VM 数据，则会发生重新保护，因为在源区域中没有可供使用的数据。
1. 如果源 VM 数据可访问，则只会通过比较两个磁盘并进行传输来计算差异。 请查看下表以获取估计的时间。

|示例情况 | 重新保护所需的时间 |
|---|---|
|源区域有1个 VM，其中标准磁盘为 1 TB。<br/>仅使用 127 GB 数据，而磁盘的其余部分为空。<br/>磁盘类型是标准版，具有 60 MiB/S 的吞吐量。<br/>故障转移后不会更改数据。| 大概时间：45分钟–1.5 小时。<br/>在重新保护期间，Site Recovery 将填充需要 127 GB/45 Mb 的所有数据的校验和，大约为45分钟。<br/>Site Recovery 自动缩放（约20-30 分钟）需要一定的开销时间。<br/>无出口费用。 |
|源区域有1个 VM，其中标准磁盘为 1 TB。<br/>仅使用 127 GB 数据，磁盘的其余部分为空。<br/>磁盘类型是标准版，具有 60 MiB/S 的吞吐量。<br/>故障转移后，45 GB 数据发生更改。| 大致时间：1小时–2小时。<br/>在重新保护期间，Site Recovery 将填充需要 127 GB/45 Mb 的所有数据的校验和，大约为45分钟。<br/>用于应用 45 45 GB/45 MBps 的更改的传输时间大约为17分钟。<br/>出口费用将用于 45 GB 数据更改，而不是用于校验和。 |

## <a name="next-steps"></a>后续步骤

保护 VM 后，可以启动故障转移。 故障转移会关闭次要区域中的 VM，在主要区域中创建并启动 VM，在此过程中会短暂停机。 建议为此过程选择合适的时间，并在启动到主站点的完全故障转移之前运行测试故障转移。 [详细了解](site-recovery-failover.md)Azure Site Recovery 故障转移。
