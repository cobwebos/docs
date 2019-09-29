---
title: SAP 工作负荷规划和部署清单 | Microsoft Docs
description: 规划 SAP 工作负荷部署到 Azure 和部署工作负载的清单
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
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 34d1ba13689eb820db754c5c0d9573dcdc235205
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350822"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Azure 上的 SAP 工作负荷：规划和部署清单

此清单适用于将 SAP NetWeaver、S/4HANA 和 Hybris 应用程序迁移到 Azure 基础结构即服务的客户。 在项目的整个持续时间内，客户和/或 SAP 合作伙伴应查看清单。 需要特别注意的是，许多检查都是在项目开始和规划阶段完成的。 部署完成后，部署的 Azure 基础结构或 SAP 软件版本的直接更改可能会变得复杂。

在项目中查看关键里程碑的清单。 这样做将使你能够在出现大问题之前检测到小型问题。 你还可以有足够的时间来重新设计和测试所需的任何更改。 不要认为此清单已完成。 根据你的情况，你可能需要执行更多检查。

此清单不包含独立于 Azure 的任务。 例如，SAP 应用程序接口在移动到 Azure 平台或托管提供商期间发生更改。

此清单还可用于已部署的系统。 自部署以来，可能已添加了新功能，例如写入加速器和可用性区域以及新的 VM 类型。 因此，定期查看清单以确保你了解 Azure 平台中的新功能是非常有用的。

## <a name="project-preparation-and-planning-phase"></a>项目准备和规划阶段
在此阶段，计划将 SAP 工作负荷迁移到 Azure 平台。 在此阶段，至少需要创建以下文档，并定义和讨论以下迁移元素：

1. 高级设计文档。 本文档应包含：
    - SAP 组件和应用程序的当前清单，以及 Azure 的目标应用程序清单。
    - 定义参与方的职责和分配的责任分配矩阵（RACI）。 从较高的层次开始，并在整个规划和第一次部署中处理更精细的级别。
    - 高级解决方案体系结构。
    - 决定要将哪些 Azure 区域部署到。 请参阅[Azure 区域列表](https://azure.microsoft.com/global-infrastructure/regions/)。 若要了解每个区域中提供了哪些服务，请参阅[按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/)。
    - 用于从本地连接到 Azure 的网络体系结构。 开始熟悉[Azure 的虚拟数据中心蓝图](https://docs.microsoft.com/azure/architecture/vdc/)。
    - 用于在 Azure 中运行影响最大的业务数据的安全原则。 若要了解数据安全，请从[Azure 安全文档](https://docs.microsoft.com/azure/security/)开始。
2.  技术设计文档。 本文档应包含：
    - 解决方案的块关系图。
    - Azure 中计算、存储和网络组件的大小。 有关 Azure Vm 的 SAP 大小调整，请参阅[sap 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533)。
    - 业务连续性和灾难恢复体系结构。
    - 有关 OS、DB、内核和 SAP 支持包版本的详细信息。 Azure Vm 不支持 SAP NetWeaver 或 S/4HANA 支持的每个操作系统版本。 同样，DBMS 版本也是如此。 请检查以下源，以便在必要时将 SAP 版本、DBMS 版本和 OS 版本升级，以确保 SAP 和 Azure 支持。 你需要具有 SAP 和 Azure 支持的发布组合才能获得 SAP 和 Microsoft 的完全支持。 如有必要，你需要计划升级某些软件组件。 有关支持的 SAP、OS 和 DBMS 软件的更多详细信息，请参阅：
        - [SAP 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533)。 此注释定义 Azure Vm 支持的最低操作系统版本。 它还定义了大多数非 HANA 数据库所需的最低数据库版本。 最后，它为 SAP 支持的 Azure VM 类型提供 SAP 大小调整。
        - [SAP 支持说明 #2039619](https://launchpad.support.sap.com/#/notes/2039619)。 此注释定义了适用于 Azure 的 Oracle 支持矩阵。 Oracle 仅支持在 Azure 上为 SAP 工作负荷提供 Windows 和 Oracle Linux 作为来宾操作系统。 此支持声明还适用于运行 SAP 实例的 SAP 应用程序层。 但是，Oracle 不支持通过 Pacemaker 在 Oracle Linux 中使用 SAP 中心服务的高可用性。 如果 Oracle Linux 上的 ASCS 需要高可用性，则需要使用适用于 Linux 的 SIOS 保护套件。 有关详细的 SAP 认证数据，请参阅 SAP 支持说明[#1662610-适用于适用于 Linux 的 SIOS 保护套件的支持详细信息](https://launchpad.support.sap.com/#/notes/1662610)。 对于 Windows，支持 sap 中心服务的 SAP 支持的 Windows Server 故障转移群集解决方案，并与 Oracle 作为 DBMS 层结合。
        - [SAP 支持说明 #2235581](https://launchpad.support.sap.com/#/notes/2235581)。 此说明为不同 OS 版本的 SAP HANA 提供支持矩阵。
        - [SAP 网站](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)上列出了 SAP HANA 支持的 Azure Vm 和[HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。
        - [SAP 产品可用性矩阵](https://support.sap.com/en/)。
        - 其他特定于 SAP 的产品的 SAP 说明。     
    - 建议为 SAP 生产系统严格执行三层设计。 我们不建议将 ASCS 和 app 服务器合并到一个虚拟机上。 在 Azure 上的 Windows 来宾操作系统上支持使用 SAP 中心服务的多 SID 群集配置。 但 Azure 上 Linux 操作系统上的 SAP 中心服务不支持此配置。 可以在以下文章中找到 Windows 来宾操作系统方案的文档：
        - [使用 Azure 上的 Windows Server 故障转移群集和共享磁盘实现 SAP ASCS/SCS 实例多 SID 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        - [在 Azure 上使用 Windows Server 故障转移群集和文件共享实现 SAP ASCS/SCS 实例的多 SID 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    - 高可用性和灾难恢复体系结构。
        - 根据 RTO 和 RPO，定义高可用性和灾难恢复体系结构的外观。
        - 若要在区域中实现高可用性，请检查所需的 DBMS 在 Azure 中提供的功能。 大多数 DBMS 包提供同步热备用的同步方法，我们建议将其用于生产系统。 还要查看不同数据库的 SAP 相关文档，从[Azure 虚拟机 DBMS 部署适用于 sap 工作负荷](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)和相关文档的注意事项开始。
           不支持将 Windows Server 故障转移群集与 DBMS 层的共享磁盘配置一起使用，例如[SQL Server 所述](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017)。 相反，请使用如下所示的解决方案：
           - [SQL Server Always On](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
           - [HANA 系统复制](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - 对于跨 Azure 区域的灾难恢复，请查看不同 DBMS 供应商提供的解决方案。 其中的大多数支持异步复制或日志传送。
        - 对于 SAP 应用程序层，确定是否要运行业务回归测试系统，理想情况下，它是生产部署的副本，位于同一 Azure 区域或 DR 区域中。 在第二种情况下，您可以将业务回归系统作为生产部署的 DR 目标。
        - 如果决定不将非生产系统置于 DR 站点，请查看 Azure Site Recovery 作为将 SAP 应用程序层复制到 Azure DR 区域中的方法。 有关详细信息，请参阅[设置多层 SAP NetWeaver 应用程序部署的灾难恢复](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)。
        - 如果决定使用[Azure 可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview)结合使用组合的 HADR 配置，请熟悉可用可用性区域的 Azure 区域。 还会考虑在两个可用性区域之间增加网络延迟所带来的限制。  
3.  所有 SAP 接口（SAP 和非 SAP）的清单。
4.  基础服务的设计。 此设计应包括以下各项：
    - Active Directory 和 DNS 设计。
    - Azure 中的网络拓扑，并分配不同的 SAP 系统。
    - 用于管理 Azure 中的基础结构和 SAP 应用程序的团队的[基于角色的访问](https://docs.microsoft.com/azure/role-based-access-control/overview)结构。
    - 资源组拓扑。
    - [标记策略](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)。
    - Vm 和其他基础结构组件以及/或逻辑名称的命名约定。
5.  Microsoft 顶级支持合同。 确定你的 Microsoft 技术客户经理（TAM）。 有关 SAP 支持的要求，请参阅[sap 支持说明 #2015553](https://launchpad.support.sap.com/#/notes/2015553)。
6.  订阅的 Azure 订阅数和核心配额。 根据需要[打开支持请求以增加 Azure 订阅配额](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。
7.  将 SAP 数据迁移到 Azure 的数据缩减和数据迁移计划。 对于 SAP NetWeaver 系统，SAP 提供了有关如何限制大量数据量的准则。 有关 SAP ERP 系统中的数据管理，请参阅[此 sap 指南](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF)。 一些内容通常也适用于 NetWeaver 和 S/4HANA 系统。
8.  自动部署方法。 Azure 上的基础结构部署自动化的目标是以确定性的方式进行部署并获得确定性的结果。 许多客户使用 PowerShell 或基于 CLI 的脚本。 但你可以使用多种开源技术来部署适用于 SAP 的 Azure 基础结构，甚至还可以安装 SAP 软件。 可在 GitHub 上找到示例：
    - [Azure 云中的自动 SAP 部署](https://github.com/Azure/sap-hana)
    - [SAP HANA 安装](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  在您的客户、系统集成商、Microsoft 以及其他参与方之间定义定期设计和部署评审节奏。

 
## <a name="pilot-phase-strongly-recommended"></a>试点阶段（强烈推荐）
 
您可以在项目规划和准备过程之前或期间运行试验。 你还可以使用试点阶段测试规划和准备阶段中所做的方法和设计。 您可以扩展试验阶段，使其成为现实概念证明。

建议在试验部署过程中设置和验证完整的 HADR 解决方案和安全设计。 在此阶段，某些客户执行可伸缩性测试。 其他客户使用 SAP 沙箱系统的部署作为试验阶段。 假设您已确定要迁移到 Azure 进行试验的系统。

1. 优化数据传输到 Azure。 最佳选择非常依赖于特定的方案。 如果 ExpressRoute 线路有足够的带宽，则通过[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)从本地传输的速度最快。 在其他情况下，通过 internet 进行传输的速度更快。
2. 对于涉及数据导出和导入的异类 SAP 平台迁移，请测试并优化导出和导入阶段。 对于 SQL Server 是目标平台的大型迁移，你可以查找[建议](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070)。 如果不需要组合发布，可以使用迁移监视器/SWPM。 将迁移与 SAP 版本升级结合使用时，可以使用[SAP sql-dmo](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/)进程。 为此，需要满足源和目标 DBMS 平台组合的某些要求。 [SUM 2.0 SP03 的数据库迁移选项（sql-dmo）](https://launchpad.support.sap.com/#/notes/2631152)中记录了此过程。
   1.  导出到源，将文件上传到 Azure，并导入性能。 导出和导入之间的最大重叠。
   2.  评估目标平台和目标平台上数据库的数量，以确定基础结构大小。
   3.  验证和优化计时。
1. 技术验证。
   1. VM 类型。
        - 请查看 SAP 支持说明、SAP HANA 硬件目录和 SAP PAM 中的资源。 请确保 Azure 的受支持 Vm、这些 VM 类型支持的操作系统版本以及支持的 SAP 和 DBMS 版本未更改。
        - 再次验证 Azure 上部署的应用程序和基础结构的大小。 如果要移动现有应用程序，通常可以从所用的基础结构和[sap 基准页面](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd)派生必要的 sap，并将其与[sap 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533)中列出的 sap 号码进行比较。 同时，请记住[有关 sap 评级的这篇文章](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)。
        - 评估和测试你在规划阶段中所选 VM 类型的最大存储吞吐量和网络吞吐量方面的 Azure Vm 大小。 可在此处找到数据：
           -  [Azure 中 Windows 虚拟机的大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)。 请务必考虑用于调整大小的*最大非缓存磁盘吞吐量*。
           -  [Azure 中 Linux 虚拟机的大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)。 请务必考虑用于调整大小的*最大非缓存磁盘吞吐量*。
   1. 存储空间。
        - 至少，将[Azure 标准 SSD 存储](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)用于代表 SAP 应用程序层的 vm，并用于部署不区分性能的 dbms。
        - 通常，我们不建议使用[Azure 标准 HDD 磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)。
        - 对远程性能敏感的任何 DBMS Vm 使用[Azure 高级存储](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)。
        - 使用[Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。
        - 对于使用 M 系列的 DBMS 日志驱动器，请使用 Azure 写入加速器。 请注意写入加速器限制和用法，如[写入加速器](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)中所述。
        - 对于不同的 DBMS 类型，请查看一般的[与 SAP 相关的 dbms 文档](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)和一般文档指向的 DBMS 特定文档。
        - 有关 SAP HANA 的详细信息，请参阅[SAP HANA 基础结构配置和 Azure 上的操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)。
        - 请勿使用设备 ID 将 Azure 数据磁盘装载到 Azure Linux VM 中。 而应该使用全局唯一标识符 (UUID)。 例如，使用图形化工具装载 Azure 数据磁盘时应小心。 仔细检查/etc/fstab 中的条目，以确保使用 UUID 装载磁盘。 可以在[本文](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)中找到更多详细信息。
   1. 上网.
        - 在不同的 Azure 虚拟网络中测试和评估虚拟网络基础结构以及 SAP 应用程序的分布情况。
        -  评估单个 Azure 虚拟网络中的中心辐射型虚拟网络体系结构方法或 microsegmentation 方法。 此评估基于：-[对等互连 Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)之间的数据交换成本。 有关成本的信息，请参阅[虚拟网络定价](https://azure.microsoft.com/pricing/details/virtual-network/)。
                  -在 Azure 虚拟网络之间快速断开对等互连的优点，而不是更改网络安全组以在虚拟网络中隔离子网。 此评估适用于虚拟网络的子网中托管的应用程序或 Vm 成为安全风险的情况。
                  -集中日志记录和审核本地、外部和在 Azure 中生成的虚拟数据中心之间的网络流量。
        - 评估和测试 SAP 应用程序层和 SAP DBMS 层之间的数据路径。
            -  不支持将[Azure 网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)放置在基于 sap NetWeaver、Hybris 或 S/4HANA 的 sap 应用程序和 DBMS 层之间的通信路径中。
            -  不支持在不对等互连的不同 Azure 虚拟网络中放置 SAP 应用程序层和 SAP DBMS。
            -  你可以使用[应用程序安全组和网络安全组规则](https://docs.microsoft.com/azure/virtual-network/security-overview)来定义 sap 应用程序层和 sap DBMS 层之间的路由。
        - 确保[Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)已在 sap 应用程序层和 sap DBMS 层中使用的 vm 上启用。 注意，需要不同的 OS 级别来支持 Azure 中的加速网络：
            - Windows Server 2012 R2 或更高版本。
            - SUSE Linux 12 SP3 或更高版本。
            - RHEL 7.4 或更高版本。
            - Oracle Linux 7.5。 如果使用的是 RHCKL 内核，则需要 release 3.10.0-862.13.1. el7。 如果使用的是 Oracle UEK 内核，则需要版本5。
        - 根据 SAP 支持说明[#500235](https://launchpad.support.sap.com/#/notes/500235)和[#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)，测试和评估 Sap 应用程序层 vm 与 DBMS vm 之间的网络延迟。 根据[SAP 支持说明 #1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中的网络延迟指南来评估结果。 网络延迟应为适中或良好的范围。 异常适用于 Vm 与 HANA 大型实例单元之间的流量，如[本文](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)中所述。
        - 请确保将 ILB 部署设置为使用直接服务器返回。 当 Azure Ilb 用于 DBMS 层上的高可用性配置时，此设置将减少延迟。
        - 如果要将 Azure 负载均衡器与 Linux 来宾操作系统一起使用，请检查 Linux 网络参数**net.tcp _timestamps**是否已设置为**0**。 此建议与较旧版本[SAP 说明 #2382421](https://launchpad.support.sap.com/#/notes/2382421)中的建议冲突。 现已更新 SAP 说明，指出需要将此参数设置为**0**才能使用 Azure 负载均衡器。
        - 请考虑使用[Azure 邻近性放置组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)来获得最佳网络延迟。 有关详细信息，请参阅[适用于 SAP 应用程序的最佳网络延迟的 Azure 邻近性放置组](sap-proximity-placement-scenarios.md)。
   1. 高可用性和灾难恢复部署。
        - 如果在不定义特定 Azure 可用性区域的情况下部署 SAP 应用程序层，请确保运行 SAP 对话框实例的所有 Vm 或单个 SAP 系统的中间件实例都部署在[可用性集中](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)。
        - 如果对于 SAP 中心服务和 DBMS 不需要高可用性，则可以将这些 Vm 部署到与 SAP 应用程序层相同的可用性集。
        - 如果使用被动复制来保护 SAP 中心服务和 DBMS 层的高可用性，请将 SAP 中心服务的两个节点放在一个单独的可用性集中，将两个 DBMS 节点置于另一个可用性集中。
        - 如果将部署到 Azure 可用性区域，则无法使用可用性集。 但你确实需要确保将主动和被动中心服务节点部署到两个不同的可用性区域中。 使用在其之间具有最低延迟的可用性区域。
          请记住，你需要使用[Azure 标准负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)来确定跨可用性区域为 DBMS 和 SAP 中心服务层建立 Windows 或 Pacemaker 故障转移群集的使用案例。 不能将[基本负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus)用于区域性部署。
   1. 超时设置。
        - 检查 SAP NetWeaver 开发人员跟踪的 SAP 实例，以确保排队服务器与 SAP 工作进程之间不存在连接中断。 可以通过设置以下两个注册表参数来避免这些连接中断：
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000。 有关详细信息，请参阅[KeepAliveTime](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))。
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000。 有关详细信息，请参阅[KeepAliveInterval](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10))。
        - 若要避免在 Azure 中部署的本地 SAP GUI 界面和 SAP 应用程序层之间发生 GUI 超时，请检查是否在 default.pfl 或实例配置文件中设置这些参数：
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - 若要防止在 SAP 排队进程和 SAP 工作进程之间建立连接中断，需要将**enque/encni/set_so_keepalive**参数设置为**true**。 另请参阅[SAP 说明 #2743751](https://launchpad.support.sap.com/#/notes/2743751)。  
        - 如果使用 Windows 故障转移群集配置，请确保为 Azure 正确设置对未响应节点作出反应的时间。 [优化故障转移群集网络阈值](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834)一文列出了参数及其对故障转移的影响区分。 假设群集节点位于同一子网中，则应更改以下参数：
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength = 30
1. 测试您的高可用性和灾难恢复过程。
   1. 通过关闭 Vm 来模拟故障转移情况（Windows 来宾操作系统）或将操作系统置于紧急模式（Linux 来宾操作系统）。 此步骤可帮助确定故障转移配置是否按设计方式工作。
   1. 度量执行故障转移所花费的时间。 如果时间太长，请考虑：
        - 对于 SUSE Linux，请使用 SBD 设备（而不是 Azure 隔离代理）来加速故障转移。
        - 对于 SAP HANA，如果重新加载数据所需的时间太长，请考虑预配更多的存储带宽。
   1. 测试您的备份/还原顺序和时间，并在需要时进行更正。 请确保备份时间足以满足需要。 还需要测试还原和时间还原活动。 如果 RTO 依赖于数据库或 VM 还原过程，请确保还原时间在 RTO Sla 中。
   1. 测试跨区域 DR 功能和体系结构。
1. 安全检查。
   1. 测试 Azure 基于角色的访问控制（RBAC）体系结构的有效性。 目标是分离并限制不同团队的访问权限和权限。 例如，SAP Basis 团队成员应能够部署 Vm，并将 Azure 存储中的磁盘分配到指定的 Azure 虚拟网络。 但 SAP Basis 团队不应能够创建自己的虚拟网络或更改现有虚拟网络的设置。 网络团队的成员不应能够将 Vm 部署到运行 SAP 应用程序和 DBMS Vm 的虚拟网络中。 此团队成员也不应更改 Vm 的属性，甚至可以删除 Vm 或磁盘。  
   1.  验证[网络安全组和 ASC](https://docs.microsoft.com/azure/virtual-network/security-overview)规则按预期方式工作并防护受保护的资源。
   1.  确保所有需要加密的资源都已加密。 定义和实施用于备份证书的进程，存储和访问这些证书，并还原已加密的实体。
   1.  在可能的情况下，使用适用于 OS 磁盘的[Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq)。
   1.  请确保不使用太多的加密层。 在某些情况下，将 Azure 磁盘加密与 DBMS 透明数据加密方法之一一起使用确实非常有意义。
1. 性能测试。 在 SAP 中，根据 SAP 跟踪和度量值进行以下比较：
   1. 如果适用，请将前10个联机报表与当前实现进行比较。
   1. 如果适用，请将前10个批处理作业与当前实现进行比较。
   1. 比较通过接口到 SAP 系统的数据传输。 专注于你知道传输现在位于不同位置（例如从本地到 Azure）的接口。


## <a name="non-production-phase"></a>非生产阶段 
在此阶段，我们假设在成功试验或概念证明（POC）后，开始将非生产 SAP 系统部署到 Azure。 将 POC 中学习并经历的所有内容纳入到此部署中。 Poc 列出的所有条件和步骤也适用于此部署。

在此阶段，通常会将开发系统、单元测试系统和业务回归测试系统部署到 Azure。 我们建议，在一个 SAP 应用程序行中至少有一个非生产系统具有将来生产系统将具有的完整高可用性配置。 下面是在此阶段需要完成的一些附加步骤：  

1.  将系统从旧平台移到 Azure 之前，收集资源消耗数据，如 CPU 使用率、存储吞吐量和 IOPS 数据。 特别是从 DBMS 层单元收集此数据，但也从应用程序层单位收集此数据。 此外，还应测量网络和存储延迟。
2.  记录系统的可用性使用时间模式。 其目的在于确定非生产系统是否需要每天都可用，或者是否有非生产系统可在一周或每月的特定阶段关闭。
3.  测试并确定是否要在 Azure 中为 Vm 创建自己的操作系统映像，或是否要使用 Azure 共享映像库中的映像。 如果你使用的是共享映像库中的映像，请确保使用反映你的操作系统供应商的支持合同的映像。 对于某些操作系统供应商，共享映像库允许你自带许可证映像。 对于其他操作系统映像，支持包含在 Azure 报价的价格中。 如果决定创建自己的 OS 映像，可以在这些文章中找到文档：
    -   [构建在 Azure 中部署的 Windows VM 的一般化映像](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    -   [构建在 Azure 中部署的 Linux VM 的一般化映像](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  如果你使用共享映像库中的 SUSE 和 Red Hat Linux 映像，则需要使用共享映像库中的 Linux 供应商提供的适用于 SAP 的映像。
4.  请确保满足 Microsoft 支持协议的 SAP 支持要求。 请参阅[SAP 支持说明 #2015553](https://launchpad.support.sap.com/#/notes/2015553)。 对于 HANA 大型实例，请参阅[载入要求](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)。
4.  请确保适当的人员获得[计划内维护通知](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/)，以便选择最佳停机时间。
5.  对于可能适用于你的部署的新功能，经常在[通道 9](https://channel9.msdn.com/)等通道上检查 Azure 演示。
6.  对于新的 VM Sku 和新支持的操作系统和 DBMS 版本，请查看与 Azure 相关的 SAP 说明，如[支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533)。 将新的 VM 类型的价格与较旧的 VM 类型进行比较，因此，你可以部署具有最佳性价比的虚拟机。
7.  重新检查 SAP 支持说明、SAP HANA 硬件目录和 SAP PAM。 请确保 Azure 的受支持 Vm、这些虚拟机上支持的操作系统版本以及支持的 SAP 和 DBMS 版本未更改。
8.  查看[SAP 网站](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)，了解 Azure 中新的 HANA 认证 sku。 将新 Sku 的定价与计划使用的 Sku 进行比较。 最终，进行必要的更改，以使用具有最佳性价比的设置。
9.  改编你的部署脚本以使用新的 VM 类型，并包含你想要使用的新 Azure 功能。
10. 部署基础结构后，根据 SAP 支持说明[#500235](https://launchpad.support.sap.com/#/notes/500235)和[#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)，测试并评估 Sap 应用程序层 vm 与 DBMS vm 之间的网络延迟。 根据[SAP 支持说明 #1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中的网络延迟指南来评估结果。 网络延迟应为适中或良好的范围。 异常适用于 Vm 与 HANA 大型实例单元之间的流量，如[本文](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)中所述。 请确保[SAP 工作负荷的 Azure 虚拟机 DBMS 部署的 Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations)和[SAP HANA 基础结构配置和操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)中所述的任何限制都适用于你的部署。
11. 请确保已将 Vm 部署到正确的[azure 邻近性放置组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)，如 azure 邻近性放置组中所述，[用于 SAP 应用程序的最佳网络延迟](sap-proximity-placement-scenarios.md)。
11. 在应用工作负荷之前，请执行列出的所有其他检查，以查看概念验证阶段。
12. 在应用工作负荷时，记录 Azure 中系统的资源消耗。 将此消耗与旧平台中的记录进行比较。 如果发现存在较大的差异，请调整未来部署的 VM 大小。 请记住，在缩小时，也会降低 Vm 的存储和网络带宽。
    - [Azure 中 Windows 虚拟机的大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Azure 中 Linux 虚拟机的大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. 试验系统复制功能和进程。 目的是使您能够轻松地复制开发系统或测试系统，使项目团队能够快速获得新系统。 请考虑对这些任务使用[SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) 。
14. 优化和增强团队的 Azure 基于角色的访问、权限和过程，确保你有责任分离。 同时，请确保所有团队都可以在 Azure 基础结构中执行其任务。
15. 练习、测试和记录高可用性和灾难恢复过程，使您的员工能够执行这些任务。 确定缺点，并改编要集成到部署中的新 Azure 功能。

 
## <a name="production-preparation-phase"></a>生产准备阶段 
在此阶段中，收集在非生产部署期间遇到并了解到的内容，并将其应用于将来的生产部署。 还需要准备当前托管位置和 Azure 之间的数据传输工作。

1.  完成生产系统的必要 SAP 版本升级，然后再迁移到 Azure。
1.  请与企业所有者就需要在迁移生产系统后执行的功能和业务测试达成一致。
1.  请确保这些测试已在当前宿主位置的源系统中完成。 避免在系统移动到 Azure 后首次执行测试。
1.  测试将生产系统迁移到 Azure 的过程。 如果在同一时间段内未将所有生产系统移到 Azure，请构建需要处于同一托管位置的生产系统组。 测试数据迁移。 下面是一些常见方法：
    - 将 DBMS 方法（如备份/还原）与 SQL Server Always On、HANA 系统复制或日志传送结合使用，以便在 Azure 中播种和同步数据库内容。
    - 对较小的数据库使用备份/还原。
    - 使用集成到 SAP SWPM 的 SAP 迁移监视器来执行异类迁移。
    - 如果需要将迁移与 SAP 版本升级结合使用，请使用[SAP sql-dmo](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/)进程。 请记住，并非所有源 DBMS 和目标 DBMS 的组合都受支持。 可以在不同版本的 SQL-DMO 的特定 SAP 支持说明中找到详细信息。 例如， [SUM 2.0 SP04 的数据库迁移选项（sql-dmo）](https://launchpad.support.sap.com/#/notes/2644872)。
    - 如果需要移动备份或 SAP 导出文件，请测试数据传输吞吐量是否更好通过 internet 或 ExpressRoute。 如果要通过 internet 移动数据，可能需要更改某些网络安全组/应用程序安全组规则，这些规则将需要为将来的生产系统准备就绪。
1.  将系统从旧平台移到 Azure 之前，请收集资源消耗数据。 有用的数据包括 CPU 使用率、存储吞吐量和 IOPS 数据。 特别是从 DBMS 层单元收集此数据，但也从应用程序层单位收集此数据。 此外，还应测量网络和存储延迟。
1.  重新检查 SAP 支持说明、SAP HANA 硬件目录和 SAP PAM。 请确保 Azure 的受支持 Vm、这些 Vm 中支持的操作系统版本以及支持的 SAP 和 DBMS 版本不会发生任何更改。
1.  更新部署脚本，以考虑你对 VM 类型和 Azure 功能所做的最新决定。
1.  部署基础结构和应用程序后，请验证：
    - 部署了正确的 VM 类型，具有正确的属性和存储大小。
    - Vm 是正确的、所需的操作系统版本和修补程序，并且是统一的。
    - Vm 按要求和统一的方式被强制执行。
    - 安装并部署了正确的应用程序版本和修补程序。
    - Vm 按计划部署到 Azure 可用性集中。
    - Azure 高级存储用于滞后于延迟的磁盘，或者需要[99.9% 的单 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) 。
    - Azure 写入加速器已正确部署。
        - 请确保在需要写入加速器的磁盘上正确生成了 Vm、存储空间或带区集。
        - 检查[Linux 上的软件 RAID 配置](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)。
        - [在 Azure 中的 Linux vm 上检查 LVM 的配置](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)。
    - [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)专门用于。
    - Vm 已部署到正确的可用性集，并可用性区域。
    - 在 SAP 应用程序层和 SAP DBMS 层中使用的 Vm 上启用[Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。
    - [Azure 网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)不在 sap 应用程序和基于 sap NetWeaver、Hybris 或 S/4HANA 的 sap 系统的 DBMS 层之间的通信路径中。
    - 应用程序安全组和网络安全组规则允许根据所需的通信和计划的通信，并在需要时阻止通信。
    - 如前文所述，正确设置了超时设置。
    - Vm 将部署到正确的[azure 邻近度布局组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)，如 azure 邻近性放置组中所述，[用于 SAP 应用程序的最佳网络延迟](sap-proximity-placement-scenarios.md)。
    - SAP 应用程序层 Vm 与 DBMS Vm 之间的网络延迟经过测试和验证，如 SAP 支持说明[#500235](https://launchpad.support.sap.com/#/notes/500235)和[#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中所述。 根据[SAP 支持说明 #1100926](https://launchpad.support.sap.com/#/notes/1100926/E)中的网络延迟指南来评估结果。 网络延迟应为适中或良好的范围。 异常适用于 Vm 与 HANA 大型实例单元之间的流量，如[本文](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)中所述。
    - 加密是在必要时通过适当的加密方法实现的。
    - 接口和其他应用程序可以连接新部署的基础结构。
1.  创建用于应对计划的 Azure 维护的操作手册。 确定应重新启动系统和 Vm 以进行计划内维护的顺序。
    

## <a name="go-live-phase"></a>上线阶段
在上线阶段，请确保遵循在之前阶段中开发的行动手册。 执行你测试和练习的步骤。 不要接受配置和过程中的最后一分钟更改。 还应完成以下步骤：

1. 验证 Azure 门户监视和其他监视工具是否正常工作。 建议适用于 Windows 的 Windows 性能监视器（perfmon）和适用于 Linux 的 SAR。
    - CPU 计数器。
        - 平均 CPU 时间，总计（所有 Cpu）
        - 平均 CPU 时间，每个处理器（M128 Vm 上的128处理器）
        - CPU 内核时间，每个处理器
        - CPU 用户时间，每个处理器
    - 内存。
        - 免费内存
        - 内存页/秒
        - 内存页输出/秒
    - 。
        - 每个磁盘的磁盘读取数 KBps
        - 每秒磁盘读取次数，每个磁盘
        - 磁盘读取数微秒/读取，每单个磁盘
        - 每个磁盘以 KBps 为单位的磁盘写入
        - 磁盘写入/秒，按单个磁盘
        - 磁盘写入（以微秒为单位），按单个磁盘读取
    - 网桥.
        - 每秒网络数据包数
        - 每秒的网络数据包数
        - 每秒的网络 KB
        - 网络 KB/秒
1.  数据迁移后，请执行你与业务所有者协商的所有验证测试。 仅在具有原始源系统的结果时才接受验证测试结果。
1.  检查接口是否正常运行，以及其他应用程序是否可以与新部署的生产系统进行通信。
1.  通过 SAP transaction STMS 检查传输和更正系统。
1.  在为生产发布系统后执行数据库备份。
1.  为生产提供系统后，为 SAP 应用程序层 Vm 执行 VM 备份。
1.  对于不属于当前生存时间的 SAP 系统，但与在此生存阶段内移动到 Azure 的 SAP 系统进行通信，你需要在 SM51 中重置主机名称缓冲区。 这样做将删除与移动到 Azure 的应用程序实例的名称关联的旧缓存 IP 地址。  


## <a name="post-production"></a>后期生产阶段
此阶段涉及监视、操作和管理系统。 从 SAP 的角度来看，你需要在旧的托管位置完成的常用任务就适用了。 还完成以下特定于 Azure 的任务：

1. 查看适用于高充电系统的 Azure 发票。
2. 优化 VM 端和存储端的性价比。
3. 优化可关闭系统的时间。  


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [SAP NetWeaver 的 Azure 虚拟机规划和实施指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [SAP NetWeaver 的 Azure 虚拟机部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

