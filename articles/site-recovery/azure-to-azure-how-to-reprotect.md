---
title: 使用 Azure Site Recovery 重新保护已故障转移回到主要 Azure 区域的 Azure VM | Microsoft Docs
description: 介绍在从主要区域故障转移后，如何使用 Azure Site Recovery 在次要区域中重新保护 Azure VM。
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 05/15/2018
ms.author: rajanaki
ms.openlocfilehash: ccec4262297314bad261a852bb5db25c428ce0a0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>重新保护已故障转移到主要区域的 Azure VM


>[!NOTE]
>
> 适用于 Azure VM 的 Site Recovery 复制功能当前为预览版。



使用 [Azure Site Recovery](site-recovery-overview.md) 将 Azure VM 从一个区域[故障转移](site-recovery-failover.md)到另一个区域后，VM 将在次要区域中启动，且不受保护。 如果将 VM 故障转移回到主要区域，则需要执行以下操作：

- 在次要区域中重新保护 VM，以便这些 VM 开始复制到主要区域。 
- 重新保护完成并且 VM 开始复制后，可将 VM 从次要区域故障转移到主要区域。

> [!WARNING]
> 如果是将计算机从主要区域[迁移](migrate-overview.md#what-do-we-mean-by-migration)到了次要区域、将 VM 转移到了另一个资源组，或者删除了 Azure VM，则无法重新保护或故障回复 VM。


## <a name="prerequisites"></a>先决条件
1. 必须提交将 VM 从主要区域故障转移到次要区域的操作。
2. 主要目标站点应该可用，并且应该能够在该区域中访问或创建资源。

## <a name="reprotect-a-vm"></a>重新保护 VM

1. 在“保管库” > “复制的项”中，右键单击已故障转移的 VM，并选择“重新保护”。 重新保护方向应该显示为从次要区域到主要区域。 

  ![重新保护](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. 查看资源组、网络、存储和可用性集。 然后单击“确定”。 如果有任何资源标记为“新”，则表示它们是在重新保护过程中创建的。
3. 重新保护作业使用最新数据植入目标站点。 完成该过程后，将发生增量复制。 然后，可以故障转移回到主要站点。 可以使用自定义选项，选择要在重新保护期间使用的存储帐户或网络。

  ![自定义选项](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>自定义重新保护设置

可在重新保护期间自定义以下目标 VM 的属性。

![自定义](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|属性 |说明  |
|---------|---------|
|目标资源组     | 修改创建 VM 的目标资源组。 在重新保护的过程中，将删除目标 VM。 可以选择在故障转移后要在其下创建 VM 的新资源组。        |
|目标虚拟网络     | 执行重新保护作业期间，无法更改目标网络。 若要更改网络，请重做网络映射。         |
|目标存储（辅助 VM 不使用托管磁盘）     | 可以更改故障转移后 VM 使用的存储帐户。         |
|副本托管磁盘（辅助 VM 使用托管磁盘）    | Site Recovery 在主要区域中创建副本托管磁盘以监视辅助 VM 的托管磁盘。         | 
|缓存存储     | 可以指定复制期间要使用的缓存存储帐户。 默认情况下，如果不存在缓存存储帐户，则会创建一个帐户。         |
|可用性集     |如果次要区域中的 VM 属于可用性集，则可为主要区域中的目标 VM 选择可用性集。 默认情况下，Site Recovery 会尝试在主要区域中查找并使用现有的可用性集。 在自定义期间，可以指定新的可用性集。         |


### <a name="what-happens-during-reprotection"></a>重新保护期间会发生什么情况？

默认会发生以下情况：

1. 在主要区域中创建一个缓存存储帐户
2. 如果目标存储帐户（主要区域中的原始存储帐户）不存在，则会创建一个新帐户。 分配的存储帐户名称是辅助 VM 使用的存储帐户名称，并带有后缀“asr”。
3. 如果 VM 使用托管磁盘，则主要区域中会创建副本托管磁盘以存储从辅助 VM 磁盘中复制的数据。 
4. 如果目标可用性集不存在，将在执行重新保护作业期间根据需要新建一个可用性集。 如果已自定义重新保护设置，则会使用选定的可用性集。

触发重新保护作业时，如果目标 VM 存在，则会发生以下情况：

1. 在重新保护过程中创建所需的组件。 如果这些组件已存在，则会重复使用。
2. 关闭正在运行的目标端 VM。
3. Site Recovery 将目标端 VM 作为种子 Blob 复制到容器中。
4. 然后删除目标端 VM。
5. 当前源端（辅助）VM 使用种子 Blob 进行复制。 这可确保仅复制增量。
6. 同步源磁盘与种子 Blob 之间的重要更改。 此操作需要一段时间才能完成。
7. 重新保护作业完成后，根据复制策略开始执行增量复制，并创建恢复点。
8. 重新保护作业成功后，VM 进入受保护状态。

## <a name="next-steps"></a>后续步骤

保护 VM 后，可以启动故障转移。 故障转移过程会关闭次要区域中的 VM，在主要区域中创建并启动 VM，这会出现短暂的停机。 我们建议安排适当的时间并运行测试故障转移，但要启动到主要站点的完全故障转移。 [详细了解](site-recovery-failover.md)故障转移。

