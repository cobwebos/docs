---
title: Azure 上的 SAP BusinessObjects BI 平台部署 |Microsoft Docs
description: 在 Azure 上规划、部署和配置 SAP BusinessObjects BI 平台
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: 9d1759e5b809bc40e63fb6024fb7f107ad347da6
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094248"
---
# <a name="sap-businessobjects-bi-platform-planning-and-implementation-guide-on-azure"></a>Azure 上的 SAP BusinessObjects BI 平台规划和实施指南

## <a name="overview"></a>概述

本指南的目的是提供有关在 Azure 上规划、部署和配置 SAP BusinessObjects BI 平台（也称为 SAP BOBI Platform）的指导原则。 本指南旨在介绍与 SAP BOBI 平台相关的常见 Azure 服务和功能。 本指南不是所有可能的配置选项的详尽列表。 它介绍典型部署方案中常见的解决方案。

本指南不打算替换标准 SAP BOBI 平台安装和管理指南、操作系统或任何数据库文档。

## <a name="plan-and-implement-sap-businessobjects-bi-platform-on-azure"></a>在 Azure 上规划和实施 SAP BusinessObjects BI 平台

Microsoft Azure 提供各种服务（包括计算、存储、网络等），使企业无需冗长的采购周期即可构建其应用程序。 Azure 虚拟机 (VM) 帮助公司根据其业务需要，为不同的 SAP 应用程序（如基于 SAP NetWeaver 的应用程序、SAP Hybris、SAP BusinessObjects BI 平台）部署按需和可缩放的计算资源。 Azure 还支持跨界连接，使公司能够将 Azure 虚拟机集成到其本地域、私有云和 SAP 系统布局中。

本文档提供有关 Azure 上的 SAP BusinessObjects BI 平台规划和实施注意事项的指导。 它是对 SAP 安装文档和 SAP 说明的补充，它们代表了 SAP BOBI 的安装和部署的主要资源。

### <a name="architecture-overview"></a>体系结构概述

SAP BusinessObjects BI 平台是独立的系统，可以存在于单个 Azure 虚拟机上，也可以扩展到多个运行不同组件的 Azure 虚拟机的群集。 SAP BOBI Platform 包含六个概念层：客户端层、Web 层、管理层、存储层、处理层和数据层。  (有关每个层的详细信息，请参阅 [SAP BusinessObjects Business 智能平台](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM/4.3/en-US) 帮助门户) 中的管理员指南。 下面是每个层的高级详细信息：

- **客户端层：** 它包含所有与 BI 平台交互的桌面客户端应用程序，以提供不同种类的报告、分析和管理功能。
- **Web 层：** 它包含部署到 JAVA web 应用程序服务器的 web 应用程序。 Web 应用程序通过 web 浏览器向最终用户提供 BI 平台功能。
- **管理层：** 它协调和控制构成 BI 平台的所有组件。 它包括中央管理服务器 (CMS) 以及事件服务器和关联服务
- **存储层：** 它负责处理文件，如文档和报表。 它还处理报表缓存，以便在用户访问报表时保存系统资源。
- **处理层：** 它会分析数据，并生成报表和其他输出类型。 这是访问包含报表数据的数据库的唯一一层。
- **数据层：** 它包含托管 CMS 系统数据库和审核数据存储的数据库服务器。

SAP BI 平台包含在一个或多个主机上运行的服务器的集合。 必须根据环境的规模、业务需求和类型选择正确的部署策略，这一点非常重要。 对于较小的安装（如开发或测试），可以将单个 Azure 虚拟机用于 web 应用程序服务器、数据库服务器和所有 BI 平台服务器。 如果你使用的是数据库即服务 (DBaaS 从 Azure) 产品，则数据库服务器将与其他组件分开运行。 对于中型和大型安装，你可以让服务器在多个 Azure 虚拟机上运行。

在下图中，显示了 Azure 虚拟机上的 SAP BOBI 平台大规模部署的体系结构，其中每个组件都将分发并放置在可在服务中断的情况下维持故障转移的可用性集。

![Azure 上的 SAP BusinessObjects BI 平台体系结构](./media/businessobjects-deployment-guide/businessobjects-architecture-on-azure.png)

#### <a name="architecture-details"></a>体系结构详细信息

- 负载均衡器

  在 SAP BOBI 多实例部署中，Web 应用程序服务器 (或 web 层) 在两个或更多主机上运行。 若要在 web 服务器之间平均分配用户负载，可以在最终用户和 web 服务器之间使用负载均衡器。 在 Azure 中，可以使用 [Azure 负载均衡器](../../../load-balancer/load-balancer-overview.md) 或 [Azure 应用程序网关](../../../application-gateway/overview.md) 来管理到 web 服务器的流量。

- Web 应用程序服务器

  Web 服务器承载 SAP BOBI 平台（如 CMC 和 BI 启动板）的 web 应用程序。 若要为 web 服务器实现高可用性，必须至少部署两个 web 应用程序服务器，以管理冗余和负载平衡。 在 Azure 中，可将这些 web 应用程序服务器放置在可用性集或可用性区域中，以获得更好的可用性。

  Tomcat 是 SAP BI 平台的默认 web 应用程序。 若要实现 tomcat 的高可用性，请使用 Azure 中的 [静态成员资格侦听器](https://tomcat.apache.org/tomcat-9.0-doc/config/cluster-membership.html#Static_Membership_Attributes) 启用会话复制。 它可确保即使 tomcat 服务中断，用户也可以访问 SAP BI web 应用程序。

  > [!Important]
  > 默认情况下，Tomcat 对 Azure (SAP 说明 [2764907](https://launchpad.support.sap.com/#/notes/2764907)) 上不支持的群集使用多播 IP 和端口。

- BI 平台服务器

  BI 平台服务器包含属于 SAP BOBI 应用程序的所有服务 (管理层、处理层和存储层) 。 当 web 服务器收到请求时，它会检测每个 BI 平台服务器 (具体来说，群集中的所有 CMS 服务器都) 并自动对其请求进行负载平衡。 如果其中一个 BI 平台主机发生故障，则 web 服务器会自动将请求发送到其他主机。

  若要实现 BI 平台的高可用性或冗余，必须在至少两个 Azure 虚拟机中部署应用程序。 根据大小调整，可以缩放 BI 平台，使其在更多的 Azure 虚拟机上运行。

- 文件存储库服务器 (FRS) 

  文件存储库服务器包含已创建的所有报表和其他 BI 文档。 在多实例部署中，BI 平台服务器在多个虚拟机上运行，并且每个 VM 都应该有权访问这些报表和其他 BI 文档。 因此，需要在所有 BI 平台服务器之间共享文件系统。

  在 Azure 中，可将 [Azure 高级文件](../../../storage/files/storage-files-introduction.md) 或 [azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-introduction.md) 用于文件存储库服务器。 这两个 Azure 服务都有内置冗余。

  > [!Important]
  > Azure 文件的 SMB 协议已公开发布，但 Azure 文件的 NFS 协议支持目前为预览版。 有关详细信息，请参阅 [Azure 文件的 NFS 4.1 支持现在为预览版](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

- CMS & 审核数据库
  
  SAP BOBI 平台需要一个数据库来存储其系统数据（称为 CMS 数据库）。 它用于存储 BI 平台信息，如用户、服务器、文件夹、文档、配置和身份验证详细信息。

  Azure 提供了 [MySQL 数据库](https://azure.microsoft.com/en-us/services/mysql/) 和 [Azure SQL 数据库](https://azure.microsoft.com/en-us/services/sql-database/) 数据库即服务 (DBaaS) 产品，可用于 CMS 数据库和审核数据库。 由于这是一个 PaaS 产品，客户无需担心数据库的操作、可用性和维护。 客户还可以根据业务需要为 CMS 和审核存储库选择自己的数据库。

## <a name="support-matrix"></a>支持矩阵

本部分介绍了不同 SAP BOBI 组件（例如 SAP BusinessObjects BI Platform 版本、操作系统和 Azure 中的数据库）的可支持性。

### <a name="sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI 平台

使用 azure 基础结构即服务 (IaaS) ，你可以在 Azure 计算上部署和配置 SAP BusinessObjects BI 平台。 它支持以下版本的 SAP BOBI 平台-

- SAP BusinessObjects BI Platform 4。3
- SAP BusinessObjects BI Platform 4.2 SP04 +
- SAP BusinessObjects BI Platform 4.1 SP05 +

SAP BI 平台在不同的操作系统和数据库上运行。 在 SAP BOBI 的 [产品可用性矩阵](https://apps.support.sap.com/sap/support/pam) 中可以找到 sap BOBI 平台的可支持性。

### <a name="operating-system"></a>操作系统

Azure 支持适用于 SAP BusinessObjects BI 平台部署的以下操作系统。

- Microsoft Windows 服务器
- SUSE Linux Enterprise Server (SLES)
- Red Hat Enterprise Linux (RHEL)
- Oracle Linux (OL) 

支持在 [SAP BUSINESSOBJECTS BI 平台 (PAM) ](https://support.sap.com/pam) 中列出的操作系统版本，前提是它们兼容于在 Azure 基础结构上运行。

### <a name="databases"></a>数据库

BI 平台需要适用于 CMS 和审核数据存储的数据库，该数据库可以安装在 [SAP 产品可用性矩阵](https://support.sap.com/pam) 中列出的任何受支持的数据库上，这些数据库包括以下内容：

- Microsoft SQL Server

- [AZURE SQL database](https://azure.microsoft.com/en-us/services/sql-database/) (仅适用于 Windows) 上的 SAP BOBI 平台的受支持数据库

  它是一种完全托管的 SQL Server 数据库引擎，基于 SQL Server 的最新稳定企业版。 Azure SQL 数据库处理大多数数据库管理功能，例如升级、修补和监视，无需用户参与。 使用 Azure SQL 数据库，可以为 Azure 中的应用程序和解决方案创建高度可用且高性能的数据存储层。 有关更多详细信息，请参阅 [AZURE SQL 数据库](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md) 文档。

- [Azure Database for MySQL](https://azure.microsoft.com/en-us/services/mysql/) (遵循与 SAP PAM 中的 MySQL AB 相同的兼容性准则) 

  它是由 MySQL 社区版提供支持的关系数据库服务。 作为一项完全托管的数据库即服务 (DBaaS) 产品，它可以通过可预测的性能和动态可伸缩性来处理任务关键型工作负荷。 它提供内置的高可用性、自动备份、软件修补、自动故障检测和时间点还原，最多35天，这大大减少了操作任务。 有关更多详细信息，请查看 [Azure Database for MySQL](../../../mysql/overview.md) 文档。

- SAP HANA

- SAP ASE

- IBM DB2

- Oracle (的版本和限制，请检查 SAP 说明 [2039619](https://launchpad.support.sap.com/#/notes/2039619)) 

- MaxDB

本文档说明了在具有 Azure Database for MySQL 的 Linux 上 **通过 AZURE SQL 数据库** 和 **sap BOBI 平台**部署适用于 Windows 的 sap BOBI 平台的准则。 这也是我们建议在 Azure 上运行 SAP BusinessObjects BI 平台的方法。

## <a name="sizing"></a>调整大小

调整大小是确定有效运行应用程序的硬件要求的过程。 对于 SAP BOBI 平台，需使用名为 [Quick Sizer](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer)的 SAP 调整工具来完成大小调整。 此工具基于输入提供 SAP，后者需要映射到经过认证的 Azure 虚拟机类型。 SAP 说明 [1928533](https://launchpad.support.sap.com/#/notes/1928533) 提供受支持的 SAP 产品和 Azure VM 类型以及 sap 的列表。 有关调整大小的详细信息，请参阅 [SAP BI 大小调整指南](https://wiki.scn.sap.com/wiki/display/BOBJ/Sizing+and+Deploying+SAP+BusinessObjects+BI+4.x+Platform+and+Add-Ons)。

对于 SAP BOBI 平台的存储需求，Azure 提供不同类型的 [托管磁盘](../../managed-disks-overview.md)。 对于 SAP BOBI 安装目录，建议使用高级托管磁盘，对于在虚拟机上运行的数据库，请按照 [针对 SAP 工作负荷的 DBMS 部署](dbms_guide_general.md)中提供的指导进行操作。

Azure 支持两种 DBaaS 产品/服务，适用于 SAP BOBI 平台数据层- [AZURE SQL 数据库](https://azure.microsoft.com/en-us/services/sql-database) (在 Windows 上运行的 Bi 应用程序) [Azure Database for MySQL](https://azure.microsoft.com/en-us/services/mysql) 和在 Linux 和 Windows) 上运行 (bi 应用程序。 因此，根据调整后的结果，您可以选择最适合您需要的购买模型。

> [!Tip]
> 若要快速调整大小，请考虑 800 SAP = 1 vCPU，同时将 SAP BOBI Platform 数据库层的 sap 结果映射到 Azure (Azure SQL 数据库或 Azure Database for MySQL) 。

### <a name="sizing-models-for-azure-sql-database"></a>Azure SQL database 的大小调整模型

Azure SQL 数据库提供以下三种购买模型：

- 基于 vCore

  它允许你选择 Vcore 数、内存量以及存储量和速度。 基于 vCore 的购买模型还允许使用[适用于 SQL Server 的 Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来节省成本。 此模型适用于值灵活性、控制和透明度的客户。

  VCore 模型提供三个 [服务层选项](../../../azure-sql/database/service-tiers-vcore.md#service-tiers) ，其中包括-常规用途、业务关键和超大规模。 服务层定义与可用性和灾难恢复相关的存储体系结构、空间、i/o 限制和业务连续性选项。 下面是每个服务层选项的高级详细信息-

  1. **常规用途** 服务层最适用于业务工作负荷。 它提供面向预算、平衡和可缩放的计算和存储选项。 有关详细信息，请参阅 [资源选项和限制](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen5)。
  2. 通过使用多个独立副本，**业务关键**服务层为业务应用程序提供了故障的最高复原能力，并为每个数据库副本提供最高的 i/o 性能。 有关详细信息，请参阅 [资源选项和限制](../../../azure-sql/database/resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen5)。
  3. **超大规模** 服务层最适用于具有高度可缩放的存储和读取缩放要求的业务工作负荷。 它通过允许配置多个独立数据库副本来提供更高的故障恢复能力。 有关详细信息，请参阅 [资源选项和限制](../../../azure-sql/database/resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)。

- 基于 DTU

  基于 DTU 的购买模型在三个服务层中提供计算、内存和 i/o 资源的混合，以支持轻型和繁重的数据库工作负荷。 每个层级中的不同计算大小提供这些资源的不同组合，你可以向其添加更多的存储资源。 它最适合需要简单的预配置资源选项的客户。

  基于 DTU 的购买模型中的[服务层](../../../azure-sql/database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers)通过一系列计算大小来区分，其中包含固定量的存储、固定的备份保留期和固定价格。

- 无服务器

  无服务器模型可根据工作负荷需求自动缩放计算，并按每秒使用的计算量计费。 当只对存储计费时，无服务器计算层会自动暂停数据库，并在活动返回时自动恢复数据库。 有关详细信息，请参阅 [资源选项和限制](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)。
  
  这更适用于一段时间内的低平均计算使用量的间歇性、不可预测的使用情况。 因此，此模型可用于非生产 SAP BOBI 部署。

> [!Note]
> 对于 SAP BOBI，可以方便地使用基于 vCore 的模型，并根据业务需求选择常规用途或业务关键服务层。

### <a name="sizing-models-for-azure-database-for-mysql"></a>用于 MySQL 的 Azure 数据库的大小调整模型

Azure Database for MySQL 附带三个不同的定价层。 它们通过 Vcore 中的计算量、每个 vCore 的内存和用于存储日期的存储技术来区分。 下面是有关选项的高级详细信息，有关不同属性的详细信息，请参阅 Azure Database for MySQL 的 [定价层](../../../mysql/concepts-pricing-tiers.md) 。

- 基本

  它用于需要轻型计算和 i/o 性能的目标工作负荷。

- 常规用途

  它适用于需要均衡计算和内存以及可缩放 i/o 吞吐量的大多数业务工作负荷。

- 内存优化

  对于需要内存中性能以实现更快的事务处理和更高并发性的高性能数据库工作负荷。

> [!Note]
> 对于 SAP BOBI，根据业务工作负荷使用常规用途或内存优化定价层是非常方便的。

## <a name="azure-resources"></a>Azure 资源

### <a name="choosing-regions"></a>选择区域

Azure 区域是数据中心的一个或多个集合，其中包含运行和托管不同 Azure 服务的基础结构。 此基础结构包括大量充当计算节点或存储节点的节点，或者运行网络功能。 并非所有区域都提供相同的服务。

SAP BI 平台包含可能需要特定 VM 类型、存储（例如 Azure 文件或 Azure NetApp 文件）或数据库即服务 (DBaaS) 的不同组件，这些组件可能不会在某些区域中提供。 可以在 [区域网站提供的产品](https://azure.microsoft.com/en-us/global-infrastructure/services/) 中了解有关 VM 类型、Azure 存储类型或其他 azure 服务的确切信息。 如果已在 Azure 上运行 SAP 系统，则可能已确定区域。 在这种情况下，需要首先调查这些区域中是否提供了必需的服务，以确定 SAP BI 平台的体系结构。

### <a name="availability-zones"></a>可用性区域

可用性区域是 Azure 区域中的物理上独立的位置。 每个可用性区域都由一个或多个数据中心组成，这些数据中心配备独立的电源、冷却和网络。

若要在 SAP BI 平台的每个层上实现高可用性，可以通过实现高可用性框架，在 Azure 中提供最佳 SLA，从而在可用性区域之间分配 Vm。 对于 Azure 中的虚拟机 SLA，请检查最新版本的 [虚拟机 sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。

对于数据层，Azure 数据库即服务 (DBaaS) 服务默认提供高可用性框架。 只需选择区域和服务固有的高可用性、冗余和复原功能，就可以从计划内和计划外停机中减少数据库停机时间，而无需配置任何其他组件。 若要详细了解 Azure 上支持的 DBaaS 产品的 SLA，请查看[AZURE SQL 数据库 Azure Database for MySQL 和高可用性](../../../azure-sql/database/high-availability-sla.md)[中的高可用性](../../../mysql/concepts-high-availability.md)。

### <a name="availability-sets"></a>可用性集

可用性集是一种逻辑分组功能，用于隔离虚拟机 (VM) 资源。 Azure 可确保在可用性集中放置的 Vm 跨多个物理服务器、计算机架、存储单元和网络交换机运行。 如果发生硬件或软件故障，则只有一部分 Vm 会受到影响，整体解决方案仍可正常运行。 因此，当将虚拟机放置在可用性集中时，Azure 结构控制器会将 Vm 分发到不同的 [容错](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#fault-domains) 域和 [升级](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#upgrade-domains) 域，以防止无法访问所有 vm，因为在一个容错域中进行基础结构维护或故障。

SAP BI 平台包含许多不同的组件，而设计体系结构时，必须确保每个组件都能复原任何中断。 可以通过将每个组件的 Azure 虚拟机放入可用性集来实现此目的。 请记住，当你将不同 VM 系列的 Vm 混合到一个可用性集中时，你可能会遇到一些问题，这些问题会阻止你将特定 VM 类型添加到此类可用性集中。 因此，在体系结构概述中突出显示了针对 Web 应用程序、适用于 SAP BI 平台的 BI 应用程序的单独的可用性集。

此外，Azure 缩放单位内的 Azure 可用性集可使用的更新域和容错域数量是有限的。 因此，如果你将 Vm 添加到单个可用性集，则两个或更多 Vm 最终将在同一容错域或更新域中结束。 有关详细信息，请参阅适用于 SAP 的 Azure 虚拟机规划和实施文档中的 [Azure 可用性集](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#azure-availability-sets) 部分。

若要了解 Azure 可用性集的概念以及可用性集与容错域和升级域的关联方式，请阅读 [管理可用性](../../manage-availability.md) 一文。

> [!Important]
> Azure 可用性区域和 Azure 可用性集的概念是互斥的。 这意味着，可以将一对或多个 VM 部署到特定的可用性区域或 Azure 可用性集中。 但不能同时部署到这两者中。

### <a name="virtual-machines"></a>虚拟机

Azure 虚拟机是一种服务产品，可让你将自定义映像作为基础结构即服务 (IaaS) 实例部署到 Azure。 它可以按需提供计算资源和存储用于托管、缩放及管理 Web 应用程序与连接的应用程序，因此简化了应用程序的维护和操作。

Azure 为你的所有应用程序需求提供各种虚拟机。 但对于 SAP 工作负荷，Azure 已将选择范围缩小为适用于 SAP 工作负荷和 SAP HANA 工作负荷的不同 VM 系列。 有关更多见解，请查看 [Azure 部署支持的 SAP 软件](sap-supported-product-on-azure.md)。

根据 SAP BI 平台大小调整，你需要将你的需求映射到 azure 中的 azure 虚拟机，这在 Azure for SAP 产品中受支持。 SAP 说明 [1928533](https://launchpad.support.sap.com/#/notes/1928533) 是在 Windows 和 Linux 上列出 sap 产品支持的 Azure VM 类型的良好开端。 另请记住，除了选择纯粹受支持的 VM 类型之外，还需要检查这些 VM 类型在特定区域是否可用。 可以在 " [按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/) " 页上查看 VM 类型的可用性。 若要选择定价模型，可参阅 [Azure 虚拟机以进行 SAP 工作负荷](planning-guide.md#azure-virtual-machines-for-sap-workload)

### <a name="storage"></a>存储

Azure 存储是 Azure 托管的云服务，提供高度可用、安全、持久、可缩放和冗余的存储。 某些存储类型对 SAP 方案的使用有限。 但对于特定 SAP 工作负荷方案，多个 Azure 存储类型非常适合或已经过优化。 有关详细信息，请参阅 [适用于 Sap 工作负荷的 Azure 存储类型](planning-guide-storage.md) 指南，因为它重点介绍了适用于 sap 的不同存储选项。

Azure 存储提供了不同的可供客户使用的存储类型，有关详细信息，请参阅 [azure 中的哪些磁盘类型可用？](../../disks-types.md)。 SAP BOBI 平台使用以下 Azure 存储来生成应用程序-

- Azure 托管磁盘

  它是由 Azure 管理的块级存储卷。 在 Azure 虚拟机上安装 SAP BOBI Platform 应用程序服务器和数据库时，可以使用这些磁盘。 有不同类型的 [Azure 托管磁盘](../../managed-disks-overview.md) 可用，但建议使用 [高级 SSD](../../disks-types.md#premium-ssd) for SAP BOBI Platform 应用程序和数据库。

  在下面的示例中，高级 Ssd 用于 BOBI 平台安装目录。 对于在虚拟机上安装的数据库，可以根据准则，将托管磁盘用于数据和日志卷。 CMS 和审核数据库通常很小，并且与其他 SAP OLTP/OLAP 数据库的存储性能要求不同。

- Azure 高级文件或 Azure NetApp 文件

  在 SAP BOBI Platform 中，File Repository Server (FRS) 指的是存储诸如报表、universes 和连接等内容的磁盘目录，这些目录由该系统的所有应用程序服务器使用。 [Azure 高级文件](../../../storage/files/storage-files-introduction.md) 或 [azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-introduction.md) 存储可用作 SAP BOBI 应用程序 FRS 的共享文件系统。 由于此存储产品/服务不可用，因此请参阅 [区域网站提供的产品](https://azure.microsoft.com/en-us/global-infrastructure/services/) 以了解最新信息。

  如果该服务在你的区域中不可用，你可以创建 NFS 服务器，从中你可以将文件系统与 SAP BOBI 应用程序共享。 但你还需要考虑它的高可用性。

![Azure 上的 SAP BusinessObjects BI 平台存储布局](media/businessobjects-deployment-guide/businessobjects-storage-layout.png)

### <a name="networking"></a>网络

SAP BOBI 是一个不包含任何业务数据的报告和分析 BI 平台。 因此，系统会连接到其他数据库服务器，从中获取所有数据并为用户提供见解。 Azure 提供了一种网络基础结构，可用于映射可通过 SAP BI 平台实现的所有方案，如连接到本地系统、不同虚拟网络中的系统和其他虚拟网络。 有关详细信息，请查看 [适用于 SAP 工作负荷的网络 Microsoft Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking)。

对于数据库即服务产品，任何新创建的数据库 (Azure SQL 数据库或 Azure Database for MySQL) 都有阻止所有外部连接的防火墙。 若要允许从 BI 平台虚拟机访问 DBaaS service，需指定一个或多个服务器级防火墙规则以启用对 DBaaS 服务器的访问。 有关详细信息，请参阅 Azure SQL 数据库的 Azure Database for MySQL 和[网络访问控制](../../../azure-sql/database/network-access-controls-overview.md)部分的[防火墙规则](../../../mysql/concepts-firewall-rules.md)。

## <a name="next-steps"></a>后续步骤

- [Linux 上的 SAP BusinessObjects BI 平台部署](businessobjects-deployment-guide-linux.md)
- [适用于 SAP 的 Azure 虚拟机规划和实施](planning-guide.md)
- [适用于 SAP 的 Azure 虚拟机部署](deployment-guide.md)
- [适用于 SAP 的 Azure 虚拟机 DBMS 部署](dbms-guide.md)