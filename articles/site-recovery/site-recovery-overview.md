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
ms.date: 03/14/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: d8e4e4bb7dd1e40d8c561adba04b8346fcb2127d
ms.lasthandoff: 03/15/2017


---
# <a name="what-is-site-recovery"></a>什么是 Site Recovery？

欢迎使用 Azure Site Recovery 服务！ 本文快速简要地介绍了该服务。

自然事件和操作失败会导致中断。 组织需制定业务连续性和灾难恢复 (BCDR) 策略，借此保证计划内和计划外停机期间的数据安全和应用可用性，并尽快恢复业务的正常运营状态。

Azure 恢复服务有助于制定 BCDR 策略。 [Azure 备份](https://docs.microsoft.com/en-us/azure/backup/)服务保证数据安全性和可恢复性。 Site Recovery 可对工作负荷进行复制、故障转移和恢复，因此发生故障时这些工作负荷仍然可用。

## <a name="what-does-site-recovery-provide"></a>Site Recovery 提供什么功能？

- **云中的灾难恢复** - 可将 VM 和物理服务器上运行的工作负荷复制到 Azure，而不是复制到辅助站点上。 这消除了维护辅助数据中心的复杂性和产生的成本。
- **混合环境中的灵活复制** - 可复制支持的本地 Hyper-V VM、VMware VM 和 Windows/Linux 物理服务器上运行的任何工作负荷。
- **迁移** - 可使用 Site Recovery 将本地 AWS 实例迁移到 Azure VM，或在 Azure 区域之间迁移 Azure VM。
- **简化的 BCDR** - 可在 Azure 门户中从单个位置部署复制。  可以对单台和多台计算机运行简单的故障转移和故障回复。
- **复原能力** - Site Recovery 会安排恢复和故障转移，且不会拦截应用程序数据。
复制的数据存储在 Azure 存储中，可享用其提供的恢复能力。 发生故障转移时，会基于复制的数据创建 Azure VM。
- **复制性能** - Site Recovery 向 Hyper-V 提供低至 30 秒的复制频率，向 VMware 提供连续复制。 可以设置恢复点目标 (RPO) 阈值，以控制创建数据恢复点的频率，还可以使用 Site Recovery 的自动恢复进程以及与 [Azure 流量管理器](https://azure.microsoft.com/en-us/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)的集成来减少恢复时间目标 (RTO)
- **应用程序一致性** - 计算机使用应用程序一致快照进行复制。 除了捕获磁盘数据，应用程序一致的快照还会捕获内存中的所有数据和正在进行的所有事务。
- **无中断测试** - 可轻松运行测试故障转移，既支持灾难恢复练习，又不会影响生产环境。
- **灵活的故障转移和恢复** - 可针对预期中断运行计划的故障转移以实现零数据丢失，或针对意外灾难运行计划外的故障转移以尽量减少数据丢失（取决于复制频率）。 主站点恢复正常时，可轻松故障回复到主站点。
- **自定义恢复计划** - 通过恢复计划，可自定义分布在多个 VM 上的多层应用程序的故障转移和恢复并对其进行建模。 在计划内对组进行排序，并添加脚本和手动操作。 恢复计划可与 Azure 自动化 Runbook 集成。
- **多层应用** - 可为多层应用已序列化的故障转移和恢复创建恢复计划。 可在恢复计划中对不同层（如数据库、Web 和应用）的计算机进行分组，还可自定义每个组的故障转移和启动方式。
* **与现有 BCDR 技术集成** - Site Recovery 与其他 BCDR 技术集成。 例如，可使用 Site Recovery 保护企业工作负荷的 SQL Server 后端（例如对 SQL Server AlwaysOn 的本机支持），进而管理可用性组的故障转移。
* **与自动化库集成** - 丰富的 Azure 自动化库提供特定于应用程序的生产就绪型脚本，可下载它们并与 Site Recovery 集成。
* **简化网络管理** - Site Recovery 和 Azure 中的高级网络管理可保留 IP 地址、配置负载均衡器并与 Azure 流量管理器集成来实现高效的网络切换，进而简化应用程序网络要求。


## <a name="whats-supported"></a>支持的操作

**支持** | **详细信息**
--- | ---
**Site Recovery 支持哪些区域？** | [支持的区域](https://azure.microsoft.com/en-us/regions/services/) |
**可复制哪些内容？** | 本地 VMware VM、Hyper-V VM、Windows 和 Linux 物理服务器。
**复制计算机需要哪些操作系统？** | VMware VM [支持的操作系统](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)<br/><br/> 对于 Hyper-V VM，支持 Azure 和 Hyper-V 所支持的任何[来宾 OS](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)。<br/><br/> 适用于物理服务器的[操作系统](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
**可复制到何处？** | 复制到 Azure 存储或辅助数据中心<br/><br/> 对于 Hyper-V，仅 System Center VMM 云中托管的 Hyper-V 主机上的 VM 才能复制到辅助数据中心。
**需要哪些 VMware 服务器/主机？** | 可通过[支持的 vSphere 主机/vCenter 服务器](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)托管想要复制的 VMware VM
**可复制哪些工作负荷** | 可复制支持的复制计算机上运行的任何工作负荷。 另外，Site Recovery 团队已针对[多个应用](site-recovery-workload.md#workload-summary)执行特定于应用的测试。


## <a name="which-azure-portal"></a>哪个 Azure 门户？

* 在较新的 [Azure 门户](https://portal.azure.com)或 [Azure 经典门户](https://manage.windowsazure.com/)中均可部署 Site Recovery。
* 在 Azure 经典门户中，可使用经典服务管理模型支持 Site Recovery。
* 在 Azure 门户中，可以支持经典模型或较新的 [Resource Manager 部署模型](../azure-resource-manager/resource-manager-deployment-model.md)。
- 经典门户应仅用于维护现有 Site Recovery 部署。 无法在经典门户中创建新的保管库。

## <a name="next-steps"></a>后续步骤
* 阅读有关[工作负荷支持](site-recovery-workload.md)的更多内容
* 详细了解 [Site Recovery 体系结构和组件](site-recovery-components.md)

