---
title: "使用 Azure Site Recovery 将物理本地服务器复制到 Azure | Microsoft Docs"
description: "概述了如何使用 Azure Site Recovery 服务将本地 Windows/Linux 物理服务器上运行的工作负载复制到 Azure。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 20122f01-929a-4675-b85b-a9b99d2618bc
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 0a09b35e98dc0b2f5283c2a707a3a2b8ac9a39f2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-physical-servers-to-azure-with-site-recovery"></a>使用 Site Recovery 将物理服务器复制到 Azure

本文概述了在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 服务将本地 Windows/Linux 物理服务器复制到 Azure 的必要步骤。


## <a name="step-1-review-architecture-and-prerequisites"></a>第 1 步：查看体系结构和先决条件

开始部署前，先查看方案体系结构，并务必了解创建部署所需的全部组件。

转到[第 1 步：查看体系结构](physical-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>第 2 步：查看先决条件

务必满足每个部署组件的先决条件：

- **Azure 先决条件**：必须有 Microsoft Azure 帐户、Azure 网络和存储帐户。
- **本地 Site Recovery 组件**：必须有运行本地 Site Recovery 组件的计算机。
- **复制的计算机**：要复制的服务器必须遵守本地和 Azure 要求。

转到[第 2 步：查看先决条件和限制](physical-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>第 3 步：计划容量

必须确定所需的复制资源，才能执行完整部署。 若要执行快速部署来测试环境，可以跳过这一步。

转到[步骤 3：计划容量](physical-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>第 4 步：计划网络

需要执行一些网络计划，以确保在故障转移发生后 Azure VM 可以连接到网络，并具有正确的 IP 地址。

转到[第 4 步：计划网络](physical-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>第 5 步：准备 Azure 资源

开始前，设置 Azure 网络和存储。 

转到[第 5 步：准备 Azure 资源](physical-walkthrough-prepare-azure.md)


## <a name="step-6-set-up-a-vault"></a>第 6 步：创建保管库

创建恢复服务保管库来安排和管理复制。 创建保管库时，指定要复制的内容，以及将内容复制到的目标位置。

转到[第 6 步：创建保管库](physical-walkthrough-create-vault.md)

## <a name="step-7-configure-source-and-target-settings"></a>第 7 步：配置源和目标设置

配置源和目标 (Azure) 站点的设置。 源设置包括运行统一安装程序来安装本地 Site Recovery 组件。

转到[第 7 步：设置源和目标](physical-walkthrough-source-target.md)

## <a name="step-8-set-up-a-replication-policy"></a>第 8 步：创建复制策略

创建复制策略，以指定应如何复制物理服务器。

转到[第 8 步：创建复制策略](physical-walkthrough-replication.md)

## <a name="step-9-install-the-mobility-service"></a>步骤 9：安装移动服务

必须在要复制的每个服务器上安装 Mobility Service。 可通过多种方法（推送或拉取安装）安装 Mobility Service。

转到[第 9 步：安装 Mobility Service](physical-walkthrough-install-mobility.md)

## <a name="step-10-enable-replication"></a>第 10 步：启用复制

在服务器上运行 Mobility Service 后，可以为它启用复制。 启用后，将进行 VM 初始复制。

转到[第 10 步：启用复制](physical-walkthrough-enable-replication.md)

## <a name="step-11-run-a-test-failover"></a>第 11 步：运行测试故障转移

初始复制完成并运行增量复制后，可以运行测试故障转移，以确保一切都符合预期。

转到[第 11 步：运行测试故障转移](physical-walkthrough-test-failover.md)

