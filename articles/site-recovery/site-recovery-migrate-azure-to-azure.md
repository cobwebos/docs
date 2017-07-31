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
ms.date: 06/14/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: ef2972c077a2b1dd2b2fd6ce53cc6560520ea870
ms.contentlocale: zh-cn
ms.lasthandoff: 07/14/2017

---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>使用 Azure Site Recovery 在 Azure 区域之间迁移 Azure IaaS 虚拟机
## <a name="overview"></a>概述
欢迎使用 Azure Site Recovery！ 如果想要在 Azure 区域之间迁移 Azure VM，请参阅此文章。 开始之前，请注意：

* Azure 提供两个不同的部署模型来创建和处理资源：Azure Resource Manager 和经典。 Azure 还有两个门户 – 支持经典部署模型的 Azure 经典门户，以及支持两种部署模型的 Azure 门户。 无论是在 Resource Manager 中还是在经典中配置 Site Recovery，迁移的基本步骤都是相同的。 但是，本文中的 UI 说明和屏幕截图与 Azure 门户相关。
* **目前仅能从一个区域迁移到另一个区域。可以将 VM 从一个 Azure 区域故障转移到另一个 Azure 区域，但不能重新对其进行故障回复。**

请将任何评论或问题发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="prerequisites"></a>先决条件
以下是执行此部署所需的组件：

* **IaaS 虚拟机**：想要迁移的 VM。 将 VM 视为物理计算机进行迁移。

## <a name="deployment-steps"></a>部署步骤
本节介绍新 Azure 门户中的部署步骤。

1. [创建保管库](site-recovery-vmware-to-azure.md)。
2. [启用复制](site-recovery-vmware-to-azure.md)。 为需要迁移的 VM 启用复制，并选择 Azure 作为源。 
3. [运行非计划的故障转移](site-recovery-failover.md)。 初始复制完成之后，可以运行从一个 Azure 区域到另一个 Azure 区域的非计划的故障转移。 （可选）你可以创建一个恢复计划并运行非计划的故障转移，在区域之间迁移多个虚拟机。 [详细了解](site-recovery-create-recovery-plans.md)恢复计划。

## <a name="next-steps"></a>后续步骤
若要详细了解其他复制方案，请参阅[什么是 Azure Site Recovery？](site-recovery-overview.md)

