---
title: 使用 Site Recovery 在灾难恢复到 Azure 期间对 VMware VM 和物理服务器进行故障转移和故障回复 | Microsoft Docs
description: 了解如何使用 Azure Site Recovery 在灾难恢复到 Azure 期间将 VMware VM 和物理服务器故障转移到 Azure 以及如何故障回复到本地站点
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9206e751fadab7a09c696fbe262aecdde002ae74
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357211"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>故障转移和故障回复 VMware VM

本文介绍如何将本地 VMware VM 故障转移到 [Azure Site Recovery](site-recovery-overview.md) 服务。 

本文是系列教程的第四篇文章，介绍如何为本地计算机设置到 Azure 的灾难恢复。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 验证 VMware VM 属性以检查是否符合 Azure 要求
> * 运行到 Azure 的故障转移


> [!NOTE]
> 教程中介绍了某个方案的最简单部署路径。 它们尽可能使用默认选项，并且不显示所有可能的设置和路径。 若要详细了解故障转移，请[查看此文](site-recovery-failover.md)。

## <a name="before-you-start"></a>开始之前
完成前一篇教程：

1. 确保已[设置 Azure](tutorial-prepare-azure.md)，以便能够将本地 VMware VM、Hyper-V VM 和物理机灾难恢复到 Azure。
2. 准备本地 [VMware](vmware-azure-tutorial-prepare-on-premises.md) 或 [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) 环境，以实现灾难恢复。 若要为物理服务器设置灾难恢复，请查看[支持矩阵](vmware-physical-secondary-support-matrix.md)。
3. 为 [VMware VM](vmware-azure-tutorial.md)、[Hyper-V VM](hyper-v-azure-tutorial.md) 或[物理机](physical-azure-disaster-recovery.md)设置灾难恢复。
4. 运行[灾难恢复演练](tutorial-dr-drill-azure.md)，以确保一切按预期方式进行。


## <a name="failover-and-failback"></a>故障转移和故障回复

故障转移和故障回复有四个阶段：

1. **故障转移到 Azure**：当本地主站点出现故障时，可将计算机故障转移到 Azure。 故障转移后，将会基于复制的数据创建 Azure VM。
2. **重新保护 Azure VM**：在 Azure 中重新保护 Azure VM，使之开始复制回到本地 VMware VM。 重新保护期间，为保证数据一致性，本地 VM 会关闭。
3. **故障转移到本地**：本地站点正常运行后，可运行故障转移以便从 Azure 故障回复。
4. **重新保护本地 VM**：故障回复数据后，重新保护故障回复到的本地 VM，使之开始复制到 Azure。

## <a name="verify-vm-properties"></a>验证 VM 属性

运行故障转移之前，请检查 VM 属性，确保其符合 [Azure 要求](vmware-physical-azure-support-matrix.md#replicated-machines)。

按如下所述检查属性：

1. 在“受保护的项”中，单击“复制的项”>“虚拟机”。

2. “复制的项”窗格中具有 VM 信息、运行状况状态和最新可用恢复点的摘要。 单击“属性”可查看更多详细信息。

3. 在“计算和网络”中，可以修改 Azure 名称、资源组、目标大小、[可用性集](../virtual-machines/windows/tutorial-availability-sets.md)和托管磁盘设置

4. 可查看和修改网络设置，包括在运行故障转移后 Azure VM 所在的网络/子网，以及将分配给它的 IP 地址。

5. 在“磁盘”中，可以看到关于 VM 上的操作系统和数据磁盘的信息。

## <a name="run-a-failover-to-azure"></a>运行到 Azure 的故障转移

1. 在“设置” > “复制的项”中，单击“VM”>“故障转移”。
2. 在“故障转移”中，选择要故障转移到的“恢复点”。 可以使用以下选项之一：
   - **最新**：此选项会首先处理发送到 Site Recovery 的所有数据。 它提供最低的 RPO（恢复点对象），因为故障转移后创建的 Azure VM 具有触发故障转移时复制到 Site Recovery 的所有数据。
   - **最新处理**：此选项将 VM 故障转移到由 Site Recovery 处理的最新恢复点。 此选项提供低 RTO（恢复时间目标），因为无需费时处理未经处理的数据。
   - **最新的应用一致**：此选项将 VM 故障转移到由 Site Recovery 处理的最新应用一致恢复点。
   - **自定义**：指定恢复点。

3. 选择“在开始故障转移之前关闭计算机”，在触发故障转移之前尝试关闭源 VM。 即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。

在某些情况下，故障转移需要大约八到十分钟的时间完成其他进程。 对于以下情况，你可能会发现测试故障转移会持续较长时间：
- VMware VM 运行的移动服务版本低于 9.8
- 物理服务器
- VMware Linux VM
- Hyper-V VM 作为物理服务器受到保护
- VMware VM 未启用 DHCP 服务
- VMware VM 不包含以下启动驱动程序：storvsc、vmbus、storflt、intelide、atapi。

> [!WARNING]
> **不会取消正在进行的故障转移**：在故障转移开始前，VM 复制已停止。 如果取消正在进行的故障转移，故障转移会停止，但 VM 将不再进行复制。

## <a name="connect-to-failed-over-vm"></a>连接到故障转移的 VM

1. 若要在故障转移后使用 RDP/SSH 连接到 Azure VM，请[检查这些要求](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。
2. 故障转移后，请转到该 VM，并通过与它建立[连接](../virtual-machines/windows/connect-logon.md)来进行验证。
3. 若要在故障转移后使用不同的恢复点，请使用“更改恢复点”。 在下一步骤中提交故障转移后，此选项不再可用。
4. 验证后，单击“提交”以确认故障转移后的 VM 恢复点。
5. 提交后，系统会删除其他所有可用的恢复点。 故障转移到此完成。

>[!TIP]
> 如果故障转移后遇到任何连接问题，请遵循此[故障排除指南](site-recovery-failover-to-azure-troubleshoot.md)予以解决。

## <a name="next-steps"></a>后续步骤

故障转移后，在本地重新保护 Azure VM。 重新保护 VM 并将其复制到本地站点后，如果已做好准备，请从 Azure 故障回复。

> [!div class="nextstepaction"]
> [重新保护 Azure VM](vmware-azure-reprotect.md)
> [从 Azure 故障回复](vmware-azure-failback.md) 
