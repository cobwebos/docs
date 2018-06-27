---
title: 使用 Site Recovery 对复制到 Azure 的 Hyper-V VM 进行故障转移和故障回复 | Microsoft Docs
description: 了解如何使用 Azure Site Recovery 将 Hyper-V VM 故障转移到 Azure 以及向本地站点进行故障回复
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 06/20/2018
ms.author: raynew
ms.openlocfilehash: 6a34187a87c6ecda461357a1c2fc8747ddf4b056
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294286"
---
# <a name="failover-and-failback-hyper-v-vms-replicated-to-azure"></a>对复制到 Azure 的 Hyper-V VM 进行故障转移和故障回复

本教程介绍如何将 Hyper-V VM 故障转移到 Azure。 故障转移后，可故障回复到本地站点（如果可用）。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 验证 Hyper-V VM 属性以检查是否符合 Azure 要求
> * 运行到 Azure 的故障转移
> * 从 Azure 故障回复到本地
> * 反向复制本地 VM，使之开始再次复制到 Azure

本教程为系列教程中的第五个教程。 它假设你已完成前面教程中的任务。    

1. [准备 Azure](tutorial-prepare-azure.md)
2. [准备本地 Hyper-V](tutorial-prepare-on-premises-hyper-v.md)
3. 为 [Hyper-V VM](tutorial-hyper-v-to-azure.md) 或为[托管在 System Center VMM 云中的 Hyper-V VM](tutorial-hyper-v-vmm-to-azure.md) 设置灾难恢复
4. [运行灾难恢复演练](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>准备故障转移和故障回复

确保 VM 上无快照，并且本地 VM 在故障回复期间已关闭。 这有助于确保复制期间的数据一致性。 在故障回复期间不要打开本地 VM。 

故障转移和故障回复具有三个阶段：

1. **故障转移到 Azure**：将 Hyper-V VM 从本地站点故障转移到 Azure。
2. **故障回复到本地**：在本地站点可用时，将 Azure VM 故障转移到本地站点。 这开始将 VM 复制回本地 Hyper-V VM。 初始数据同步后，将 Azure VM 故障转移到本地站点。  
3. **反向复制本地 VM**：对数据进行故障回复以后，反向复制本地 VM，使之开始复制到 Azure。

## <a name="verify-vm-properties"></a>验证虚拟机属性

在故障转移前验证 VM 属性，确保 VM 符合 [Azure 要求](hyper-v-azure-support-matrix.md#replicated-vms)。

1. 在“受保护的项”中，单击“复制的项”> VM 名称。

2. 在“复制的项”窗格中，查看 VM 信息、运行状况状态和最近可用恢复点。 单击“属性”可查看更多详细信息。
     - 在“计算和网络”中可修改 VM 设置和网络设置，包括 Azure VM 故障转移后将位于的网络/子网，以及将分配给它的 IP 地址。 托管磁盘不支持从 Azure 到 Hyper-V 的故障回复。
      - 在“磁盘”中，可以看到关于 VM 上的操作系统和数据磁盘的信息。

## <a name="failover-to-azure"></a>故障转移到 Azure

1. 在“设置” > “复制的项”中，单击“VM”>“故障转移”。
2. 故障转移后，选择最新恢复点。 
3. 选择“在开始故障转移前关闭计算机”。 在触发故障转移之前，Site Recovery 会尝试关闭源 VM。 即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。
4. 验证故障转移后，单击“提交”。 这会删除所有可用的恢复点。

> [!WARNING]
> **请勿取消正在进行的故障转移**：如果取消正在进行中，故障转移将停止，但 VM 将不会再复制。

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>将 Azure VM 故障回复到本地并反向复制本地 VM

故障回复操作基本上是从 Azure 故障转移到本地站点，在反向复制中它会开始将 VM 从本地站点复制到 Azure。

1. 在“设置” > “复制的项”中，单击“VM”>“计划内故障转移”。
2. 在“确认计划故障转移”中，验证故障转移方向（从 Azure），并选择源和目标位置。
3. 选择“在故障转移前同步数据(仅同步增量更改)”。 此选项可最大程度减小 VM 停机时间，因为它在不关闭 VM 的情况下进行同步操作。
4. 启动故障转移。 可以在“**作业**”选项卡上跟踪故障转移进度。
5. 请在完成初始数据同步且准备好关闭 Azure VM 后，单击“作业”> 计划故障转移作业名称 >“完成故障转移”。 这会关闭 Azure VM，将最新更改传输到本地，并启动本地 VM。
6. 登录到本地 VM，检查它是否按预期方式可用。
7. 本地 VM 当前处于“提交挂起”状态。 单击“提交”。 这会删除 Azure VM 及其磁盘，并准备要进行反向复制的本地 VM。
若要开始将本地 VM 复制到 Azure，请启用“反向复制”。 这会触发复制自关闭 Azure VM 以来发生的增量更改。  
