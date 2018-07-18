---
title: 使用 Site Recovery 对复制到 Azure 的 VMware VM 和物理服务器进行故障转移和故障回复 | Microsoft Docs
description: 了解如何使用 Azure Site Recovery 将 VMware VM 和物理服务器故障转移到 Azure 以及如何故障回复到本地站点
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/06/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 1f7856edef3bb93300fce0ff00d9434400e239f8
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917034"
---
# <a name="fail-over-and-fail-back-vmware-vms-and-physical-servers-replicated-to-azure"></a>对复制到 Azure 的 VMware VM 和物理服务器进行故障转移和故障回复

本教程介绍如何将 VMware VM 故障转移到 Azure。 故障转移后，可故障回复到本地站点（若可行）。 本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 验证 VMware VM 属性以检查是否符合 Azure 要求
> * 运行到 Azure 的故障转移
> * 创建用于故障回复的进程服务器和主目标服务器
> * 将 Azure VM 重新保护到本地站点
> * 从 Azure 故障转移到本地
> * 重新保护本地 VM，以再次复制到 Azure

>[!NOTE]
>教程旨在向你展示方案的最简单部署路径。 它们尽可能使用默认选项，并且不显示所有可能的设置和路径。 若要详细了解测试性故障转移步骤，请阅读[操作方法指南](site-recovery-failover.md)。

此教程为系列教程中的第五个教程。 本教程假设你已完成先前教程中的任务。

1. [准备 Azure](tutorial-prepare-azure.md)
2. [准备本地 VMware](vmware-azure-tutorial-prepare-on-premises.md)
3. [设置灾难恢复](vmware-azure-tutorial.md)
4. [运行灾难恢复演练](tutorial-dr-drill-azure.md)
5. 除了上述步骤，也可以[查看灾难恢复方案的体系结构](vmware-azure-architecture.md)。

## <a name="failover-and-failback"></a>故障转移和故障回复

故障转移和故障回复有四个阶段：

1. **故障转移到 Azure**：将计算机从本地站点故障转移到 Azure。
2. 重新保护 Azure VM：重新保护 Azure VM，使之开始复制回本地 VMware VM。 重新保护期间，本地 VM 将关闭。 这有助于确保复制期间的数据一致性。
3. 故障转移到本地：运行故障转移以从 Azure 进行故障回复。
4. 重新保护本地 VM：对数据进行故障回复以后，对故障回复到的本地 VM 进行重新保护，使之开始复制到 Azure。

## <a name="verify-vm-properties"></a>验证 VM 属性

验证 VM 属性，确保 VM 符合 [Azure 要求](vmware-physical-azure-support-matrix.md#replicated-machines)。

1. 在“受保护的项”中，单击“复制的项”>“VM”。

2. “复制的项”窗格中具有 VM 信息、运行状况状态和最新可用恢复点的摘要。 单击“属性”可查看更多详细信息。

3. 在“计算和网络”中，可修改 Azure 名称、资源组、目标大小、[可用性集](../virtual-machines/windows/tutorial-availability-sets.md)和[托管的磁盘设置](#managed-disk-considerations)

4. 可查看和修改网络设置，包括在运行故障转移后 Azure VM 所在的网络/子网，以及将分配给它的 IP 地址。

5. 在“磁盘”中，可以看到关于 VM 上的操作系统和数据磁盘的信息。

## <a name="run-a-failover-to-azure"></a>运行到 Azure 的故障转移

1. 在“设置” > “复制的项”中，单击“VM”>“故障转移”。

2. 在“故障转移”中，选择要故障转移到的“恢复点”。 可以使用以下选项之一：
   - **最新**（默认选项）：此选项会首先处理发送到 Site Recovery 的所有数据。 它提供最低的 RPO（恢复点对象），因为故障转移后创建的 Azure VM 具有触发故障转移时复制到 Site Recovery 的所有数据。
   - **最新处理**：此选项将 VM 故障转移到由 Site Recovery 处理的最新恢复点。 此选项提供低 RTO（恢复时间目标），因为无需费时处理未经处理的数据。
   - **最新的应用一致**：此选项将 VM 故障转移到由 Site Recovery 处理的最新应用一致恢复点。
   - 自定义：指定恢复点。

3. 选择“在开始故障转移之前关闭计算机”，在触发故障转移之前尝试关闭源虚拟机。 即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。

在某些情况下，故障转移需要大约八到十分钟的时间完成其他进程。 你可能会注意到，使用的移动服务版本低于 9.8 的 VMware 虚拟机、物理服务器、VMware Linux 虚拟机、作为物理服务器保护的 Hyper-V 虚拟机、未启用 DHCP 服务的 VMware VM 和未安装 storvsc、vmbus、storflt、intelide、atapi 启动驱动程序的 VMware VM **需要更长的测试性故障转移时间**。

> [!WARNING]
> **请勿取消正在进行的故障转移**：在故障转移开始前，停止 VM 复制。
> 如果取消正在进行的故障转移，故障转移会停止，但 VM 将不再进行复制。

## <a name="connect-to-failed-over-virtual-machine-in-azure"></a>连接到 Azure 中已故障转移的虚拟机

1. 故障转移后，请转到虚拟机，并通过[连接](../virtual-machines/windows/connect-logon.md)到它来进行验证。
2. 验证后，请单击“提交”，以完成故障转移后虚拟机的恢复点。 提交后，系统会删除所有其他的可用恢复点。 这样就完成了故障转移活动。

>[!TIP]
> 使用“更改恢复点”，可以在不满意已进行故障转移的虚拟机的情况下，在故障转移后选择另一恢复点。 **提交**后，此选项将不再可用。

## <a name="preparing-for-reprotection-of-azure-vm"></a>准备对 Azure VM 进行重新保护

### <a name="create-a-process-server-in-azure"></a>在 Azure 中创建进程服务器

进程服务器从 Azure VM 检索数据，并近期发送到本地站点。 在进程服务器与受保护 VM 之间需要配置低延迟网络。

- 出于测试目的，如果有 Azure ExpressRoute 连接，可使用自动安装于配置服务器上的本地进程服务器（内置进程服务器）。
- 如果有 VPN 连接，或者在生产环境中运行故障回复，则必须将 Azure VM 设置为基于 Azure 的进程服务器才能进行故障回复。
- 若要在 Azure 中设置进程服务器，请按照[本文](vmware-azure-set-up-process-server-azure.md)说明进行操作。

### <a name="configure-the-master-target-server"></a>配置主目标服务器

主目标服务器接收和处理故障回复期间来自 Azure 的复制数据。 默认情况下，它在本地配置服务器上可用。 在本教程中，让我们使用默认的主目标服务器。

>[!NOTE]
>保护基于 Linux 的虚拟机需要创建单独的主目标服务器。 [单击此处](vmware-azure-install-linux-master-target.md)了解详细信息。

如果 VM 位于**由 vCenter server 托管的 ESXi 主机**上，主目标服务器必须有权限访问此 VM 的数据存储 (VMDK)，以便将复制的数据写入 VM 磁盘。 确保在具有读写访问权限的主目标主机上装载 VM 数据存储。

如果 VM 位于**非 vCenter 服务器所托管的 ESXi** 上，Site Recovery 服务在重新保护期间会创建新的 VM。 此 VM 的创建位置为创建主目标服务器所在的 ESX 主机。
此 VM 的硬盘必须位于可由运行主目标服务器的主机访问的数据存储中。

如果 VM **不使用 vCenter**，在重新保护此计算机前，应对其上运行主目标服务器的主机完成发现。 此规则同样适用于对物理服务器进行故障回复。 另一选择（如果本地 VM 存在）是在进行故障回复之前将其删除。 故障回复之后会在与主目标 ESX 主机相同的主机上创建新的 VM。 故障回复到备用位置时，会将数据恢复到与本地主目标服务器所用数据存储和 ESX 主机相同的数据存储和 ESX 主机。

不能在主目标服务器上使用存储 vMotion。 如果使用，故障回复将不起作用，因为磁盘不可使用它。 请从 vMotion 列表中排除主目标服务器。

>[!Warning]
>如果使用不同的主目标服务器来重新保护复制组，则服务器无法提供共同的时间点。

## <a name="reprotect-azure-vms"></a>重新保护 Azure VM

重新保护 Azure VM 会导致数据被复制到本地 VM。 必须完成此必要步骤，然后才能从 Azure 故障转移到本地 VM。 请遵照以下给定的说明执行重新保护。

1. 在“设置” > “复制的项”中，右键单击已故障转移的 VM，然后单击“重新保护”。
2. 在“重新保护”中，确保选择“Azure 到本地”。
3. 指定本地主目标服务器和进程服务器。
4. 在“数据存储”中，选择要将本地磁盘恢复到的主目标数据存储。 如果 VM 已删除，则会在此数据存储中创建新磁盘。 如果磁盘已存在，则会忽略此设置，但仍需指定一个值。
5. 选择主目标保留驱动器。 将自动选择故障回复策略。
6. 单击“确定”开始重新保护。 一个作业会开始将虚拟机从 Azure 复制到本地站点。 可以在“**作业**”选项卡上跟踪进度。
7. 在**已复制项**上 VM 的状态更改为“受保护”后，就可以将计算机故障转移到本地了。

> [!NOTE]
> Azure VM 可以恢复到现有的本地 VM 或备用位置。 若要了解详细信息，请阅读[此文](concepts-types-of-failback.md)。

## <a name="run-a-failover-from-azure-to-on-premises"></a>运行从 Azure 到本地的故障转移

若要复制回本地，需要使用故障回复策略。 创建用于复制到 Azure 的复制策略时，此策略自动创建：

- 此策略自动与配置服务器关联。
- 此策略无法修改。
- 策略值如下：
    - RPO 阈值 = 15 分钟
    - 恢复点保留期 = 24 小时
    - 应用一致性快照频率 = 60 分钟

运行故障转移，如下所示：

1. 在“复制的项”页中右键单击该计算机，然后单击“故障转移”。
2. 在“确认故障转移”中，验证故障转移方向为从 Azure 转移。
    ![failover-direction](media/vmware-azure-tutorial-failover-failback/failover-direction.PNG)
3. 选择要用于此故障转移的恢复点。 应用一致性恢复点在最新恢复点之前发生，这会导致丢失某些数据。

    >[!WARNING]
    >故障转移运行时，Site Recovery 会关闭 Azure VM，并启动本地 VM。 这会导致出现停机时间，因此请选择适当的时间。

4. 可以在“恢复服务保管库” > “监视和报告” > “Site Recovery 作业”上跟踪作业进度。
5. 完成故障转移后，请右键单击虚拟机，然后单击“提交”。 由此触发的作业会删除 Azure VM。
6. 验证 Azure VM 已按预期情况关闭。

## <a name="reprotect-on-premises-machines-to-azure"></a>将本地计算机重新保护到 Azure

数据现应返回到本地站点，但不会复制到 Azure。 可按如下操作开始再次复制到 Azure：

1. 在“保管库”>“受保护的项” >“复制的项”中，选择已故障回复的 VM，然后单击“重新保护”。
2. 选择会用于将复制数据发送到 Azure 的进程服务器，然后单击“确定”。

重新保护完成后，该 VM 将复制回 Azure，此时可按需运行故障转移。
