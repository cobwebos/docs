---
title: 使用 Azure Site Recovery 运行从 Azure VMware 解决方案到 Azure 的灾难恢复演练
description: 了解如何使用 Azure Site Recovery 运行从 Azure VMware 解决方案私有云到 Azure 的灾难恢复演练。
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 46d5e1cf773a24b032874ee54021c780e1f361af
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91814179"
---
# <a name="run-a-disaster-recovery-drill-from-azure-vmware-solution-to-azure"></a>运行从 Azure VMware 解决方案到 Azure 的灾难恢复演练

本文介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 服务运行从 Azure VMware 解决方案 VM 到 Azure 的灾难恢复演练。 演练在不丢失数据的情况下验证你的复制策略。


本文是系列教程的第四篇文章，介绍如何为 Azure VMware 解决方案计算机设置到 Azure 的灾难恢复。

本教程介绍如何执行下列操作：

> [!div class="checklist"]

> * 设置隔离式网络以用于测试故障转移
> * 准备在故障转移后连接到 Azure VM
> * 为单一计算机运行测试故障转移。

> [!NOTE]
> 教程介绍了某个方案的最简单部署路径。 它们尽可能使用默认选项，并且不显示所有可能的设置和路径。 如果想要更详细地了解灾难恢复演练步骤，请[查看本文](site-recovery-test-failover-to-azure.md)。

## <a name="before-you-start"></a>开始之前

完成前一篇教程：

1. 确保已为到 Azure 的灾难恢复[设置 Azure](avs-tutorial-prepare-azure.md)。
2. 遵循[这些步骤](avs-tutorial-prepare-avs.md)准备本地 Azure VMware 解决方案部署，以便能够灾难恢复到 Azure。
3. [设置](avs-tutorial-replication.md) Azure VMware 解决方案 VM 的灾难恢复。
 

## <a name="verify-vm-properties"></a>验证 VM 属性

运行测试故障转移前，请验证 VM 属性，确保 [VMware VM](vmware-physical-azure-support-matrix.md#replicated-machines) 符合 Azure 要求。

1. 在“受保护的项”**** 中，单击“复制的项”****，然后单击 VM。
2. “复制的项”窗格中具有 VM 信息、运行状况状态和最新可用恢复点的摘要  。 单击“属性”  ，查看详细信息。
3. 在“计算和网络”中，可修改 Azure 名称、资源组、目标大小、可用性集和托管磁盘设置。
4. 可查看和修改网络设置，包括在运行故障转移后 Azure VM 所在的网络/子网，以及将分配给它的 IP 地址。
5. 在“磁盘”中，可以看到关于 VM 上的操作系统和数据磁盘的信息。

## <a name="create-a-network-for-test-failover"></a>创建用于测试故障转移的网络

对于测试故障转移，我们建议选择与每个 VM 的“计算和网络”设置中指定的生产恢复站点网络相互独立的网络。**** 默认情况下，创建 Azure 虚拟网络时，该网络独立于其他网络。 测试网络应模拟生产网络：

- 测试网络中的子网数目应与生产网络中的子网数目相同。 这些子网的名称应该相同。
- 测试网络应使用相同的 IP 地址类和子网范围。
- 使用“计算和网络”设置中为 DNS VM 指定的 IP 地址更新测试网络的 DNS。**** 有关更多详细信息，请参阅 [Active Directory 的测试性故障转移注意事项](site-recovery-active-directory.md#test-failover-considerations)。

## <a name="run-a-test-failover-for-a-single-vm"></a>为单个 VM 运行测试故障转移

运行测试故障转移时需执行下列操作：

1. 运行必备项检查，确保故障转移所需的所有条件都已就绪。
2. 故障转移处理数据，以便创建 Azure VM。 如果选择最新恢复点，则根据该数据创建恢复点。
3. 使用上一步中处理的数据创建 Azure VM。

按如下所述运行测试故障转移：

1. 在“设置” > “复制的项”中，单击“VM”>“+测试故障转移”。
2. 为本教程选择最近处理的恢复点。**** 这会将 VM 故障转移到最近的可用时间点上。 将显示时间戳。 使用此选项时，无需费时处理数据，因此 RTO（恢复时间目标）会较低。
3. 在“测试故障转移”中，选择 Azure VM 在故障转移之后要连接到的目标 Azure 网络。
4. 单击“确定”  开始故障转移。 可以通过单击 VM 打开其属性来跟踪进度。 或者，**可以在保管库名称** > **设置** > **作业** >
   **Site Recovery 作业** 中，单击“测试故障转移” 作业。
5. 故障转移完成后，副本 Azure VM 会显示在 Azure 门户 >“虚拟机”中。 请确保虚拟机的大小适当、已连接到正确的网络，并且正在运行。
6. 现在应该能够连接到 Azure 中复制的 VM。
7. 若要删除在测试故障转移期间创建的 Azure VM，请在 VM 上单击“清理测试故障转移”  。 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。 

在某些情况下，故障转移需要大约八到十分钟的时间完成其他进程。 你可能注意到，VMware Linux 计算机、未启用 DHCP 服务的 VMware VM，以及未安装启动驱动程序（storvsc、vmbus、storflt、intelide、atapi）的 VMware VM 需要更长的测试故障转移时间。

## <a name="connect-after-failover"></a>在故障转移后连接

如果要在故障转移后使用 RDP/SSH 连接到 Azure VM，请[准备连接](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。 如果故障转移后遇到任何连接问题，请遵循[故障排除](site-recovery-failover-to-azure-troubleshoot.md)指南予以解决。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [运行故障转移](avs-tutorial-failover.md)

