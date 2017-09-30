---
title: "使用 Site Recovery 对复制到 Azure 的 Hyper-V VM 进行故障转移和故障回复 | Microsoft Docs"
description: "了解如何使用 Azure Site Recovery 将 Hyper-V VM 故障转移到 Azure 以及向本地站点进行故障回复"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72065d01-ed0f-430e-b117-a5885cecd1db
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/15/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e1cc21661450a983c25b24fe2a6228e26ceecec6
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>对复制到 Azure 的 Hyper-V VM 进行故障转移和故障回复

[Azure Site Recovery](site-recovery-overview.md) 服务可管理和协调本地计算机和 Azure 虚拟机 (VM) 的复制、故障转移和故障回复。

本教程介绍如何将 Hyper-V VM 故障转移到 Azure。 故障转移后，可故障回复到本地站点（若可行）。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 将 Hyper-V VM 从本地故障转移到 Azure
> * 从 Azure 故障回复到本地，并再次复制到 Azure

## <a name="overview"></a>概述
故障转移和故障回复有 2 个阶段：

1. **故障转移到 Azure**：将计算机从本地站点故障转移到 Azure。
2. 从 Azure 故障回复到本地：运行从 Azure 到本地的计划内故障转移。 在计划内故障转移后，启用反向复制，再次从本地复制到 Azure。 


## <a name="fail-over-to-azure"></a>故障转移到 Azure

### <a name="failover-prerequisites"></a>故障转移先决条件

要完成故障转移：

- 确保已完成[灾难恢复演练](tutorial-dr-drill-azure.md)，检查所有内容是否都按预期工作。
- （可选）准备在故障转移前连接到 Azure VM。
- 在运行故障转移之前先验证 VM 属性。

#### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>准备在故障转移后连接到 Azure VM

如果想要在故障转移后使用 RDP 连接到 Azure VM，请执行以下操作：

##### <a name="azure-vms-running-windows"></a>运行 Windows 的 Azure VM

1. 若要通过 Internet 访问 Azure VM，请在故障转移之前在本地 VM 上启用 RDP。 请确保为“公共”配置文件添加了 TCP 和 UDP 规则，并确保在“Windows 防火墙” > “允许的应用”中针对所有配置文件允许 RDP。
2. 若要通过站点到站点 VPN 进行访问，请在本地计算机上启用 RDP。 应在“Windows 防火墙” -> “允许的应用和功能”中针对“域和专用”网络允许 RDP。 检查操作系统的 SAN 策略是否已设置为 OnlineAll。 [了解详细信息](https://support.microsoft.com/kb/3031135)。 触发故障转移时，VM 上不应存在待处理的 Windows 更新。 如果存在，则在更新完成之前无法登录到虚拟机。 
3. 在 Windows Azure VM 上执行故障转移后，请选中“启动诊断”查看 VM 的屏幕截图。 如果无法连接，请检查 VM 是否正在运行，并查看这些[疑难解答提示](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。


##### <a name="azure-vms-running-linux"></a>运行 Linux 的 Azure VM

1. 在本地计算机上执行故障转移之前，请确保安全外壳服务设置为在系统启动时自动启动。 确保防火墙规则允许 SSH 连接。
2. 在 Azure VM 上执行故障转移后，允许已故障转移的 VM 及其所连接 Azure 子网上的网络安全组规则与 SSH 端口建立传入连接。  为 VM [添加公共 IP 地址](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine)。 可选中“启动诊断”查看 VM 的屏幕截图。


#### <a name="verify-vm-properties"></a>验证 VM 属性

验证 VM 属性，确保 VM 符合 [Azure 要求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。

1. 在“受保护的项”中，单击“复制的项”>“VM”。
2. “复制的项”窗格中具有 VM 信息、运行状况状态和最新可用恢复点的摘要。 单击“属性”可查看更多详细信息。
3. 在“计算和网络”中，可修改 Azure 名称、资源组、目标大小、[可用性集](../virtual-machines/windows/tutorial-availability-sets.md)和[托管的磁盘设置](#managed-disk-considerations)
4. 可查看和修改网络设置，包括在运行故障转移后 Azure VM 所在的网络/子网，以及将分配给它的 IP 地址。
5. 在“磁盘”中，可以看到关于 VM 上的操作系统和数据磁盘的信息。


### <a name="run-a-failover-from-on-premises-to-azure"></a>运行从本地到 Azure 的故障转移

可以为 Hyper-V VM 运行常规或计划内故障转移

- 使用常规故障转移处理意外中断。 运行此故障转移时，Site Recovery 会创建一个 Azure VM，并将其启动。 针对特定恢复点运行故障转移。 可能发生数据丢失，具体取决于所用的恢复点。
- 可在维护或预期中断过程中使用计划内故障转移。 此选项保证无数据丢失。 触发计划内故障转移时，关闭源 VM。 同步未同步的数据，并触发故障转移。

本过程介绍如何运行常规故障转移。


1. 在“设置” > “复制的项”中，单击“VM”>“故障转移”。
2. 在“故障转移”中，选择要故障转移到的“恢复点”。 可以使用以下选项之一：
    - **最新**（默认选项）：此选项会首先处理发送到 Site Recovery 的所有数据。 它提供最低的 RPO（恢复点对象），因为故障转移后创建的 Azure VM 具有触发故障转移时复制到 Site Recovery 的所有数据。
    - **最新处理**：此选项将 VM 故障转移到由 Site Recovery 处理的最新恢复点。 此选项提供低 RTO（恢复时间目标），因为无需费时处理未经处理的数据。
    - **最新的应用一致**：此选项将 VM 故障转移到由 Site Recovery 处理的最新应用一致恢复点。 
    - 自定义：指定恢复点。
3. 如果要将 System Center VMM 云中的 Hyper-V VM 故障转移到 Azure，并且为云启用了数据加密，请在“加密密钥”中，选择在 VMM 服务器上安装提供程序期间启用数据加密时颁发的证书。
4. 如果希望 Site Recovery 在触发故障转移之前尝试关闭源虚拟机，请选择“在开始故障转移之前关闭计算机”。 在触发故障转移前，Site Recovery 还会尝试同步尚未发送到 Azure 的本地数据。 请注意：即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。
5. 如果已准备好连接到 Azure VM，请进行连接，以在故障转移后对其进行验证。
6. 验证后，提交故障转移。 这会删除所有可用的恢复点。

> [!WARNING]
> **请勿取消正在进行的故障转移**：在故障转移开始前，停止 VM 复制。 如果取消正在进行的故障转移，故障转移会停止，但 VM 将不再进行复制。  


## <a name="fail-back-from-azure-to-on-premises"></a>从 Azure 故障回复到本地

### <a name="prerequisites-for-failback"></a>故障回复的先决条件

若要完成故障回复，请确保主站点 VMM 服务器/Hyper-V 服务器已连接到 Site Recovery。


### <a name="run-a-failback-and-reprotect-on-premises-vms"></a>运行故障回复并重新保护本地 VM

从 Azure 故障转移到本地站点，然后开始将 VM 从本地站点复制到 Azure。

1. 在“设置” > “复制的项”中，单击“VM”>“计划内故障转移”。
2. 在“确认计划故障转移”中，验证故障转移方向（从 Azure），并选择源和目标位置。 
3. 在“数据同步”中，指定所需同步方式：
    - **在故障转移之前同步数据（仅同步增量更改）**- 此选项可最大程度地减少 VM 停机时间，因为它可在不关闭 VM 的情况下执行同步。 下面是它的作用：
        1. 生成 Azure VM 快照，并将快照复制到本地 Hyper-V 主机。 VM 在 Azure 中不间断运行。
        2. 关闭 Azure VM，确保其不会发生任何新的更改。 将最终的增量更改集传输到本地服务器，并启动本地 VM。
    - 仅在故障转移期间同步数据（完整下载）- 如果已在 Azure 中长时间运行，则使用此选项。 此选项速度更快，因为预计会有多个磁盘更改，并且无需费时进行校验和计算。 此选项执行磁盘下载。 如果已删除本地 VM，此选项也很有帮助。
4. 如果要将 System Center VMM 云中的 Hyper-V VM 故障转移到 Azure，并且为云启用了数据加密，请在“加密密钥”中，选择在 VMM 服务器上安装提供程序期间启用数据加密时颁发的证书。
5. 启动故障转移。 可以在“**作业**”选项卡上跟踪故障转移进度。
6. 如果选择了在故障转移之前同步数据，请在完成初始数据同步且准备好关闭 Azure VM 后，单击“作业”> 计划故障转移作业名称 >“完成故障转移”。 这会关闭 Azure VM，将最新更改传输到本地，并启动本地 VM。
7. 登录到本地 VM，检查它是否按预期方式可用。
8. 本地 VM 当前处于“提交挂起”状态。 单击“提交”提交故障转移。 这会删除 Azure VM 及其磁盘，并准备本地 VM 进行反向复制。
9. 若要开始将本地 VM 复制到 Azure，请启用“反向复制”。


> [!NOTE]
> 反向复制仅复制自 Azure VM 关闭以来发生的更改，并且仅发送增量更改。


