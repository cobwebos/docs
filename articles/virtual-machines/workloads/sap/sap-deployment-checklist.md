---
title: SAP 工作负荷规划和部署清单 | Microsoft Docs
description: 用于规划 SAP 工作负载部署到 Azure 和部署工作负荷的检查表
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b8c1b0bcc74d73f1f869972488ba7c5dfe610d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060061"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Azure 上的 SAP 工作负载：规划和部署检查表

此检查表专为将 SAP NetWeaver、S/4HANA 和 Hybris 应用程序移至 Azure 基础结构作为服务的客户而设计。 在整个项目期间，客户和/或 SAP 合作伙伴应查看检查表。 请务必注意，许多检查是在项目开始时和规划阶段完成的。 部署完成后，对已部署的 Azure 基础结构或 SAP 软件版本进行直接更改可能会变得复杂。

在项目期间查看关键里程碑的检查表。 这样做将使您能够在小问题成为大问题之前检测到它们。 您还将有足够的时间重新设计和测试任何必要的更改。 不要认为此清单已完成。 根据您的情况，您可能需要执行更多检查。

该检查表不包括独立于 Azure 的任务。 例如，SAP 应用程序接口在移动到 Azure 平台或托管提供程序期间会更改。

此检查表还可用于已部署的系统。 自部署以来，可能添加了新功能（如写入加速器和可用性区域）和新的 VM 类型。 因此，定期查看检查表以确保了解 Azure 平台中的新功能非常有用。

## <a name="project-preparation-and-planning-phase"></a>项目准备和规划阶段
在此阶段，您可以计划将 SAP 工作负荷迁移到 Azure 平台。 至少，在此阶段，您需要创建以下文档，并定义和讨论迁移的以下元素：

1. 高级设计文档。 本文档应包含：
    - SAP 组件和应用程序的当前清单，以及 Azure 的目标应用程序清单。
    - 责任分配矩阵 （RACI），用于定义相关各方的责任和分配。 从高级别开始，在整个规划和第一次部署过程中工作到更精细的级别。
    - 高级解决方案体系结构。
    - 有关要部署到哪个 Azure 区域的决定。 请参阅[Azure 区域的列表](https://azure.microsoft.com/global-infrastructure/regions/)。 要了解每个区域提供哪些服务，请参阅[按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/)。
    - 从本地连接到 Azure 的网络体系结构。 开始熟悉 Azure 的[虚拟数据中心蓝图](https://docs.microsoft.com/azure/architecture/vdc/)。
    - 在 Azure 中运行高影响业务数据的安全原则。 要了解数据安全性，从 Azure[安全文档](https://docs.microsoft.com/azure/security/)开始。
2.  技术设计文档。 本文档应包含：
    - 解决方案的框图。
    - Azure 中计算、存储和网络组件的大小调整。 有关 Azure VM 的 SAP 大小，请参阅[SAP 支持说明#1928533](https://launchpad.support.sap.com/#/notes/1928533)。
    - 业务连续性和灾难恢复体系结构。
    - 有关操作系统、数据库、内核和 SAP 支持包版本的详细信息。 SAP NetWeaver 或 S/4HANA 支持的每个操作系统版本在 Azure VM 上并不一定如此。 DBMS 版本也是如此。 检查以下源以对齐并在必要时升级 SAP 版本、DBMS 版本和操作系统版本，以确保 SAP 和 Azure 支持。 您需要有 SAP 和 Azure 支持发布组合，才能获得 SAP 和 Microsoft 的全力支持。 如有必要，您需要计划升级某些软件组件。 有关支持的 SAP、操作系统和 DBMS 软件的更多详细信息，请在此处记录：
        - [SAP 支持说明#1928533](https://launchpad.support.sap.com/#/notes/1928533)。 本说明定义了 Azure VM 上支持的最低操作系统版本。 它还定义了大多数非 HANA 数据库所需的最小数据库版本。 最后，它为 SAP 支持的 Azure VM 类型提供了 SAP 大小调整。
        - [SAP 支持说明#2015553](https://launchpad.support.sap.com/#/notes/2015553)。 本说明定义了有关与 Microsoft 所需的 Azure 存储和支持关系的支持策略。
        - [SAP 支持说明#2039619](https://launchpad.support.sap.com/#/notes/2039619)。 本说明定义了 Azure 的 Oracle 支持矩阵。 Oracle 仅支持 Windows 和 Oracle Linux 作为 Azure 上的访客操作系统，用于 SAP 工作负载。 此支持语句也适用于运行 SAP 实例的 SAP 应用程序层。 但是，Oracle 不支持通过起搏器在 Oracle Linux 中为 SAP 中央服务提供高可用性。 如果您需要在 Oracle Linux 上为 ASCS 提供高可用性，则需要使用适用于 Linux 的 SIOS 保护套件。 有关详细的 SAP 认证数据，请参阅 #1662610 的 SAP 支持说明[- 适用于 Linux 的 SIOS 保护套件的支持详细信息](https://launchpad.support.sap.com/#/notes/1662610)。 对于 Windows，SAP 支持的 WINDOWS 服务器故障转移群集解决方案与 Oracle 作为 DBMS 层一起支持。
        - [SAP 支持说明#2235581](https://launchpad.support.sap.com/#/notes/2235581)。 本说明提供了不同操作系统版本上的 SAP HANA 支持矩阵。
        - SAP HANA 支持的 Azure VM 和[HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)列在[SAP 网站上](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。
        - [SAP 产品可用性矩阵](https://support.sap.com/en/)。
        - [SAP 支持说明#2555629 - SAP HANA 2.0 动态分层 – 虚拟机管理程序和云支持](https://launchpad.support.sap.com/#/notes/2555629)
        - [SAP 支持说明#1662610 - 适用于 Linux 的 SIOS 保护套件的支持详细信息](https://launchpad.support.sap.com/#/notes/1662610)
        - 其他 SAP 特定产品的 SAP 说明。     
    - 我们建议为 SAP 生产系统提供严格的三层设计。 我们不建议将 ASCS 和/或 DBMS 和/或应用服务器合并到一个 VM 上。 Azure 上的 Windows 来宾操作系统支持为 SAP 中央服务使用多 SID 群集配置。 但是，在 Azure 上的 Linux 操作系统上的 SAP 中央服务不支持此配置。 您可以在以下文章中找到 Windows 来宾操作系统方案的文档：
        - [使用 Azure 上的 Windows Server 故障转移群集和共享磁盘实现 SAP ASCS/SCS 实例多 SID 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        - [在 Azure 上使用 Windows Server 故障转移群集和文件共享实现 SAP ASCS/SCS 实例的多 SID 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    - 高可用性和灾难恢复体系结构。
        - 基于 RTO 和 RPO，定义高可用性和灾难恢复体系结构需要的外观。
        - 要在区域内获得高可用性，请查看所需的 DBMS 在 Azure 中提供的内容。 大多数 DBMS 包提供同步热备用的同步方法，我们建议用于生产系统。 还要检查不同数据库的 SAP 相关文档，从[SAP 工作负载和相关文档的 Azure 虚拟机 DBMS 部署注意事项](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)开始。
           不支持使用 Windows Server 故障转移群集与 DBMS 层的共享磁盘配置，例如，[对于 SQL Server 所述](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017)。 相反，请使用以下解决方案：
           - [SQL Server Always On](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle 数据防护](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
           - [HANA 系统复制](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - 有关跨 Azure 区域的灾难恢复，请查看不同 DBMS 供应商提供的解决方案。 它们大多支持异步复制或日志传送。
        - 对于 SAP 应用程序层，确定是运行业务回归测试系统（理想情况下是生产部署的副本、在同一 Azure 区域中还是在 DR 区域中）。 在第二种情况下，您可以将该业务回归系统作为生产部署的 DR 目标。
        - 如果决定不将非生产系统放在 DR 站点中，请查看 Azure 站点恢复，作为将 SAP 应用程序层复制到 Azure DR 区域的方法。 有关详细信息，请参阅[为多层 SAP NetWeaver 应用部署设置灾难恢复](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)。
        - 如果您决定使用[Azure 可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview)使用组合的 HADR 配置，请熟悉可用区域的 Azure 区域。 还要考虑两个可用区域之间网络延迟增加可能引入的限制。  
3.  所有 SAP 接口（SAP 和非 SAP）的清单。
4.  基础服务的设计。 此设计应包括以下项目：
    - 活动目录和 DNS 设计。
    - Azure 中的网络拓扑和分配不同的 SAP 系统。
    - Azure 中管理基础结构和 SAP 应用程序的团队基于[角色的访问](https://docs.microsoft.com/azure/role-based-access-control/overview)结构。
    - 资源组拓扑。
    - [标记策略](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)。
    - 命名 VM 和其他基础结构组件和/或逻辑名称的约定。
5.  微软卓越支持合同。 确定您的 Microsoft 技术客户经理 （TAM）。 有关 SAP 支持要求，请参阅[SAP 支持说明#2015553](https://launchpad.support.sap.com/#/notes/2015553)。
6.  订阅的 Azure 订阅数和核心配额数。 [打开支持请求，根据需要增加 Azure 订阅的配额](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。
7.  用于将 SAP 数据迁移到 Azure 的数据缩减和数据迁移计划。 对于 SAP NetWeaver 系统，SAP 有有关如何限制大量数据量的指导原则。 请参阅[此 SAP 指南](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2)，了解 SAP ERP 系统中的数据管理。 部分内容也适用于 NetWeaver 和 S/4HANA 系统。
8.  自动部署方法。 Azure 上基础结构部署自动化的目标是以确定性的方式进行部署，并取得确定性结果。 许多客户使用基于 PowerShell 或基于 CLI 的脚本。 但是，可以使用各种开源技术来部署用于 SAP 的 Azure 基础结构，甚至安装 SAP 软件。 您可以在 GitHub 上找到示例：
    - [Azure 云中的自动 SAP 部署](https://github.com/Azure/sap-hana)
    - [SAP HANA 安装](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  将客户、系统集成商、Microsoft 和其他相关方之间的常规设计和部署审核节奏定义。

 
## <a name="pilot-phase-strongly-recommended"></a>试验阶段（强烈建议）
 
您可以在项目规划和准备之前或期间运行试验。 您还可以使用试验阶段来测试在规划和准备阶段制定的方法和设计。 您可以扩展试验阶段，使其成为概念的真正证明。

我们建议您在试验部署期间设置和验证完整的 HADR 解决方案和安全设计。 在此阶段，某些客户执行可伸缩性测试。 其他客户使用 SAP 沙盒系统的部署作为试验阶段。 我们假设您已经确定了要迁移到 Azure 进行试验的系统。

1. 优化数据传输到 Azure。 最佳选择高度依赖于特定方案。 如果 ExpressRoute 电路具有足够的带宽，则通过[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)从本地传输速度最快。 在其他情况下，通过互联网传输更快。
2. 对于涉及数据导出和导入的异构 SAP 平台迁移，测试和优化导出和导入阶段。 对于 SQL Server 是目标平台的大型迁移，可以找到[建议](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070)。 如果您不需要合并版本升级，则可以使用迁移监视器/SWPM。 将迁移与 SAP 版本升级相结合时，可以使用[SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/)流程。 为此，您需要满足源和目标 DBMS 平台组合的某些要求。 此过程记录在[SUM 2.0 SP03 的数据库迁移选项 （DMO） 中](https://launchpad.support.sap.com/#/notes/2631152)。
   1.  导出到源，将文件上载导出到 Azure，并导入性能。 最大化导出和导入之间的重叠。
   2.  评估目标平台和目标平台上数据库的容量，以便进行基础结构大小调整。
   3.  验证和优化计时。
1. 技术验证。
   1. VM 类型。
        - 再次查看 SAP 支持说明、SAP HANA 硬件目录中和 SAP PAM 中的资源。 确保没有更改 Azure 支持的 VM、这些 VM 类型的受支持操作系统版本以及支持的 SAP 和 DBMS 版本。
        - 再次验证 Azure 上部署的应用程序和基础结构的大小。 如果要移动现有应用程序，通常可以从您使用的基础结构和[SAP 基准网页](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd)派生必要的 SAPS，并将其与[SAP 支持说明中列出的 SAPS 编号进行比较，#1928533](https://launchpad.support.sap.com/#/notes/1928533)。 还要记住[关于 SAPS 评级的文章](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)。
        - 根据规划阶段选择的 VM 类型的最大存储吞吐量和网络吞吐量评估和测试 Azure VM 的大小。 您可以在此处找到数据：
           -  [Azure 中的 Windows 虚拟机的大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)。 请务必考虑*最大未缓存磁盘吞吐量*，以便调整大小。
           -  [Azure 中的 Linux 虚拟机的大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)。 请务必考虑*最大未缓存磁盘吞吐量*，以便调整大小。
   2. 存储。
        - 至少，对表示 SAP 应用程序层的 VM 以及部署不对性能敏感的 DBMS 使用[Azure 标准 SSD 存储](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)。
        - 通常，我们不建议使用[Azure 标准硬盘磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)。
        - 对远程性能敏感的任何 DBMS VM 使用[Azure 高级存储](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)。
        - 使用[Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。
        - 对于使用 M 系列的 DBMS 日志驱动器，请使用 Azure 写入加速器。 请注意写入加速器的限制和用法，如[写入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)中所述。
        - 对于不同的 DBMS 类型，请查看通用文档指向的[通用 SAP 相关 DBMS 文档](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)和特定于 DBMS 的文档。
        - 有关 SAP HANA 的详细信息，请参阅[SAP HANA 基础结构配置和 Azure 上的操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)。
        - 请勿使用设备 ID 将 Azure 数据磁盘装载到 Azure Linux VM 中。 而应该使用全局唯一标识符 (UUID)。 例如，使用图形化工具装载 Azure 数据磁盘时应小心。 仔细检查 /etc/fstab 中的条目，以确保 UUID 用于装载磁盘。 你可以[在本文](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)中找到更多的细节。
   3. 联网。
        - 测试和评估虚拟网络基础结构以及 SAP 应用程序在不同 Azure 虚拟网络之间或内部的分布情况。
        -  评估中心分支虚拟网络体系结构方法或单个 Azure 虚拟网络中的微分段方法。 此评估的基础是：
               1. [对等 Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)之间的数据交换成本。 有关成本的信息，请参阅[虚拟网络定价](https://azure.microsoft.com/pricing/details/virtual-network/)。
               2. 快速断开 Azure 虚拟网络之间的对等互连，而不是更改网络安全组以隔离虚拟网络中的子网。 此评估适用于托管在虚拟网络子网中的应用程序或 VM 成为安全风险的情况。
                3. 本地、外部世界和您在 Azure 中构建的虚拟数据中心之间的网络流量的中央日志记录和审核。
        - 评估和测试 SAP 应用程序层和 SAP DBMS 层之间的数据路径。
            -  不支持将[Azure 网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)放置在 SAP 应用程序和基于 SAP NetWeaver、Hybris 或 S/4HANA 的 SAP 系统的 DBMS 层之间的通信路径中。
            -  不支持将 SAP 应用程序层和 SAP DBMS 放置在不同的 Azure 虚拟网络中，这些网络不进行对等处理。
            -  您可以使用[应用程序安全组和网络安全组规则](https://docs.microsoft.com/azure/virtual-network/security-overview)定义 SAP 应用程序层和 SAP DBMS 层之间的路由。
        - 确保在 SAP 应用程序层和 SAP DBMS 层中使用的 VM 上启用[Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。 注意，需要不同的 OS 级别来支持 Azure 中的加速网络：
            - Windows Server 2012 R2 或更高版本。
            - SUSE Linux 12 SP3 或更高版本。
            - RHEL 7.4 或更高版本。
            - Oracle Linux 7.5。 如果您使用的是 RHCKL 内核，则需要版本 3.10.0-862.13.1.el7。 如果您使用的是 Oracle UEK 内核，则需要第 5 个版本。
        - 根据 SAP 支持说明[#500235](https://launchpad.support.sap.com/#/notes/500235)和[#1100926，](https://launchpad.support.sap.com/#/notes/1100926/E)测试和评估 SAP 应用程序层 VM 和 DBMS VM 之间的网络延迟。 根据[SAP 支持说明#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中的网络延迟指南评估结果。 网络延迟应处于中等或良好范围内。 例外情况适用于 VM 和 HANA 大型实例单元之间的流量，[如本文](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)所述。
        - 确保将 ILB 部署设置为使用直接服务器返回。 当 Azure ILB 用于 DBMS 层上的高可用性配置时，此设置将减少延迟。
        - 如果将 Azure 负载均衡器与 Linux 来宾操作系统一起使用，请检查 Linux 网络参数**net.ipv4.tcp_timestamps**是否设置为**0**。 此建议与旧版本的[SAP 注释#2382421](https://launchpad.support.sap.com/#/notes/2382421)中的建议相冲突。 SAP 注释现在更新为说明需要将此参数设置为**0**才能与 Azure 负载均衡器配合使用。
        - 请考虑使用[Azure 接近放置组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)来获得最佳网络延迟。 有关详细信息，请参阅[Azure 接近放置组，了解 SAP 应用程序的最佳网络延迟](sap-proximity-placement-scenarios.md)。
   4. 高可用性和灾难恢复部署。
        - 如果在未定义特定的 Azure 可用性区域的情况下部署 SAP 应用程序层，请确保在[可用性集中](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)部署了运行 SAP 对话框实例的所有 VM 或单个 SAP 系统的中间件实例。
        - 如果 SAP 中央服务和 DBMS 不需要高可用性，则可以将这些 VM 部署到与 SAP 应用程序层相同的可用性集中。
        - 如果使用被动复制保护 SAP 中央服务和 DBMS 层以获得高可用性，则将 SAP 中央服务的两个节点放在一个单独的可用性集中集中集中集中集，将两个 DBMS 节点放在另一个可用性集中。
        - 如果部署到 Azure 可用性区域，则无法使用可用性集。 但是，您确实需要确保将主动和被动的中央服务节点部署到两个不同的可用性区域。 使用它们之间的延迟最低的可用区域。
          请记住，您需要使用[Azure 标准负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)来使用跨可用性区域为 DBMS 和 SAP 中央服务层建立 Windows 或 Pacemaker 故障转移群集的用例。 不能将[基本负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)用于地区部署。
   5. 超时设置。
        - 检查 SAP NetWeaver 开发人员对 SAP 实例的跟踪，以确保在排队服务器和 SAP 工作流程之间没有连接中断。 通过设置这两个注册表参数，可以避免这些连接中断：
            - HKLM_SYSTEM_当前控制集_服务\Tcpip_参数\保持生存时间 = 120000。 有关详细信息，请参阅[保持生存时间](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))。
            - HKLM_SYSTEM_当前控制集_服务\Tcpip_参数\保持Alive间隔 = 120000。 有关详细信息，请参阅[保持活动间隔](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10))。
        - 为避免本地 SAP GUI 接口和部署在 Azure 中的 SAP 应用程序层之间的 GUI 超时，请检查这些参数是在默认值.pfl 还是实例配置文件中设置的：
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - 为了防止中断 SAP 排队流程和 SAP 工作流程之间的已建立连接，您需要将**enque/encni/set_so_keepalive**参数设置为**true**。 另请参阅[SAP 注释#2743751](https://launchpad.support.sap.com/#/notes/2743751)。  
        - 如果使用 Windows 故障转移群集配置，请确保为 Azure 正确设置对无响应节点做出反应的时间。 "[调整故障转移群集网络阈值"](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834)一文列出了参数及其如何影响故障转移敏感性。 假设群集节点位于同一子网中，则应更改以下参数：
            - 相同的子网延迟 = 2000
            - SameSubNetThreshold = 15
            - 路由历史记录长度 = 30
    6. 操作系统设置或修补程序
        - 要在 SAP 上运行 HANA，请阅读以下注释和文档：
            -   [SAP 支持说明#2814271 - SAP HANA 备份在 Azure 上失败，但检查与错误](https://launchpad.support.sap.com/#/notes/2814271)
            -   [SAP 支持说明#2753418 - 由于计时器回退可能导致性能下降](https://launchpad.support.sap.com/#/notes/2753418)
            -   [SAP 支持说明#2791572 - 由于 Azure 中缺少对超 V 的 VDSO 支持，性能下降](https://launchpad.support.sap.com/#/notes/2791572)
            -   [SAP 支持说明#2382421 - 在 HANA 和操作系统级别上优化网络配置](https://launchpad.support.sap.com/#/notes/2382421)
            -   [#2694118 SAP 支持说明 - Azure 上的红帽企业 Linux HA 附加组件](https://launchpad.support.sap.com/#/notes/2694118)
            -   [#1984787 SAP 支持说明 - SUSE LINUX 企业服务器 12：安装说明](https://launchpad.support.sap.com/#/notes/1984787)
            -   [SAP 支持说明#2002167 - 红帽企业 Linux 7.x：安装和升级](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [SAP support note #2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690)（SAP 支持说明 #2292690 - SAP HANA DB：RHEL 7 的建议 OS 设置）
            -   [SAP 支持说明#2772999 - 红帽企业 Linux 8.x：安装和配置](https://launchpad.support.sap.com/#/notes/2772999)
            -   [SAP 支持说明#2777782 - SAP HANA DB：RHEL 8 的推荐操作系统设置](https://launchpad.support.sap.com/#/notes/2777782)
            -   [SAP 支持说明#2578899 - SUSE Linux 企业服务器 15：安装说明](https://launchpad.support.sap.com/#/notes/2578899)
            -   [SAP 支持说明#2455582 - Linux：运行使用 GCC 6.x 编译的 SAP 应用程序](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [SAP 支持说明 #2729475 - HWCCT 失败，在经过 SAP HANA 认证的 Azure VM 上出现错误"不支持虚拟机管理程序"](https://launchpad.support.sap.com/#/notes/2729475)
1. 测试高可用性和灾难恢复过程。
   1. 通过关闭 VM（Windows 来宾操作系统）或将操作系统置于映像模式（Linux 来宾操作系统）来模拟故障转移情况。 此步骤将帮助您确定故障转移配置是否按照设计工作。
   1. 测量执行故障转移所需的时间。 如果时间太长，请考虑：
        - 对于 SUSE Linux，请使用 SBD 设备而不是 Azure 围栏代理来加快故障转移。
        - 对于 SAP HANA，如果重新加载数据时间过长，请考虑预配更多存储带宽。
   3. 测试备份/恢复顺序和计时，并在需要时进行更正。 确保备份时间已足够。 您还需要测试还原和时间还原活动。 无论 RTO 依赖于数据库或 VM 还原过程，请确保还原时间在 RTO SLÉ 范围内。
   4. 测试跨区域 DR 功能和体系结构。
1. 安全检查。
   1. 测试基于 Azure 角色的访问控制 （RBAC） 体系结构的有效性。 目标是分离和限制不同团队的访问权限和权限。 例如，SAP Basis 团队成员应该能够部署 VM 并将 Azure 存储中的磁盘分配给给定的 Azure 虚拟网络。 但是 SAP Basis 团队不应能够创建自己的虚拟网络或更改现有虚拟网络的设置。 网络团队成员不应能够将 VM 部署到运行 SAP 应用程序和 DBMS VM 的虚拟网络中。 此团队成员也不应更改 VM 的属性，甚至删除 VM 或磁盘。  
   1.  验证[网络安全组和 ASC](https://docs.microsoft.com/azure/virtual-network/security-overview)规则是否按预期工作，并保护受保护的资源。
   1.  确保所有需要加密的资源都已加密。 定义和实现进程以备份证书、存储和访问这些证书以及还原加密的实体。
   1.  从操作系统支持的角度来看，尽可能对操作系统磁盘使用[Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq)。
   1.  确保您没有使用太多的加密层。 在某些情况下，将 Azure 磁盘加密与 DBMS 透明数据加密方法之一一起使用以保护同一服务器上的不同磁盘或组件确实有意义。  例如，在 SAP DBMS 服务器上，可以在操作系统引导磁盘（如果操作系统支持 ADE）上启用 Azure 磁盘加密 （ADE），并且这些数据磁盘（如果支持 ADE）和 DBMS 数据持久性文件未使用的数据磁盘。  例如，在持有 DBMS TDE 加密密钥的磁盘上使用 ADE。
1. 性能测试。 在 SAP 中，基于 SAP 跟踪和测量，进行以下比较：
   - 在适用的情况下，将前 10 个在线报告与当前实施情况进行比较。
   - 在适用的情况下，将前 10 个批处理作业与当前实现进行比较。
   - 比较通过接口传输到 SAP 系统的数据传输。 关注知道传输在不同位置（如从本地到 Azure）之间的接口。


## <a name="non-production-phase"></a>非生产阶段 
在此阶段，我们假定在成功进行试验或概念验证 （POC） 后，您开始将非生产 SAP 系统部署到 Azure。 将您在 POC 期间学到的和体验的所有内容都整合到此部署中。 为 POC 列出的所有条件和步骤也适用于此部署。

在此阶段，您通常将开发系统、单元测试系统和业务回归测试系统部署到 Azure。 我们建议在一个 SAP 应用程序行中至少有一个非生产系统具有未来生产系统将具有的完整高可用性配置。 以下是在此阶段需要完成的其他步骤：  

1.  在将系统从旧平台移动到 Azure 之前，收集资源消耗数据，如 CPU 使用情况、存储吞吐量和 IOPS 数据。 特别是从 DBMS 层单元收集此数据，但也从应用程序层单元中收集这些数据。 此外，还应测量网络和存储延迟。
2.  记录系统的可用性使用时间模式。 目标是确定非生产系统是否需要全天、每天可用，或者是否有非生产系统可以在一周或每月的特定阶段关闭。
3.  测试并确定是要为 Azure 中的 VM 创建自己的操作系统映像，还是希望使用 Azure 共享映像库中的图像。 如果您使用的是共享映像库中的图像，请确保使用反映与操作系统供应商的支持合同的图像。 对于某些操作系统供应商，共享映像库允许您自带许可证映像。 对于其他操作系统映像，支持包含在 Azure 引用的价格中。 如果决定创建自己的 OS 映像，可以在这些文章中找到文档：
    -   [生成部署在 Azure 中的 Windows VM 的通用映像](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    -   [构建部署在 Azure 中的 Linux VM 的通用映像](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  如果您使用共享映像库中的 SUSE 和红帽 Linux 映像，则需要在共享映像库中为 Linux 供应商提供的 SAP 使用映像。
4.  确保满足 Microsoft 支持协议的 SAP 支持要求。 请参阅[SAP 支持说明#2015553](https://launchpad.support.sap.com/#/notes/2015553)。 有关 HANA 大型实例，请参阅[载入要求](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)。
4.  确保合适的人员获得[计划维护通知](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/)，以便您可以选择最佳停机时间。
5.  经常检查[通道 9](https://channel9.msdn.com/)等通道上的 Azure 演示文稿，了解可能适用于部署的新功能。
6.  检查与 Azure 相关的 SAP 注释，如[支持注释#1928533](https://launchpad.support.sap.com/#/notes/1928533)，了解新的 VM SKU 和新支持的操作系统和 DBMS 版本。 将新 VM 类型的定价与较旧的 VM 类型的定价进行比较，以便部署具有最佳性价比的 VM。
7.  重新检查 SAP 支持说明、SAP HANA 硬件目录和 SAP PAM。 确保 Azure 支持的 VM 中没有更改，这些 VM 上支持操作系统版本，以及支持的 SAP 和 DBMS 版本。
8.  [查看 SAP 网站](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)，了解 Azure 中新的 HANA 认证 SKU。 将新 SKU 的定价与您计划使用的 SKU 进行比较。 最终，进行必要的更改，以使用具有最佳性价比的更改。
9.  调整部署脚本以使用新的 VM 类型并合并要使用的新 Azure 功能。
10. 部署基础结构后，根据 SAP 支持说明[#500235](https://launchpad.support.sap.com/#/notes/500235)和[#1100926，](https://launchpad.support.sap.com/#/notes/1100926/E)测试和评估 SAP 应用程序层 VM 和 DBMS VM 之间的网络延迟。 根据[SAP 支持说明#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中的网络延迟指南评估结果。 网络延迟应处于中等或良好范围内。 例外情况适用于 VM 和 HANA 大型实例单元之间的流量，[如本文](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)所述。 确保[Azure 虚拟机 DBMS 部署中针对 SAP 工作负荷](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations)和 AZURE 上的 SAP [HANA 基础结构配置和操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)的注意事项中提到的任何限制都不适用于您的部署。
11. 确保 VM 部署到正确的[Azure 接近放置组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)，如[Azure 接近放置组中所述，以便使用 SAP 应用程序实现最佳网络延迟](sap-proximity-placement-scenarios.md)。
11. 在应用工作负载之前，执行为概念验证阶段列出的所有其他检查。
12. 当工作负荷应用时，在 Azure 中记录系统的资源消耗。 将此消耗与旧平台的记录进行比较。 如果您发现存在较大差异，请调整未来部署的 VM 大小。 请记住，当您缩小 VM 的尺寸、存储和网络带宽时，也会减少这些带宽。
    - [Azure 中 Windows 虚拟机的大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Azure 中 Linux 虚拟机的大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. 试验系统复制功能和流程。 目标是让您轻松复制开发系统或测试系统，以便项目团队能够快速获得新系统。 请考虑将[SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance)用于这些任务。
14. 优化和磨练团队的基于 Azure 的访问权限、权限和流程，以确保职责分离。 同时，请确保所有团队都可以在 Azure 基础结构中执行任务。
15. 练习、测试和记录高可用性和灾难恢复过程，使员工能够执行这些任务。 识别缺点并调整要集成到部署中的 Azure 新功能。

 
## <a name="production-preparation-phase"></a>生产准备阶段 
在此阶段，收集您在非生产部署期间所体验和学习的内容，并将其应用于未来的生产部署。 您还需要准备当前托管位置和 Azure 之间的数据传输工作。

1.  在迁移到 Azure 之前，完成生产系统的必要 SAP 版本升级。
1.  就生产系统迁移后需要执行的功能和业务测试与业务所有者达成一致。
1.  确保这些测试与当前托管位置的源系统一起完成。 避免在系统移动到 Azure 后首次执行测试。
1.  测试将生产系统迁移到 Azure 的过程。 如果未在同一时间段内将所有生产系统移动到 Azure，则生成需要位于同一托管位置的生产系统组。 测试数据迁移。 下面是一些常见方法：
    - 使用 DBMS 方法，如备份/还原与 SQL Server 始终打开、HANA 系统复制或日志传送到 Azure 中的数据库内容种子和同步。
    - 对较小的数据库使用备份/还原。
    - 使用集成到 SAP SWPM 中的 SAP 迁移监视器执行异构迁移。
    - 如果需要将迁移与 SAP 版本升级相结合，请使用[SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/)流程。 请记住，并非所有源 DBMS 和目标 DBMS 的组合都受支持。 您可以在 DMO 不同版本的特定 SAP 支持说明中找到更多信息。 例如[，SUM 2.0 SP04 的数据库迁移选项 （DMO）](https://launchpad.support.sap.com/#/notes/2644872)。
    - 测试数据传输吞吐量是否通过互联网或 ExpressRoute 更好，以防需要移动备份或 SAP 导出文件。 如果要通过 Internet 传输数据，则可能需要更改未来生产系统需要具备的一些网络安全组/应用程序安全组规则。
1.  在将系统从旧平台移动到 Azure 之前，请收集资源消耗数据。 有用的数据包括 CPU 使用率、存储吞吐量和 IOPS 数据。 特别是从 DBMS 层单元收集此数据，但也从应用程序层单元中收集这些数据。 此外，还应测量网络和存储延迟。
1.  重新检查 SAP 支持说明和所需的操作系统设置、SAP HANA 硬件目录和 SAP PAM。 确保 Azure 支持的 VM 中没有更改，这些 VM 中支持操作系统版本，以及支持的 SAP 和 DBMS 版本。
1.  更新部署脚本，以考虑您在 VM 类型和 Azure 功能方面做出的最新决策。
1.  部署基础结构和应用程序后，验证：
    - 部署了正确的 VM 类型，具有正确的属性和存储大小。
    - VM 位于正确和所需的操作系统版本和修补程序上，并且是一致的。
    - VM 根据需要以统一的方式进行硬化。
    - 安装并部署了正确的应用程序版本和修补程序。
    - VM 已按计划部署到 Azure 可用性集中。
    - Azure 高级存储用于延迟敏感磁盘或需要[99.9% 的单 VM SLA。](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)
    - Azure 写入加速器部署正确。
        - 确保在 VM 内，存储空间或条带集在需要写入加速器的磁盘上正确构建。
        - 检查[Linux上软件RAID的配置](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)。
        - 检查[Azure 中的 Linux VM 上的 LVM 配置](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)。
    - [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)是专门使用的。
    - VM 已部署到正确的可用性集和可用性区域。
    - [Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)在 SAP 应用程序层和 SAP DBMS 层中使用的 VM 上启用。
    - SAP 应用程序与基于 SAP NetWeaver、Hybris 或 S/4HANA 的 SAP 系统的 DBMS 层之间的通信路径中没有[Azure 网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)。
    - 应用程序安全组和网络安全组规则允许根据需要进行通信，并根据需要阻止通信。
    - 超时设置设置设置正确，如前面所述。
    - VM 部署到正确的 Azure[接近放置组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)，如[Azure 接近放置组中所述，以便使用 SAP 应用程序实现最佳网络延迟](sap-proximity-placement-scenarios.md)。
    - SAP 应用程序层 VM 和 DBMS VM 之间的网络延迟[#500235和](https://launchpad.support.sap.com/#/notes/500235)[#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)的 SAP 支持说明中所述。 根据[SAP 支持说明#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中的网络延迟指南评估结果。 网络延迟应处于中等或良好范围内。 例外情况适用于 VM 和 HANA 大型实例单元之间的流量，[如本文](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)所述。
    - 加密是在必要时使用适当的加密方法实现的。
    - 接口和其他应用程序可以连接新部署的基础结构。
1.  创建行动手册，以便对计划的 Azure 维护做出反应。 确定应重新启动系统和 VM 以进行计划维护的顺序。
    

## <a name="go-live-phase"></a>上线阶段
在上线阶段，请务必遵循您在早期阶段开发的剧本。 执行测试和实践的步骤。 不接受配置和流程的最后一分钟更改。 还要完成以下步骤：

1. 验证 Azure 门户监视和其他监视工具是否正常工作。 我们建议为 Windows 和 Linux SAR 提供 Windows 性能监视器（perfmon）。
    - CPU 计数器。
        - 平均 CPU 时间，总计（所有 CPU）
        - 每个处理器的平均 CPU 时间（M128 VM 上的 128 个处理器）
        - CPU 内核时间，每个处理器
        - CPU用户时间，每个处理器
    - 内存。
        - 免费内存
        - 内存页（以/秒表示）
        - 内存页出/秒
    - 。
        - 每个磁盘以 KBps 读取磁盘
        - 每个磁盘的磁盘读取/秒
        - 每个磁盘以微秒/读取为单位读取磁盘
        - 磁盘写入 KBps，每个磁盘
        - 每个磁盘的磁盘写入/秒
        - 磁盘写入微秒/读取，每个磁盘
    - Network.
        - 网络数据包（以/秒表示）
        - 网络数据包出/秒
        - 网络 KB 以/秒为单位
        - 网络 KB 出/秒
1.  数据迁移后，执行您与业务所有者商定的所有验证测试。 仅当原始源系统有结果时，才接受验证测试结果。
1.  检查接口是否正常工作，以及其他应用程序是否可以与新部署的生产系统通信。
1.  通过 SAP 事务 STMS 检查运输和校正系统。
1.  在系统发布以进行生产后执行数据库备份。
1.  在发布系统进行生产后，对 SAP 应用程序层 VM 执行 VM 备份。
1.  对于不属于当前上线阶段的 SAP 系统，但在此上线阶段与迁移到 Azure 的 SAP 系统通信，需要在 SM51 中重置主机名缓冲区。 这样做将删除与移动到 Azure 的应用程序实例的名称关联的旧缓存 IP 地址。  


## <a name="post-production"></a>后期生产阶段
此阶段是关于监视、操作和管理系统。 从 SAP 的角度来看，在旧托管位置中完成所需的常见任务适用。 也完成这些特定于 Azure 的任务：

1. 查看高收费系统的 Azure 发票。
2. 优化 VM 端和存储端的价格/性能效率。
3. 优化可以关闭系统的时间。  


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [SAP NetWeaver 的 Azure 虚拟机规划和实施指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [适用于 SAP NetWeaver 的 Azure 虚拟机部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [用于 SAP 工作负载的 Azure 虚拟机 DBMS 部署注意事项](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

