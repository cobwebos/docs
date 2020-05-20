---
title: 在 Azure Site Recovery 中设置 Hyper-V VM 到 Azure 的故障转移
description: 了解如何使用 Azure Site Recovery 将 Hyper-V VM 故障转移到 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 03826abf6da94859c510f4c127dfce035aa79370
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "75498169"
---
# <a name="fail-over-hyper-v-vms-to-azure"></a>将 Hyper-V VM 故障转移到 Azure

本教程介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 将 Hyper-V VM 故障转移到 Azure。 故障转移后，可故障回复到本地站点（若可行）。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 验证 Hyper-V VM 属性以检查是否符合 Azure 要求。
> * 将特定 VM 故障转移到 Azure。


本教程为系列教程中的第五个教程。 它假设你已完成前面教程中的任务。    

1. [准备 Azure](tutorial-prepare-azure.md)
2. [准备本地 Hyper-V](tutorial-prepare-on-premises-hyper-v.md)
3. 为 [Hyper-V VM](tutorial-hyper-v-to-azure.md) 或为[托管在 System Center VMM 云中的 Hyper-V VM](tutorial-hyper-v-vmm-to-azure.md) 设置灾难恢复
4. [运行灾难恢复演练](tutorial-dr-drill-azure.md)

[了解](failover-failback-overview.md#types-of-failover)不同类型的故障转移。 如果要在恢复计划中对多个 VM 进行故障转移，请查看[本文](site-recovery-failover.md)。

## <a name="prepare-for-failover"></a>准备故障转移 
确保 VM 上无快照，并且本地 VM 在故障回复期间已关闭。 这有助于确保复制期间的数据一致性。 在故障回复期间不要打开本地 VM。 

故障转移和故障回复具有三个阶段：

1. **故障转移到 Azure**：将 Hyper-V VM 从本地站点故障转移到 Azure。
2. **故障回复到本地**：在本地站点可用时，将 Azure VM 故障转移到本地站点。 它开始将数据从 Azure 同步到本地，并在完成同步后，启动本地的 VM。  
3. **反向复制本地 VM**：故障回复到本地后，反向复制本地 VM，开始将其复制到 Azure。

## <a name="verify-vm-properties"></a>验证 VM 属性

在故障转移前验证 VM 属性，确保 VM 符合 [Azure 要求](hyper-v-azure-support-matrix.md#replicated-vms)。

在“受保护的项”  中，单击“复制的项”  >“虚拟机”。

1. “复制的项”窗格中具有 VM 信息、运行状况状态和最新可用恢复点的摘要  。 单击“属性”  可查看更多详细信息。

1. 在“计算和网络”  中，可修改 Azure 名称、资源组、目标大小、[可用性集](../virtual-machines/windows/tutorial-availability-sets.md)和托管的磁盘设置

1. 可查看和修改网络设置，包括在运行故障转移后 Azure VM 所在的网络/子网，以及将分配给它的 IP 地址。

1. 在“磁盘”  中，可以看到关于 VM 上的操作系统和数据磁盘的信息。

## <a name="fail-over-to-azure"></a>故障转移到 Azure

1. 在“设置” > “复制的项”中，单击“VM”>“故障转移”。
2. 在“故障转移”中，选择“最新”恢复点   。 
3. 选择“在开始故障转移前关闭计算机”  。 在触发故障转移之前，Site Recovery 会尝试关闭源 VM。 即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。 
4. 验证故障转移后，单击“提交”  。 这会删除所有可用的恢复点。

> [!WARNING]
> **请勿取消正在进行的故障转移**：如果取消正在进行中，故障转移将停止，但 VM 将不会再复制。

## <a name="connect-to-failed-over-vm"></a>连接到故障转移的 VM

1. 如果想在故障转移后通过使用远程桌面协议 (RDP) 和安全外壳 (SSH) 连接到 Azure VM，请[验证是否符合要求](failover-failback-overview.md#connect-to-azure-after-failover)。
2. 故障转移后，请转到该 VM，并通过与它建立[连接](../virtual-machines/windows/connect-logon.md)来进行验证。
3. 若要在故障转移后使用不同的恢复点，请使用“更改恢复点”。  在下一步骤中提交故障转移后，此选项不再可用。
4. 验证后，选择“提交”以确认故障转移后的 VM 恢复点  。
5. 提交后，系统会删除其他所有可用的恢复点。 完成此步骤，就完成了故障转移。

>[!TIP]
> 如果故障转移后遇到任何连接问题，请遵循[故障排除指南](site-recovery-failover-to-azure-troubleshoot.md)予以解决。


## <a name="next-steps"></a>后续步骤

故障转移后，重新保护 Azure VM，以便它们可以从 Azure 复制到本地。 重新保护 VM 并将其复制到本地站点后，如果已做好准备，请从 Azure 故障回复。
