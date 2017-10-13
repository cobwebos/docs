---
title: "使用 Azure Site Recovery 对辅助本地站点运行灾难恢复演练 | Microsoft 文档"
description: "了解使用 Azure Site Recovery 对辅助本地站点运行灾难恢复演练的相关信息"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 445878e2-6682-49ba-914d-4c6824ab08a6
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 8737c2a22bd729cfc15d5448e1ec0becef643fd5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="run-a-disaster-recovery-drill-for-hyper-v-vms-to-your-secondary-on-premises-site"></a>对辅助本地站点的 Hyper-V VM 运行灾难恢复演练

[Azure Site Recovery](site-recovery-overview.md) 服务有助于实施灾难恢复策略，因为它可以管理和协调本地计算机和 Azure 虚拟机 (VM) 的复制、故障转移和故障回复。

本教程将演示如何对辅助本地站点的 Hyper-V VM 运行灾难恢复演练。 必须在 System Center Virtual Machine Manager (VMM) 私有云中管理 Hyper-V VM。 演练如何在不丢失数据或不停机的情况下验证你的复制策略，且不会影响你的生产环境。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 准备测试故障转移的先决条件
> * 为单一计算机运行测试故障转移


## <a name="prerequisites"></a>先决条件

- 可以在辅助站点使用多个网络选项运行测试故障转移。 可以在没有网络或在现有网络下运行故障转移，或让 Site Recovery 自动创建测试网络。 
**如果想要使用现有生产网络进行故障转移测试：**
    - 执行测试故障转移时，应关闭主 VM。 否则，具有相同标识的两个 VM 将同时在同一网络中运行。 
    - 如果对测试虚拟机进行了更改，清理测试故障转移后这些更改将丢失。 这些更改不会复制回主虚拟机。
    - 测试生产网络会导致生产工作负荷出现停机。 灾难恢复演练进行期间，要求用户不要使用相关应用。 
- 副本测试网络无需与用于测试故障转移的 VMM 逻辑网络类型匹配。 但是，某些组合不起作用。 例如，如果副本使用 DHCP 和基于 VLAN 的隔离，则无法使用 Windows 网络虚拟化进行故障转移网络的测试，因为它需要 IP 地址池。 
- 我们建议不要将所选的网络用于网络映射、测试故障转移。


## <a name="run-a-test-failover-for-a-vm"></a>为 VM 运行测试故障转移

1. 在“复制的项”中，单击“虚拟机”>“测试故障转移”。
2. 在“测试故障转移”中，指定在测试故障转移后，测试 VM 如何连接到网络。 出于本教程的学习目的，我们建议让 Site Recovery 自动创建测试网络。 [了解详细信息](site-recovery-test-failover-vmm-to-vmm.md#network-options-in-site-recovery)。
3. 单击“确定”开始故障转移。 在“**作业**”选项卡上跟踪进度。
4. 故障转移完成后，验证测试 VM 是否成功启动。
5. 完成后，单击“清理测试故障转移”。 该操作将删除测试虚拟机以及测试故障转移期间创建的任何网络。
6. 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。 


## <a name="next-steps"></a>后续步骤

[运行生产故障转移](tutorial-vmm-to-vmm-failover-failback.md)






