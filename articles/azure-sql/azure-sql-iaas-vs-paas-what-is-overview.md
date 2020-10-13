---
title: 什么是 Azure SQL？
description: 了解 Azure SQL 服务系列中的不同选项：Azure VM 上的 Azure SQL 数据库、Azure SQL 托管实例和 SQL Server。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: overview
keywords: SQL Server 云, 云端 SQL Server, PaaS 数据库, 云 SQL Server, DBaaS, IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: 4cc1eefa93366451b568da789fd48d8a8c658439
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618261"
---
# <a name="what-is-azure-sql"></a>什么是 Azure SQL？ 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Azure SQL 是一系列智能安全的托管产品，它们使用 Azure 云中的 SQL Server 数据库引擎。

- **Azure SQL 数据库**：支持对智能的托管数据库服务（包括无服务器计算）使用现代云应用程序。 
- **Azure SQL 托管实例**：大规模地实现现有 SQL Server 应用程序的现代化，其中智能且完全托管的实例用作服务，且与 SQL Server 数据库引擎之间的功能奇偶一致性几乎达 100%。 最适合用于大部分云迁移方案。
- **Azure VM 中的 SQL Server**：轻松地直接迁移 SQL Server 工作负载，保持 100% 的 SQL Server 兼容性和操作系统级别的访问权限。 
 
Azure SQL 是基于熟悉的 SQL Server 引擎构建的，因此你可轻松迁移应用程序，继续使用熟悉的工具、语言和资源。 你的技能和经验同样适用于云，因此你可运用已学知识实现更多目标。 

了解每款产品如何契合 Microsoft 的 Azure SQL 数据平台，以与适合你业务需求的选项相符。 无论你是优先考虑成本节省还是精简管理，本文都会帮助你确定哪种方法能够满足你最重视的业务要求。


如果你不熟悉 Azure SQL，请观看我们深度讲解的 [Azure SQL 视频系列](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)中的“什么是 Azure SQL”视频：
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/What-is-Azure-SQL-3-of-61/player]



## <a name="overview"></a>概述

在当今数据驱动的世界中，推动数字化转型越来越取决于我们管理大量数据并挖掘其潜力的能力。 但如今的数据资产日益复杂，数据托管在本地、云或网络边缘。 构建智能和沉浸式应用程序的开发人员可能会发现自己受到限制的束缚，而这些限制最终可能会影响他们的体验。 平台不兼容、数据不够安全、资源不足和性价比障碍等原因造成的限制会导致应用现代化和开发变得更加复杂。 

在 Azure 与本地 SQL Server 数据库的任何介绍中，要了解的要点之一是可以同时使用两者。 Microsoft 数据平台利用 SQL Server 技术，使其可在跨本地物理计算机、私有云环境、第三方托管的私有云环境和公有云中使用。 


### <a name="fully-managed-and-always-up-to-date"></a>完全托管并始终保持最新 

花更多时间进行创新，减少修补、更新和备份数据库所耗的时间。 Azure 是唯一具有长效 SQL 的云，后者能够自动应用最新的更新和补丁程序，使数据库始终保持最新状态，从而消除了支持终止带来的麻烦。 甚至可自动执行复杂的任务，例如性能优化、高可用性、灾难恢复和备份，使你能够专注于应用程序。  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>利用内置的智能安全性保护你的数据 

Azure 会持续监视你的数据是否受到威胁。 借助 Azure SQL，你可以：

- 通过智能[高级威胁检测](https://docs.microsoft.com/azure/security/fundamentals/threat-detection#advanced-threat-detection-features-other-azure-services)和主动漏洞评估警报，实时修正潜在威胁。 
- 使用[内置安全控件](https://azure.microsoft.com/overview/security/)（包括 T-SQL、身份验证、网络和密钥管理）获得业界领先的多层保护。 
- 充分利用任何云数据库服务最全面的[符合性](https://azure.microsoft.com/overview/trusted-cloud/compliance/)。 


### <a name="business-motivations"></a>业务驱动因素

有多个因素可能会影响你在不同数据产品/服务之间所做的选择：

- [成本](#cost)：PaaS 和 IaaS 选项都包括基本价格，其中涵盖了底层基础结构和许可证的费用。 但是，使用 IaaS 选项时，需要投入额外的时间和资源来管理数据库，而 PaaS 的价格已包括这些管理功能的费用。 借助 IaaS 选项可在不使用资源时将其关闭来降低成本；对于 PaaS，除非删除资源，然后在需要时重新创建资源，否则它会一直运行。
- [管理](#administration)：PaaS 选项可减少管理数据库所需投入的时间。 但是，它还会限制你可以执行或运行的自定义管理任务和脚本的范围。 例如，SQL 数据库不支持 CLR，但 SQL 托管实例的实例支持 CLR。 此外，PaaS 中没有部署选项支持使用跟踪标志。
- [服务级别协议](#service-level-agreement-sla)：IaaS 和 PaaS 都提供较高的行业标准 SLA。 在基础结构方面，PaaS 选项保证 99.99% 的 SLA，而 IaaS 保证 99.95% 的 SLA，这意味着，需要实施附加的机制才能确保数据库的可用性。 可获得 99.99% 的 SLA，只需创建附加的 SQL 虚拟机并实现 SQL Server Always On 可用性组高可用性解决方案即可。 
- [迁移到 Azure 所需的时间](#market)：Azure VM 中 SQL Server 与你的环境完全匹配，因此从本地迁移到 Azure VM 与在不同的本地服务器之间迁移数据库没有什么不同。 SQL 托管实例还可实现轻松迁移；但是，可能需要在迁移之前应用某些更改。 


## <a name="service-comparison"></a>服务比较

   ![云 SQL Server 选项：IaaS 上的 SQL Server，或云中的 SaaS SQL 数据库。](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

如图所示，每款服务产品可根据你在基础结构中所拥有的管理级别，以及成本效益程度等特征进行分类。

在 Azure 中，可让 SQL Server 工作负荷作为托管服务 ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) 或托管基础结构 ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) 运行。 在 PaaS 中有多个产品选项，每个选项中有多个服务层级。 在 PaaS 与 IaaS 之间作出抉择时，需要提出的一个重要问题是，你是要自行管理数据库、应用修补程序、创建备份，还是将这些操作委托给 Azure？

### <a name="azure-sql-database"></a>Azure SQL 数据库

[Azure SQL 数据库](database/sql-database-paas-overview.md)是 Azure 中托管的关系数据库即服务 (DBaaS)，属于“平台即服务 (PaaS)”行业类别。 
- 最适合用于需要使用最新的 SQL Server 稳定功能，且开发与面市时间有限的新式云应用程序。 
- 完全托管的 SQL Server 数据库引擎，基于最新稳定的 SQL Server Enterprise Edition。 SQL 数据库有两个部署选项，每个选项都以 Microsoft 所拥有、托管及维护的标准化硬件和软件为基础。 

在 SQL Server 中，无论是本地的还是 Azure 虚拟机中，都可使用需要进行广泛配置的内置特性和功能。 使用 SQL 数据库时，可以即用即付，并使用向上或向外缩放选项获得更强大的功能且不会中断服务。 SQL 数据库具有 SQL Server 所不能提供的其他一些功能，例如，内置的高可用性、智能和管理。


Azure SQL 数据库提供了以下部署选项：
  - 充当自带资源集的[单一数据库](database/single-database-overview.md)，该资源集通过[逻辑 SQL 服务器](database/logical-servers.md)进行托管。 单一数据库类似于 SQL Server 中的[包含的数据库](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)。 此选项已针对新式应用程序开发进行优化，可用于开发新型的云原生应用程序。 可以使用[超大规模](database/service-tier-hyperscale.md)和[无服务器](database/serverless-tier-overview.md)选项。
  - [弹性池](database/elastic-pool-overview.md)：一个数据库集合，具有通过[逻辑 SQL 服务器](database/logical-servers.md)管理的一组共享资源。 可以将单一数据库移入或移出弹性池。 此选项已针对新式应用程序开发进行优化，可用于使用多租户 SaaS 应用程序模式开发新型的云原生应用程序。 弹性池提供经济高效的解决方案用于管理使用模式可变的多个数据库的性能。

### <a name="azure-sql-managed-instance"></a>Azure SQL 托管实例

[Azure SQL 托管实例](managed-instance/sql-managed-instance-paas-overview.md)属于“平台即服务 (PaaS)”行业类别，是大多数云迁移的最佳选择。 SQL 托管实例是系统和用户数据库的集合，包含一组随时可直接迁移的共享资源。  
- 最适合用于需要使用最新的 SQL Server 稳定功能，并且在进行极少量更改的情况下迁移到云中的新应用程序或现有本地应用程序。 SQL 托管实例的实例与 [Microsoft SQL Server 数据库引擎](https://docs.microsoft.com/sql/database-engine/sql-server-database-engine-overview)的实例类似，为数据库和实例范围内的其他功能提供共享资源。 
- SQL 托管实例支持从本地迁移数据库，只要求对数据库进行极少量的更改，甚至根本不需要更改。 此选项提供 Azure SQL 数据库的所有 PaaS 优势，同时还添加了以前只在 SQL Server VM 中提供的功能。 这包括本机虚拟网络，以及与本地 SQL Server 的近乎 100% 的兼容性。 SQL 托管实例的实例提供完全的 SQL Server 访问和功能兼容性，可用于将 SQL Server 迁移到 Azure。

### <a name="sql-server-on-azure-vm"></a>Azure VM 上的 SQL Server

[Azure VM 上的 SQL Server](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) 属于“基础结构即服务 (IaaS)”行业类别，可让你在 Azure 中完全托管的虚拟机 (VM) 上运行 SQL Server。 
- 最适合用于需要 OS 级别访问的迁移和应用程序。 Azure 中的 SQL 虚拟机随时可直接迁移，适用于需要快速迁移到云中且只需进行极少量更改甚至不需要任何更改的现有应用程序。 SQL 虚拟机针对要迁移到 Azure 的 SQL Server 实例和底层 OS 提供全面的管理控制度。 
- 想要快速完成开发和测试方案，但又不想购买本地 SQL Server 非生产硬件。 SQL 虚拟机也在 Microsoft 所拥有、托管及维护的标准化硬件上运行。 使用 SQL 虚拟机时，既可使用 SQL Server 映像中已包含的 SQL Server 许可证即用即付，也可轻松使用现有的许可证。 还可以根据需要停止或恢复 VM。 
- 在云中安装和托管的 SQL Server 将在 Azure 上运行的 Windows Server 或 Linux 虚拟机上运行，也称为基础结构即服务 (IaaS)。 SQL 虚拟机非常适合用于迁移本地 SQL Server 数据库和应用程序，无需进行任何数据库更改。 所有当前 SQL Server 版本都可安装在 IaaS 虚拟机中。 

    与 SQL 数据库和 SQL 托管实例之间最重要的差别在于，可使用 Azure 虚拟机上的 SQL Server 完全控制数据库引擎。 可以选择维护/修补开始时间、将恢复模式更改为简单模式或批量记录模式、根据需要暂停或启动服务，并且可以完全自定义 SQL Server 数据库引擎。 控制度的提高也意味着在管理虚拟机方面需要承担更大的责任。
- 已针对将现有应用程序迁移到 Azure 或将现有本地应用程序扩展到混合部署中的云进行了优化。 此外，可以使用虚拟机中的 SQL Server 开发和测试传统 SQL Server 应用程序。 有了 SQL 虚拟机，即拥有了专用 SQL Server 实例和基于云的 VM 的完全管理权限。 当组织拥有可用来维护虚拟机的 IT 资源时，此选项是最佳选择。 使用这些功能可以构建高度定制的系统，解决应用程序的具体性能与可用性要求。

下表列出了其他差别，但 SQL 数据库和 SQL 托管实例都经过优化，可将预配和管理许多数据库的整体管理成本降到最低。 由于无需管理任何虚拟机、操作系统或数据库软件，因此可持续降低管理成本。 用户不必管理升级、高可用性或[备份](database/automated-backups-overview.md)。 

一般而言，SQL 数据库和 SQL 托管实例会大幅增加由单个 IT 或开发资源管理的数据库数目。 [弹性池](database/elastic-pool-overview.md)也支持 SaaS 多租户应用程序体系结构，提供租户隔离等功能，并可以跨数据库共享资源，通过缩放来降低成本。 [SQL 托管实例](managed-instance/sql-managed-instance-paas-overview.md)支持面向实例的功能，可用于轻松迁移现有应用程序，以及在数据库之间共享资源。

### <a name="comparison-table"></a>比较表

| Azure SQL 数据库 | Azure SQL 托管实例 | Azure VM 上的 SQL Server |
| :--- | :--- | :--- |
|支持大多数本地数据库级功能。 提供最常用的 SQL Server 功能。<br/>保证 99.995% 的可用性。<br/>内置备份、修补和恢复。<br/>最新稳定的数据库引擎版本。<br/>可将必要的资源（CPU/存储）分配到单个数据库。<br/>内置高级智能和安全性。<br/>联机更改资源（CPU/存储）。| 几乎支持所有的本地实例级和数据库级功能。 与 SQL Server 高度兼容。<br/>保证 99.99% 的可用性。<br/>内置备份、修补和恢复。<br/>最新稳定的数据库引擎版本。<br/>从 SQL Server 轻松迁移。<br/>Azure 虚拟网络中的专用 IP 地址。<br/>内置高级智能和安全性。<br/>联机更改资源（CPU/存储）。| 可以完全控制 SQL Server 引擎。 支持所有本地功能。<br/>高达 99.99% 的可用性。<br/>完全可与匹配的本地 SQL Server 版本搭配使用。<br/>固定的已知数据库引擎版本。<br/>从 SQL Server 轻松迁移。<br/>Azure 虚拟网络中的专用 IP 地址。<br/>可将应用程序或服务部署到 SQL Server 所在的主机上。|
|可能难以从 SQL Server 迁移。<br/>某些 SQL Server 功能不可用。<br/>不保证确切的维护时间（但几乎是透明的）。<br/>只能使用数据库兼容性级别来实现与 SQL Server 版本的兼容。<br/>[Azure 专用链接](database/private-endpoint-overview.md)支持专用 IP 地址。|仍有极少量的 SQL Server 功能不可用。<br/>不保证确切的维护时间（但几乎是透明的）。<br/>只能使用数据库兼容性级别来实现与 SQL Server 版本的兼容。|需要自行管理备份和修补程序。<br>需要实施高可用性解决方案。<br/>更改资源（CPU/存储）会造成停机|
| 最多 100 TB 的数据库。 | 最多 8 TB。 | 具有高达 256 TB 存储空间的 SQL Server 实例。 实例可以支持所需数量的数据库。 |
| 本地应用程序可以访问 Azure SQL 数据库中的数据。 | 使用 Azure Express Route 或 VPN 网关[实现本机虚拟网络](managed-instance/vnet-existing-add-subnet.md)并连接到本地环境。 | 借助 SQL 虚拟机，应用程序可以一部分在云中运行，一部分在本地运行。 例如，可以通过 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)，将本地网络和 Active Directory 域扩展到云中。 有关混合云解决方案的详细信息，请参阅[将本地数据解决方案扩展到云](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud)。 |


## <a name="cost"></a>成本

无论你是现金不足的新公司，或是在预算有限的情况下运作的已成立公司的小组，资金的限制通常是决定数据库托管方式的主要考虑因素。 在本部分中，你将学习 Azure 中与 Azure SQL 服务系列相关联的计费和许可方面的基础知识。  此外，还介绍如何计算应用程序总成本。

### <a name="billing-and-licensing-basics"></a>计费和许可基础概念

目前，SQL 数据库和 SQL 托管实例均以服务的形式出售，提供了多个选项和多个服务层级，不同资源采用不同的价格，所有资源根据你选择的服务层级和计算大小以固定费率按小时计费 。 有关当前支持的服务层级、计算大小和存储量的最新信息，请参阅 [SQL 数据库基于 DTU 的购买模型](database/service-tiers-dtu.md)和 [SQL 数据库和 SQL 托管实例基于 vCore 的购买模型](database/service-tiers-vcore.md)。

- 使用 SQL 数据库时，可根据需求选择各种价格的服务层级（起步价为基本层级的每月 5 美元），你也可创建[弹性池](database/elastic-pool-overview.md)，在数据库之间共享资源，从而降低成本并应对使用高峰。
- 使用 SQL 托管实例时，还可以自带许可证。 有关自带许可的详细信息，请参阅 [Azure 上通过软件保障实现的许可移动性](https://azure.microsoft.com/pricing/license-mobility/)，或使用 [Azure 混合权益计算器](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database)来了解如何将成本最高节省 40%。

此外，需要对固定的 [数据传输速率](https://azure.microsoft.com/pricing/details/data-transfers/)的 Internet 传出流量付费。 可以动态调整服务层级和计算大小，以满足应用程序的不同吞吐量需求。

使用 SQL 数据库和 SQL 托管实例时，Azure 会自动配置、修补和升级数据库软件，这可降低管理成本 。 此外，它的 [内置备份](database/automated-backups-overview.md) 功能可帮助你大幅降低成本，尤其是拥有大量的数据库时。

在 Azure VM 上的 SQL 中，可使用平台提供的任何 SQL Server 映像（附带许可证），也可自带 SQL Server 许可证。 所有受支持的 SQL Server 版本（2008R2、2012、2014、2016、2017、2019）和特别版（开发人员版、Express 版、Web 版、Standard 版、Enterprise 版）都可用。 此外，映像的自带许可版本 (BYOL) 也可用。 使用 Azure 提供的映像时，营运成本取决于所选的 VM 大小以及 SQL Server 版本。 无论 VM 大小或 SQL Server 版本为何，都需要支付 SQL Server 和 Windows 或 Linux 服务器的每分钟许可成本，以及 VM 磁盘的 Azure 存储成本。 每分钟计费选项可让你随时使用 SQL Server，而无需另外购买 SQL Server 许可证。 如果在 Azure 中使用自己的 SQL Server 许可证，则只需支付服务器和存储成本。 有关自带许可证的详细信息，请参阅 [Azure 上通过软件保障实现的许可移动性](https://azure.microsoft.com/pricing/license-mobility/)。 此外，需要对固定的 [数据传输速率](https://azure.microsoft.com/pricing/details/data-transfers/)的 Internet 传出流量付费。

#### <a name="calculating-the-total-application-cost"></a>计算应用程序总成本

开始使用云平台时，运行应用程序的成本包括新开发成本和后续管理成本，以及公有云平台服务成本。

有关定价的详细信息，请参阅以下资源：

- [SQL 数据库和 SQL 托管实例定价](https://azure.microsoft.com/pricing/details/sql-database/)
- 针对 [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows) 的[虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 价格计算器](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>管理

对许多企业来说，决定过渡到到云服务的关键在于降低管理复杂度。 Azure 使用 IaaS 和 PaaS 管理底层基础结构和自动复制所有数据以提供灾难恢复，配置及升级数据库软件，管理负载均衡，并在数据中心发生服务器故障时执行透明的故障转移。

- 使用 SQL 数据库和 SQL 托管实例时，可继续管理数据库，但无需再管理数据库引擎、操作系统或硬件 。 可以继续管理的项目示例包括数据库和登录、索引和查询优化，以及审核和安全性。 此外，在另一个数据中心配置高可用性只需极少量的配置和管理。
- 借助 Azure VM 上的 SQL，可完全掌控操作系统和 SQL Server 实例配置。 使用 VM，可以决定何时更新/升级操作系统与数据库软件，以及何时安装任何其他软件（例如防病毒软件）。 提供的某些自动化功能可以大大简化修补、备份和高可用性。 此外，还可以控制 VM 的大小、磁盘数目及其存储配置。 Azure 允许根据需要更改 VM 的大小。 有关信息，请参阅 [Azure 的虚拟机和云服务大小](../virtual-machines/windows/sizes.md)。

## <a name="service-level-agreement-sla"></a>服务级别协议 (SLA)

对于许多 IT 部门而言，首要任务是达到服务级别协议 (SLA) 规定的正常运行时间。 本部分会说明 SLA 对每个数据库托管选项代表的含义。

对于 Azure SQL 数据库和 Azure SQL 托管实例，Microsoft 提供 99.99% 的可用性 SLA。 有关最新信息，请参阅[服务级别协议](https://azure.microsoft.com/support/legal/sla/sql-database/)。

对于 Azure VM 上的 SQL，Microsoft 提供 99.95% 的可用性 SLA（仅涵盖虚拟机）。 此 SLA 不涵盖 VM 上运行的进程（例如 SQL Server），并且要求你在可用性集中托管至少两个 VM 实例。 有关最新信息，请参阅 [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。 为了在 VM 中实现数据库高可用性 (HA)，应在 SQL Server 中配置受支持的高可用性选项之一，例如 [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)。 使用支持的高可用性选项不会提供更多 SLA，但使用户能够实现 >99.99% 的数据库可用性。

## <a name="time-to-move-to-azure"></a><a name="market"></a>迁移到 Azure 的时机

当开发人员的工作效率和新解决方案快速面市的时间至关重要时，适合使用 Azure SQL 数据库来处理面向云的应用程序。 此选项提供类似于编程 DBA 的功能，非常适合云架构师和开发人员，因为它能降低管理基础操作系统和数据库的需求。

Azure SQL 托管实例大大简化了将现有应用程序迁移到 Azure 的过程，让你能够在 Azure 中将已迁移的数据库应用程序快速推向市场。

如果现有或新的应用程序需要大型数据库或者需要访问 SQL Server 或 Windows/Linux 中的所有功能，而且你想要避免购置新本地硬件所耗的时间和费用，那么理想的选择是 Azure VM 上的 SQL。 此外，如果想要将现有的本地应用程序和数据库按原样迁移到 Azure，而 SQL 数据库或 SQL 托管实例并不合适，则上述解决方案也是一个不错的选择。 由于无需更改表示层、应用层和数据层，所以在重新构建现有解决方案时可以节省时间和预算。 相反地，可以将重点放在将所有解决方案迁移到 Azure，并执行 Azure 平台可能需要的某些性能优化。 有关详细信息，请参阅 [Azure 虚拟机上 SQL Server 的性能最佳做法](virtual-machines/windows/performance-guidelines-best-practices.md)。

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>后续步骤

- 若需 SQL 数据库的入门，请参阅[创建第一个 Azure SQL 数据库](database/single-database-create-quickstart.md)。
- 若要开始使用 SQL 托管实例，请参阅[你的第一个 Azure SQL 托管实例](managed-instance/instance-create-quickstart.md)。 
- 请参阅 [SQL Database pricing](https://azure.microsoft.com/pricing/details/sql-database/)。
- 若要在 Azure VM 上开始使用 SQL Server，请参阅[在 Azure 中预配 SQL Server 虚拟机](virtual-machines/windows/create-sql-vm-portal.md)。
- [确定适合本地数据库的 SQL 数据库或 SQL 托管实例 SKU](/sql/dma/dma-sku-recommend-sql-db/)。
