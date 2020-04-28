---
title: 使用 Azure Site Recovery 从 Azure 故障回复 VMware VM/物理服务器
description: 了解如何在将 VMware VM 和物理服务器灾难恢复到 Azure 期间故障转移到 Azure 后，故障回复到本地站点。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: cd4cc90fb102d517a47ba458619e22b8921dd498
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "75495332"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>将 VMware VM 故障回复到本地站点

本文介绍如何使用 [Azure Site Recovery](site-recovery-failover.md)将 Azure VM 故障回复到本地站点，然后将本地 VM [故障转移](site-recovery-overview.md)到 Azure。 故障回复到本地后启用复制，使本地 VM 开始复制到 Azure。

## <a name="before-you-start"></a>开始之前

1. 了解 [VMware 故障回复](failover-failback-overview.md#vmwarephysical-reprotectionfailback)。 
2. 确保已查看并完成[准备故障回复](vmware-azure-prepare-failback.md)的步骤，并已部署全部所需的组件。 组件包括 Azure 中的进程服务器、本地主目标服务器，以及用于故障回复的 VPN 站点到站点连接（或 ExpressRoute 专用对等互连）。
3. 确保符合重新保护和故障回复的[要求](vmware-azure-reprotect.md#before-you-begin)，并已[启用 Azure VM 的重新保护](vmware-azure-reprotect.md#enable-reprotection)，以便从 Azure 复制到本地站点。 VM 必须处于已复制状态才能故障回复。




## <a name="run-a-failover-to-fail-back"></a>运行故障转移以进行故障回复

1. 确保 Azure VM 已重新受保护，并正在复制到本地站点。 
    - VM 至少需有一个恢复点才能故障回复。
    - 如果对恢复计划进行故障回复，该计划中的所有计算机应至少有一个恢复点。
2. 在保管库中 >“复制的项”，选择 VM  。 右键单击该 VM 并选择“计划内故障转移”  。
3. 在“确认故障转移”中，确认故障转移方向为从 Azure 转移  。
4. 选择要用于此故障转移的恢复点。
    - 建议使用“最新”恢复点  。 应用一致性点会在最新的时间点之后，并会导致丢失部分数据。
    - “最新”是崩溃一致性恢复点  。
    - 使用“最新”时，VM 将故障转移到其最新可用时间点。  如果恢复计划中存在多 VM 一致性的复制组，该组中的每个 VM 将故障转移到其独立的最新时间点。
    - 如果使用应用一致性恢复点，每个 VM 将故障回复到其最新可用时间点。 如果恢复计划包含复制组，每个组将恢复到其公共的可用恢复点。
5. 故障转移开始。 Site Recovery 关闭 Azure VM。
6. 故障转移完成后，检查是否一切都符合预期。 检查 Azure VM 是否已关闭。 
7. 进行全面的验证后，右键单击 VM 并选择“提交”，以完成故障转移过程。  “提交”操作会删除已故障转移的 Azure VM。 

> [!NOTE]
> 对于 Windows VM，Site Recovery 在故障转移期间会禁用 VMware 工具。 在故障回复 Windows VM 期间，会再次启用 VMware 工具。 




## <a name="reprotect-from-on-premises-to-azure"></a>从本地到 Azure 进行重新保护

提交故障回复后，将删除 Azure VM。 VM 将返回到本地站点，但不受保护。 可按如下所述开始将 VM 复制到 Azure：

1. 在保管库中选择“复制的项”，选择已故障回复的 VM，然后选择“重新保护”。  
2. 指定用于将数据发回到 Azure 的进程服务器。
3. 选择“确定”启动重新保护作业。 

> [!NOTE]
> 本地 VM 启动后，最多需要花费 15 分钟让代理重新注册到配置服务器。 在此期间，重新保护会失败并返回一条错误消息，指出未安装代理。 如果发生这种情况，请等待几分钟，然后再重新保护。

## <a name="next-steps"></a>后续步骤

重新保护作业完成后，本地 VM 将复制到 Azure。 可根据需要[再次运行到 Azure 的故障转移](site-recovery-failover.md)。

