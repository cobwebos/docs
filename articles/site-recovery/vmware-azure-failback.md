---
title: 利用 Azure Site Recovery 从 Azure 故障回复 VMware Vm/物理服务器
description: 了解如何在将 VMware VM 和物理服务器灾难恢复到 Azure 期间故障转移到 Azure 后，故障回复到本地站点。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: cd4cc90fb102d517a47ba458619e22b8921dd498
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495332"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>将 VMware Vm 故障回复到本地站点

本文介绍如何使用[Azure Site Recovery](site-recovery-overview.md)将本地 vm[故障转移](site-recovery-failover.md)到 Azure 后，将 Azure vm 故障回复到本地站点。 故障回复到本地后，启用复制，使本地 Vm 开始复制到 Azure。

## <a name="before-you-start"></a>开始之前

1. 了解[VMware 故障回复](failover-failback-overview.md#vmwarephysical-reprotectionfailback)。 
2. 请确保已查看并完成了[准备故障回复](vmware-azure-prepare-failback.md)所需的步骤，并且部署了所有必需的组件。 组件包括 Azure 中的进程服务器、本地主目标服务器和用于故障回复的 VPN 站点到站点连接（或 ExpressRoute 专用对等互连）。
3. 请确保已完成重新保护和故障回复的[要求](vmware-azure-reprotect.md#before-you-begin)，并且已启用 azure vm 的[重新保护](vmware-azure-reprotect.md#enable-reprotection)，以便将其从 azure 复制到本地站点。 Vm 必须处于复制状态才能进行故障回复。




## <a name="run-a-failover-to-fail-back"></a>运行故障转移以进行故障回复

1. 请确保 Azure Vm 重新保护并复制到本地站点。 
    - VM 至少需要一个恢复点才能进行故障回复。
    - 如果对恢复计划进行故障回复，则计划中的所有计算机应该至少有一个恢复点。
2. 在保管库中 > "**复制的项**"，选择 VM。 右键单击 VM > 非**计划的故障转移**。
3. 在“确认故障转移”中，确认故障转移方向为从 Azure 转移。
4. 选择要用于此故障转移的恢复点。
    - 建议使用**最新**的恢复点。 应用一致性点位于最新的时间点之后，导致某些数据丢失。
    - **最新版本**为崩溃一致的恢复点。
    - **最新**的 VM 会故障转移到其最新的可用时间点。 如果在恢复计划中有一个用于多 VM 一致性的复制组，则该组中的每个 VM 都将故障转移到其独立的最新时间点。
    - 如果使用应用一致的恢复点，则每个 VM 都将故障回复到其最新的可用点。 如果恢复计划具有复制组，则每个组都将恢复到其公共的可用恢复点。
5. 故障转移开始。 Site Recovery 关闭 Azure Vm。
6. 故障转移完成后，检查所有内容是否按预期运行。 检查 Azure Vm 是否已关闭。 
7. 验证所有内容后，右键单击 VM > "**提交**" 以完成故障转移过程。 Commit 将删除故障转移的 Azure VM。 

> [!NOTE]
> 对于 Windows Vm，Site Recovery 在故障转移过程中禁用 VMware 工具。 在 Windows VM 的故障回复期间，VMware 工具会再次启用。 




## <a name="reprotect-from-on-premises-to-azure"></a>从本地到 Azure 进行重新保护

提交故障回复后，将删除 Azure Vm。 VM 会回到本地站点，但不受保护。 若要开始再次将 Vm 复制到 Azure，请执行以下操作：

1. 在保管库中 > "**复制的项**"，选择 "故障回复 vm"，然后选择 "**重新保护**"。
2. 指定用于将数据发送回 Azure 的进程服务器。
3. 选择“确定”启动重新保护作业。

> [!NOTE]
> 本地 VM 启动后，最多需要15分钟的时间让代理重新注册到配置服务器。 在此期间，重新保护会失败并返回一条错误消息，指出未安装代理。 如果发生这种情况，请等待几分钟，然后重新保护。

## <a name="next-steps"></a>后续步骤

重新保护作业完成后，会将本地 VM 复制到 Azure。 根据需要，可以[运行另一个故障转移](site-recovery-failover.md)到 Azure。

