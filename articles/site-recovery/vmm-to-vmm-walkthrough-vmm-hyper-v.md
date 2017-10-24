---
title: "使用 Azure Site Recovery 为执行到辅助站点的复制设置 VMM 和 Hyper-V | Microsoft Docs"
description: "介绍如何为执行到辅助 VMM 站点的复制设置 System Center VMM 服务器和 Hyper-V 主机。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d0389e3b-3737-496c-bda6-77152264dd98
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 73bd1790c56ac52166f638de2e80c2a2cfb87e53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="step-4-set-up-vmm-and-hyper-v-for-hyper-v-vm-replication-to-a-secondary-site"></a>步骤 4：为执行 Hyper-V VM 到辅助站点的复制设置 VMM 和 Hyper-V 

为网络做好准备后，为在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 执行 Hyper-V 虚拟机 (VM) 到辅助站点的复制设置 System Center Virtual Machine Manager (VMM) 服务器和 Hyper-V 主机。 

阅读本文后，请在底部发布评论，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。



## <a name="prepare-vmm-servers"></a>准备 VMM 服务器 

准备部署：


1. 请确保 VMM 服务器符合[支持要求](site-recovery-support-matrix-to-sec-site.md#on-premises-servers)和[部署先决条件](vmm-to-vmm-walkthrough-prerequisites.md)。
2. 请确保 VMM 服务器已连接到 Internet，并可以访问这些 URL。
    
    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - 如果设置了基于 IP 地址的防火墙规则，请确保这些规则允许与 Azure 通信。
    - 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。
    - 允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围（用于访问控制和标识管理）。
3. 请确保 VMM 服务器已[为网络映射做好准备](vmm-to-vmm-walkthrough-network.md#prepare-for-network-mapping)


## <a name="prepare-hyper-v-hostsclusters"></a>准备 Hyper-V 主机/群集

1. 请确保 Hyper-V 主机/群集符合[支持要求](site-recovery-support-matrix-to-sec-site.md#on-premises-servers)和[部署先决条件](vmm-to-vmm-walkthrough-prerequisites.md)。
2. 验证 [Hyper-V VM](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions) 的要求
3. 验证[网络](site-recovery-support-matrix-to-sec-site.md#network-configuration)和[存储](site-recovery-support-matrix-to-sec-site.md#storage)要求。
4. 请确保 Hyper-V 主机已连接到 Internet，并可以访问这些 URL。
    
    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - 如果设置了基于 IP 地址的防火墙规则，请确保这些规则允许与 Azure 通信。
    - 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)和 HTTPS (443) 端口。
    - 允许订阅的 Azure 区域的 IP 地址范围以及美国西部的 IP 地址范围（用于访问控制和标识管理）。

## <a name="prepare-for-single-server-deployment"></a>准备单个服务器部署


如本文档所述，如果只有一个 VMM 服务器，可以在 VMM 云中将 Hyper-V 主机上的 VM 复制到 [Azure](hyper-v-site-walkthrough-overview.md)，或复制到辅助 VMM 云。 建议选择前者，因为云之间复制不是无缝复制。

如果要在云之间进行复制，可通过单个独立 VMM 服务器，或通过 Windows 延伸群集的单个 VMM 服务器进行复制

### <a name="replicate-with-a-standalone-vmm-server"></a>通过独立的 VMM 服务器复制

在此方案中，需要在主站点中部署单个 VMM 服务器作为虚拟机，并使用 Site Recovery 和 Hyper-V 副本将此 VM 复制到辅助站点。

1. **在 Hyper-V VM 上设置 VMM**。 为此，建议将 VMM 所用的 SQL Server 实例共置到同一 VM 上。 这可以节省时间，因为只需创建一个 VM。 如果希望使用 SQL Server 的远程实例，而此时发生了中断，则需先恢复该实例，再恢复 VMM。
2. **确保该 VMM 服务器至少配置了两个云**。 一个云将包含要复制的 VM，另一个云将充当辅助位置。 包含要保护的 VM 的云应该满足[先决条件](#prerequisites)。
3. 如本文中所述设置站点恢复。 在保管库中创建和注册 VMM 服务器、设置复制策略，并启用复制。 源和目标 VMM 名称将相同。 指定要通过网络进行初始复制。
4. 设置网络映射时，应将主云的 VM 网络映射到辅助云的 VM 网络。
5. 在 Hyper-V 管理器控制台中，在包含 VMM VM 的 Hyper-V 主机上启用 Hyper-V 副本，在 VM 上启用复制。 请确保不将 VMM 虚拟机添加到受 Site Recovery 保护的云中，使得 Hyper-V 副本设置不会被 Site Recovery 重写。
6. 如果为故障转移创建恢复方案，应为源和目标使用同一个 VMM 服务器。
7. 若出现完全中断，请按如下所述进行故障转移和恢复：

   1. 在辅助站点的 Hyper-V 管理器控制台中，运行未计划的故障转移，将主 VMM VM 故障转移到辅助站点。
   2. 验证 VMM VM 是否已启动并正在运行，并在保管库中运行未计划的故障转移，将 VM 从主云故障转移到辅助云。 提交故障转移，并根据需要选择备用恢复点。
   3. 完成非计划的故障转移之后，可再次通过主站点访问所有资源。
   4. 主站点在辅助站点的 Hyper-V 管理器控制台中重新可用时，为 VMM VM 启用反向复制。 这会为 VM 启动从辅助站点到主站点的复制。
   5. 在辅助站点的 Hyper-V 管理器控制台中，运行未计划的故障转移，将 VMM VM 故障转移到主站点。 提交故障转移。 然后启用反向复制，以便再次将 VMM VM 从主站点复制到辅助站点。
   6. 在恢复服务保管库中，启用工作负荷 VM 的反向复制，开始将它们从辅助站点复制到主站点。
   7. 在恢复服务保管库中，运行计划的故障转移将工作负荷 VM 故障回复到主站点。 提交故障转移即可完成。 然后，启用反向复制开始将工作负荷 VM 从主站点复制到辅助站点。

### <a name="replicate-with-a-stretched-vmm-cluster"></a>通过外延式 VMM 群集复制

不必将单独的 VMM 服务器部署为将内容复制到辅助站点的 VM，而只需将其部署为 Windows 故障转移群集中的 VM 来提高 VMM 的可用性。 这可以为工作负荷提供弹性并防止硬件故障。 要通过站点恢复进行部署，应将 VMM VM 部署在一个外延式群集中，该群集在地理位置上跨多个单独的站点。 为此，请按以下步骤操作：

1. 将 VMM 安装在 Windows 故障转移群集的虚拟机上，在安装过程中请选择合适的选项，确保该服务器在运行的时候可用性高。
2. 应该使用 SQL Server AlwaysOn 可用性组来复制 VMM 所用的 SQL Server 实例，使得辅助站点中存在数据库的副本。
3. 按照本文中的说明创建保管库、注册服务器并设置保护。 需要在恢复服务保管库中注册群集中的每个 VMM 服务器。 若要执行此操作，请在活动节点上安装提供程序并注册 VMM 服务器。 然后在其他节点上安装提供程序。
4. 发生中断时，VMM 服务器及其相应的 SQL Server 数据库将故障转移，并可从辅助站点访问。



## <a name="next-steps"></a>后续步骤

转到[步骤 5：设置保管库](vmm-to-vmm-walkthrough-create-vault.md)。