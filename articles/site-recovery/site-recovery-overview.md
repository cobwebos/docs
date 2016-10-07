<properties
	pageTitle="什么是 Site Recovery？| Microsoft Azure" 
	description="概述 Azure Site Recovery 服务，并说明如何部署该服务。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="02/22/2016" 
	ms.author="raynew"/>

#  什么是 Site Recovery？

欢迎使用 Azure Site Recovery！ 请以此文章作为开端，快速了解 Site Recovery 服务以及它如何有助于业务连续性和灾难恢复 (BCDR) 策略。

## 概述

组织需要制定 BCDR 策略来确定应用、工作负荷和数据如何在计划和非计划停机期间保持运行和可用，并尽快恢复正常运行情况。BCDR 策略应保持业务数据的安全性和可恢复性，并确保在发生灾难时工作负荷持续可用。

站点恢复是一项 Azure 服务，可以通过协调从本地物理服务器和虚拟机到云 (Azure) 或辅助数据中心的的复制，来为 BCDR 策略提供辅助。当主要位置发生故障时，你可以故障转移到辅助位置，使应用和工作负荷保持可用。当主要位置恢复正常时，你可以故障转移回到主要位置。有关详细信息，请参阅[什么是 Site Recovery？](site-recovery-overview.md)

## Azure 门户中的 Site Recovery

Azure 使用两种不同的[部署模型](../resource-manager-deployment-model.md)创建和处理资源：Azure Resource Manager 模型和经典服务管理模型。Azure 还有两个门户，即支持经典部署模型的 [Azure 经典门户](https://manage.windowsazure.com/)和支持两种部署模型的 [Azure 门户](https://portal.azure.com)。

经典门户和 Azure 门户中都提供 Site Recovery。在 Azure 经典门户中，可以使用经典服务管理模型支持 Site Recovery。在 Azure 门户中，可以支持经典模型或资源管理器部署。[详细了解](site-recovery-overview.md#site-recovery-in-the-azure-portal)如何使用 Azure 门户进行部署。

本文中的信息同时适用于经典部署和 Azure 门户部署。将对差异进行说明（如果适用）。


## 为何使用 Site Recovery？ 

以下是 Site Recovery 可以为企业提供的帮助：

- **简化 BCDR 策略** — Site Recovery 可让你从单个位置轻松处理多个企业工作负荷和应用的复制、故障转移及恢复。Site Recovery 会协调复制和故障转移，但不会拦截应用程序数据或拥有任何相关信息。
- **提供灵活的复制** — 借助 Site Recovery，可以复制 Hyper-V 虚拟机、VMware 虚拟机和 Windows/Linux 物理服务器上运行的工作负荷。
- **简单故障转移和恢复** — Site Recovery 提供测试故障转移，既能支持灾难恢复练习，又不会影响生产环境。你也可以运行计划的故障转移，因为是预期中的中断，所以不会丢失任何数据；或是运行非计划的故障转移，以在发生非预期的灾难时将数据损失减到最少（取决于复制频率）。在故障转移之后，你可以故障回复到主站点。Site Recovery 提供包含脚本和 Azure 自动化工作簿的恢复计划，以供你自定义多层应用程序的故障转移和恢复。
- **消除辅助数据中心** — 可以复制到辅助本地站点或 Azure。使用 Azure 作为灾难恢复目的地可免除辅助站点的维护成本与复杂度，而且复制的数据会存储在 Azure 存储空间，能够具备其提供的所有复原能力。
- **与现有 BCDR 技术集成** — Site Recovery 能够与其他应用程序的 BCDR 功能配合使用。例如，你可以使用 Site Recovery 保护企业工作负荷的 SQL Server 后端，包括本机支持 SQL Server AlwaysOn 以便管理可用性组的故障转移。

## 我可以复制哪些内容？

以下是可以使用 Site Recovery 进行复制的内容摘要。

![本地到本地](./media/site-recovery-overview/asr-overview-graphic.png)

**REPLICATE** | **复制源（本地）** | **REPLICATE TO** | **ARTICLE**
---|---|---|---
VMware VM | VMware 服务器 | Azure | [了解详细信息](site-recovery-vmware-to-azure-classic.md)
VMware VM | VMware 服务器 | 辅助 VMware 站点 | [了解详细信息](site-recovery-vmware-to-vmware.md) 
Hyper-V VM | VMM 云中的 Hyper-V 主机 | Azure | [了解详细信息](site-recovery-vmm-to-azure.md) 
Hyper-V VM | VMM 云中的 Hyper-V 主机 | 辅助 VMM 站点 | [了解详细信息](site-recovery-vmm-to-vmm.md)
Hyper-V VM | VMM 云中具有 SAN 存储功能的 Hyper-V 主机| 具有 SAN 存储的辅助 VMM 站点 | [了解详细信息](site-recovery-vmm-san.md)
Hyper-V VM | Hyper-V 主机（无 VMM） | Azure | [了解详细信息](site-recovery-hyper-v-site-to-azure.md)
Windows/Linux 物理服务器 | 物理服务器 | Azure | [了解详细信息](site-recovery-vmware-to-azure-classic.md)
物理 Windows/Linux 服务器上运行的工作负荷 | 物理服务器 | 辅助数据中心 | [了解详细信息](site-recovery-vmware-to-vmware.md) 


## 我可以保护哪些工作负荷？

Site Recovery 有助于应用程序感知 BCDR，让工作负荷和应用在发生中断时继续以一致的方式运行。Site Recovery 提供：

- **应用程序一致的快照** — 使用单个或多层应用的应用程序一致快照进行复制。
- **近乎同步的复制** — Hyper-V 的复制频率最低可为 30 秒，VMware 则可连续复制。
- **与 SQL Server AlwaysOn 集成** — 可以在 Site Recovery 恢复计划中管理可用性组的故障转移。
- **灵活的恢复计划** — 可以使用外部脚本、手动操作和 Azure 自动化 Runbook 创建并自定义恢复计划，允许你通过单击恢复整个应用程序堆栈。
- **自动化库** — 丰富的 Azure 自动化库提供特定于应用程序的生产就绪型脚本，这些脚本可以下载并与 Site Recovery 集成。
- **简单网络管理** — Site Recovery 和 Azure 中的高级网络管理可以简化应用程序网络要求，包括保留 IP 地址、配置负载平衡器或和集成 Azure 流量管理器，以便进行有效的网络切换操作。


## 后续步骤

- 有关详细信息，请参阅 [Site Recovery 可以保护哪些工作负荷？](site-recovery-workload.md)
- 有关 Site Recovery 体系结构的详细信息，请参阅 [Site Recovery 的工作原理](site-recovery-components.md)
 

<!---HONumber=AcomDC_0921_2016-->