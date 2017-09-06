---
title: "运行测试故障转移以将 Hyper-V（包含 System Center VMM）复制到 Azure | Microsoft Docs"
description: "总结了运行测试故障转移以使用 Azure Site Recovery 服务将 VMM 云中的 Hyper-V VM 复制到 Azure 的必要步骤。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 7b562a23-7ba7-48ee-905d-c22b4b5d6466
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/25/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 4688fc4bc74a9e0e04487cfbe965006070fd9a7b
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---

# <a name="step-11-run-a-test-failover-for-hyper-v-replication-with-vmm-to-azure"></a>步骤 11：运行测试故障转移以将 Hyper-V（包含 VMM）复制到 Azure

[启动 Hyper-V VM 复制后](vmm-to-azure-walkthrough-enable-replication.md)，根据本文运行测试故障转移，以在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 服务将 System Center Virtual Machine Manager (VMM) 云中托管的本地 Hyper-V 虚拟机 (VM) 复制到 Azure。

请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="before-you-start"></a>开始之前

在运行测试故障转移之前，我们建议你验证 VM 属性，并进行任何所需的更改。 可以访问“复制项”中的 VM 属性。 “概要”边栏选项卡显示有关计算机设置和状态的信息。

## <a name="managed-disk-considerations"></a>托管磁盘注意事项

[托管磁盘](../virtual-machines/windows/managed-disks-overview.md)通过管理与 VM 磁盘关联的存储帐户简化了 Azure VM 的磁盘管理。 

- 只有在发生到 Azure 的故障转移时，才会创建托管磁盘并将其附加到 VM。 启用保护后，本地 VM 中的数据将复制到存储帐户。
- 只能为使用 Resource Manager 部署模型部署的 VM 创建托管磁盘。
- 使用托管磁盘的虚拟机目前不支持从 Azure 故障回复到本地 Hyper-V 环境。 只有仅执行迁移时（故障转移到 Azure 且不进行故障回复）才应当将“使用托管磁盘”设置为“是”
- 启用此设置后，仅可以选择启用了“使用托管磁盘”的资源组中的可用性集。 包含托管磁盘的 VM 必须位于“使用托管磁盘”设置为“是”的可用性集中。 如果没有为 VM 启用此设置，那么仅可以选择未启用“使用托管磁盘”的资源组中的可用性集。 [了解详细信息](../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)。
- - 如果已使用存储服务加密加密用于复制的存储帐户，则无法在故障转移期间创建托管磁盘。 在这种情况下，要么不要启用托管磁盘，要么为 VM 禁用保护功能，并重新启用为使用未启用加密的存储帐户。 [了解详细信息](../virtual-machines/windows/managed-disks-overview.md#managed-disks-and-encryption)。

 
## <a name="network-considerations"></a>网络注意事项
    
- 你可以设置要为故障转移后的 Azure VM 使用的目标 IP 地址。 如果未提供地址，故障转移的计算机将使用 DHCP。 如果设置了无法用于故障转移的地址，故障转移会失败。 如果地址可用于测试故障转移网络，则同一个目标 IP 地址可用于测试故障转移。
- 网络适配器数目根据你为目标虚拟机指定的大小来确定，如下所述：
    - 如果源计算机上的网络适配器数小于或等于目标计算机大小允许的适配器数，则目标的适配器数将与源相同。
    - 如果源虚拟机的适配器数大于目标大小允许的数目，则使用目标大小允许的最大数目。
    - 例如，如果源计算机有两个网络适配器，而目标计算机大小支持四个，则目标计算机将有两个适配器。 如果源计算机有两个适配器，但支持的目标大小只支持一个，则目标计算机只有一个适配器。     
- 如果 VM 有多个网络适配器，它们将全部连接到同一个网络。
- 如果虚拟机有多个网络适配器，列表中显示的第一个适配器将成为 Azure 虚拟机中的*默认*网络适配器。


## <a name="view-and-manage-vm-settings"></a>查看和管理 VM 设置

在运行故障转移之前，建议你验证源计算机的属性。

1. 在“受保护的项”中，单击“复制的项”，然后单击此 VM。

    ![启用复制](./media/vmm-to-azure-walkthrough-test-failover/test-failover1.png)
2. 在“复制的项”窗格中，你可以看到 VM 信息、运行状况状态和最新可用恢复点的摘要。 单击“属性”可查看更多详细信息。

    ![启用复制](./media/vmm-to-azure-walkthrough-test-failover/test-failover2.png)
3. 在“计算和网络”中，你可以：
    - 修改 Azure VM 名称。 名称必须符合 [Azure 要求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
    - 指定故障转移后的[资源组]。
    - 指定 Azure VM 的目标大小
    - 选择[可用性集](../virtual-machines/windows/tutorial-availability-sets.md)。
    - 指定是否使用[托管磁盘](#managed-disk-considerations)。 若要将托管磁盘附加到迁移到 Azure 的计算机，请选择“是”。
    - 查看或修改网络设置，包括在故障转移后 Azure VM 将位于其中的网络/子网，以及将分配给它的 IP 地址。

    ![启用复制](./media/vmm-to-azure-walkthrough-test-failover/test-failover4.png)
4. 在“磁盘”中，可以看到关于 VM 上的操作系统和数据磁盘的信息。


## <a name="run-a-test-failover"></a>运行测试故障转移

现在，运行测试故障转移，确保一切如预期正常运行。

- 如果要在故障转移后使用 RDP 连接到 Azure VM，请[准备连接](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。
 - 若要全面测试，需要在测试环境中复制 Active Directory 和 DNS。 [了解详细信息](site-recovery-active-directory.md#test-failover-considerations)。
 - 有关测试故障转移的完整信息，请参阅[本文](site-recovery-test-failover-to-azure.md)。
 
 现在，运行故障转移：

1. 若要故障转移单个计算机，请在“复制的项”中，单击该 VM >“+测试故障转移”图标。
2. 若要故障转移某个恢复计划，请在“恢复计划”中，右键单击该计划 >“测试故障转移”。 若要创建恢复计划，请[遵循这些说明](site-recovery-create-recovery-plans.md)。
3. 在“测试故障转移”中，选择 Azure VM 在故障转移之后要连接到的 Azure 网络。
4. 单击“确定”开始故障转移。 若要跟踪进度，可以单击 VM 以打开其属性，或者在保管库名称 >“作业” > “Site Recovery 作业”中单击“测试故障转移”作业。
5. 故障转移完成后，还应该能够看到副本 Azure 计算机显示在 Azure 门户的“虚拟机”中。 应确保 VM 的大小适当、已连接到相应的网络，并且正在运行。
6. 如果已准备好故障转移后的连接，应该能够连接到 Azure VM。
7. 完成后，在恢复计划中单击“清理测试故障转移”。 在“**说明**”中，记录并保存与测试性故障转移相关联的任何观测结果。 此时会删除在测试性故障转移期间创建的虚拟机。



## <a name="next-steps"></a>后续步骤

- [详细了解](site-recovery-failover.md)不同类型的故障转移，以及如何运行它们。
- [了解故障回复](site-recovery-failback-from-azure-to-hyper-v.md)，以便将 Azure VM 故障回复和复制回本地主 VMM 云。


