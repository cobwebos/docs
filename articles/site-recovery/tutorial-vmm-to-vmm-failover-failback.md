---
title: "使用 Site Recovery 对复制到辅助数据中心的 Hyper-V VM 进行故障转移和故障回复 | Microsoft Docs"
description: "了解如何使用 Azure Site Recovery 将 Hyper-V VM 故障转移到辅助本地站点以及故障回复到主站点"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 44a662fa-2e7a-4996-86df-fdd6d6f5dedf
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/16/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 8f139070de99c4249207d048d445e86dd41e9060
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>对复制到辅助本地站点的 Hyper-V VM 进行故障转移和故障回复

[Azure Site Recovery](site-recovery-overview.md) 服务可管理和协调本地计算机和 Azure 虚拟机 (VM) 的复制、故障转移和故障回复。

本教程介绍如何将 System Center Virtual Machine Manager (VMM) 云中托管的 Hyper-V VM 故障转移到辅助 VMM 站点。 故障转移后，可故障回复到本地站点（若可行）。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 将 Hyper-V VM 从主 VMM 云故障转移到辅助 VMM 云
> * 从辅助站点重新保护到主站点，并进行故障回复
> * （可选）再次开始从主站点复制到辅助站点

## <a name="overview"></a>概述

故障转移和故障回复具有三个阶段：

1. **故障转移到辅助站点**：将计算机从主站点故障转移到辅助站点。
2. **从辅助站点故障回复**：将 VM 从辅助站点复制到主站点，并运行计划的故障转移以进行故障回复。
3. 在计划的故障转移之后，可以选择再次开始从主站点复制到辅助站点。


## <a name="fail-over-to-a-secondary-site"></a>故障转移到辅助站点

### <a name="failover-prerequisites"></a>故障转移先决条件

确保已完成[灾难恢复演练](tutorial-dr-drill-secondary.md)，检查所有内容是否都按预期工作。


### <a name="run-a-failover-from-primary-to-secondary"></a>运行从主站点到辅助站点的故障转移

可以为 Hyper-V VM 运行常规或计划的故障转移。

- 使用常规故障转移处理意外中断。 当运行此故障转移时，Site Recovery 会在辅助站点中创建一个 VM，并将其启动。 针对特定恢复点运行故障转移。 可能发生数据丢失，具体取决于所用的恢复点。
- 可在维护或预期中断过程中使用计划内故障转移。 此选项保证无数据丢失。 触发计划内故障转移时，关闭源 VM。 同步未同步的数据，并触发故障转移。 
- 
本过程介绍如何运行常规故障转移。


1. 在“设置” > “复制的项”中，单击“VM”>“故障转移”。
2. 在“故障转移”中，选择要故障转移到的“恢复点”。 可以使用以下选项之一：
    - **最新**（默认选项）：此选项会首先处理发送到 Site Recovery 的所有数据。 它提供最低的 RPO（恢复点对象），因为故障转移后创建的副本 VM 具有触发故障转移时复制到 Site Recovery 的所有数据。
    - **最新处理**：此选项将 VM 故障转移到由 Site Recovery 处理的最新恢复点。 此选项提供低 RTO（恢复时间目标），因为无需费时处理未经处理的数据。
    - **最新的应用一致**：此选项将 VM 故障转移到由 Site Recovery 处理的最新应用一致恢复点。 
3. 此方案与加密密钥无关。
4. 如果希望 Site Recovery 在触发故障转移之前尝试关闭源 VM，请选择“在开始故障转移前关闭计算机”。 在触发故障转移前，Site Recovery 还会尝试同步尚未发送到辅助站点的本地数据。 请注意：即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。
5. 你现在应能够在辅助 VMM 云中看到 VM。
6. 验证 VM 后，“提交”故障转移。 这会删除所有可用的恢复点。

> [!WARNING]
> **请勿取消正在进行的故障转移**：在故障转移开始前，停止 VM 复制。 如果取消正在进行的故障转移，故障转移会停止，但 VM 将不再进行复制。  


## <a name="reprotect-and-fail-back-from-secondary-to-primary"></a>从辅助站点重新保护并故障回复到主站点

### <a name="prerequisites-for-failback"></a>故障回复的先决条件

若要完成故障回复，请确保主和辅助 VMM 服务器已连接到 Site Recovery。


### <a name="reprotect-and-fail-back"></a>重新保护和故障回复

开始从辅助站点复制到主站点，并故障回复到主站点。 VM 主站点中再次运行之后，可以将它们再次复制到辅助站点。  

1. 在“设置” > “复制的项”中，单击 VM 并启用“反向复制”。 VM 开始复制回主站点。
2. 单击 VM >“计划的故障转移”。
3. 在“确认计划的故障转移”中，确认故障转移方向（从复制 VMM 云），并选择源和目标位置。 
4. 在“数据同步”中，指定所需同步方式：
    - **在故障转移之前同步数据（仅同步增量更改）**- 此选项可最大程度地减少 VM 停机时间，因为它可在不关闭 VM 的情况下执行同步。 下面是它的作用：
        - 拍摄副本 VM 的快照，并将快照复制到主 Hyper-V 主机。 副本 VM 会保持运行。
        - 关闭副本 VM，使其中不会发生任何新的更改。 最后一组增量更改会传输到主站点，并且主站点中的 VM 会启动。
    - **仅在故障转移期间同步数据(完整下载)** - 如果已在辅助站点中长时间运行，则使用此选项。 此选项速度更快，因为预计会有多个磁盘更改，并且无需费时进行校验和计算。 此选项执行磁盘下载。 如果已删除主 VM，此选项也很有帮助。
5. 此方案与加密密钥无关。
6. 启动故障转移。 可以在“**作业**”选项卡上跟踪故障转移进度。
7. 如果选择了在故障转移之前同步数据，请在完成初始数据同步且已准备好关闭辅助站点中的副本 VM 后，单击“作业”> 计划的故障转移作业名称 >“完成故障转移”。 这会关闭辅助 VM，将最新更改传输到主站点，并启动主虚拟机。
8. 在主 VMM 云中，检查 VM 是否可用。
9. 主 VM 现在处于“提交挂起”状态。 单击“提交”提交故障转移。
10. 如果要再次开始将主 VM 复制回辅助站点，请启用“反向复制”。


> [!NOTE]
> 反向复制仅复制自副本 VM 关闭以来发生的更改，并且仅发送增量更改。


