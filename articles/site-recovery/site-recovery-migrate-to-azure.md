---
title: "将迁移到 Azure Site Recovery 与 |Microsoft 文档"
description: "本文概述了迁移的虚拟机和物理服务器到与 Azure Site Recovery 的 Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/05/2017
ms.author: raynew
ms.openlocfilehash: f4dfe430fba51bd009431ca72279a21be55e3a40
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="migrate-to-azure-with-site-recovery"></a>将迁移到 Azure 站点恢复

阅读有关概述使用 Azure Site Recovery 服务的虚拟机和物理服务器的迁移的这篇文章。

站点恢复是通过协调复制的本地物理服务器和虚拟机到云 (Azure)，或到辅助数据中心可能会导致 BCDR 策略，一个 Azure 服务。 在您的主要位置中发生服务中断，当你故障转移到辅助位置，使应用程序和工作负荷可用。 当它返回到正常操作故障回复到主位置。 了解详细信息中[什么是站点恢复？](site-recovery-overview.md) 你可以使用 Site Recovery 将你现有的本地工作负荷迁移到 Azure，以加快云旅程和利用 Azure 提供的功能的数组。

有关如何执行迁移的快速概述，请参阅此视频。
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]

本指南介绍了中的部署[Azure 门户](https://portal.azure.com)。 [Azure 经典门户](https://manage.windowsazure.com/)用于维护现有站点恢复保管库，但不是能创建新的保管库。

发布任何评论底部的这篇文章。 在询问技术问题[Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## <a name="what-do-we-mean-by-migration"></a>我们通过迁移代表什么？

你可以部署 Site Recovery 为复制的本地虚拟机和物理服务器，到 Azure 或辅助站点。 复制机，它们时故障转移从主站点中断发生，并且它们返回故障到主站点时它会恢复。 除此之外，你可以使用 Site Recovery 将虚拟机和物理服务器迁移到 Azure，以便用户可以访问它们作为 Azure Vm。 迁移需要复制，并从主站点到 Azure，以及在完成迁移笔势故障转移。

## <a name="what-can-site-recovery-migrate"></a>Site Recovery 可以迁移？

你可以：

- 迁移的本地 HYPER-V Vm、 VMware 虚拟机和物理服务器，在 Azure Vm 上运行上运行的工作负荷。 你还可以执行完全复制与在此方案中的故障回复。
- 迁移[Azure IaaS Vm](site-recovery-migrate-azure-to-azure.md) Azure 区域之间。 当前仅迁移支持在此方案中，这意味着故障回复不受支持。
- 迁移[AWS Windows 实例](site-recovery-migrate-aws-to-azure.md)到 Azure IaaS Vm。 当前仅迁移支持在此方案中，这意味着故障回复不受支持。

## <a name="migrate-on-premises-vms-and-physical-servers"></a>迁移在本地虚拟机和物理服务器

迁移在本地 HYPER-V 虚拟机、 VMware 虚拟机和物理服务器，请按照与所使用的常规复制几乎相同的步骤。

1. 设置恢复服务保管库
2. 配置所需管理服务器 (VMware、 VMM、 HYPER-V-具体取决于你想要迁移)，将它们添加到保管库，并指定复制设置。
3. 为你想要迁移的机启用复制
4. 初始迁移之后，运行快速测试故障转移以确保所有内容按预期工作。
5. 在验证正在复制环境后，你使用计划或非计划故障转移中的，具体取决于[支持的功能](site-recovery-failover.md)为你的方案。 我们建议你使用计划的故障转移在可能的情况。
6. 对于迁移，你不需要以提交故障转移，或将其删除。 相反，选择**完成迁移**为你想要迁移的每台计算机的选项。
     - 在**复制项**，右键单击虚拟机，然后单击**完成迁移**。 单击**确定**才能完成。 你也可以通过监视中的完成迁移作业中跟踪在 VM 属性中，进度**Site Recovery 作业，**。
     - **完成迁移**操作完成迁移过程中, 删除的计算机，复制并停止机的 Site Recovery 计费。

![completemigration](./media/site-recovery-hyper-v-site-to-azure/migrate.png)

## <a name="migrate-between-azure-regions"></a>Azure 区域之间迁移

你可以使用 Site Recovery 的区域之间迁移 Azure 虚拟机。 在这种情况下支持仅限迁移。 换而言之，你可以复制 Azure Vm 和进行故障转移到另一个区域，但你无法进行故障回复。 在此方案中设置恢复服务保管库，部署在本地配置服务器以管理复制，将其添加到保管库，并指定复制设置。 为你想要迁移，机启用复制并运行快速测试故障转移。 然后运行未计划故障转移**完成迁移**选项。

## <a name="migrate-aws-to-azure"></a>将 AWS 迁移到 Azure

你可以将 AWS 实例迁移到 Azure Vm 中。 在这种情况下支持仅限迁移。 换而言之，你可以复制 AWS 实例和故障转移到 Azure，但你无法进行故障回复。 采用与物理服务器以进行迁移相同的方式来处理 AWS 实例。 设置恢复服务保管库，部署在本地配置服务器以管理复制，将其添加到保管库，并指定复制设置。 为你想要迁移，机启用复制并运行快速测试故障转移。 然后运行未计划故障转移**完成迁移**选项。




## <a name="next-steps"></a>后续步骤

- [将 VMware 虚拟机迁移到 Azure](site-recovery-vmware-to-azure.md)
- [将 VMM 云中的 HYPER-V Vm 迁移到 Azure](site-recovery-vmm-to-azure.md)
- [将没有 VMM 的 HYPER-V Vm 迁移到 Azure](site-recovery-hyper-v-site-to-azure.md)
- [Azure 区域之间迁移 Azure 虚拟机](site-recovery-migrate-azure-to-azure.md)
- [将 AWS 实例迁移到 Azure](site-recovery-migrate-aws-to-azure.md)
- [准备迁移的机启用复制](site-recovery-azure-to-azure-after-migration.md)到灾难恢复的另一个区域需要。
- 开始保护你的工作负荷通过[复制 Azure 虚拟机。](site-recovery-azure-to-azure.md)
