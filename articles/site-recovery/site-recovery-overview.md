---
title: "什么是 Site Recovery？ | Microsoft Docs"
description: "简要介绍 Azure Site Recovery 服务并概述讲解部署方案。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: e9b97b00-0c92-4970-ae92-5166a4d43b68
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 7455d6f99ed8ceb401224f98105f7b651f55c724
ms.openlocfilehash: 98bf94960c39565243995a1c4dd45787478b8f40


---
# <a name="what-is-site-recovery"></a>什么是 Site Recovery？
欢迎使用 Azure Site Recovery 服务！ 本文快速简要地介绍了 Site Recovery。

组织需制定业务连续性和灾难恢复 (BCDR) 策略，借此保证计划内和计划外停机期间的数据安全和可用性，并尽快恢复正常运行状态。

Site Recovery 可安排本地虚拟机和物理服务器的复制，进而有利于 BCDR 策略。 服务器和 VM 从本地主数据中心复制到云端 (Azure) 或辅助数据中心。

主站点发生故障时，可故障转移到辅助站点，保证工作负荷可访问且可用。 当主要位置恢复正常时，可故障转移回到该位置。

## <a name="site-recovery-in-the-azure-portal"></a>Azure 门户中的 Site Recovery
Azure 具有两种用于创建和处理资源的[部署模型](../azure-resource-manager/resource-manager-deployment-model.md)（两者不同）。 Azure Resource Manager 模型和经典服务管理模型。 Azure 也有两个门户。 [Azure 经典门户](https://manage.windowsazure.com/)和 [Azure 门户](https://portal.azure.com)。

* Site Recovery 可在经典门户和 Azure 门户中进行部署。
* 在 Azure 经典门户中，可使用经典服务管理模型支持 Site Recovery。
* 在 Azure 门户中，可支持经典模型或 Resource Manager 部署。

本文中的信息同时适用于经典部署和 Azure 门户部署。 并在适用处标注了差异。

## <a name="why-deploy-site-recovery"></a>为何部署 Site Recovery？
以下是 Site Recovery 可以为企业提供的帮助：

* **简化 BCDR** - 可在 Azure 门户的单个位置执行多个工作负荷的复制、故障转移和恢复。 Site Recovery 会安排恢复和故障转移，且不会拦截应用程序数据。
* **实现灵活复制** - 可复制在受支持的 Hyper-V VM、VMware VM 和 Windows/Linux 物理服务器上运行的任何工作负荷。
* **不再使用辅助数据中心** - 可将工作负荷复制到 Azure，而不是辅助站点上。 这消除了维护辅助数据中心的复杂性和产生的成本。 复制的数据存储在 Azure 存储中，可享用其提供的恢复能力。 发生故障转移时，会使用复制的数据创建 Azure VM。
* **执行简单的复制测试** - 可轻松运行测试故障转移，既支持灾难恢复练习，又不会影响生产环境。
* **故障转移和恢复** - 可针对预期中断运行计划的故障转移以实现零数据丢失，或针对意外灾难运行计划外的故障转移以尽量减少数据丢失（取决于复制频率）。 可在主站点再次可用后故障回复到该站点。
* **多个 VM 故障转移** - 可设置恢复计划，内附脚本和 Azure 自动化 runbook。 通过恢复计划，可自定义分布在多个 VM 上的多层应用程序的故障转移和恢复并对其进行建模。
* **与现有 BCDR 技术集成** - Site Recovery 与其他 BCDR 技术相集成。 例如，可使用 Site Recovery 保护企业工作负荷的 SQL Server 后端（例如对 SQL Server AlwaysOn 的本机支持），进而管理可用性组的故障转移。

## <a name="what-can-i-replicate"></a>可复制哪些内容？
下面概述介绍了可使用 Site Recovery 复制的内容。

![本地到本地](./media/site-recovery-overview/asr-overview-graphic.png)

| **REPLICATE** | **REPLICATE TO** |
| --- | --- |
| 本地 VMware VM |[Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [辅助站点](site-recovery-vmware-to-vmware.md) |
| VMM 云中托管的本地 Hyper-V VM |[Azure](site-recovery-vmm-to-azure.md)<br/><br/> [辅助站点](site-recovery-vmm-to-vmm.md) |
| VMM 云中托管的本地 Hyper-V VM（需要 SAN 存储） |[辅助站点](site-recovery-vmm-san.md) |
| 本地 Hyper-V VM（无需 VMM） |[Azure](site-recovery-hyper-v-site-to-azure.md) |
| Windows/Linux 本地物理服务器 |[Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [辅助站点](site-recovery-vmware-to-vmware.md) |

## <a name="how-does-site-recovery-protect-workloads"></a>Site Recovery 如何保护工作负荷？
Site Recovery 提供应用程序感知复制，使工作负荷和应用在出现中断时继续以一致的方式运行。

* **应用程序一致的快照** - 计算机针对单层或多层应用使用应用程序一致的快照进行复制。 除了捕获磁盘数据，应用程序一致的快照还会捕获内存中的所有数据和正在进行的所有事务。
* **近乎同步的复制** - Site Recovery 向 Hyper-V 提供低至 30 秒的复制频率，向 VMware 提供连续复制。
* **灵活的恢复计划** - 可使用外部脚本创建和自定义恢复计划，并可手动操作。 通过与 Azure 自动化 runbook 集成，让你只需单击一次即可恢复整个应用程序堆栈。
* **与 SQL Server AlwaysOn 集成** - 可使用恢复计划管理可用性组的故障转移。
* **自动化库** - 丰富的 Azure 自动化库提供特定于应用程序的生产就绪型脚本，可下载它们并与 Site Recovery 集成。
* **简化网络管理** - Site Recovery 和 Azure 中的高级网络管理可保留 IP 地址、配置负载均衡器并与 Azure 流量管理器集成来实现高效的网络切换，进而简化应用程序网络要求。

## <a name="next-steps"></a>后续步骤
* 有关详细信息，请参阅 [Site Recovery 可以保护哪些工作负荷？](site-recovery-workload.md)
* 有关 Site Recovery 体系结构的详细信息，请参阅 [Site Recovery 的工作原理](site-recovery-components.md)




<!--HONumber=Nov16_HO4-->


