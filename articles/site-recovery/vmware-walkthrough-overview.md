---
title: "使用 Azure Site Recovery 将 VMware VM 复制到 Azure | Microsoft Docs"
description: "概述了如何将 VMware VM 上运行的工作负载复制到 Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: db6f5f95929503e82a529dba26b56af1edb0767f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-vmware-vms-to-azure-with-site-recovery"></a>使用 Site Recovery 将 VMware VM 复制到 Azure

本文概述了在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 服务将本地 VMware 虚拟机复制到 Azure 的必要步骤。


![部署过程](./media/vmware-walkthrough-overview/vmware-to-azure-process.png)

**图 1：部署过程摘要**

## <a name="step-1-review-architecture-and-prerequisites"></a>第 1 步：查看体系结构和先决条件

开始部署前，先查看方案体系结构，并务必了解部署所需的全部组件

转到[第 1 步：查看体系结构](vmware-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>第 2 步：查看先决条件

务必满足每个部署组件的先决条件：

- **Azure 先决条件**：必须有 Microsoft Azure 帐户、Azure 网络和存储帐户。
- **本地 Site Recovery 组件**：必须有运行本地 Site Recovery 组件的计算机。
- **本地 VMware 先决条件**：必须创建帐户，以便 Site Recovery 可以访问 VMware 服务器和 VM。
- **复制的 VM**：要复制的 VM 必须符合 Azure 要求，并且已安装 Mobility Service 组件。

转到[第 2 步：查看先决条件和限制](vmware-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>第 3 步：计划容量

必须确定所需的复制资源，才能执行完整部署。 有几款工具有助于执行此操作。 转到第 2 步。 若要执行快速部署来测试环境，可以跳过这一步。

转到[第 3 步：计划容量](vmware-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>第 4 步：计划网络

需要执行一些网络计划，以确保在故障转移发生后 Azure VM 可以连接到网络，并具有正确的 IP 地址。

转到[第 4 步：计划网络](vmware-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>第 5 步：准备 Azure 资源

开始前，设置 Azure 网络和存储。 可以在部署期间执行此操作，但建议在开始前这样做。

转到[第 5 步：准备 Azure 资源](vmware-walkthrough-prepare-azure.md)


## <a name="step-6-prepare-vmware"></a>第 6 步：准备 VMware

必须创建帐户，以便 Site Recovery 可用于执行以下操作：

- 访问 VMware 虚拟化服务器，以自动检测 VM。
- 访问 VM 以安装 Mobility Service。 对于要复制的每个 VM，必须先在其上安装 Mobility Service 代理，然后才能启用复制。

转到[第 6 步：准备 VMware](vmware-walkthrough-prepare-vmware.md)

## <a name="step-7-set-up-a-vault"></a>第 7 步：创建保管库

需要创建恢复服务保管库来安排和管理复制。 创建保管库时，指定要复制的内容，以及将内容复制到的目标位置。

转到[第 7 步：创建保管库](vmware-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>第 8 步：配置源和目标设置

设置用于复制的源和目标。 源设置包括运行统一安装程序来安装本地 Site Recovery 组件。

转到[第 8 步：设置源和目标](vmware-walkthrough-source-target.md)

## <a name="step-9-set-up-a-replication-policy"></a>第 9 步：创建复制策略

创建复制策略，以指定保管库中 VMware VM 的复制设置。

转到[第 9 步：创建复制策略](vmware-walkthrough-replication.md)

## <a name="step-10-install-the-mobility-service"></a>第 10 步：安装 Mobility Service

必须在要复制的每个 VM 上安装 Mobility Service。 可通过多种方法（推送或拉取安装）安装 Mobility Service。

转到[第 10 步：安装 Mobility Service](vmware-walkthrough-install-mobility.md)

## <a name="step-11-enable-replication"></a>第 11 步：启用复制

在 VM 上运行 Mobility Service 后，可以为它启用复制。 启用后，将进行 VM 初始复制。

转到[第 11 步：启用复制](vmware-walkthrough-enable-replication.md)

## <a name="step-12-run-a-test-failover"></a>第 12 步：运行测试故障转移

初始复制完成并运行增量复制后，可以运行测试故障转移，以确保一切都符合预期。

转到[第 12 步：运行测试故障转移](vmware-walkthrough-test-failover.md)
