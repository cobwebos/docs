---
title: "在 Azure 区域之间迁移 Azure IaaS VM | Microsoft 文档"
description: "使用 Azure Site Recovery 将 Azure IaaS 虚拟机从一个 Azure 区域迁移到另一个 Azure 区域。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: raynew
ms.openlocfilehash: f645150aa7b93ebbd98899cc8fdd495dfeaf3076
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>使用 Azure Site Recovery 在 Azure 区域之间迁移 Azure IaaS 虚拟机

欢迎使用 Azure Site Recovery！ 如果想要在 Azure 区域之间迁移 Azure VM，请参阅此文章。
>[!NOTE]
>
> 要了解如何将 Azure VM 复制到其他区域以满足灾难恢复和迁移需求，请参阅[本文档](../site-recovery-azure-to-azure.md)。 用于 Azure 虚拟机的 Site Recovery 复制当前处于预览状态。


## <a name="prerequisites"></a>先决条件
以下是执行此部署所需的组件：

* **IaaS 虚拟机**：想要迁移的 VM。 将 VM 视为物理计算机进行迁移。

## <a name="deployment-steps"></a>部署步骤
本节介绍新 Azure 门户中的部署步骤。

1. [创建保管库](../site-recovery-azure-to-azure.md#create-a-recovery-services-vault)。
2. 为需要迁移的 VM [启用复制]../(site-recovery-azure-to-azure.md)，并选择 Azure 作为源。
  >[!NOTE]
  >
  > 当前不支持使用托管磁盘来实现 Azure VM 的本机复制。 可使用[本文档](../site-recovery-vmware-to-azure.md)中的“物理机到 Azure”选项，通过托管磁盘来迁移 VM。
3. [运行故障转移](../site-recovery-failover.md)。 初始复制完成后，可以运行从一个 Azure 区域到另一个 Azure 区域的故障转移。 （可选）可以创建一个恢复计划并运行故障转移，在区域之间迁移多个虚拟机。 [详细了解](../site-recovery-create-recovery-plans.md)恢复计划。

## <a name="next-steps"></a>后续步骤
若要详细了解其他复制方案，请参阅[什么是 Azure Site Recovery？](../site-recovery-overview.md)
