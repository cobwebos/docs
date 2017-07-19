---
title: "使用 Azure Site Recovery 将 Hyper-V VM 复制到 Azure | Microsoft Docs"
description: "介绍如何安排本地 Hyper-V VM 到 Azure 的复制、故障转移和恢复"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: efddd986-bc13-4a1d-932d-5484cdc7ad8d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: da10b213bc2543942b5ac77cf5c5d8547c00220c
ms.contentlocale: zh-cn
ms.lasthandoff: 06/23/2017

---

# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure"></a>将 Hyper-V 虚拟机（不使用 VMM）复制到 Azure 

> [!div class="op_single_selector"]
> * [Azure 门户](site-recovery-hyper-v-site-to-azure.md)
> * [Azure 经典](site-recovery-hyper-v-site-to-azure-classic.md)
> * [PowerShell - 资源管理器](site-recovery-deploy-with-powershell-resource-manager.md)
>
>

本文概述了在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 将本地 Hyper-V 虚拟机复制到 Azure 的必要步骤。 在此部署中，Hyper-V VM 不由 System Center Virtual Machine Manager (VMM) 托管。


阅读本文后，欢迎在页面底部发表任何看法，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上咨询技术问题。


## <a name="step-1-review-architecture-and-prerequisites"></a>第 1 步：查看体系结构和先决条件

开始部署前，先查看方案体系结构，并务必了解部署所需的全部组件

转到[第 1 步：查看体系结构](hyper-v-site-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>第 2 步：查看先决条件

务必满足每个部署组件的先决条件：

- **Azure 先决条件**：必须有 Microsoft Azure 帐户、Azure 网络和存储帐户。
- **本地 Hyper-V 先决条件**：务必为 Site Recovery 部署准备 Hyper-V 主机。
- **复制的 VM**：要复制的 VM 必须符合 Azure 要求。

转到[第 2 步：验证先决条件和限制](hyper-v-site-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>第 3 步：计划容量

必须确定所需的复制资源，才能执行完整部署。 有几款工具有助于执行此操作。 转到第 2 步。 若要执行快速部署来测试环境，可以跳过这一步。

转到[第 3 步：计划容量](hyper-v-site-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>第 4 步：计划网络

需要执行一些网络计划，以确保在故障转移发生后 Azure VM 可以连接到网络，并具有正确的 IP 地址。

转到[第 4 步：计划网络](hyper-v-site-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>第 5 步：准备 Azure 资源

开始前，设置 Azure 网络和存储。 可以在部署期间执行此操作，但建议在开始前这样做。

转到[第 5 步：准备 Azure 资源](hyper-v-site-walkthrough-prepare-azure.md)


## <a name="step-6-prepare-hyper-v"></a>第 6 步：准备 Hyper-V

请确保 HYPER-V 服务器符合 Site Recovery 部署要求。

转到[第 6 步：准备 Hyper-V](hyper-v-site-walkthrough-prepare-hyper-v.md)

## <a name="step-7-set-up-a-vault"></a>第 7 步：创建保管库

需要创建恢复服务保管库来安排和管理复制。 创建保管库时，指定要复制的内容，以及将内容复制到的目标位置。

转到[第 7 步：创建保管库](hyper-v-site-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>第 8 步：配置源和目标设置

设置用于复制的源和目标。 配置源设置包括将 Hyper-V 主机添加到 Hyper-V 站点、在每个 Hyper-V 主机上安装 Site Recovery 提供程序和恢复服务代理，以及在恢复服务保管库中注册站点。

转到[第 8 步：设置源和目标](hyper-v-site-walkthrough-source-target.md)

## <a name="step-9-set-up-a-replication-policy"></a>第 9 步：创建复制策略

创建复制策略，以指定保管库中 Hyper-V VM 的复制设置。

转到[第 9 步：创建复制策略](hyper-v-site-walkthrough-replication.md)


## <a name="step-10-enable-replication"></a>第 10 步：启用复制

创建复制策略并启用复制后，将进行 VM 初始复制。

转到[第 10 步：启用复制](hyper-v-site-walkthrough-enable-replication.md)

## <a name="step-11-run-a-test-failover"></a>第 11 步：运行测试故障转移

初始复制完成并运行增量复制后，可以运行测试故障转移，以确保一切都符合预期。

转到[第 11 步：运行测试故障转移](hyper-v-site-walkthrough-test-failover.md)

