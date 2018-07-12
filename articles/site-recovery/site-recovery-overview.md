---
title: 关于 Azure Site Recovery | Microsoft Docs
description: 简要介绍 Azure Site Recovery 服务并概述讲解部署方案。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: overview
ms.date: 07/06/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: a3242c9402d738d580f4abf5f1a0bb64f2fa7728
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919897"
---
# <a name="about-site-recovery"></a>关于 Site Recovery

欢迎使用 Azure Site Recovery 服务！ 本文快速简要地介绍了该服务。

组织需采用业务连续性和灾难恢复 (BCDR) 策略，在发生计划内和计划外停机时确保数据的安全以及应用和工作负荷的正常启动和运行。

Azure 恢复服务有助于制定 BCDR 策略：

- **Site Recovery 服务**：Site Recovery 有助于保持业务应用和工作负荷在停机期间的正常运行，从而确保业务连续性。 Site Recovery 将在物理机和虚拟机 (VM) 上运行的工作负荷从主站点复制到辅助位置。 主站点发生停机时，可以故障转移到辅助位置，从该位置访问应用。 在主位置再次运行后，可以故障回复到该位置。  
- **备份服务**：[Azure 备份](https://docs.microsoft.com/azure/backup/)服务还会将数据备份到 Azure，从而确保其安全并使之可以恢复。

Site Recovery 可以为以下计算机管理复制：

- 在 Azure 区域之间进行复制的 Azure VM。
- 将数据复制到 Azure 或辅助站点的本地 VM 和物理服务器。


## <a name="what-does-site-recovery-provide"></a>Site Recovery 提供什么功能？


**功能** | **详细信息**
--- | ---
**简单的 BCDR 解决方案** | 可以在 Azure 门户中使用 Site Recovery，以便设置和管理从单个位置进行的复制、故障转移和故障回复。
**Azure VM 复制** | 可以设置 Azure VM 从主要区域到次要区域的灾难恢复。
**本地 VM 复制** | 可以将本地 VM 和物理服务器复制到 Azure 或辅助性的本地数据中心。 将数据复制到 Azure 以后，就不需进行复杂的辅助数据中心维护，从而消除相关成本。
**工作负荷复制** | 复制在支持的 Azure VM、本地 Hyper-V 和 VMware VM 以及 Windows/Linux 物理服务器上运行的任何工作负荷。
**数据复原能力** | Site Recovery 会安排恢复，且不会拦截应用程序数据。 复制到 Azure 时，数据存储在 Azure 存储中，具有后者提供的复原能力。 发生故障转移时，会基于复制的数据创建 Azure VM。
**RTO 和 RPO 目标** | 让恢复时间目标 (RTO) 和恢复点目标 (RPO) 始终处于组织限制范围内。 Site Recovery 为 Azure VM 和 VMware VM 提供持续复制，为 Hyper-V 提供低至 30 秒的复制频率。 可以通过与 [Azure 流量管理器](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)集成来进一步降低 RTO。
**让应用在故障转移后保持一致** | 可以通过应用程序一致性快照使用恢复点进行复制。 这些快照可捕获磁盘数据、内存中的所有数据，以及正在处理的所有事务。
**在不中断的情况下测试** | 可轻松地运行灾难恢复练习，不会影响正在进行的复制。
**灵活的故障转移** | 可针对预期会出现的中断运行计划内故障转移，确保不丢失任何数据；或者针对意外灾难运行计划外故障转移，尽量减少数据丢失（具体取决于复制频率）。 主站点恢复正常时，可轻松故障回复到主站点。
**自定义的恢复计划** | 可以通过恢复计划对多个 VM 上运行的多层应用程序的故障转移和恢复进行自定义和排序操作。 可以在恢复计划中将计算机组合到一起，选择性地添加脚本和手动操作。 恢复计划可与 Azure 自动化 Runbook 集成。
**BCDR 集成** | Site Recovery 可与其他 BCDR 技术集成。 例如，可使用 Site Recovery 保护企业工作负荷的 SQL Server 后端，为 SQL Server AlwaysOn 提供本机支持，进而管理可用性组的故障转移。
**Azure 自动化集成** | 丰富的 Azure 自动化库提供特定于应用程序的生产就绪型脚本，可下载它们并将其与 Site Recovery 集成。
**网络集成** | Site Recovery 和 Azure 集成可简化应用程序网络管理，具体包括：保留 IP 地址、配置负载均衡器并集成 Azure 流量管理器，从而实现高效的网络切换。


## <a name="what-can-i-replicate"></a>可复制哪些内容？

**支持** | **详细信息**
--- | ---
**复制方案** | 将 Azure VM 从一个 Azure 区域复制到另一个 Azure 区域。<br/><br/>  将本地 VMware VM、Hyper-V VM、物理服务器（Windows 和 Linux）复制到 Azure。<br/><br/> 将本地 VMware VM、System Center VMM 托管的 Hyper-V VM 以及物理服务器复制到辅助站点。
**区域** | 查看 Site Recovery [支持的区域](https://azure.microsoft.com/regions/services/)。 |
复制的计算机 | 查看 [Azure VM](azure-to-azure-support-matrix.md#support-for-replicated-machine-os-versions) 复制、[本地 VMware VM 和物理服务器](vmware-physical-azure-support-matrix.md#replicated-machines)以及[本地 Hyper-V VM](hyper-v-azure-support-matrix.md#replicated-vms) 的复制要求。
**VMware 服务器/主机** | 要复制的 VMware VM 可以位于[支持的主机和虚拟化服务器](vmware-physical-azure-support-matrix.md)上。
**工作负荷** | 可以在支持复制的计算机上复制运行的任何工作负荷。 另外，Site Recovery 团队已针对[多个应用](site-recovery-workload.md#workload-summary)执行特定于应用的测试。



## <a name="next-steps"></a>后续步骤
* 阅读有关[工作负荷支持](site-recovery-workload.md)的更多内容。
* [区域之间的 Azure VM 复制](azure-to-azure-quickstart.md)入门。 
