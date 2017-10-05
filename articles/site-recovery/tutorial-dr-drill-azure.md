---
title: "使用 Azure Site Recovery 对 Azure 本地计算机运行灾难恢复演练 | Microsoft 文档"
description: "了解使用 Azure Site Recovery 对从 Azure 本地计算机运行灾难恢复演练的相关信息"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ddd17921-68f4-41c7-ba4c-b767d36f1733
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 15e4487217ec21bb33380422640cb19dfcbcee39
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

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
2. “复制的项”窗格中具有虚拟机信息、运行状况状态和最新可用恢复点的摘要。 单击“属性”可查看更多详细信息。
3. 在“计算和网络”中，可修改 Azure 名称、资源组、目标大小、[可用性集](../virtual-machines/windows/tutorial-availability-sets.md)和托管的磁盘设置
4. 可以查看和修改网络设置，包括在故障转移后 Azure VM 将位于其中的网络/子网，以及将分配给它的 IP 地址。
5. 在“磁盘”中，可以看到关于虚拟机上的操作系统和数据磁盘的信息。

## <a name="run-a-test-failover-for-a-single-vm"></a>为单一虚拟机运行测试故障转移

运行测试故障转移时需执行下列操作：

1. 运行必备项检查，确保故障转移所需的所有条件都已就绪。
2. 故障转移处理数据，以便创建 Azure VM。 如果选择最新恢复点，则根据该数据创建恢复点。
3. 使用上一步中处理的数据创建 Azure VM。

按如下所述运行测试故障转移：

1. 在“设置” > “复制的项”中，单击“虚拟机”>“+测试故障转移”。

2. 选择要用于故障转移的恢复点：
    - 最新处理：将虚拟机故障转移到由 Site Recovery 处理的最新恢复点。 将显示时间戳。 使用此选项时，无需费时处理数据，因此 RTO（恢复时间目标）会较低。
    - 最新的应用一致：此选项将所有虚拟机故障转移到最新的应用一致恢复点。 将显示时间戳。
    - 自定义：选择任何恢复点。
3. 在“测试故障转移”中，选择 Azure VM 在故障转移之后要连接到的目标 Azure 网络。
4. 单击“确定”开始故障转移。 可以通过单击虚拟机打开其属性来跟踪进度。 或者，可以在保管库名称 >“设置”**** > “作业”**** >
   “Site Recovery 作业”****中，单击“测试故障转移”****作业。
5. 故障转移完成后，副本 Azure VM 会显示在 Azure 门户 >“虚拟机”中。 请确保虚拟机的大小适当、已连接到正确的网络，并且正在运行。
6. 现在应该能够连接到 Azure 中复制的虚拟机。
7. 若要删除在测试故障转移期间创建的 Azure VM，请在恢复计划上单击“清理测试故障转移”。 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。

在某些情况下，故障转移需要大约 8-10 分钟的时间完成其他进程。 你可能注意到，VMware Linux 计算机、未启用 DHCP 服务的 VMware VM，以及未安装启动驱动程序（storvsc、vmbus、storflt、intelide、atapi）的 VMware VM 需要更长的测试故障转移时间。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为本地 VMware VM 运行故障转移和故障回复](tutorial-vmware-to-azure-failover-failback.md)。

