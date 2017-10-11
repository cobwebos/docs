---
title: "运行测试故障转移以使用 Azure Site Recovery 将 Hyper-V VM 复制到辅助站点 | Microsoft Docs"
description: "介绍如何运行测试故障转移以使用 Azure Site Recovery 将 Hyper-V VM 复制到辅助 System Center VMM 站点。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a3fd11ce-65a1-4308-8435-45cf954353ef
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 23d235d326273e7ec59feee6588a39f685401e52
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="step-10-run-a-test-failover-for-hyper-v-replication-to-a-secondary-site"></a>步骤 10：运行测试故障转移以将 Hyper-V 复制到辅助站点


使用 [Azure Site Recovery](site-recovery-overview.md) 实现 Hyper-V 虚拟机 (VM) 的复制后，请使用本文运行测试故障转移。 测试故障转移可验证复制是否有效，且不会影响生产环境。 


阅读本文后，请在底部发布评论，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## <a name="before-you-start"></a>开始之前

- 触发测试故障转移后，可指定测试测试副本 VM 将连接的网络。 [深入了解](site-recovery-test-failover-vmm-to-vmm.md#network-options-in-site-recovery)网络选项。
- 建议不要选择网络映射期间所选的网络。
- 本文中的说明介绍如何故障转移单个 VM。 如果要同时故障转移多个 VM，请参阅[创建恢复计划](site-recovery-create-recovery-plans.md)。
- 参阅[测试故障转移的限制](site-recovery-test-failover-vmm-to-vmm.md#things-to-note)。
- 如果 IP 地址可用于测试故障转移网络，则在测试故障转移期间指定给 VM 的 IP 地址与在计划或非计划故障转移时 VM 收到的 IP 地址相同。 如果此 IP 地址在测试故障转移网络中不可用，那么 VM 会收到可在测试故障转移网络中使用的其他 IP 地址。
- 如果要在生产网络中执行测试故障转移，以便对端到端网络连接计算机进行完整验证，请注意：
    - 执行测试故障转移时，应关闭主 VM。 否则，具有相同标识的两个 VM 将同时在同一网络中运行。 
    - 如果对测试 VM 进行了更改，清理测试故障转移后这些更改将丢失。 这些更改不会复制回主 VM。
    - 测试生产网络会导致生产工作负荷出现停机。 灾难恢复演练进行期间，要求用户不要使用应用。  


## <a name="run-a-test-failover-for-a-vm"></a>为 VM 运行测试故障转移

1. 若要故障转移单个 VM，请在“复制的项”中，单击“VM”>“测试故障转移”。
2. 在“测试故障转移”中，指定在测试故障转移后，测试 VM 如何连接到网络。 
3. 单击“确定”开始故障转移。 在“**作业**”选项卡上跟踪进度。
5. 故障转移完成后，验证测试 VM 是否成功启动。
6. 完成后，在恢复计划上单击“清理测试故障转移”。
7. 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。 此步骤会删除在测试故障转移期间创建的虚拟机和网络。


## <a name="next-steps"></a>后续步骤

测试部署后，深入了解[故障转移](site-recovery-failover.md)的其他类型。
