---
title: "针对将 VMware 复制到 Azure 运行测试故障转移 | Microsoft Docs"
description: "总结了使用 Azure Site Recovery 服务运行复制到 Azure 的 VMware VM 的测试故障转移所需的步骤。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a640e139-3a09-4ad8-8283-8fa92544f4c6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 4be4cb8285d2e0e8b8520c289ef7668164c9fe50
ms.contentlocale: zh-cn
ms.lasthandoff: 06/29/2017


---
# <a name="step-12-run-a-test-failover-to-azure-for-vmware-vms"></a>步骤 12： 运行 VMware VM 到 Azure 的测试故障转移

本文介绍如何在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 服务，运行从本地 VMware 虚拟机到 Azure 的测试故障转移。

请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## <a name="before-you-start"></a>开始之前

在运行测试故障转移之前，我们建议验证 VM 属性，并进行任何所需的更改。 可以访问“复制项”中的 VM 属性。 “概要”边栏选项卡显示有关计算机设置和状态的信息。

## <a name="managed-disk-considerations"></a>托管磁盘注意事项

[托管磁盘](../storage/storage-managed-disks-overview.md)通过管理与 VM 磁盘关联的存储帐户简化了 Azure VM 的磁盘管理。 

- 当启用 VM 保护时，VM 数据将复制到存储帐户。 只有在发生故障转移时，才会创建托管磁盘并将其附加到 VM。
- 只能为使用 Resource Manager 模型部署的 VM 创建托管磁盘。  
- 启用此设置后，仅可以选择启用了“使用托管磁盘”的资源组中的可用性集。 使用托管磁盘的 VM 必须在可用性集中，其中“使用托管磁盘”设置为“是”。 如果没有为 VM 启用此设置，那么仅可以选择未启用托管磁盘的资源组中的可用性集。
- [详细了解](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set)托管磁盘和可用性集。
- 如果已使用存储服务加密对用于复制的存储帐户进行了加密，则无法在故障转移期间创建托管磁盘。 在这种情况下，要么不启用托管磁盘，要么禁用 VM 保护，然后重新启用它以使用未启用加密的存储帐户。 [了解详细信息](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption)。


## <a name="network-considerations"></a>网络注意事项

你可以为在故障转移后创建的 Azure VM 设置目标 IP 地址。

- 如果未提供地址，故障转移的计算机将使用 DHCP。
- 如果设置了在故障转移时不可用的地址，故障转移将不会正常工作。
- 如果地址可用于测试故障转移网络，则同一个目标 IP 地址可用于测试故障转移。
- 网络适配器数目根据你为目标虚拟机指定的大小来确定：

     - 如果源计算机上的网络适配器数小于或等于目标计算机大小允许的适配器数，则目标的适配器数将与源相同。
     - 如果源虚拟机的适配器数大于目标大小允许的数目，则将使用目标大小允许的最大数目。
     - 例如，如果源计算机有两个网络适配器，而目标计算机大小支持四个，则目标计算机将有两个适配器。 如果源计算机有两个适配器，但支持的目标大小只支持一个，则目标计算机只有一个适配器。     
   - 如果虚拟机有多个网络适配器，它们将全部连接到同一个网络。
   - 如果虚拟机有多个网络适配器，列表中显示的第一个适配器将成为 Azure 虚拟机中的*默认*网络适配器。
 - [详细了解](vmware-walkthrough-network.md) IP 寻址。



## <a name="view-and-modify-vm-settings"></a>查看并修改 VM 设置

在运行故障转移之前，建议你验证源计算机的属性。

1. 在“受保护的项”中，单击“复制的项”，然后单击 VM。
2. 在“复制的项”窗格中，你可以看到 VM 信息、运行状况状态和最新可用恢复点的摘要。 单击“属性”可查看更多详细信息。
3. 在“计算和网络”中，你可以：
    - 修改 Azure VM 名称。 名称必须满足 [Azure 要求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
    - 指定故障转移后[资源组](../virtual-machines/windows/infrastructure-resource-groups-guidelines.md)
    - 指定 Azure VM 的目标大小
    - 选择[可用性集](../virtual-machines/windows/infrastructure-availability-sets-guidelines.md)。
    - 指定是否使用[托管磁盘](#managed-disk-considerations)。 如果你想要将托管磁盘附加到迁移到 Azure 的计算机上，则选择“是”。
    - 查看或修改网络设置，包括在故障转移后 Azure VM 将位于其中的网络/子网，以及将分配给它的 IP 地址。
4. 在“磁盘”中，可以看到 VM 上有关操作系统和数据磁盘的信息。

## <a name="run-a-test-failover"></a>运行测试故障转移

完成全部设置后，运行测试故障转移，确保一切按预期运行。

- 如果要在故障转移后使用 RDP 连接到 Azure VM，请[准备连接](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。
 - 若要全面测试，需要在测试环境中复制 Active Directory 和 DNS。 [了解详细信息](site-recovery-active-directory.md#test-failover-considerations)。
 - 有关测试故障转移的完整信息，请参阅[本文](site-recovery-test-failover-to-azure.md)。
- 在开始之前，获取短片概述：


     >[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video4-Recovery-Plan-DR-Drill-and-Failover/player]


现在，运行故障转移：

1. 若要故障转移单个计算机，请在“设置”>“复制的项”中，单击“VM” > “+测试故障转移”图标。

    ![测试故障转移](./media/vmware-walkthrough-test-failover/test-failover.png)

2. 若要故障转移某个恢复计划，请在“设置” > “恢复计划”中，右键单击该计划 >“测试性故障转移”。 若要创建恢复计划，请[遵循这些说明](site-recovery-create-recovery-plans.md)。  

3. 在“测试故障转移”中，选择 Azure VM 在故障转移之后要连接到的 Azure 网络。

4. 单击“确定”开始故障转移。 若要跟踪进度，可以单击 VM 以打开其属性，或者在保管库名称 >“设置” > “作业” > “Site Recovery 作业”中选择“测试故障转移”作业。

5. 故障转移完成后，你还应该能够看到副本 Azure 计算机显示在 Azure 门户的“虚拟机”中。 应确保 VM 的大小适当、已连接到相应的网络，并且正在运行。

6. 如果已准备好故障转移后的连接，应该能够连接到 Azure VM。

7. 完成后，在恢复计划中单击“清理测试故障转移”。 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。 这将删除在执行测试故障转移期间创建的 VM。



## <a name="next-steps"></a>后续步骤

- [详细了解](site-recovery-failover.md)不同类型的故障转移，以及如何运行它们。
- 如果要迁移计算机而不执行复制和故障回复，请[阅读详细信息](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers)。
- [详细了解故障回复](site-recovery-failback-azure-to-vmware.md)，以便将 Azure VM 故障回复和复制回本地主站点。

