---
title: "使用 Site Recovery 将本地 VM 和物理服务器迁移到 Azure | Microsoft Docs"
description: "本文介绍如何使用 Azure Site Recovery 将本地 VM 和物理服务器迁移到 Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: 423a1727efb0e1fd54eb0f8d5971ace3f8efc6cb
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2017
---
# <a name="migrate-to-azure-with-site-recovery"></a>使用 Site Recovery 迁移到 Azure

请阅读本文，了解如何使用 [Azure Site Recovery](site-recovery-overview.md) 服务将本地虚拟机 (VM) 和物理服务器迁移到 Azure VM。

## <a name="before-you-start"></a>开始之前

观看此视频，快速了解迁移到 Azure 的必要步骤。
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]


## <a name="what-do-we-mean-by-migration"></a>迁移的意思是什么？

可以部署 Site Recovery，以便复制本地 VM 和物理服务器并将其迁移。

- 复制时，可以将本地计算机配置为定期复制到 Azure。 然后，当出现中断时，可以将计算机从本地站点故障转移到 Azure，从该处进行访问。 当本地站点再次可用时，便可从 Azure 进行故障回复。
- 使用 Site Recovery 进行迁移时，请将本地计算机复制到 Azure。 然后将其从本地站点故障转移到 Azure，完成迁移过程。 不涉及故障回复。  

## <a name="what-can-site-recovery-migrate"></a>Site Recovery 可以迁移哪些工作负荷？

可以：

- **从本地迁移**：将本地 Hyper-V VM、VMware VM 和物理服务器迁移到 Azure。 迁移之后，在本地计算机上运行的工作负荷将在 Azure VM 上运行。 
- **在 Azure 中迁移**：在 Azure 区域之间迁移 Azure VM。 
- **迁移 AWS**：将 AWS Windows 实例迁移到 Azure IaaS VM。 

## <a name="migrate-from-on-premises-to-azure"></a>从本地迁移到 Azure

迁移本地 VMware VM、Hyper-V VM 和物理服务器时，所执行的步骤与进行完全复制几乎相同。 


## <a name="migrate-between-azure-regions"></a>在 Azure 区域之间迁移

在区域之间迁移 Azure VM 时，所执行的步骤与进行完全迁移几乎相同。

1. 为需要迁移的虚拟机[启用复制](azure-to-azure-tutorial-enable-replication.md)。
2. [运行快速测试故障转移](azure-to-azure-tutorial-dr-drill.md)，确保一切正常运行。
3. 然后，使用“完成迁移”选项[运行计划外故障转移](azure-to-azure-tutorial-failover-failback.md)。
4. 完成迁移后，即可[设置灾难恢复复制](site-recovery-azure-to-azure-after-migration.md)，以便从迁移到的 Azure 区域复制到辅助区域。



## <a name="migrate-aws-to-azure"></a>将 AWS 迁移到 Azure

可将 AWS 实例迁移到 Azure VM。
- 此方案仅支持迁移。 换而言之，可以复制 AWS 实例并将其故障转移到 Azure，但无法故障回复。
- 在迁移过程中，AWS 实例的处理方式与物理服务器相同。 需要设置一个恢复服务保管库，部署本地配置服务器用于管理复制，将该服务器添加到保管库，然后指定复制设置。
- 为想要迁移的计算机启用复制，运行快速测试故障转移。 然后，使用“完成迁移”选项运行计划外故障转移。






## <a name="next-steps"></a>后续步骤

- [将本地计算机迁移到 Azure](tutorial-migrate-on-premises-to-azure.md)
- [将 VM 从一个 Azure 区域迁移到另一个](site-recovery-migrate-azure-to-azure.md)
- [将 AWS 迁移到 Azure](tutorial-migrate-aws-to-azure.md)
