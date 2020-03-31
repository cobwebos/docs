---
title: 有关使用 Azure 站点恢复的本地应用的灾难恢复
description: 介绍可以通过将灾难恢复与 Azure Site Recovery 服务配合使用来保护的工作负荷。
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062839"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>关于本地应用的灾难恢复

本文介绍了使用[Azure 站点恢复](site-recovery-overview.md)服务保护灾难恢复的本地工作负荷和应用。

## <a name="overview"></a>概述

组织需要业务连续性和灾难恢复 （BCDR） 策略，以确保工作负载和数据在计划内和计划外停机期间的安全以及可用。 并且，恢复到正常的工作条件。

站点恢复就是能够帮助实现 BCDR 策略的一个 Azure 服务。 使用站点恢复，可将应用程序感知的复制部署到云或辅助站点中。 您可以使用站点恢复来管理复制、执行灾难恢复测试以及运行故障转移和故障恢复。 你的应用可以在 Windows 或基于 Linux 的计算机、物理服务器、VMware 或 Hyper-V 上运行。

Site Recovery 与 Microsoft 应用程序集成，例如 SharePoint、Exchange、Dynamics、SQL Server 和 Active Directory。 微软与 Oracle、SAP 和红帽等领先供应商密切合作。 可以针对每个应用自定义复制解决方案。

## <a name="why-use-site-recovery-for-application-replication"></a>为何要使用站点恢复来复制应用程序？

站点恢复可帮助实现应用程序级的保护和恢复，如下所述：

- 与应用无关，并为在受支持的计算机上运行的任何工作负载提供复制。
- 近同步复制，恢复点目标 （RPO） 低至 30 秒，可满足大多数关键业务应用的需求。
- 针对单层或多层应用程序的应用一致性快照。
- 与 SQL Server AlwaysOn 集成，并与其他应用程序级复制技术合作。 例如，活动目录复制、SQL Alwayson 和交换数据库可用性组 （DG）。
- 灵活的恢复计划，使您能够只需单击一下即可恢复整个应用程序堆栈，并在计划中包括外部脚本和手动操作。
- 站点恢复和 Azure 中的高级网络管理，以简化应用网络要求。 网络管理，例如保留 IP 地址、配置负载平衡以及与 Azure 流量管理器集成以实现低恢复时间目标 （RTO） 网络切换的能力。
- 丰富的自动化库，提供特定于应用程序的生产就绪型脚本，可以下载并与恢复计划集成。

## <a name="workload-summary"></a>工作负荷摘要

站点恢复可复制受支持计算机上运行的任何应用。 我们与产品团队合作，对下表中指定的应用执行其他测试。

| **工作量** |**将 Azure VM 复制到 Azure** |**将 Hyper-V VM 复制到辅助站点** | **将 Hyper-V VM 复制到 Azure** | **将 VMware VM 复制到辅助站点** | **将 VMware VM 复制到 Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory、DNS |是 |是 |是 |是 |是|
| Web 应用（IIS、SQL） |是 |是 |是 |是 |是|
| System Center Operations Manager |是 |是 |是 |是 |是|
| SharePoint |是 |是 |是 |是 |是|
| SAP<br/><br/>将非群集 SAP 站点复制到 Azure |是（由微软测试） |是（由微软测试） |是（由微软测试） |是（由微软测试） |是（由微软测试）|
| Exchange（非 DAG） |是 |是 |是 |是 |是|
| 远程桌面/VDI |是 |是 |是 |是 |是|
| Linux（操作系统和应用） |是（由微软测试） |是（由微软测试） |是（由微软测试） |是（由微软测试） |是（由微软测试）|
| Dynamics AX |是 |是 |是 |是 |是|
| Windows 文件服务器 |是 |是 |是 |是 |是|
| Citrix XenApp 和 XenDesktop |是|空值 |是 |空值 |是 |

## <a name="replicate-active-directory-and-dns"></a>复制 Active Directory 和 DNS

Active Directory 和 DNS 基础结构对于大多数企业应用而言至关重要。 在灾难恢复期间，您需要在恢复工作负载和应用程序之前保护和恢复这些基础结构组件。

可以使用站点恢复，为 Active Directory 和 DNS 创建一个完整的自动化灾难恢复计划。 例如，要将 SharePoint 和 SAP 从主站点故障转移到辅助站点，可以设置恢复计划，该计划首先在活动目录上失败。 然后，使用其他特定于应用的恢复计划来故障接管依赖于 Active Directory 的其他应用。

[详细了解](site-recovery-active-directory.md)活动目录和 DNS 的灾难恢复。

## <a name="protect-sql-server"></a>保护 SQL Server

SQL Server 为本地数据中心中的许多业务应用提供了数据服务基础。 站点恢复可与 SQL Server HA/DR 技术一起使用，以保护使用 SQL Server 的多层企业应用。

Site Recovery 提供：

- 为 SQL Server 提供简单且经济高效的灾难恢复解决方案。 将多个版本的 SQL Server 独立服务器和群集复制到 Azure 或辅助站点。
- 集成 SQL AlwaysOn 可用性组，使用 Azure Site Recovery 恢复计划管理故障转移和故障回复。
- 适用于应用程序中所有层（包括 SQL Server 数据库）的端到端恢复计划。
- _通过将_SQL Server 转换为 Azure 中较大的 IaaS 虚拟机大小，通过站点恢复扩展 SQL Server 以达到峰值负载。
- 简单的 SQL Server 灾难恢复测试。 可以运行测试故障转移来分析数据，并可以运行合规性检查，且不影响生产环境。

了解有关 SQL 服务器灾难恢复[的更多](site-recovery-sql.md)信息。

## <a name="protect-sharepoint"></a>保护 SharePoint

Azure Site Recovery 可帮助保护 SharePoint 部署，如下所述：

- 消除对用于灾难恢复的备用场的需要以及相关的基础结构成本。 使用站点恢复将整个服务器场（Web、应用和数据库层）复制到 Azure 或辅助站点。
- 简化应用程序部署和管理。 部署到主站点的更新将自动复制。 在故障转移和恢复辅助站点中的服务器场后，更新可用。 降低与保持备用服务器场最新相关的管理复杂性和成本。
- 按照副本环境的需要创建一个与生产类似的副本来进行测试和调试，从而简化 SharePoint 应用程序的开发与测试。
- 使用站点恢复将 SharePoint 部署迁移到 Azure，从而简化从本地到云的过渡过程。

[详细了解](site-recovery-sharepoint.md)SharePoint 的灾难恢复。

## <a name="protect-dynamics-ax"></a>保护 Dynamics AX

Azure Site Recovery 可通过以下方式帮助保护 Dynamics AX ERP 解决方案：

- 管理整个 Dynamics AX 环境（Web 和 AOS 层、数据库层、SharePoint）对 Azure 或辅助站点的复制。
- 简化 Dynamics AX 部署到云 (Azure) 的迁移。
- 通过按需创建一个与生产类似的副本来进行测试和调试，简化 Dynamics AX 应用程序的开发与测试。

[了解有关](site-recovery-dynamicsax.md)动态 AX 灾难恢复的更多详细信息。

## <a name="protect-remote-desktop-services"></a>保护远程桌面服务

远程桌面服务 （RDS） 支持虚拟桌面基础结构 （VDI）、基于会话的桌面和应用程序，允许用户在任何地方工作。

使用 Azure 站点恢复，您可以复制以下服务：

- 将托管或非托管池虚拟桌面复制到辅助站点。
- 将远程应用程序和会话复制到辅助站点或 Azure。

下表显示了复制选项：

| **Rds** |**将 Azure VM 复制到 Azure** | **将 Hyper-V VM 复制到辅助站点** | **将 Hyper-V VM 复制到 Azure** | **将 VMware VM 复制到辅助站点** | **将 VMware VM 复制到 Azure** | **将物理服务器复制到辅助站点** | **将物理服务器复制到 Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **入池虚拟桌面（非托管）** |否|是 |否 |是 |否 |是 |否 |
| **入池虚拟桌面（托管但不包含 UPD）** |否|是 |否 |是 |否 |是 |否 |
| **远程应用程序和桌面会话（不包含 UPD）** |是|是 |是 |是 |是 |是 |是 |

[详细了解](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure)RDS 的灾难恢复。

## <a name="protect-exchange"></a>保护 Exchange

站点恢复可通过以下方式帮助保护 Exchange：

- 对于小型 Exchange 部署，例如单一服务器或服务器单机，站点恢复可以复制和故障转移到 Azure 或辅助站点。
- 对于大型部署，站点恢复可与 Exchange DAGS 集成。
- 在企业中进行 Exchange 灾难恢复时，Exchange DAG 是建议的解决方案。  站点恢复中的恢复计划可以包含 DAG，以便跨站点协调 DAG 故障转移。

要了解有关灾难恢复交换的更多，请参阅交换[DAG](/Exchange/high-availability/database-availability-groups/database-availability-groups)和[Exchange 灾难恢复](/Exchange/high-availability/disaster-recovery/disaster-recovery)。

## <a name="protect-sap"></a>保护 SAP

按如下所述使用站点恢复来保护 SAP 部署：

- 将组件复制到 Azure，以便保护在本地运行的 SAP NetWeaver 和非 NetWeaver 生产应用程序。
- 将组件复制到其他 Azure 数据中心，以便保护在 Azure 中运行的 SAP NetWeaver 和非 NetWeaver 生产应用程序。
- 使用站点恢复将 SAP 部署迁移到 Azure，从而简化云迁移。
- 通过创建一个按需生产克隆来测试 SAP 应用程序，简化 SAP 项目的升级、测试和原型制作。

[详细了解](site-recovery-sap.md)SAP 的灾难恢复。

## <a name="protect-internet-information-services"></a>保护互联网信息服务

使用站点恢复保护您的互联网信息服务 （IIS） 部署，如下所示：

Azure Site Recovery 可以将环境中的关键组件复制到冷远程站点或公有云（例如 Microsoft Azure），从而提供灾难恢复。 由于具有 Web 服务器和数据库的虚拟机已复制到恢复站点，因此无需对配置文件或证书进行单独的备份。 依赖于环境变量（在故障转移后已更改）的应用程序映射和绑定可以通过集成到灾难恢复计划中的脚本进行更新。 虚拟机仅在故障转移期间在恢复站点上启动。 Azure 站点恢复还通过提供以下功能，帮助您协调端到端故障转移：

- 顺序安排各层中虚拟机的关机和启动。
- 添加脚本，以便在虚拟机启动后更新应用程序依赖项和绑定。 也可使用脚本更新 DNS 服务器，使之指向恢复站点。
- 通过映射主网络和恢复网络并使用故障转移后不需要更新的脚本，将 IP 地址分配给虚拟机故障转移前。
- 能够为多个 Web 应用程序进行一键式故障转移，从而消除灾难期间的混乱范围。
- 能够在适用于 DR 演练的隔离环境中测试恢复计划。

[了解有关](site-recovery-iis.md)IIS 灾难恢复的更多详细信息。

## <a name="protect-citrix-xenapp-and-xendesktop"></a>保护 Citrix XenApp 和 XenDesktop

使用 Site Recovery 保护 Citrix XenApp 和 XenDesktop 部署，如下所示：

- 启用 Citrix XenApp 和 XenDesktop 部署的保护。 将不同的部署层复制到 Azure：活动目录、DNS 服务器、SQL 数据库服务器、Citrix 交付控制器、StoreFront 服务器、XenApp 主控形状 （VDA）、Citrix XenApp 许可证服务器。
- 使用 Site Recovery 将 Citrix XenApp 和 XenDesktop 部署迁移到 Azure，从而简化云迁移。
- 按需创建一个与生产类似的副本来进行测试和调试，从而简化 Citrix XenApp/XenDesktop 测试。
- 此解决方案仅适用于 Windows Server 虚拟桌面，不适用于客户端虚拟桌面。 Azure 中尚不支持客户端虚拟桌面进行许可。 [了解](https://azure.microsoft.com/pricing/licensing-faq/) Azure 中的客户端/服务器桌面授权。

[详细了解](site-recovery-citrix-xenapp-and-xendesktop.md)Citrix XenApp 和 XenDesktop 部署的灾难恢复情况。 或者，您可以参考[Citrix 白皮书](https://aka.ms/citrix-xenapp-xendesktop-with-asr)。

## <a name="next-steps"></a>后续步骤

[详细了解](azure-to-azure-quickstart.md)Azure VM 的灾难恢复。
