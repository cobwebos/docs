---
title: 使用 Site Recovery 对复制到 Azure 的 Hyper-V VM 进行故障转移和故障回复 | Microsoft Docs
description: 了解如何使用 Azure Site Recovery 将 Hyper-V VM 故障转移到 Azure 以及向本地站点进行故障回复
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/8/2018
ms.author: raynew
ms.openlocfilehash: 7863feb29fbb04f643aa3b7e1984209f44cdbe9a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852891"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>对复制到 Azure 的 Hyper-V VM 进行故障转移和故障回复

本教程介绍如何将 Hyper-V VM 故障转移到 Azure。 故障转移后，可故障回复到本地站点（若可行）。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 验证 Hyper-V VM 属性以检查是否符合 Azure 要求
> * 运行到 Azure 的故障转移
> * 将 Azure VM 重新保护到本地站点
> * 从 Azure 故障回复到本地
> * 重新保护本地 VM，以再次复制到 Azure

此教程为系列教程中的第五个教程。 本教程假设你已完成先前教程中的任务。

1. [准备 Azure](tutorial-prepare-azure.md)
2. [准备本地 VMware](tutorial-prepare-on-premises-hyper-v.md)
3. 为 [Hyper-V VM](tutorial-hyper-v-to-azure.md) 或为[托管在 System Center VMM 云中的 Hyper-V VM](tutorial-hyper-v-vmm-to-azure.md) 设置灾难恢复
4. [运行灾难恢复演练](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>准备故障转移和故障回复

确保 VM 上无快照，以及本地 VM 在重新保护期间已关闭。 这有助于确保复制期间的数据一致性。 重新保护完成后，请勿打开 VM。 

故障转移和故障回复具有 4 个阶段：

1. **故障转移到 Azure**：将计算机从本地站点故障转移到 Azure。
2. **重新保护 Azure VM**：重新保护 Azure VM，使之开始复制回本地 Hyper-V VM。
3. **故障转移到本地**：若可用，在 Azure 到本地站点间运行故障转移。
4. **重新保护本地 VM**：对数据进行故障回复以后，对 VM 进行重新保护，使之开始复制到 Azure。

## <a name="verify-vm-properties"></a>验证虚拟机属性

验证 VM 属性，确保 VM 符合 [Azure 要求](hyper-v-azure-support-matrix.md#replicated-vms)。

1. 在“受保护的项”中，单击“复制的项”> VM 名称。

2. 在“复制的项”窗格中，查看 VM 信息、运行状况状态和最近可用恢复点。 单击“属性”可查看更多详细信息。
     - 在“计算和网络”中，可以修改 VM 设置和网络设置，包括 Azure VM 所在的网络/子网。 托管磁盘不支持从 Azure 到 Hyper-V 的故障回复。
   将在故障转移后定位，以及将为其分配 IP 地址。
    - 在“磁盘”中，可以看到关于 VM 上的操作系统和数据磁盘的信息。

## <a name="fail-over-to-azure"></a>故障转移到 Azure

1. 在“设置” > “复制的项”中，单击“VM”>“故障转移”。
2. 故障转移后，选择最新恢复点。 
3. 选择“在开始故障转移前关闭计算机”。 在触发故障转移之前，Site Recovery 会尝试关闭源 VM。 即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。
4. 验证故障转移后，单击“提交”。 这会删除所有可用的恢复点。

> [!WARNING]
> **请勿取消正在进行的故障转移**：在故障转移开始前，停止 VM 复制。 如果取消正在进行的故障转移，故障转移会停止，但 VM 将不再进行复制。

## <a name="reprotect-azure-vms"></a>重新保护 Azure VM

1. 在“AzureVMVault” > “已复制项”中，右键单击已故障转移的 VM，并选择“重新保护”。
2. 验证保护方向是否设置为“从 Azure 到本地”。
3. 重新保护期间，为保证数据一致性，本地 VM 会关闭。 重新保护完成后，请勿打开 VM。
4. 重新保护进程完成后，VM 开始从 Azure 复制到本地站点。



## <a name="fail-over-from-azure-and-reprotect-the-on-premises-vm"></a>从 Azure 故障转移和重新保护本地 VM

从 Azure 故障转移到本地站点，然后开始将 VM 从本地站点复制到 Azure。

1. 在“设置” > “复制的项”中，单击“VM”>“计划内故障转移”。
2. 在“确认计划故障转移”中，验证故障转移方向（从 Azure），并选择源和目标位置。
3. 选择“在故障转移前同步数据(仅同步增量更改)”。 此选项可最大程度减小 VM 停机时间，因为它在不关闭 VM 的情况下进行同步操作。
4. 启动故障转移。 可以在“**作业**”选项卡上跟踪故障转移进度。
5. 请在完成初始数据同步且准备好关闭 Azure VM 后，单击“作业”> 计划故障转移作业名称 >“完成故障转移”。 这会关闭 Azure VM，将最新更改传输到本地，并启动本地 VM。
6. 登录到本地 VM，检查它是否按预期方式可用。
7. 本地 VM 当前处于“提交挂起”状态。 单击“提交”。 这会删除 Azure VM 及其磁盘，并准备本地 VM 进行反向复制。
若要开始将本地 VM 复制到 Azure，请启用“反向复制”。 这会触发自关闭 Azure VM 以来发生的增量更改。  
