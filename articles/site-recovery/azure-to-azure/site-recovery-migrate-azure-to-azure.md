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
ms.date: 08/31/2017
ms.author: raynew
ms.openlocfilehash: 805582d89ee906e21fbe588e895ce0fe3a926a66
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>使用 Azure Site Recovery 在 Azure 区域之间迁移 Azure IaaS 虚拟机

如果想要使用 [Site Recovery](../site-recovery-overview.md) 服务在 Azure 区域之间迁移 Azure 虚拟机 (VM)，请参阅本文。

## <a name="prerequisites"></a>先决条件

准备好想要迁移的 IaaS VM。

## <a name="deployment-steps"></a>部署步骤

1. [创建保管库](azure-to-azure-tutorial-enable-replication.md#create-a-vault)。
2. 为需要迁移的 VM [启用复制](azure-to-azure-tutorial-enable-replication.md#enable-replication)，并选择 Azure 作为源。 目前不支持使用托管磁盘来实现 Azure VM 的本机复制。
3. [运行故障转移](../site-recovery-failover.md)。 初始复制完成后，可以运行从一个 Azure 区域到另一个 Azure 区域的故障转移。 （可选）可以创建一个恢复计划并运行故障转移，在区域之间迁移多个虚拟机。 [详细了解](../site-recovery-create-recovery-plans.md)恢复计划。

## <a name="next-steps"></a>后续步骤
若要了解其他复制方案，请参阅[什么是 Azure Site Recovery？](../site-recovery-overview.md)
