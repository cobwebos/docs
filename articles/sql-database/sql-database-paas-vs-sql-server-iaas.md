---
title: 在 Azure SQL 中选择正确的部署选项 |Microsoft Docs
description: 了解如何在 azure 虚拟机中的 SQL 数据库、SQL 托管实例和 SQL Server 之间选择 Azure SQL 中的部署选项。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: SQL Server 云, 云中 SQL Server, PaaS 数据库, 云 SQL Server, DBaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/22/2019
ms.openlocfilehash: 998318ad1418c1076676725af03bd7f33c963b3a
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279936"
---
# <a name="choose-the-right-deployment-option-in-azure-sql"></a>在 Azure SQL 中选择正确的部署选项

了解每个部署选项如何适应 Microsoft 的 Azure SQL 数据平台，并获得与正确选项匹配的帮助以满足你的业务要求。 无论是按成本节约还是最小管理的优先级，本文都可以帮助您决定哪种方法可满足您最关心的业务要求。

## <a name="microsofts-azure-sql-data-platform"></a>Microsoft 的 Azure SQL 数据平台

从迁移到现有应用程序的现代化，到构建现代云服务，Azure SQL 是一种新式 SQL 平台，它提供多种部署选项，由业界领先的 Microsoft SQL Server 引擎提供支持。 Azure SQL 旨在支持多种应用程序模式，这些模式具有对底层平台的不同控制级别，以满足最苛刻的迁移和现代化要求。 Azure SQL 消除了单一统一的管理体验，从而消除了以大规模方式管理基于 SQL Server 的各种应用程序的复杂性。

在 Azure 与本地 SQL Server 数据库的任何介绍中，要了解的要点之一是可以同时使用两者。 Microsoft 的数据平台利用 SQL Server 技术，使其可用于整个本地计算机、私有云环境、第三方托管的私有云环境和公有云。 使用 Azure 虚拟机（SQL 虚拟机）上的 SQL Server，你可以通过本地和云托管部署的组合来满足独特的多样化业务需求，同时使用相同的服务器产品、开发工具和专业知识集在这些环境中。

   ![云 SQL Server 选项：IaaS 上的 SQL Server，或云中的 SaaS SQL 数据库。](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

如图中所示，每个产品可按您对基础结构的管理级别，以及成本效率的程度来表征。

在 Azure 中，可将 SQL Server 工作负荷作为托管服务（[PaaS](https://azure.microsoft.com/overview/what-is-paas/)）或托管基础结构（[IaaS](https://azure.microsoft.com/overview/what-is-iaas/)）运行。 在 PaaS 中，有多个部署选项，每个部署选项中有多个服务层级。 在 PaaS 或 IaaS 之间进行决定时需要询问的关键问题是：要管理数据库、应用修补程序并进行备份，还是要将这些操作委派给 Azure？

根据具体的答案，可以选择以下选项：

- [**SQL 数据库**](sql-database-technical-overview.md)：最适用于想要使用最新稳定 SQL Server 功能，并在开发和营销中具有时间限制的新式云应用程序。 完全托管的 SQL 数据库引擎，基于最新稳定的 SQL Server Enterprise Edition。 这是托管在 Azure 云中的关系数据库即服务 (DBaaS)，属于“平台即服务 (PaaS)”行业类别。 SQL 数据库有多个部署选项，每个选项都以 Microsoft 所拥有、托管及维护的标准化硬件和软件为基础。 使用 SQL Server，可以使用需要广泛配置（本地或 Azure 虚拟机）的内置功能。 使用 SQL 数据库时，可以即用即付，并使用纵向扩展或横向扩展选项获得更强大的功能且不会中断服务。 SQL 数据库具有一些其他功能，这些功能在 SQL Server 中不可用，例如内置的高可用性、智能和管理。


  数据库提供以下部署选项：
  - 作为具有一组通过数据库服务器管理的资源的[***单一数据库***](sql-database-single-database.md)。 单个数据库与 SQL Server 中的[包含数据库](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)类似。 此选项已针对新式应用程序开发进行优化，可用于开发新型的云原生应用程序。 [超大规模](sql-database-service-tier-hyperscale.md)和[无服务器](sql-database-serverless.md)选项可用。
  - [***弹性池***](sql-database-elastic-pool.md)，它是具有通过数据库服务器管理的共享资源集的数据库的集合。 可以将单一数据库移入或移出弹性池。 此选项已针对使用多租户 SaaS 应用程序模式的全新云开发应用程序的新式应用程序开发进行了优化。 弹性池提供了一个经济高效的解决方案，用于管理具有可变使用模式的多个数据库的性能。
  - 用于管理单个数据库和弹性池的组的[***数据库服务器***](sql-database-servers.md)。 数据库服务器充当多个单一或共用数据库的中心管理点，[登录名](sql-database-manage-logins.md)、[防火墙规则](sql-database-firewall-configure.md)、[审核规则](sql-database-auditing.md)、[威胁检测策略](sql-database-threat-detection.md)和[故障转移组](sql-database-auto-failover-group.md)。

- [**SQL 托管实例**](sql-database-managed-instance.md)：最适用于大多数到云的迁移。 托管实例是具有一组共享资源的系统和用户数据库的集合，这些资源是一组可以直接迁移的资源。 最适用于想要使用最新稳定 SQL Server 功能的新应用程序或现有本地应用程序，并且只需进行少量更改即可迁移到云。 托管实例类似于为数据库提供共享资源的[Microsoft SQL Server 数据库引擎](https://docs.microsoft.com/sql/database-engine/sql-server-database-engine-overview)的实例和其他实例范围的功能。 托管实例支持从本地迁移数据库，只要求对数据库进行极少量的更改，甚至根本不需要更改。 此选项提供 Azure SQL 数据库的所有 PaaS 优势，但同时也添加了以前只在 SQL VM 中提供的功能。 这包括本机虚拟网络 (VNet)，以及与本地 SQL Server 的接近 100% 的兼容性。 托管实例提供完全 SQL Server 访问和功能兼容性，以便将 SQL Server 迁移到 Azure。


- [**SQL 虚拟机**](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)：最适合需要 OS 级别访问的迁移和应用程序。 对于需要快速迁移到云的现有应用程序，SQL 虚拟机是无变化或无更改。 SQL 虚拟机为迁移到 Azure 的 SQL Server 实例和基础操作系统提供完全的管理控制。 想要快速完成开发和测试方案，但又不想购买本地 SQL Server 非生产硬件。 SQL 虚拟机属于行业类别的*基础结构即服务（IaaS）* ，可让你在 Azure 云中完全托管的虚拟机（VM）中运行 SQL Server。 SQL 虚拟机还在由 Microsoft 拥有、托管和维护的标准化硬件上运行。 使用 SQL 虚拟机时，你可以使用即用即付 SQL Server 许可证已包含在 SQL Server 映像中，也可以轻松使用现有许可证。 还可以根据需要停止或恢复 VM。 在云中安装并托管 SQL Server 在 Azure 上运行的 Windows Server 或 Linux 虚拟机上运行，也称为基础结构即服务（IaaS）。 SQL 虚拟机是一种很好的选择，用于在无需更改数据库的情况下迁移本地 SQL Server 数据库和应用程序。 所有当前 SQL Server 版本都可安装在 IaaS 虚拟机中。 与 SQL 数据库和 SQL 托管实例的最大差别在于，SQL Server Vm 允许完全控制数据库引擎。 你可以选择何时开始维护/修补、将恢复模式更改为简单或大容量日志记录、暂停或启动服务（如果需要），并且可以完全自定义 SQL Server 数据库引擎。 通过此附加控制，添加了管理虚拟机的责任。

  已针对将现有应用程序迁移到 Azure 或将现有本地应用程序扩展到混合部署中的云进行了优化。 此外，还可以使用虚拟机中的 SQL Server 开发和测试传统的 SQL Server 应用程序。 使用 SQL 虚拟机，你对专用 SQL Server 实例和基于云的 VM 具有完全管理权限。 当组织拥有可用来维护虚拟机的 IT 资源时，此选项是最佳选择。 使用这些功能，可以构建高度定制的系统，以解决应用程序的特定性能和可用性要求。


下表中列出了其他差别，但***数据库和托管实例已进行了优化，以减少设置和管理多个数据库所需的总管理成本。*** 由于无需管理任何虚拟机、操作系统或数据库软件，因此可以持续降低管理成本。 用户不必管理升级、高可用性或 [备份](sql-database-automated-backups.md)。 一般而言，Azure SQL 数据库可以大幅增加由单个 IT 或开发资源管理的数据库数目。 [弹性池](sql-database-elastic-pool.md)也支持 SaaS 多租户应用程序体系结构，提供租户隔离等功能，并可以跨数据库共享资源，通过缩放来降低成本。 [托管实例](sql-database-managed-instance.md)支持面向实例的功能，可用于轻松迁移现有应用程序，以及在数据库之间共享资源。

| SQL 数据库 | SQL 托管实例 | SQL 虚拟机 |
| :--- | :--- | :--- |
|支持大多数本地数据库级功能。 提供最常用的 SQL Server 功能。<br/>99.995% 的可用性保证。<br/>内置备份、修补和恢复。<br/>最新稳定的数据库引擎版本。<br/>可将必要的资源（CPU/存储）分配到单个数据库。<br/>内置高级智能和安全性。<br/>联机更改资源（CPU/存储）。| 几乎支持所有的本地实例级和数据库级功能。 与本地 SQL Server 高度兼容。<br/>保证 99.99% 的可用性。<br/>内置备份、修补和恢复。<br/>最新稳定的数据库引擎版本。<br/>从 SQL Server 轻松迁移。<br/>Azure VNet 中的专用 IP 地址。<br/>内置高级智能和安全性。<br/>联机更改资源（CPU/存储）。| 可以完全控制 SQL Server 引擎。 支持所有本地功能。<br/>可用性高达 99.99%。<br/>完全可与匹配的本地 SQL Server 版本搭配使用。<br/>固定的已知数据库引擎版本。<br/>从本地 SQL Server 轻松迁移。<br/>Azure VNet 中的专用 IP 地址。<br/>可将应用程序或服务部署到 SQL Server 所在的主机上。|
|可能难以从 SQL Server 迁移。<br/>某些 SQL Server 功能不可用。<br/>不保证确切的维护时间（但几乎是透明的）。<br/>只能使用数据库兼容性级别来实现与 SQL Server 版本的兼容。<br/>无法分配专用 IP 地址（可以使用防火墙规则限制访问）。|仍有极少量的 SQL Server 功能不可用。<br/>不保证确切的维护时间（但几乎是透明的）。<br/>只能使用数据库兼容性级别来实现与 SQL Server 版本的兼容。|需要自行管理备份和修补程序。<br>需要实施高可用性解决方案。<br/>更改资源（CPU/存储）会造成停机|
| 最多 100 TB 的数据库。 | 最多 8 TB。 | SQL Server 实例，最高可达 256 TB 的存储空间。 此实例可以根据需要支持任意数目的数据库。 |
| 本地应用程序可以访问 Azure SQL 数据库中的数据。 | 使用 Azure Express Route 或 VPN 网关[实现本机虚拟网络](sql-database-managed-instance-vnet-configuration.md)并连接到本地环境。 | 使用 SQL 虚拟机，你可以让应用程序在云中部分运行，部分在本地运行。 例如，可以通过 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)，将本地网络和 Active Directory 域扩展到云中。 有关混合云解决方案的详细信息，请参阅[将本地数据解决方案扩展到云](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud)。 |




## <a name="business-motivations-for-choosing-databases-managed-instances-or-sql-virtual-machines"></a>用于选择数据库、托管实例或 SQL 虚拟机的业务动机

有几个因素可能会影响你在不同数据产品之间进行选择的决策：

- [成本](#cost) - PaaS 和 IaaS 选项都包括基本价格，其中涵盖了底层基础结构和许可证的费用。 但是，使用 IaaS 选项时，需要投入额外的时间和资源来管理数据库，而 PaaS 的价格已包括这些管理功能的费用。 使用 IaaS 选项，你可以在不使用资源来降低成本的同时关闭资源，而 PaaS 版本始终运行，除非在需要时删除并重新创建资源。
- [管理](#administration) - PaaS 选项可以减少管理数据库所要投入的时间量。 但是，它还会限制你可以执行或运行的自定义管理任务和脚本的范围。 例如，单一数据库或共用数据库不支持 CLR，但托管实例支持 CLR。 此外，PaaS 中没有部署选项支持使用跟踪标志。
- [服务级别协议](#service-level-agreement-sla) - IaaS 和 PaaS 都提供较高的行业标准 SLA。 在基础结构方面，PaaS 选项保证 99.99% 的 SLA，而 IaaS 保证 99.95% 的 SLA，这意味着，需要实施附加的机制才能确保数据库的可用性。 可以通过在 VM 中创建其他 SQL Server 并配置 AlwaysOn 可用性组，在 99.99% 实现高可用性解决方案。
- [迁移到 Azure 所需的时间](#market) - Azure VM 中 SQL Server 与你的环境完全匹配，因此，从本地迁移到 Azure SQL VM，与在不同的本地服务器之间迁移数据库没有什么不同。 托管实例还可以实现极其轻松的迁移；但是，在迁移到托管实例之前，可能需要应用某些更改。

以下部分将更详细地讨论这些因素。

### <a name="cost"></a>开销

无论是现金不足的新公司，或是在预算有限的情况下运作的已成立公司的小组，有限资金经常是决定数据库托管方式的主要考虑因素。 在本部分中，将了解 Azure 中有关以下两个关系数据库选项的计费和许可基本概念：SQL 数据库和 SQL 虚拟机。 还将了解如何计算应用程序总成本。

#### <a name="billing-and-licensing-basics"></a>计费和许可基础概念

目前，**SQL 数据库**以服务的形式出售，提供了多个部署选项和多个服务层级，根据资源采用不同的价格，所有资源根据所选的服务层级和计算大小，以固定费率向你收取每小时费用。 有关当前支持的服务层级、计算大小和存储量的最新信息，请参阅[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)和[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)。

- 使用 SQL 数据库，你可以从从 5 $/月 for basic 层开始，选择满足你的需求的服务层。
- 你可以创建[弹性池](sql-database-elastic-pool.md)，在数据库实例之间共享资源，以降低成本和应对使用高峰期。
- 对于 SQL 托管实例，还可以自带许可证。 有关自带许可的详细信息，请参阅 [Azure 上通过软件保障实现的许可移动性](https://azure.microsoft.com/pricing/license-mobility/)，或使用 [Azure 混合权益计算器](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database)来了解如何将成本**最高节省 40%** 。

此外，需要对固定的 [数据传输速率](https://azure.microsoft.com/pricing/details/data-transfers/)的 Internet 传出流量付费。 可以动态调整服务层级和计算大小，以满足应用程序的不同吞吐量需求。

使用**sql 数据库和 sql 托管实例**，Microsoft 自动配置、修补和升级数据库软件，从而降低管理成本。 此外，它的 [内置备份](sql-database-automated-backups.md) 功能可帮助你大幅降低成本，尤其是拥有大量的数据库时。

使用**SQL 虚拟机**，你可以使用平台提供的任何 SQL Server 映像（包括许可证）或引入你的 SQL Server 许可证。 所有受支持的 SQL Server 版本（2008R2、2012、2014、2016）和特别版（开发人员版、Express 版、Web 版、Standard 版、Enterprise 版）都可用。 此外，映像的自带许可版本 (BYOL) 也可用。 使用 Azure 提供的映像时，营运成本取决于所选的 VM 大小以及 SQL Server 版本。 无论 VM 大小或 SQL Server 版本为何，都需要支付 SQL Server 和 Windows 或 Linux 服务器的每分钟许可成本，以及 VM 磁盘的 Azure 存储成本。 每分钟计费选项可让你随时使用 SQL Server，而无需另外购买 SQL Server 许可证。 如果在 Azure 中使用自己的 SQL Server 许可证，则只需支付服务器和存储成本。 有关自带许可证的详细信息，请参阅 [Azure 上通过软件保障实现的许可移动性](https://azure.microsoft.com/pricing/license-mobility/)。 此外，需要对固定的 [数据传输速率](https://azure.microsoft.com/pricing/details/data-transfers/)的 Internet 传出流量付费。

#### <a name="calculating-the-total-application-cost"></a>计算应用程序总成本

开始使用云平台时，运行应用程序的成本包括新开发成本和后续管理成本，以及公有云平台服务成本。

有关定价的详细信息，请参阅以下资源：

- [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)
- 针对 [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows) 的[虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 价格计算器](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>管理

对许多企业来说，决定过渡到到云服务的关键在于降低管理复杂度。 Microsoft 使用 IaaS 和 PaaS 管理底层基础结构和自动复制所有数据以提供灾难恢复，配置及升级数据库软件，管理负载均衡，并在数据中心发生服务器故障时执行透明的故障转移。

- 通过**sql 数据库和 sql 托管实例**，你可以继续管理数据库，但不再需要管理数据库引擎、操作系统或硬件。 可以继续管理的项目示例包括数据库和登录、索引和查询优化，以及审核和安全性。 此外，在另一个数据中心配置高可用性只需极少量的配置和管理。
- 使用**SQL 虚拟机**，你可以完全控制操作系统和 SQL Server 实例配置。 使用 VM，可以由用户决定何时更新/升级操作系统和数据库软件，以及何时安装任何其他软件（例如防病毒软件）。 提供的一些自动化功能能够大大简化修补、备份以及高可用性。 此外，还可以控制 VM 的大小、磁盘数目及其存储配置。 Azure 允许用户根据需要更改 VM 的大小。 有关信息，请参阅 [Azure 的虚拟机和云服务大小](../virtual-machines/windows/sizes.md)。

### <a name="service-level-agreement-sla"></a>服务级别协议 (SLA)

对于许多 IT 部门而言，达到服务级别协议 (SLA) 规定的正常运行时间义务是首要任务。 在本部分中，我们将了解 SLA 对每个数据库托管选项代表的含义。

对于 **SQL 数据库**，Microsoft 提供 99.99% 的可用性 SLA。 有关最新信息，请参阅 [服务级别协议](https://azure.microsoft.com/support/legal/sla/sql-database/)。

对于**SQL 虚拟机**，Microsoft 提供 99.95% 的可用性 SLA，只涉及虚拟机。 此 SLA 不涵盖 VM 上运行的进程（例如 SQL Server），并且要求在可用性集中托管至少两个 VM 实例。 有关最新信息，请参阅 [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。 为了在 VM 中实现数据库高可用性 (HA)，应在 SQL Server 中配置一个受支持的高可用性选项，例如 [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)。 使用支持的高可用性选项不会提供更多 SLA，但使用户能够实现 >99.99% 的数据库可用性。

### <a name="market"></a>迁移到 Azure 的时机

**SQL 数据库**当开发人员的工作效率和快速上市的新解决方案至关重要时，单一数据库或弹性池是适用于云设计的应用程序的解决方案。 此选项提供类似于编程 DBA 的功能，非常适合云架构师和开发员，因为它能降低管理基础操作系统和数据库的需求。

**SQL 托管实例**大大简化了将现有应用程序迁移到 azure SQL 的速度，使你能够在 azure 中快速将迁移的数据库应用程序推向市场。

如果现有的或新的应用程序需要大型数据库或访问 SQL Server 或 Windows/Linux 中的所有功能，并且想要避免获取新的本地硬件的时间和开销，则**SQL 虚拟机**是理想的。 此外，如果想要将现有的本地应用程序和数据库按原样迁移到 Azure，而 Azure SQL 数据库托管实例并不合适，则上述解决方案也是一个不错的选择。 由于无需更改表示层、应用层和数据层，所以在重新构建现有解决方案时可以节省时间和预算。 相反地，可以将重点放在将所有解决方案迁移到 Azure，并执行 Azure 平台可能需要的某些性能优化。 有关详细信息，请参阅 [Azure 虚拟机上 SQL Server 的性能最佳实践](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)。

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>后续步骤

- 有关 SQL 数据库的入门，请参阅[第一个 AZURE SQL 数据库](sql-database-single-database-get-started.md)。
- 请参阅 [《SQL Database pricing》](https://azure.microsoft.com/pricing/details/sql-database/)（SQL 数据库定价）。
- 若要开始在 Azure VM 上使用 SQL Server，请参阅 [《Provision a SQL Server virtual machine in Azure》](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) （在 Azure 中预配 SQL Server 虚拟机）。
- [为本地数据库标识正确的 sql 数据库或 sql 托管实例 SKU](/sql/dma/dma-sku-recommend-sql-db/)。
