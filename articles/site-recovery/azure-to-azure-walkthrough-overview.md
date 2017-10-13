---
title: "在 Azure 区域间复制 Azure VM | Microsoft Docs"
description: "总结了使用 Azure 门户中的 Azure Site Recovery 服务在 Azure 区域间复制 Azure VM 的步骤"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 6dd36239-4363-4538-bf80-a18e71b8ec67
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: 9258613161a61e36b1d0c5796d5763c916d66859
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>使用 Azure Site Recovery 在区域间复制 Azure VM

>本文概述了将一个 Azure 区域中的 Azure 虚拟机 (VM) 复制到另一个区域中的 Azure VM 所需的步骤。 

>[!NOTE]
>
> Azure VM 复制目前处于预览版。

请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="step-1-review-architecture"></a>第 1 步：查看体系结构

开始部署之前，先查看方案体系结构以及要部署的组件。

转到[第 1 步：查看体系结构](azure-to-azure-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>第 2 步：查看先决条件

检查是否具备 Azure 先决条件，包括订阅、虚拟网络、存储帐户和 VM 需求。

转到[第 2 步：验证先决条件和限制](azure-to-azure-walkthrough-prerequisites.md)


## <a name="step-3-plan-networking"></a>第 3 步：计划网络

检查要复制的 Azure VM 上是否设置了出站连接和本地连接。

转到[第 4 步：计划网络](azure-to-azure-walkthrough-network.md)



## <a name="step-4-create-a-vault"></a>第 4 步：创建保管库 

需要通过设置恢复服务保管库来安排和管理复制，以及指定源区域。

转到[第 4 步：创建保管库](azure-to-azure-walkthrough-vault.md)


## <a name="step-5-enable-replication"></a>第 5 步：启用复制


若要启用复制，请配置目标位置设置，设置复制策略，然后选择要复制的 Azure VM。 启用后，将进行 VM 初始复制。

转到[第 5 步：启用复制](azure-to-azure-walkthrough-enable-replication.md)


## <a name="step-6-run-a-test-failover"></a>第 6 步：运行测试故障转移

初始复制完成并运行增量复制后，可以运行测试故障转移，以确保一切都符合预期。

转到[第 6 步：运行测试故障转移](azure-to-azure-walkthrough-test-failover.md)


