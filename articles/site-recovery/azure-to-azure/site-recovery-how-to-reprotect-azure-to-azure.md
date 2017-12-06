---
title: "如何重新保护回复到 Azure 主要区域的故障转移 Azure 虚拟机 | Microsoft Docs"
description: "VM 从一个 Azure 区域故障转移到另一个 Azure 区域后，可使用 Azure Site Recovery 反向保护虚拟机。 了解在再次故障转移前执行重新保护的步骤。"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: ruturajd
ms.openlocfilehash: 0004323fee44c1433cdd3c39fc1fa7dad965dbf7
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2017
---
# <a name="reprotect-azure-vms-back-to-the-primary-region"></a>在主要区域中重新保护 Azure VM



>[!NOTE]
>
> Azure 虚拟机 (VM) 的 Site Recovery 复制当前处于预览阶段。


将 VM 从一个 Azure 区域[故障转移](../site-recovery-failover.md)到另一个区域时，故障转移后的 VM 处于不受保护状态。 如果想要将这些虚拟机回复到主要区域，则首先需要开始复制 VM，然后再次执行故障转移。 无论故障转移方向如何，方式没有任何差别。 同样，启用 VM 复制后，故障转移之后或故障回复之后的重新保护没有差别。

为了解释重新保护过程，我们举例假设受保护 VM 的主站点位于东亚区域，恢复站点位于东南亚。 故障转移期间，会将 VM 故障转移到东南亚区域。 在故障回复之前，需要将 VM 从东南亚复制回到东亚。 本文介绍重新保护所要执行的步骤。

> [!WARNING]
> 如果已经完成迁移、已将虚拟机转移至另一资源组或已删除 Azure 虚拟机，则无法进行故障回复。

重新保护完成并且正在复制 VM 后，可在 VM 上启动故障转移，将其回复到东亚区域。

## <a name="prerequisites"></a>先决条件
1. VM 应已提交。
2. 目标站点（东亚）应该可用，并且应该能够访问/创建该区域中的新资源。

## <a name="reprotect"></a>重新保护

遵循以下步骤使用默认设置重新保护 VM。

1. 在“保管库” > “已复制项”中，右键单击已故障转移的 VM，并选择“重新保护”。 也可以单击该计算机，并从命令按钮中选择“重新保护”。

  ![重新保护](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. 确认已选择复制方向“东南亚到东亚”。

  ![重新保护](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotectblade.png)

3. 查看“资源组、网络、存储和可用性集”信息并单击“确定”。 如果有任何资源标记为“新”，则会在重新保护过程中创建这些资源。

这会触发一个使用最新数据植入目标站点种子的重新保护作业，完成该作业后，将在故障回复到东南亚之前复制增量数据。

### <a name="reprotect-customization"></a>重新保护自定义选项
如果想要在重新保护期间选择提取存储帐户或网络，可使用“重新保护”页上提供的自定义选项完成相关操作。

![自定义选项](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

可在重新保护期间自定义以下目标虚拟机属性。

![自定义](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|属性 |说明  |
|---------|---------|
|目标资源组     | 可选择更改要在其中创建虚拟机的目标资源组。 在重新保护过程中，目标虚拟机会被删除，因此可选择新资源组，以便故障转移后在其下创建 VM         |
|目标虚拟网络     | 重新保护期间，无法更改网络。 若要更改网络，请重做网络映射。         |
|目标存储     | 可更改故障转移后要在其中创建虚拟机的存储帐户。         |
|缓存存储     | 可指定复制期间要使用的缓存存储帐户。 如果遵从默认设置，则在不存在缓存存储帐户的情况下，会创建新帐户。         |
|可用性集     |如果东亚虚拟机是可用性集的一部分，则可为东南亚目标虚拟机选择可用性集。 默认设置会查找现有 SEA 可用性集并尝试使用它。 自定义期间，可指定全新的可用性集。         |


### <a name="what-happens-during-reprotect"></a>重新保护期间会发生什么情况？

和第一次启用保护后一样，使用默认设置时会创建以下项目。
1. 东亚区域中会创建一个缓存存储帐户。
2. 如果目标存储帐户（东南亚 VM 的原始存储帐户）不存在，则会创建一个新帐户。 名称为使用“asr”作为后缀的东亚虚拟机的存储帐户。
3. 如果目标可用性集不存在，并且默认设置检测到需要创建新的可用性集，则会在执行重新保护作业期间进行创建。 如果已自定义重新保护，则会使用选定的可用性集。


以下是触发重新保护作业时所执行步骤的列表。 这是目标端虚拟机存在时会发生的情况。

1. 所需项目会在重新保护过程中创建。 如果已存在，则会重复使用。
2. 目标端（东南亚）虚拟机首先关闭（如果正在运行）。
3. 目标端虚拟机的磁盘由 Azure Site Recovery 作为种子 blob 复制到容器中。
4. 随后会删除目标端虚拟机。
5. 当前源端（东亚）虚拟机使用种子 blob 进行复制。 这可确保仅复制增量。
6. 源磁盘和种子 blob 之间的主要更改会同步。 此操作需要一段时间才能完成。
7. 重新保护作业完成后，根据策略创建恢复点的增量复制会开始执行。

> [!NOTE]
> 无法在恢复计划级别进行保护。 仅可在每个 VM 级别重新保护。

重新保护成功后，虚拟机将进入受保护状态。

## <a name="next-steps"></a>后续步骤

虚拟机进入受保护状态后，可启动故障转移。 故障转移会关闭东亚 Azure 区域中的虚拟机，然后创建并启动东南亚区域中的虚拟机。 因此，应用程序存在较短的停机时间。 请选择应用程序可以容许停机的时间作为故障转移时间。 建议在启动故障转移前对虚拟机执行测试故障转移，确保它正常运行。

-   [启动虚拟机测试故障转移的步骤](../site-recovery-test-failover-to-azure.md)

-   [启动虚拟机故障转移的步骤](../site-recovery-failover.md)
