---
title: SQL (PaaS) 数据库与云中 VM 上的 SQL Server (IaaS) | Microsoft Docs
description: 了解哪个云 SQL Server 选项适合应用程序：Azure SQL (PaaS) 数据库或 Azure 虚拟机上云中的 SQL Server。
services: sql-database, virtual-machines
keywords: SQL Server 云, 云中 SQL Server, PaaS 数据库, 云 SQL Server, DBaaS
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 2fb5a7cbca4df0faa06864f580814f31cc2b609c
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114395"
---
# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>选择云 SQL Server 选项：Azure SQL (PaaS) 数据库或 Azure VM 上的 SQL Server (IaaS)

在 Azure 中，可让 SQL Server 工作负荷在托管的基础结构 (IaaS) 或托管的服务 ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) 中运行：

* [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)：基于 SQL Server 企业版的 SQL 数据库引擎，已针对新型应用程序开发进行优化。 Azure SQL 数据库以托管服务的形式提供两个 SQL 版本：逻辑服务器和 [Azure SQL 数据库托管实例（预览版）](sql-database-managed-instance.md)。 Azure SQL 数据库通过这两个版本提供了 SQL Server 所不能提供的其他功能，例如内置智能和管理。 使用第一个版本可以创建包含[单一数据库](sql-database-servers-databases.md)的逻辑服务器，并将服务器分组到[弹性池](sql-database-elastic-pool.md)，以共享资源和降低成本。 包含单一数据库和入池数据库的 Azure SQL 数据库逻辑服务器提供 SQL Server 具有的大多数面向数据库的功能。 借助 Azure SQL 数据库托管实例，Azure SQL 数据库可为数据库提供共享资源，以及其他面向实例的功能。 Azure SQL 数据库托管实例支持数据库迁移，只要求对数据库进行极少量的更改，甚至根本不需要更改。
* [Azure 虚拟机上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)：在 Azure 上运行的云中 Windows Server 或 Linux 虚拟机 (VM) 上安装并托管的 SQL Server，也称为基础结构即服务 (IaaS)。 Azure 虚拟机上的 SQL Server 非常适合用于迁移本地 SQL Server 数据库和应用程序，无需进行任何数据库更改。 所有当前 SQL Server 版本都可安装在 IaaS 虚拟机中。 与 SQL 数据库之间的最重要差别在于，SQL Server VM 允许完全控制数据库引擎。 可以选择维护/修补何时开始、将恢复模式更改为简单模式或批量记录模式，以更快地加载更少的日志、根据需要暂停或启动引擎，并可以完全自定义 SQL Server 数据库引擎。 控制度的提高也意味着在管理虚拟机方面需要承担更大的责任。

了解每个选项如何配合 Microsoft 数据平台一起运行，并在匹配适合业务要求的选项时获得帮助。 无论是以节省成本为优先考虑，还是将精简管理视为第一要素，本文都会帮助你确定哪种方法能够满足你最重视的业务要求。

## <a name="microsofts-sql-data-platform"></a>Microsoft 的 SQL 数据平台

在 Azure 与本地 SQL Server 数据库的任何介绍中，要了解的要点之一是可以同时使用两者。 Microsoft 数据平台利用 SQL Server 技术，使其可在跨本地物理机、私有云环境、第三方托管的私有云环境和公有云中使用。 使用 Azure 虚拟机上的 SQL Server，可以通过本地和云托管部署的组合来满足独特的多样化业务需求，并同时在这些环境中使用相同的服务器产品、开发工具和专业知识组合。

   ![云 SQL Server 选项：IaaS 上的 SQL Server，或云中的 SaaS SQL 数据库。](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

如图所示，每个产品可根据你在基础结构（X 轴）中所拥有的管理级别，以及数据库级别合并与自动化（Y 轴）所达到的成本效益程度等特征进行分类。

设计应用程序时，可以使用四个基本选项来托管属于应用程序一部分的 SQL Server：

* 非虚拟化物理机上的 SQL Server
* 本地虚拟机中的 SQL Server（私有云）
* Azure 虚拟机中的 SQL Server（Microsoft 公有云）
* Azure SQL 数据库（Microsoft 公有云）

在以下部分中，将了解 Microsoft 公有云中的 SQL Server：Azure SQL 数据库和 Azure VM 上的 SQL Server。 此外，将探讨常见的业务动机，以判断哪一个选项最适合应用程序。

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Azure SQL 数据库和 Azure VM 中的 SQL Server 详述

**Azure SQL 数据库**是托管在 Azure 云中的关系数据库即服务 (DBaaS)，属于*平台即服务 (PaaS)* 行业类别。 [SQL 数据库](sql-database-technical-overview.md) 构建在 Microsoft 所拥有、托管及维护的标准化硬件和软件基础之上。 使用 SQL 数据库，可以使用需要在 SQL Server 中进行广泛配置的内置特性和功能。 使用 SQL 数据库时，可以即用即付，并使用纵向扩展或横向扩展选项获得更强大的功能且不会中断服务。 Azure SQL 数据库支持[单一数据库](sql-database-servers-databases.md)和用于共享资源的[弹性池](sql-database-elastic-pool.md)，是在云中开发新应用程序的理想环境。 使用 [Azure SQL 数据库托管实例](sql-database-managed-instance.md)时，可以自带许可证。 此外，此选项提供 Azure SQL 数据库的所有 PaaS 优势，但同时也添加了以前只在 SQL VM 中提供的功能。 这包括本机虚拟网络 (VNet)，以及与本地 SQL Server 的接近 100% 的兼容性。 [托管实例](sql-database-managed-instance.md)非常适合用于将本地数据库迁移到 Azure，这只需进行极少量的更改。 

**Azure 虚拟机 (VM) 上的 SQL Server** 属于“基础结构即服务 (IaaS)”行业类别，可让你在云中的虚拟机上运行 SQL Server。 [SQL Server 虚拟机](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)也在 Microsoft 所拥有、托管及维护的标准化硬件上运行。 使用 VM 上的 SQL Server 时，既可使用 SQL Server 映像中已包含的 SQL Server 许可证即付即用，也可方便地使用现有的许可证。 还可以根据需要停止或恢复 VM。

通常，这两个 SQL 选项已针对不同的用途进行优化：

* **Azure SQL 数据库**经过优化，可将预配和管理许多数据库的整体管理成本降到最低。 由于无需管理任何虚拟机、操作系统或数据库软件，因此可以持续降低管理成本。 用户不必管理升级、高可用性或 [备份](sql-database-automated-backups.md)。 一般而言，Azure SQL 数据库可以大幅增加由单个 IT 或开发资源管理的数据库数目。 [弹性池](sql-database-elastic-pool.md)也支持 SaaS 多租户应用程序体系结构，提供租户隔离等功能，并可以跨数据库共享资源，通过缩放来降低成本。 [Azure SQL 数据库托管实例](sql-database-managed-instance.md)支持面向实例的功能，可用于轻松迁移现有应用程序，以及在数据库之间共享资源。
* **Azure VM 上运行的 SQL Server** 已针对将现有应用程序迁移到 Azure 或将现有本地应用程序扩展到混合部署中的云进行了优化。 此外，还可以使用虚拟机中的 SQL Server 开发和测试传统的 SQL Server 应用程序。 有了 Azure VM 上的 SQL server，即拥有了专用 SQL Server 实例和基于云的 VM 的完全管理权限。 当组织拥有可用来维护虚拟机的 IT 资源时，此选项是最佳选择。 使用这些功能，可以构建高度定制的系统，以解决应用程序的特定性能和可用性要求。

下表汇总了 SQL 数据库和 Azure VM 中 SQL Server 的主要特征：

| | Azure SQL 数据库<br>逻辑服务器、弹性池和单一数据库 | Azure SQL 数据库<br>托管实例 |Azure 虚拟机<br>SQL Server |
| --- | --- | --- |---|
| **最适用于：** |需要使用最新的 SQL Server 稳定功能，并且开发与面市时间有限制的新云式设计应用程序。 | 需要使用最新的 SQL Server 稳定功能，并且在进行极少量更改的情况下迁移到云中的新应用程序或现有本地应用程序。  | 需要快速迁移到云中且只需进行极少量更改甚至不需要任何更改的现有应用程序。 想要快速完成开发和测试方案，但又不想购买本地 SQL Server 非生产硬件。 |
|  | 需要对数据库内置高可用性、灾难恢复和升级的团队。 | 与 SQL 数据库相同。 | 可为 SQL Server 配置、微调、自定义和管理高可用性、灾难恢复与修补的团队。 提供的某些自动化功能可极大地简化此操作。 | |
|  | 不想要管理基础操作系统和配置设置的团队。 | 与 SQL 数据库相同。 | 需要一个具有完全管理权限的自定义环境。 | |
|  | 最大为 4 TB 或更大的可使用扩展模式进行[水平或垂直分区](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling)的数据库。 | 与 SQL 数据库相同。 | 存储最大为 64 TB 的 SQL Server 实例。 此实例可以根据需要支持任意数目的数据库。 |
| **兼容性** | 支持大多数本地数据库级功能。 | 几乎支持所有的本地实例级和数据库级功能。 | 支持所有本地功能。 |
| **资源：** | 不想使用 IT 资源来配置和管理底层基础结构，而想要重点关注应用程序层。 | 与 SQL 数据库相同。 | 将一些 IT 资源用于配置和管理。 提供的某些自动化功能可极大地简化此操作。 |
| **总拥有成本：** | 消除硬件成本并减少管理成本。 | 与 SQL 数据库相同。 | 消除硬件成本。 |
| **业务连续性：** |除了[内置的容错基础结构功能](sql-database-high-availability.md)以外，Azure SQL 数据库还提供可提高业务连续性的功能，例如[自动备份](sql-database-automated-backups.md)、[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)、[异地还原](sql-database-recovery-using-backups.md#geo-restore)和[故障转移组与活动异地复制](sql-database-geo-replication-overview.md)。 有关详细信息，请参阅 [《SQL Database business continuity overview》](sql-database-business-continuity.md)（SQL 数据库业务连续性概述）。 | 与 SQL 数据库相同，此外还提供用户发起的仅限复制的备份。 | 使用 Azure VM 上的 SQL Server 可以设置高可用性和灾难恢复解决方案，以满足数据库的具体需求。 因此，可以构建针对应用程序高度优化的系统。 可以视需要自我测试并运行故障转移。 有关详细信息，请参阅 [《High Availability and Disaster Recovery for SQL Server on Azure Virtual Machines》](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md)（Azure 虚拟机中 SQL Server 的高可用性和灾难恢复）。 |
| **混合云：** |本地应用程序可以访问 Azure SQL 数据库中的数据。 | 使用 Azure Express Route 或 VPN 网关[实现本机虚拟网络](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-vnet-configuration)并连接到本地环境。 | 使用 Azure VN 上的 SQL Server，应用程序可以一部分在云中运行，一部分在本地运行。 例如，可以通过 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)，将本地网络和 Active Directory 域扩展到云中。 此外，可以使用 [Azure 中的 SQL Server 数据文件功能](http://msdn.microsoft.com/library/dn385720.aspx)，将本地数据文件存储在 Azure 存储中。 有关详细信息，请参阅 [SQL Server 2014 混合云简介](http://msdn.microsoft.com/library/dn606154.aspx)。 |
|  | 作为订阅服务器支持使用 [SQL Server 事务复制](https://msdn.microsoft.com/library/mt589530.aspx) 来复制数据。 | Azure SQL 数据库托管实例不支持复制。 | 完全支持使用 [SQL Server 事务复制](https://msdn.microsoft.com/library/mt589530.aspx)、[Always On 可用性组](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md)、集成服务和日志传送来复制数据。 此外，还完全支持传统的 SQL Server 备份 | |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>选择 Azure SQL 数据库或 Azure VM 上的 SQL Server 时的业务动机
### <a name="cost"></a>成本
无论是现金不足的新公司，或是在预算有限的情况下运作的已成立公司的小组，有限资金经常是决定数据库托管方式的主要考虑因素。 在本部分中，将了解 Azure 中有关以下两个关系数据库选项的计费和许可基本概念：SQL 数据库和 Azure VM 中的 SQL Server。 还将了解如何计算应用程序总成本。

#### <a name="billing-and-licensing-basics"></a>计费和许可基础概念

目前，**SQL 数据库**以服务的形式出售，在多个服务层中可用，根据资源采用不同的价格，并根据所选的服务层和性能级别，以固定费率向你收取每小时费用。 使用 Azure SQL 数据库托管实例时，还可以自带许可证。 有关自带许可证的详细信息，请参阅 [Azure 上通过软件保障实现的许可移动性](https://azure.microsoft.com/pricing/license-mobility/)。 此外，需要对固定的 [数据传输速率](https://azure.microsoft.com/pricing/details/data-transfers/)的 Internet 传出流量付费。 可以在服务层和性能级别之间动态调整，以满足应用程序的不同吞吐量需求。 有关当前支持的服务层的最新信息，请参阅[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)和[基于 vCore 的购买模型（预览版）](sql-database-service-tiers-vcore.md)。 还可以创建[弹性池](sql-database-elastic-pool.md)，在数据库实例之间共享资源，以降低成本和应对使用高峰期。

使用 **Azure SQL 数据库**，Microsoft 会自动配置、修补和升级数据库软件，从而可以降低管理成本。 此外，它的 [内置备份](sql-database-automated-backups.md) 功能可帮助你大幅降低成本，尤其是拥有大量的数据库时。 

在 **Azure VM 上的 SQL Server**中，可以使用平台提供的 SQL Server 映像（附带许可证），也可以引入自己的 SQL Server 许可证。 所有受支持的 SQL Server 版本（2008R2、2012、2014、2016）和特别版（开发人员版、Express 版、Web 版、Standard 版、Enterprise 版）都可用。 此外，映像的自带许可版本 (BYOL) 也可用。 使用 Azure 提供的映像时，营运成本取决于所选的 VM 大小以及 SQL Server 版本。 无论 VM 大小或 SQL Server 版本为何，都需要支付 SQL Server 和 Windows 或 Linux 服务器的每分钟许可成本，以及 VM 磁盘的 Azure 存储成本。 每分钟计费选项可让你随时使用 SQL Server，而无需另外购买 SQL Server 许可证。 如果在 Azure 中使用自己的 SQL Server 许可证，则只需支付服务器和存储成本。 有关自带许可证的详细信息，请参阅 [Azure 上通过软件保障实现的许可移动性](https://azure.microsoft.com/pricing/license-mobility/)。 此外，需要对固定的 [数据传输速率](https://azure.microsoft.com/pricing/details/data-transfers/)的 Internet 传出流量付费。

#### <a name="calculating-the-total-application-cost"></a>计算应用程序总成本
开始使用云平台时，运行应用程序的成本包括新开发成本和后续管理成本，以及公有云平台服务成本。

**使用 Azure SQL 数据库时：**

- 大幅降低管理成本
- 已迁移的应用程序的有限开发成本
- SQL 数据库服务成本
- 无硬件购置成本

**使用 Azure VM 上的 SQL Server 时：**

- 更高的管理成本
- 已迁移的应用程序的有限开发成本，甚至没有成本
- 虚拟机服务成本
- SQL Server 许可成本
- 无硬件购置成本

有关定价的详细信息，请参阅以下资源：

* [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)
* [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows) 的[虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure 价格计算器](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>管理
对许多企业来说，决定过渡到到云服务的关键在于降低管理复杂度。 Microsoft 使用 IaaS 和 PaaS 管理底层基础结构和自动复制所有数据以提供灾难恢复，配置及升级数据库软件，管理负载均衡，并在数据中心发生服务器故障时执行透明的故障转移。 

- 使用 **Azure SQL 数据库**可以继续管理数据库，但不再需要管理数据库引擎、服务器操作系统或硬件。  可以继续管理的项目示例包括数据库和登录、索引和查询优化，以及审核和安全性。 此外，在另一个数据中心配置高可用性只需极少量的配置和管理。
- 使用 **Azure VM 上的 SQL Server**，可以完全掌控操作系统和 SQL Server 实例配置。 使用 VM，可以由用户决定何时更新/升级操作系统和数据库软件，以及何时安装任何其他软件（例如防病毒软件）。 提供的一些自动化功能能够大大简化修补、备份以及高可用性。 此外，还可以控制 VM 的大小、磁盘数目及其存储配置。 Azure 允许用户根据需要更改 VM 的大小。 有关信息，请参阅 [Azure 的虚拟机和云服务大小](../virtual-machines/windows/sizes.md)。 

### <a name="service-level-agreement-sla"></a>服务级别协议 (SLA)
对于许多 IT 部门而言，达到服务级别协议 (SLA) 规定的正常运行时间义务是首要任务。 在本部分中，我们将了解 SLA 对每个数据库托管选项代表的含义。

对于 **SQL 数据库**，Microsoft 提供 99.99% 的可用性 SLA。 有关最新信息，请参阅 [服务级别协议](https://azure.microsoft.com/support/legal/sla/sql-database/)。 

对于 **Azure VM 上运行的 SQL Server**，Microsoft 提供 99.95% 的可用性 SLA（仅涵盖虚拟机）。 此 SLA 不涵盖 VM 上运行的进程（例如 SQL Server），并且要求在可用性集中托管至少两个 VM 实例。 有关最新信息，请参阅 [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。 为了在 VM 中实现数据库高可用性 (HA)，应在 SQL Server 中配置一个受支持的高可用性选项，例如 [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)。 使用支持的高可用性选项不会提供更多 SLA，但使用户能够实现 >99.99% 的数据库可用性。

### <a name="market"></a>迁移到 Azure 的时机
当开发人员工作效率和新解决方案的快速面市时间至关重要时，**SQL 数据库逻辑服务器、弹性池和单一数据库**是云设计应用程序的理想解决方案。 此选项提供类似于编程 DBA 的功能，非常适合云架构师和开发员，因为它能降低管理基础操作系统和数据库的需求。 

**SQL 数据库托管实例**大大简化了将现有应用程序迁移到 Azure SQL 数据库的过程，使你能够快速将已迁移的数据库应用程序在 Azure 中推向市场。

如果现有或新的应用程序需要大型数据库或者访问 SQL Server 或 Windows/Linux 中的所有功能，并且你想要避免购置新本地硬件所要花费的时间和费用，则 **Azure VM 上运行的 SQL Server** 是理想选择。 此外，如果想要将现有的本地应用程序和数据库按原样迁移到 Azure，而 Azure SQL 数据库托管实例并不合适，则上述解决方案也是一个不错的选择。 由于无需更改呈现、应用程序和数据层，在重新架构现有解决方案时节省时间和预算。 相反地，可以将重点放在将所有解决方案迁移到 Azure，并执行 Azure 平台可能需要的某些性能优化。 有关详细信息，请参阅 [Azure 虚拟机上 SQL Server 的性能最佳实践](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)。

## <a name="summary"></a>摘要
本文探讨了 SQL 数据库和 Azure 虚拟机 (VM) 上的 SQL Server，并讨论了可能会影响决策的常见业务动机。 以下是供你考虑的建议摘要：

对于以下情况，请选择 **Azure SQL 数据库** ：

* 打算构建全新的基于云的应用程序，以利用云服务提供的成本节省和性能优化功能。 此方法提供全面管理云服务的优势，有助于加速产品面市，并提供长期的成本效益。
* 想要让 Microsoft 在数据库上运行常见管理操作，因而数据库需要更高的可用性 SLA。
* 想要将现有应用程序按原样迁移到 Azure SQL 数据库托管实例，并利用 SQL Server 和/或高级安全性和网络的更高平衡度。 对于新的和现有的应用程序而言，托管实例是个不错的选择。

对于以下情况，请选择 **Azure VM 上的 SQL Server** ：

* 有想要迁移或扩展到云中的现有本地应用程序，或者如果要构建大于 4 TB 的企业应用程序。 此方法提供的优点包括：可以使用所选的 SQL Server 版本、大数据库容量、可完全控制 SQL Server 和 Windows/Linux，以及通过隧道安全连接到本地。 此方法用于开发和修改现有应用程序的成本降至最低。
* 有现有的 IT 资源，并且最终可以拥有修补、备份和数据库高可用性。 请注意，一些自动化功能可以极大地简化这些操作。 

## <a name="next-steps"></a>后续步骤

* 若要开始使用 SQL 数据库，请参阅[第一个 Azure SQL 数据库](sql-database-get-started-portal.md)。
* 请参阅 [《SQL Database pricing》](https://azure.microsoft.com/pricing/details/sql-database/)（SQL 数据库定价）。
* 若要开始在 Azure VM 上使用 SQL Server，请参阅 [《Provision a SQL Server virtual machine in Azure》](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) （在 Azure 中预配 SQL Server 虚拟机）。