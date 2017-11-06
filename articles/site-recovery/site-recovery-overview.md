---
title: "关于 Azure Site Recovery？ | Microsoft Docs"
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
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 90f9fe5775f493298dad3b12f2be9d6da6cb480e
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2017
---
# <a name="about-site-recovery"></a>关于 Site Recovery

欢迎使用 Azure Site Recovery 服务！ 本文快速简要地介绍了该服务。

## <a name="business-continuity-and-disaster-recovery-bcdr-with-azure-recovery-services"></a>Azure 恢复服务的业务连续性和灾难恢复 (BCDR)

组织需确定在发生计划内和计划外中断时，如何维护数据的安全，以及如何确保应用/工作负荷处于运行状态。

Azure 恢复服务有助于制定 BCDR 策略：

- Site Recovery 服务：Site Recovery 可以在站点出现故障时，让应用始终在 VM 上运行，让物理服务器始终可用，从而确保业务连续性。 Site Recovery 可以复制在 VM 和物理服务器上运行的工作负荷，因此当主站点不可用时，始终可以在次要位置使用这些工作负荷。 当主站点重新启动并运行时，它会将工作负荷恢复到主站点。
- 备份服务：另外，[Azure 备份](https://docs.microsoft.com/azure/backup/)服务还会将数据备份到 Azure，从而确保其安全并使之可以恢复。

Site Recovery 可以为以下计算机管理复制：

- 在 Azure 区域之间进行复制的 Azure VM。
- 将数据复制到 Azure 或辅助站点的本地虚拟机和物理服务器。


## <a name="what-does-site-recovery-provide"></a>Site Recovery 提供什么功能？

**功能** | **详细信息**
--- | ---
**部署简单的 BCDR 解决方案** | 可以在 Azure 门户中使用 Site Recovery，以便设置和管理从单个位置进行的复制、故障转移和故障回复。
**复制 Azure VM** | 可以设置 BCDR 策略，以便在 Azure 区域之间复制 Azure VM。
**在场外复制本地 VM** | 可以将本地 VM 和物理服务器复制到 Azure 或辅助性的本地站点。 将数据复制到 Azure 以后，就不需进行复杂的辅助数据中心维护，从而消除相关成本。
**复制任何工作负荷** | 复制在支持的 Azure VM、本地 Hyper-V VM、VMware VM 和 Windows/Linux 物理服务器上运行的任何工作负荷。
**确保数据可复原且安全** | Site Recovery 会安排恢复，且不会拦截应用程序数据。 复制的数据存储在 Azure 存储中，可享用其提供的恢复能力。 发生故障转移时，会基于复制的数据创建 Azure VM。
**满足 RTO 和 RPO** | 让恢复时间目标 (RTO) 和恢复点目标 (RPO) 始终处于组织限制范围内。 Site Recovery 为 Azure VM 和 VMware VM 提供持续复制，为 Hyper-V 提供低至 30 秒的复制频率。 可以通过与 [Azure 流量管理器](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)集成来进一步降低恢复时间目标 (RTO)。
**让应用在故障转移后保持一致** | 可以通过应用程序一致性快照配置恢复点。 应用程序一致性快照可捕获磁盘数据、内存中的所有数据，以及正在处理的所有事务。
**在不中断的情况下测试** | 可轻松运行测试性故障转移，既支持灾难恢复练习，又不会影响正在进行的复制。
**运行灵活的故障转移** | 可针对预期会出现的中断运行计划内故障转移，确保不丢失任何数据；或者针对意外灾难运行计划外故障转移，尽量减少数据丢失（具体取决于复制频率）。 主站点恢复正常时，可轻松故障回复到主站点。
**创建恢复计划** | 可以通过恢复计划对多个 VM 上的多层应用程序的故障转移和恢复进行自定义和排序操作。 在计划内对计算机分组，并添加脚本和手动操作。 恢复计划可与 Azure 自动化 Runbook 集成。
**与现有 BCDR 技术集成** | Site Recovery 可与其他 BCDR 技术集成。 例如，可使用 Site Recovery 保护企业工作负荷的 SQL Server 后端（例如对 SQL Server AlwaysOn 的本机支持），进而管理可用性组的故障转移。
**与自动化库集成** | 丰富的 Azure 自动化库提供特定于应用程序的生产就绪型脚本，可下载它们并将其与 Site Recovery 集成。
**管理网络设置** | Site Recovery 和 Azure 集成可简化应用程序网络管理，具体包括：保留 IP 地址、配置负载均衡器并集成 Azure 流量管理器，从而实现高效的网络切换。


## <a name="what-can-i-replicate"></a>可复制哪些内容？

**支持** | **详细信息**
--- | ---
**可复制哪些内容？** | 在 Azure 区域之间的 Azure VM。<br/><br/>  从本地 VMware VM、Hyper-V VM、物理服务器（Windows 和 Linux）复制到 Azure。<br/><br/> 从本地 VMware VM、Hyper-V VM、物理服务器复制到 Virtual Machine Manager (VMM)。
**Site Recovery 支持哪些区域？** | [支持的区域](https://azure.microsoft.com/regions/services/) |
**复制计算机需要哪些操作系统？** | [Azure VM 要求](site-recovery-support-matrix-azure-to-azure.md#support-for-replicated-machine-os-versions)</br></br>[VMware VM 要求](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)<br/><br/> 对于 Hyper-V VM，支持 Azure 和 Hyper-V 所支持的任何[来宾 OS](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)。<br/><br/> [物理服务器要求](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
**需要哪些 VMware 服务器/主机？** | VMware VM 可以位于[支持的 vSphere 主机/vCenter 服务器](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)上。

**可复制哪些工作负荷？** | 可复制支持的复制计算机上运行的任何工作负荷。 另外，Site Recovery 团队已针对[多个应用](site-recovery-workload.md#workload-summary)执行特定于应用的测试。



## <a name="next-steps"></a>后续步骤
* 阅读有关[工作负荷支持](site-recovery-workload.md)的更多内容。
* [区域之间的 Azure VM 复制](azure-to-azure-quickstart.md)入门。 
