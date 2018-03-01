---
title: "使用 Site Recovery 对复制到 Azure 的物理服务器进行故障转移和故障回复 | Microsoft Docs"
description: "了解如何使用 Azure Site Recovery 将物理服务器故障转移到 Azure 以及如何故障回复到本地站点"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 02/22/2018
ms.author: raynew
ms.openlocfilehash: bbad2a0ea1a58834eaf32e0d3286f6e8a794d364
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>对复制到 Azure 的物理服务器进行故障转移和故障回复

本教程介绍如何将物理服务器故障转移到 Azure。 故障转移后，将服务器故障回复到本地站点（如果可用）。 

## <a name="preparing-for-failover-and-failback"></a>准备执行故障转移和故障回复

使用 Site Recovery 复制到 Azure 的物理服务器只能作为 VMware VM 故障回复。 必须有 VMware 基础结构，才能进行故障回复。 

故障转移和故障回复具有 4 个阶段：

1. **故障转移到 Azure**：将计算机从本地站点故障转移到 Azure。
2. **重新保护 Azure VM**：重新保护 Azure VM，使之开始复制回本地 VMware VM。
3. 故障转移到本地：运行故障转移以从 Azure 进行故障回复。
4. **重新保护本地 VM**：对数据进行故障回复以后，对故障回复到的本地 VMware VM 进行重新保护，使之开始复制到 Azure。

## <a name="verify-server-properties"></a>验证服务器属性

验证服务器属性，确保其符合 Azure VM 的 [Azure 要求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。

1. 在“受保护的项”中，单击“复制的项”，并选择计算机。

2. “复制的项”窗格中具有计算机信息、运行状况状态和最新可用恢复点的摘要。 单击“属性”可查看更多详细信息。
3. 在“计算和网络”中，可修改 Azure 名称、资源组、目标大小、[可用性集](../virtual-machines/windows/tutorial-availability-sets.md)和[托管的磁盘设置](#managed-disk-considerations)
4. 可查看和修改网络设置，包括在运行故障转移后 Azure VM 所在的网络/子网，以及将分配给它的 IP 地址。
5. 在“磁盘”中，可以看到有关计算机操作系统和数据磁盘的信息。

## <a name="run-a-failover-to-azure"></a>运行到 Azure 的故障转移

1. 在“设置” > “复制的项”中，单击计算机 >“故障转移”。
2. 在“故障转移”中，选择要故障转移到的“恢复点”。 可以使用以下选项之一：
   - **最新**（默认选项）：此选项会首先处理发送到 Site Recovery 的所有数据。 它提供最低的 RPO（恢复点对象），因为故障转移后创建的 Azure VM 具有触发故障转移时复制到 Site Recovery 的所有数据。
   - **最新处理**：此选项将计算机故障转移到由 Site Recovery 处理的最新恢复点。 此选项提供低 RTO（恢复时间目标），因为无需费时处理未经处理的数据。
   - **最新的应用一致**：此选项将计算机故障转移到由 Site Recovery 处理的最新应用一致恢复点。
   - 自定义：指定恢复点。

3. 如果希望 Site Recovery 在触发故障转移之前尝试关闭源计算机，请选择“在开始故障转移前关闭计算机”。 即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。
4. 如果已准备好连接到 Azure VM，请进行连接，以在故障转移后对其进行验证。
5. 验证后，提交故障转移。 这会删除所有可用的恢复点。

> [!WARNING]
> 不会取消正在进行的故障转移。 在故障转移开始之前，将停止计算机复制。 如果取消故障转移，它会停止，但计算机不会再次复制。
> 对于物理服务器，其他故障转移处理可能需要大约八到十分钟时间才能完成。 

## <a name="create-a-process-server-in-azure"></a>在 Azure 中创建进程服务器

进程服务器从 Azure VM 检索数据，并近期发送到本地站点。 在进程服务器与受保护计算机之间需要配置低延迟网络。

- 出于测试目的，如果具有 Azure ExpressRoute 连接，可使用自动安装于配置服务器上的本地进程服务器。
- 如果具有 VPN 连接，或者在生产环境中运行故障回复，为进行故障回复则必须将 Azure VM 设置为基于 Azure 的进程服务器。
- 按照[本文](site-recovery-vmware-setup-azure-ps-resource-manager.md)中的说明在 Azure 中设置进程服务器。

## <a name="configure-the-master-target-server"></a>配置主目标服务器

默认情况下，主目标服务器接收故障回复数据。 它在本地配置服务器上运行。

- 如果故障回复到的 VMware VM 位于由 VMware vCenter Server 托管的 ESXi 主机上，则主目标服务器必须有权访问此 VM 的数据存储 (VMDK)，以便将复制的数据写入 VM 磁盘。 确保在具有读写访问权限的主目标主机上装载 VM 数据存储。
- 如果 VM 位于非 vCenter Server 托管的 ESXi 主机上，Site Recovery 服务在重新保护期间会创建新的 VM。 此 VM 的创建位置为创建主目标 VM 所在的 ESX 主机上。 此 VM 的硬盘必须位于可由运行主目标服务器的主机访问的数据存储中。
- 对于故障回复的物理计算机，在重新保护此计算机前，应对运行主目标服务器的主机完成发现。
- 另一选择（如果故障回复的本地 VM 已存在）是在进行故障回复之前将其删除。 故障回复之后会在与主目标 ESX 主机相同的主机上创建新的 VM。 故障回复到备用位置时，会将数据恢复到与本地主目标服务器所用数据存储和 ESX 主机相同的数据存储和 ESX 主机。
- 不能在主目标服务器上使用存储 vMotion。 如果使用，故障回复将不起作用，因为磁盘不可使用它。 请从 vMotion 列表中排除主目标服务器。

## <a name="reprotect-azure-vms"></a>重新保护 Azure VM

此过程假设本地 VM 不可用，你需要重新保护到另一位置。

1. 在“设置”>“复制的项”中，右键单击已故障转移的 VM，然后单击“重新保护”。
2. 在“重新保护”中，确保选择“Azure 到本地”。
3. 指定本地主目标服务器和进程服务器。

4. 在“数据存储”中，选择要将本地磁盘恢复到的主目标数据存储。 本地 VM 已被删除且你需要创建新磁盘时，请使用此选项。 如果磁盘已存在，请忽略此设置，但你仍然需要指定一个值。
5. 选择主目标保留驱动器。 将自动选择故障回复策略。
6. 单击“确定”开始重新保护。 一个作业会开始将虚拟机从 Azure 复制到本地站点。 可以在“**作业**”选项卡上跟踪进度。

> [!NOTE]
> 如果要将 Azure VM 恢复到现有本地 VM，请使用读/写访问权限将本地虚拟机的数据存储装载在主目标服务器的 ESXi 主机上。


## <a name="run-a-failover-from-azure-to-on-premises"></a>运行从 Azure 到本地的故障转移

若要复制回本地，需要使用故障回复策略。 创建用于复制到 Azure 的复制策略时，此策略自动创建：

- 此策略自动与配置服务器关联。
- 此策略无法修改。
- 策略值如下：
    - RPO 阈值 = 15 分钟
    - 恢复点保留期 = 24 小时
    - 应用一致性快照频率 = 60 分钟

运行故障转移，如下所示：

1. 在“复制的项”页中右键单击该计算机，然后单击“非计划的故障转移”。
2. 在“确认故障转移”中，验证故障转移方向为从 Azure 转移。

3. 选择要用于此故障转移的恢复点。 应用一致性恢复点在最新恢复点之前发生，这会导致丢失某些数据。 故障转移运行时，Site Recovery 会关闭 Azure VM，并启动本地 VM。 这会导致出现停机时间，因此请选择适当的时间。
4. 右键单击该计算机，然后单击“提交”。 由此触发的作业会删除 Azure VM。
5. 验证 Azure VM 已按预期情况关闭。


## <a name="reprotect-on-premises-machines-to-azure"></a>将本地计算机重新保护到 Azure

数据现应返回到本地站点，但不会复制到 Azure。 可按如下操作开始再次复制到 Azure：

1. 选择此保管库，单击“设置”>“复制的项”，再选择已故障回复的 VM，然后单击“重新保护”。
2. 选择用于将复制数据发送到 Azure 的进程服务器，然后单击“确定”。

重新保护完成后，该 VM 将复制回 Azure，此时可按需运行故障转移。

