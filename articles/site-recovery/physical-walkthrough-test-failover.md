---
title: "运行测试故障转移以便使用 Azure Site Recovery 将物理服务器复制到 Azure | Microsoft Docs"
description: "总结了运行测试故障转移以便使用 Azure Site Recovery 服务将物理服务器复制到 Azure 的必要步骤。"
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
ms.openlocfilehash: e6ebab3e4d7deeefbab395b0a898fbf441d75b5d
ms.contentlocale: zh-cn
ms.lasthandoff: 06/29/2017


---
# <a name="step-11-run-a-test-failover-of-physical-servers-to-azure"></a>第 11 步：运行测试故障转移以便将物理服务器复制到 Azure

本文介绍了如何运行测试故障转移，以便在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 服务将本地物理服务器复制到 Azure。

请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## <a name="before-you-start"></a>开始之前

运行测试故障转移前，建议先验证服务器属性，并执行所需的任何更改。 可以在“复制的项”中查看 VM 属性。 “概要”边栏选项卡显示有关计算机设置和状态的信息。

## <a name="managed-disk-considerations"></a>托管磁盘注意事项

[托管磁盘](../storage/storage-managed-disks-overview.md)管理与 VM 磁盘关联的存储帐户，简化了 Azure VM 的磁盘管理工作。 

- 为服务器启用保护功能后，VM 数据复制到存储帐户中。 只有当故障转移发生时，才会创建托管磁盘，并将它附加到 VM。
- 只能为使用 Resource Manager 模型部署的 Azure VM 创建托管磁盘。  
- 启用此设置后，只能选择资源组中启用了“使用托管磁盘”的可用性集。 包含托管磁盘的 VM 必须位于“使用托管磁盘”设置为“是”的可用性集中。 如果 VM 尚未启用此设置，那么只能选择资源组中未启用“使用托管磁盘”的可用性集。
- [详细了解](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set)托管磁盘和可用性集。
- 如果已使用存储服务加密来加密用于复制的存储帐户，无法在故障转移期间创建托管磁盘。 在这种情况下，要么不要启用托管磁盘，要么为 VM 禁用保护功能，并重新启用为使用未启用加密的存储帐户。 [了解详细信息](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption)。


## <a name="network-considerations"></a>网络注意事项

可以为故障转移后创建的 Azure VM 设置目标 IP 地址。

- 如果未提供地址，故障转移的计算机将使用 DHCP。
- 如果设置了在故障转移时不可用的地址，故障转移将不会正常工作。
- 如果地址可用于测试故障转移网络，则同一个目标 IP 地址可用于测试故障转移。
- 网络适配器数目根据你为目标虚拟机指定的大小来确定：

     - 如果源计算机上的网络适配器数小于或等于目标计算机大小允许的适配器数，则目标的适配器数将与源相同。
     - 如果源虚拟机的适配器数大于目标大小允许的数目，则将使用目标大小允许的最大数目。
     - 例如，如果源计算机有两个网络适配器，而目标计算机大小支持四个，则目标计算机将有两个适配器。 如果源计算机有两个适配器，但支持的目标大小只支持一个，则目标计算机只有一个适配器。     
   - 如果虚拟机有多个网络适配器，它们将全部连接到同一个网络。
   - 如果虚拟机有多个网络适配器，列表中显示的第一个适配器将成为 Azure 虚拟机中的*默认*网络适配器。
 - [详细了解](vmware-walkthrough-network.md) IP 地址。



## <a name="view-and-modify-vm-settings"></a>查看并修改 VM 设置

建议在运行故障转移前先验证源服务器的属性。

1. 在“受保护的项”中，依次单击“复制的项”和计算机。
2. 在“复制的项”窗格中，可以概览计算机信息、运行状况和最新可用恢复点。 单击“属性”，查看详细信息。
3. 在“计算和网络”中，可以执行下列操作：
    - 修改 Azure VM 名称。 名称必须符合 [Azure 要求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。
    - 指定故障转移后的[资源组](../virtual-machines/windows/infrastructure-resource-groups-guidelines.md)
    - 指定 Azure VM 的目标大小
    - 选择[可用性集](../virtual-machines/windows/infrastructure-availability-sets-guidelines.md)。
    - 指定是否使用[托管磁盘](#managed-disk-considerations)。 若要将托管磁盘附加到迁移到 Azure 的计算机，请选择“是”。
    - 查看或修改网络设置，包括在运行故障转移后 Azure VM 所在的网络/子网，以及将分配给它的 IP 地址。
4. 在“磁盘”中，可以查看操作系统和 VM 上数据磁盘的相关信息。

## <a name="run-a-test-failover"></a>运行测试故障转移

完成全部设置后，运行测试故障转移，确保一切按预期运行。

- 若要在运行故障转移后使用 RDP 连接到 Azure VM，请[做好连接准备](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。
 - 若要全面测试，需要在测试环境中复制 Active Directory 和 DNS。 [了解详细信息](site-recovery-active-directory.md#test-failover-considerations)。
 - 有关测试故障转移的完整信息，请参阅[这篇文章](site-recovery-test-failover-to-azure.md)。
- 在开始之前，获取短片概述：

     
     >[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video4-Recovery-Plan-DR-Drill-and-Failover/player]

现在，运行故障转移：

1. 若要对一台计算机进行故障转移，请依次转到“设置” > “复制的项”，再依次单击“计算机”>“+测试故障转移”图标。

    ![测试故障转移](./media/physical-walkthrough-test-failover/test-failover.png)

2. 若要故障转移某个恢复计划，请在“设置” > “恢复计划”中，右键单击该计划 >“测试性故障转移”。 若要创建恢复计划，请[遵循这些说明](site-recovery-create-recovery-plans.md)。  

3. 在“测试故障转移”中，选择 Azure VM 在故障转移之后要连接到的 Azure 网络。

4. 单击“确定”开始故障转移。 若要跟踪进度，可以通过单击计算机打开其属性，也可以依次转到保管库名称 >“设置” > “作业” > “Site Recovery 作业”，单击“测试故障转移”作业。

5. 故障转移完成后，还应该能够在“Azure 门户”>“虚拟机”中看到副本 Azure VM。 应确保 VM 的大小适当、已连接到相应的网络，并且正在运行。

6. 如果已准备好故障转移后的连接，应该能够连接到 Azure VM。

### <a name="delete-test-failover-vms"></a>删除测试故障转移 VM

1. 完成后，单击恢复计划或计算机上的“清理测试故障转移”。
2. 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。
3. 此清理操作将删除在执行测试故障转移期间创建的 Azure VM。

## <a name="summary"></a>摘要

如果已成功完成测试故障转移，物理服务器将会进行复制（已检查过它们可以故障转移到 Azure）。 现在，可以根据组织要求来运行故障转移。 

请注意，暂无法从 Azure 故障回复到物理服务器。 必须故障回复到 VMware VM。 也就是说，必须有本地 VMware 基础结构，才能进行故障回复。 [详细了解](site-recovery-failback-azure-to-vmware.md)如何将 Azure VM 故障回复到 VMware。


## <a name="next-steps"></a>后续步骤

- 根据需要，[运行故障转移](site-recovery-failover.md)。

