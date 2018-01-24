---
title: "使用 Azure Site Recovery 对 Azure 本地计算机运行灾难恢复演练 | Microsoft 文档"
description: "了解使用 Azure Site Recovery 对从 Azure 本地计算机运行灾难恢复演练的相关信息"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/31/2017
ms.author: raynew
ms.openlocfilehash: f7dc5e2df95a64685a8b70d25e839c371d4fc2de
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2018
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>运行 Azure 灾难恢复演练

本教程演示如何使用测试故障转移对 Azure 本地计算机运行灾难恢复演练。 演练在不丢失数据的情况下验证你的复制策略。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置隔离式网络以用于测试故障转移
> * 准备在故障转移后连接到 Azure VM
> * 为单一计算机运行测试故障转移

此教程为系列教程中的第四个教程。 本教程假设你已完成前面教程中的以下任务。

1. [准备 Azure](tutorial-prepare-azure.md)
2. [准备本地 VMware](tutorial-prepare-on-premises-vmware.md)
3. [设置灾难恢复](tutorial-vmware-to-azure.md)

## <a name="verify-vm-properties"></a>验证虚拟机属性

运行测试故障转移前，验证虚拟机属性，确保虚拟机符合 [Azure 要求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。

1. 在“受保护的项”中，单击“复制的项”>“虚拟机”。
2. “复制的项”窗格中具有 VM 信息、运行状况状态和最新可用恢复点的摘要。 单击“属性”可查看更多详细信息。
3. 在“计算和网络”中，可修改 Azure 名称、资源组、目标大小、[可用性集](../virtual-machines/windows/tutorial-availability-sets.md)和托管的磁盘设置
   
      >[!NOTE]
      当前不支持从使用托管磁盘的 Azure VM 故障回复到本地 Hyper-V 计算机。 如果计划将本地 VM 迁移到 Azure，而不对其进行故障回复，则只应为故障转移使用托管磁盘选项。
   
4. 可查看和修改网络设置，包括在运行故障转移后 Azure VM 所在的网络/子网，以及将分配给它的 IP 地址。
5. 在“磁盘”中，可以看到关于 VM 上的操作系统和数据磁盘的信息。

## <a name="run-a-test-failover-for-a-single-vm"></a>为单个 VM 运行测试故障转移

运行测试故障转移时需执行下列操作：

1. 运行必备项检查，确保故障转移所需的所有条件都已就绪。
2. 故障转移处理数据，以便创建 Azure VM。 如果选择最新恢复点，则根据该数据创建恢复点。
3. 使用上一步中处理的数据创建 Azure VM。

按如下所述运行测试故障转移：

1. 在“设置” > “复制的项”中，单击“虚拟机”>“+测试故障转移”。
2. 为本教程选择最近处理的恢复点。 这会将 VM 故障转移到最近的可用时间点上。 将显示时间戳。 使用此选项时，无需费时处理数据，因此 RTO（恢复时间目标）会较低。
3. 在“测试故障转移”中，选择 Azure VM 在故障转移之后要连接到的目标 Azure 网络。
4. 单击“确定”开始故障转移。 可以通过单击虚拟机打开其属性来跟踪进度。 或者，**可以在保管库名称** > **设置** > **作业** >
   **Site Recovery 作业** 中，单击“测试故障转移” 作业。
5. 故障转移完成后，副本 Azure VM 会显示在 Azure 门户 >“虚拟机”中。 请确保虚拟机的大小适当、已连接到正确的网络，并且正在运行。
6. 现在应该能够连接到 Azure 中复制的虚拟机。
7. 若要删除在测试故障转移期间创建的 Azure VM，请在 VM 上单击“清理测试故障转移”。 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。

在某些情况下，故障转移需要大约八到十分钟的时间完成其他进程。 你可能注意到，VMware Linux 计算机、未启用 DHCP 服务的 VMware VM，以及未安装启动驱动程序（storvsc、vmbus、storflt、intelide、atapi）的 VMware VM 需要更长的测试故障转移时间。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为本地 VMware VM 运行故障转移和故障回复](tutorial-vmware-to-azure-failover-failback.md)。
