---
title: "使用 Azure Site Recovery 将 Hyper-V VM 复制到辅助 VMM 站点 | Microsoft Docs"
description: "概述了如何使用 Azure 门户将 Hyper-V VM 复制到辅助 VMM 站点。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 476ca82a-8f5c-4498-9dcf-e1011d60ed59
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: raynew
ms.openlocfilehash: b422dd2cf23426de2f154a553b38509082536309
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>将 VMM 云中的 Hyper-V 虚拟机复制到辅助 VMM 站点

> [!div class="op_single_selector"]
> * [Azure 门户](site-recovery-vmm-to-vmm.md)
> * [经典门户](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell - 资源管理器](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

本文概述了在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 将 System Center Virtual Machine Manager (VMM) 云中管理的本地 Hyper-V 虚拟机 (VM) 复制到辅助 VMM 站点所需执行的步骤。

阅读本文后，请在底部发布评论，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## <a name="step-1-review-the-scenario-architecture"></a>步骤 1：查看方案体系结构

在开始部署前，请先查看方案体系结构，并务必了解进行部署所需的全部组件。

转到[步骤 1：查看方案体系结构](vmm-to-vmm-walkthrough-architecture.md)。

## <a name="step-2-review-prerequisites-and-limitations"></a>步骤 2：查看先决条件和限制

确保了解部署先决条件和限制。

**Azure 先决条件**：需要一个 Microsoft Azure 订阅和 Azure 恢复服务保管库来安排和管理复制。
**本地 VMM 服务器和 Hyper-V 主机**：确保 VMM 服务器和 Hyper-V 主机符合条件并且已为 Site Recovery 部署做好了准备。

转到[步骤 2：验证先决条件和限制](vmm-to-vmm-walkthrough-prerequisites.md)。

## <a name="step-3-plan-networking"></a>步骤 3：规划网络

需要执行一些网络规划以确保在部署方案时可以在 VMM VM 网络之间配置网络映射。

转到[步骤 3：规划网络](vmm-to-vmm-walkthrough-network.md)。


## <a name="step-4-prepare-vmm-and-hyper-v"></a>步骤 4：准备 VMM 和 Hyper-V

为 Site Recovery 部署准备 VMM 服务器和 Hyper-V 主机。

转到[步骤 4：准备本地服务器](vmm-to-vmm-walkthrough-vmm-hyper-v.md)。

## <a name="step-5-set-up-a-vault"></a>步骤 5：设置保管库

设置一个恢复服务保管库。 保管库包含配置设置，并且安排复制。

[步骤 5：设置保管库](vmm-to-vmm-walkthrough-create-vault.md)。

## <a name="step-6-set-up-source-and-target-settings"></a>步骤 6：设置源和目标设置

设置源和目标复制 VMM 位置。 将 VMM 服务器添加到保管库，并下载 Site Recovery 组件的安装文件。 在 VMM 服务器上运行 Azure Site Recovery 提供程序安装程序。 安装程序将在 VMM 服务器上安装该提供程序，并在保管库中注册该服务器。 需要在每台 Hyper-V 主机上安装 Microsoft 恢复服务代理。

转到[步骤 6：设置源和目标设置](vmm-to-vmm-walkthrough-source-target.md)。

## <a name="step-7-configure-network-mapping"></a>步骤 7：配置网络映射

在源和目标位置中映射 VMM VM 网络。 故障转移后，将在映射到源 Hyper-V VM 所在的源 VM 网络的目标网络中创建 VM。

转到[步骤 7：配置网络映射](vmm-to-vmm-walkthrough-network-mapping.md)。


## <a name="step-8-set-up-a-replication-policy"></a>步骤 8：设置复制策略

指定如何在 VMM 位置之间复制 VM。

转到[步骤 8：设置复制策略](vmm-to-vmm-walkthrough-replication.md)。


## <a name="step-9-enable-replication-for-vms"></a>步骤 9：为 VM 启用复制

选择要复制的 VM。 为 VM 启用复制将触发到辅助站点的初始复制，之后将进行日常的增量复制。

转到[步骤 9：启用复制](vmm-to-vmm-walkthrough-enable-replication.md)。


## <a name="step-10-run-a-test-failover"></a>步骤 10：运行测试故障转移

运行测试故障转移，确保一切按预期运行。

转到[步骤 10：运行测试故障转移](vmm-to-vmm-walkthrough-test-failover.md)。
