---
title: "使用 Azure Site Recovery 将 VMM 云中的 Hyper-V VM 复制到 Azure | Microsoft Docs"
description: "概述了如何使用 Azure Site Recovery 服务将 VMM 云中的 Hyper-V VM 复制到 Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.openlocfilehash: af68d21184c137b2b0f1bb4c1afb9bf507e8332d
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-site-recovery-in-the-azure-portal"></a>在 Azure 门户中使用 Site Recovery 将 VMM 云中的 Hyper-V 虚拟机复制到 Azure
> [!div class="op_single_selector"]
> * [Azure 门户](site-recovery-vmm-to-azure.md)
> * [Azure 经典](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell 资源管理器](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell 经典](site-recovery-deploy-with-powershell.md)


本文概述了需执行哪些步骤以在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 服务将 System Center Virtual Machine Manager (VMM) 云中托管的本地 Hyper-V 虚拟机 (VM) 复制到 Azure。

阅读本文后，请在底部发布评论，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## <a name="step-1-review-the-scenario-architecture"></a>步骤 1：查看方案体系结构

在开始部署前，请先查看方案体系结构，确保了解部署所需的全部组件。

转到[步骤 1：查看体系结构](vmm-to-azure-walkthrough-architecture.md)

## <a name="step-2-review-prerequisites-and-limitations"></a>步骤 2：查看先决条件和限制

确保了解部署先决条件和限制。

**Azure 先决条件**：必须有 Microsoft Azure 帐户、Azure 网络和存储帐户。
**本地 VMM 服务器和 Hyper-V 主机**：确保 VMM 服务器和 Hyper-V 主机符合条件且准备好用于 Site Recovery 部署。
**复制的 VM**：检查要复制的 VM 是否符合 Azure 要求。

转到[步骤 2：验证先决条件和限制](vmm-to-azure-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>步骤 3：计划容量

需确定所需的复制资源，才能执行完整部署。 有几款工具有助于执行此操作。 若要执行快速部署来测试环境，可以跳过这一步。

转到[步骤 3：计划容量](vmm-to-azure-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>步骤 4：计划网络

需要执行一些网络规划，确保可在部署方案时配置网络映射、Azure VM 将在故障转移后连接到 Azure 虚拟网络，以及已向其分配适当的 IP 地址。

转到[步骤 4：计划网络](vmm-to-azure-walkthrough-network.md)


## <a name="step-5-prepare-azure-resources"></a>步骤 5：准备 Azure 资源

设置 Azure 帐户、网络和存储。 可以在部署期间执行此操作，但建议在开始前这样做。

转到[步骤 5：准备 Azure 资源](vmm-to-azure-walkthrough-prepare-azure.md)

## <a name="step-6-prepare-vmm-and-hyper-v"></a>步骤 6：准备 VMM 和 Hyper-V

为 Site Recovery 部署准备本地 VMM 服务器和 Hyper-V 主机。

转到[步骤 6：准备本地服务器](vmm-to-azure-walkthrough-vmm-hyper-v.md)

## <a name="step-7-set-up-a-vault"></a>步骤 7：创建保管库

设置恢复服务保管库。 保管库包含配置设置，并且安排复制。

[步骤 7：创建保管库](vmm-to-azure-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>步骤 8：配置源和目标设置

设置源和目标复制位置。 将 VMM 服务器添加到保管库，并下载 Site Recovery 组件的安装文件。 在 VMM 服务器上运行 Azure Site Recovery 提供程序安装程序。 安装程序将在 VMM 服务器上安装该提供程序，并在保管库中注册该服务器。 在每台 Hyper-V 主机上安装 Microsoft 恢复服务代理。

转到[步骤 8：配置源和目标设置](vmm-to-azure-walkthrough-source-target.md)

## <a name="step-9-configure-network-mapping"></a>步骤 9：配置网络映射

将本地 VMM VM 网络映射到 Azure 虚拟网络。 故障转移后，将在映射到源 Hyper-V 所在的本地 VM 网络的 Azure 网络中创建 Azure VM。

转到[步骤 9：配置网络映射](vmm-to-azure-walkthrough-network-mapping.md)


## <a name="step-10-set-up-a-replication-policy"></a>步骤 10：设置复制策略

指定如何将本地 VM 复制到 Azure。

转到[步骤 10：设置复制策略](vmm-to-azure-walkthrough-replication.md)


## <a name="step-11-enable-replication-for-vms"></a>步骤 11：为 VM 启用复制

选择要复制的 VM。 为 VM 启用复制将触发到 Azure 的初始复制，之后将进行日常的增量复制。

转到[步骤 11：启用复制](vmm-to-azure-walkthrough-enable-replication.md)


## <a name="step-12-run-a-test-failover"></a>步骤 12：运行测试故障转移

运行测试故障转移，确保一切按预期运行。

转到[步骤 12：运行测试故障转移](vmm-to-azure-walkthrough-test-failover.md)


