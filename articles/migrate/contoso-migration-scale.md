---
title: Contoso - 到 Azure 的大规模迁移 | Microsoft Docs
description: 了解 Contoso 如何处理到 Azure 的大规模迁移。
services: azure-migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: raynew
ms.openlocfilehash: 37f0ba800cca4b096691a8bb6b43eb33a636d833
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284857"
---
# <a name="contoso---scale-a-migration-to-azure"></a>Contoso - 到 Azure 的大规模迁移

在本文中，Contoso 将执行到 Azure 的大规模迁移。 他们考虑如何规划并执行超过 3000 工作负载、8000 个数据库和超过 10,000 VM 的迁移。


我们提供一系列的文章展示虚拟公司 Contoso 如何将其本地资源迁移到 Microsoft Azure 云中，而本文是该系列中的其中一篇文章。 该系列介绍了背景和规划信息，同时提供一系列部署场景来描述如何设置迁移基础结构、评估本地资源是否适合迁移以及运行不同类型的迁移。 应用场景越来越复杂。 我们将逐渐添加其他文章进行讲解。


**文章** | **详细信息** | **Status**
--- | --- | ---
[文章 1：概述](contoso-migration-overview.md) | 概述文章系列、Contoso 的迁移策略和该系列所用的示例应用。 | 可用
[文章 2：部署 Azure 基础结构](contoso-migration-infrastructure.md) | Contoso 准备其本地和 Azure 基础结构进行迁移。 该系列的所有迁移文章共用同一个基础结构。 | 可用。
[文章 3：评估要迁移到 Azure 的本地资源](contoso-migration-assessment.md)  | Contoso 评估 VMware 上运行的本地 SmartHotel360 应用。 Contoso 使用 Azure Migrate 服务评估应用 VM，并使用数据迁移助手评估应用 SQL Server 数据库。 | 可用
[文章 4：在 Azure VM 和 SQL 数据库托管实例上重新托管应用](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso 将其本地 SmartHotel360 应用直接迁移到 Azure。 Contoso 使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 迁移应用前端 VM。 Contoso 使用 [Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/dms-overview)将应用数据库迁移到 Azure SQL 数据库托管实例。 | 可用   
[文章 5：在 Azure VM 上重新托管应用](contoso-migration-rehost-vm.md) | Contoso 使用 Site Recovery 服务将其 SmartHotel360 应用 VM 迁移到 Azure VM。 | 可用
[文章 6：在 Azure VM 和 SQL Server AlwaysOn 可用性组中重新托管应用](contoso-migration-rehost-vm-sql-ag.md) | Contoso 使用 Site Recovery 来迁移应用 VM，同时使用数据库迁移服务将应用数据库迁移到受 AlwaysOn 可用性组保护的 SQL Server 群集，从而迁移应用。 | 可用
[文章 7：将 Linux 应用重新托管到 Azure VM](contoso-migration-rehost-linux-vm.md) | Contoso 使用 Site Recovery 服务将其 Linux osTicket 应用直接迁移到 Azure VM。 | 可用
[文章 8：在 Azure VM 和 Azure Database for MySQL 上重新托管 Linux 应用](contoso-migration-rehost-linux-vm-mysql.md) | Contoso 使用 Site Recovery 将其 Linux osTicket 应用迁移到 Azure VM。 它使用 MySQL Workbench 将应用数据库迁移到 Azure Database for MySQL。 | 可用
[文章 9：在 Azure Web 应用和 Azure SQL 数据库中重构应用](contoso-migration-refactor-web-app-sql.md) | Contoso 将其 SmartHotel360 应用迁移到 Azure Web 应用，并使用数据库迁移助手将应用数据库迁移到 Azure SQL Server 实例。 | 可用    
[文章 10：在 Azure Web 应用和 Azure Database for MySQL 中重构 Linux 应用](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso 将其 Linux osTicket 应用迁移到多个站点中的 Azure Web 应用。 该 Web 应用与 GitHub 集成以便持续交付。 Contoso 将应用数据库迁移到 Azure Database for MySQL 实例。 | 可用
[文章 11：在 Azure DevOps Services 中重构 Team Foundation Server](contoso-migration-tfs-vsts.md) | Contoso 将其本地 Team Foundation Server 部署迁移到 Azure 中的 Azure DevOps Services。 | 可用
[文章 12：在 Azure 容器和 Azure SQL 数据库中重构应用](contoso-migration-rearchitect-container-sql.md) | Contoso 将其 SmartHotel 应用迁移到 Azure。 然后，它将应用 Web 层重新架构为 Azure Service Fabric 中运行的 Windows 容器，以及具有 Azure SQL 数据库的应用数据库。 | 可用    
[文章 13：在 Azure 中重新生成应用](contoso-migration-rebuild.md) | Contoso 使用一系列 Azure 功能和服务（包括 Azure 应用服务、Azure Kubernetes 服务 (AKS)、Azure Functions、Azure 认知服务和 Azure Cosmos DB）重新生成其 SmartHotel 应用。 | 可用 
文章 14：到 Azure 的大规模迁移 | 尝试过组合迁移后，Contoso 准备大规模整体迁移到 Azure。 | 本文

## <a name="business-drivers"></a>业务驱动因素

IT 领导团队与其业务合作伙伴密切协作，以了解合作伙伴希望在本次迁移中实现的目标：

- **满足业务增长**：Contoso 在不断壮大，这给本地系统和基础结构造成了压力。
- **增加效率**：Contoso 需要摒弃不必要的流程，简化开发人员和用户流程。 业务要求 IT 反应迅速，不浪费时间金钱，从而更快满足客户需求。
- **提高灵活性**：Contoso IT 需要对业务需求更加敏感。 它必须能够抢在市场变化之前作出反应，这样才能在全球经济中取得成功。 同时它不能阻碍发展，成为业务的绊脚石。
- 扩展：随着业务成功发展，Contoso IT 团队必须提供能够同步成长的系统。
- **改进成本模型**：Contoso 希望降低 IT 预算中的资本需求。  Contoso 希望使用云功能进行扩展并降低昂贵硬件的需求。
- **较低许可成本**：Contoso 希望将云成本降至最低。


## <a name="migration-goals"></a>迁移目标

Contoso 云团队制定了本次迁移的目标。 这些目标用于确定最佳迁移方式。

**要求** | **详细信息**
--- | --- 
**快速迁移到 Azure** | Contoso 希望尽快开始将应用和 VM 迁移到 Azure。
**编译完整清单** | Contoso 需要组织中所有应用、数据库和 VM 的完整清单。
**评估并对应用进行分类** | Contoso 希望充分利用云。 默认情况下，Contoso 假定所有服务将以 PaaS 的形式运行。 IaaS 将用于 PaaS 不适用的情况。 
**培训并移到 DevOps** | Contoso 希望移到 DevOps 模型。 Contoso 将提供 Azure 和 DevOps 培训，并根据需要重组团队。 


确定目标和要求后，Contoso 将查看 IT 占用情况，并确定迁移过程。

## <a name="current-deployment"></a>当前部署

规划和设置 [Azure 基础结构](contoso-migration-infrastructure.md)并尝试上表中所述不同概念证明 (POC) 迁移组合后，Contoso 已准备好实施到 Azure 的大规模迁移。 Contoso 希望迁移的内容如下。

**Item** | **数据量** | **详细信息**
--- | --- | ---
**工作负荷** | 超过 3,000 个应用 | VM 上运行的应用。<br/><br/>  应用包括 Windows、SQL 和 OSS LAMP。
**数据库** | 约 8,500 个 | 数据库包括 SQL Server、MySQL 和 PostgreSQL。
**VM** | 超过 10,000 个 | VM 在 VMware 主机上运行，并由 vCenter Server 管理。


## <a name="migration-process"></a>迁移过程

现在，Contoso 已确定业务驱动因素和迁移目标，它将确定迁移过程的 4 个阶段：

- **第 1 阶段 - 评估**：发现当前资产，并确定它们是否适合迁移到 Azure。
- **第 2 阶段 - 迁移**：将资产移到 Azure。 他们将应用和对象迁移到 Azure 的方式具体取决于应用，以及想要实现的目标。
- **第 3 阶段 - 优化**：将资源移到 Azure 后，Contoso 需要对其进行改进和简化，以获得最佳性能和最大效率。
- **第 4 阶段 - 保护和管理**：一切就绪后，Contoso 现可使用 Azure 安全和管理资源以及服务来管理、保护和监视其在 Azure 中的云应用。


上述阶段在组织中不是连续的。 Contoso 的迁移项目的每个部分将处于评估和迁移过程的不同阶段。 优化、安全和管理将贯穿整个过程。


## <a name="phase-1-assess"></a>第 1 阶段：评估

Contoso 从发现和评估本地应用、数据和基础结构开始迁移过程。 下面是 Contoso 要做的事项：

- Contoso 需要发现应用，在应用之间映射依赖项并确定迁移顺序和优先级。
- Contoso 评估时，将生成应用和资源的综合清单。 随同清单一起，Contoso 还将使用和更新现有的配置管理数据库 (CMDB) 和服务目录。
    - CMDB 保存 Contoso 应用的技术配置。
    - 服务目录记录应用的操作详细信息，包括关联的业务合作伙伴和服务级别协议 (SLA)

### <a name="discover-apps"></a>发现应用

Contoso 在各种服务器中运行的应用达数千个。 除 CMDB 和服务目录外，Contoso 还需要发现和评估工具。 

- 工具必须提供一种可将评估数据亏送到迁移过程的机制。
- 评估工具必须提供可帮助生成 Contoso 物理和虚拟资源的智能清单的数据。 数据应包括配置文件信息和性能指标。
- 发现完成后，Contoso 应具有完整的资产清单，以及与之关联的元数据。 此清单将用于定义迁移规划。

### <a name="identify-classifications"></a>标识分类

Contoso 可标识部分常见类别，以对清单中的资产进行分类。 这些分类对 Contoso 制定迁移决策至关重要。 分类列表有助于建立迁移优先级并确定复杂问题。

**类别** | **分配值** | **详细信息**
--- | --- | ---
业务组 | 业务组名称列表 | 哪个组负责清单项？
POC 候选对象 | Y/N | 应用能否用作云迁移的 POC 或早期采用者？
技术债务 | 无/部分/严重 | 清单项是否正在运行或使用不支持的产品、平台或操作系统？
防火墙影响 | Y/N | 应用是否与 Internet/外部流量进行通信？  应用是否与防火墙集成？
安全问题 | Y/N | 应用是否存在已知安全问题？  应用使用未加密数据还是过期平台？


### <a name="discover-app-dependencies"></a>发现应用依赖项

作为评估过程的一部分，Contoso 需要确定应用运行的位置，并找出应用服务器之间的依赖项和连接。 Contoso 将逐步映射环境。

1. 第一步，Contoso 了解服务器和计算机如何映射到各个应用、网络位置和组。
2. 借助此信息，Contoso 可以清楚地标识具有较少依赖项的应用，因此适合快速迁移。
3. Contoso 可以使用映射来帮助他们标识更复杂的依赖项和应用服务器之间的通信。 然后，Contoso 可以通过逻辑方式组合这些服务器，用于表示应用，并根据这些组规划迁移策略。


映射完成后，Contoso 可以确保标识所有应用组件并在制定迁移计划时加以考虑。 

![依赖项映射](./media/contoso-migration-scale/dependency-map.png)


### <a name="evaluate-apps"></a>评估应用

发现和评估过程的最后一步，Contoso 可以对评估和映射结果进行评估，以确定如何迁移服务目录中的每个应用。 

为了捕获此评估过程，他们将向清单添加多个其他分类。

**类别** | **分配值** | **详细信息**
--- | --- | ---
业务组 | 业务组名称列表 | 哪个组负责清单项？
POC 候选对象 | Y/N | 应用能否用作云迁移的 POC 或早期采用者？
技术债务 | 无/部分/严重 | 清单项是否正在运行或使用不支持的产品、平台或操作系统？
防火墙影响 | Y/N | 应用是否与 Internet/外部流量进行通信？  应用是否与防火墙集成？
安全问题 | Y/N | 应用是否存在已知安全问题？  应用使用未加密数据还是过期平台？
迁移策略 | Rehost/Refactor/Rearchitect/Rebuild | 应用需要哪种类型的迁移？ 应用将如何在 Azure 中进行部署？ [了解详细信息](contoso-migration-overview.md#migration-strategies)。
技术复杂性 | 1-5 | 迁移复杂程度如何？ 此值应由 Contoso DevOps 和相关合作伙伴定义。
业务关键性 | 1-5 | 应用对业务的重要性如何？ 例如，一个小型工作组应用可能分配到分数 1，而组织中使用的关键应用可能分配到分数 5。 此分数将影响迁移优先级别。
迁移优先级 | 1/2/3 | 应用的迁移优先级是多少？
迁移风险  | 1-5 | 迁移应用的分析等级是多少？ 此值应获得 Contoso DevOps 和相关合作伙伴同意。




### <a name="figure-out-costs"></a>算出成本

若要算出 Azure 迁移的成本和可能的节省，Contoso 可以使用[总拥有成本 (TCO) 计算器](https://azure.microsoft.com/pricing/tco/calculator/)，计算并将 Azure 的 TCO 与可比较本的地部署进行比较。

### <a name="identify-assessment-tools"></a>确定评估工具

Contoso 确定用于发现、评估和生成清单的工具。 Contoso 可确定多种 Azure 工具和服务、本机应用工具和脚本以及合作伙伴工具。 Contoso 尤其对 Azure Migrate 用于规模评估感兴趣。


#### <a name="azure-migrate"></a>Azure Migrate


在为迁移到 Azure 进行准备时，Azure Migrate 服务可助你发现和评估本地 VMware VM。 下面是 Azure Migrate 的用途：

1. 发现：发现本地 VMware VM。
    - Azure Migrate 支持从多个 vCenter 服务器（按顺序）中发现，并可以在单独的 Azure Migrate 项目中运行发现。
    - Azure Migrate 通过运行 Migrate 收集器的 VMware VM 执行发现。 相同收集器可以发现不同 vCenter 服务器上的 VM，并向不同项目发送数据。
1. 评估就绪性：评估本地计算机是否适合在 Azure 中运行。 评估内容包括：
    - 大小建议：获取 Azure VM 的大小建议，具体取决于本地 VM 的性能历史记录。
    - 估算每月成本：获取在 Azure 中运行本地计算机的估算成本。
2. 标识依赖项：可视化本地计算机的依赖项，以创建用于评估和迁移的最佳计算机组。


![Azure Migrate](./media/contoso-migration-scale/azure-migrate.png)


##### <a name="migrate-at-scale"></a>大规模迁移

Contoso 需要正确使用 Azure Migrate，以提供此迁移规模。 

- Contoso 将使用 Azure Migrate 执行逐应用评估。 这可确保 Azure Migrate 及时向 Azure 门户返回数据。
- Contoso 管理员需要了解[大规模部署 Azure Migrate](how-to-scale-assessment.md) 的内容
- Contoso 说明下表中汇总的 Azure Migrate 限制。


**Action** | **限制**
--- | ---
创建 Azure Migrate 项目 | 1500 VM
发现 | 1500 VM
评估 | 1500 VM

Contoso 将按如下方式使用 Azure Migrate：

- 在 vCenter 中，Contoso 会将 VM 整理到文件夹中。 这使他们针对特定文件夹中的 VM 运行评估时，更容易集中注意力。
- Azure Migrate 使用服务映射来评估计算机之间的依赖项。 这要求在要评估的 VM 上安装代理。 
    - Contoso 将使用自动化脚本来安装所需的 Windows 或 Linux 代理。
    - 通过编写脚本，Contoso 可以将安装推送到 vCenter 文件夹中的 VM。


#### <a name="database-tools"></a>数据库工具

除 Azure Migrate 外，Contoso 将重点关注如何使用专用于数据评估的工具。 [数据迁移助手](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)等工具有助于评估用于迁移的 SQL Server 数据库。

数据迁移助手 (DMA) 可帮助 Contoso 了解本地数据库是否与各种 Azure 数据库解决方案（例如 Azure SQL 数据库、Azure IaaS VM 上运行的 SQL Server 以及 Azure SQL 托管实例）兼容。 

除 DMS 外，Contoso 还可以使用其他脚本来发现和记录 SQL Server 数据库。 这些脚本位于 GitHub 存储库。

#### <a name="partner-tools"></a>合作伙伴工具

有多种其他合作伙伴工具，Contoso 可使用这些工具评估用于向 Azure 迁移的本地环境。 [详细了解](https://azure.microsoft.com/migration/partners/) Azure 迁移合作伙伴。  

## <a name="phase-2-migrate"></a>阶段 2：迁移

评估完成后，Contoso 需要确定工具，以将其应用、数据和基础结构迁移到 Azure。 




### <a name="migration-strategies"></a>迁移策略

有四个广泛的迁移策略可供 Contoso 考虑。 

**策略** | **详细信息** | **使用情况**
--- | --- | ---
**重新托管**  | 这种无代码选项通常称为“直接迁移”，可快速将现有应用迁移到 Azure。<br/><br/> 应用都按原样迁移，以便从云中获益，而不会产生与更改代码相关的风险或费用。 | Contoso 可以重新承载策略性不强的应用，且无需更改代码。
**重构** |  这种策略也称为“重新打包”，要求最少应用代码或配置更改，以将应用连接到 Azure PaaS，并更好地利用云功能。 | Contoso 可以重构策略应用，以保留相同的基本功能，但需要将其迁移到 Azure 平台（例如 Azure 应用服务）才能运行。<br/><br/> 这要求最少代码更改。<br/><br/> 另一方面，Contoso 必须维护 VM 平台，因为该平台不由 Microsoft 管理。
**重新架构** | 此策略修改或扩展应用的基本代码，以针对云功能和缩放优化应用体系结构。<br/><br/> 这可使应用更现代化，成为一个可复原且高度可缩放的独立部署体系结构。<br/><br/> Azure 服务可以加快进度，自信地缩放应用程序和轻松管理应用。
**重新生成** | 此策略使用云原生的技术重新生成应用。<br/><br/> Azure 平台即服务 (PaaS) 提供完整的云开发和部署环境。 它消除了软件许可的部分费用和复杂性，并且无需删除基础应用基础结构、中间件和其他资源。 | Contoso 可以从零开始重新编写关键应用，以利用云技术（例如无服务器计算机或微服务）。<br/><br/> Contoso 将管理其自身开发的应用和服务，而 Azure 将管理其他所有内容。


还必须考虑数据，尤其是 Contoso 的数据库卷。 Contoso 的默认方法是使用 PaaS 服务（例如 Azure SQL 数据库）来充分利用云功能。 通过移到适用于数据库的 PaaS 服务，Contoso 仅需维护数据，将基础平台留给 Microsoft 即可。

### <a name="evaluate-migration-tools"></a>评估迁移工具

Contoso 主要使用多个 Azure 服务和工具进行迁移：

- [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)：安排灾难恢复并将本地 VM 迁移到 Azure。
- [Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/dms-overview)：将本地数据库（如 SQL Server、MySQL 和 Oracle 等）迁移到 Azure。


#### <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery 是用于安排灾难恢复和从 Azure 内部迁移以及从本地站点迁移到 Azure 的主要 Azure 服务。

1. Site Recovery 支持和安排从本地站点到 Azure 的复制。
2. 设置并运行复制后，本地计算机可故障转移到 Azure，从而完成迁移。

Contoso 已[完成 POC](contoso-migration-rehost-vm.md)，为的是了解 Site Recovery 如何帮助他们迁移到云。

##### <a name="using-site-recovery-at-scale"></a>大规模使用 Site Recovery

Contoso 对运行多个直接迁移进行规划。 若要确保此操作正常，Site Recovery 将同时复制约 100 VM 的批处理。 要了解工作原理，Contoso 需要对建议的 Site Recovery 迁移执行容量规划。

- Contoso 需要收集有关其流量卷的信息。 具体而言：
    - Contoso 需要确定想要复制的 VM 的变化率。
    - Contoso 还需要考虑从本地站点到 Azure 的网络连接。
- 在响应容量和卷要求方面，Contoso 需要根据所需 VM 的每日数据更改率分配足够的带宽，以满足其恢复点目标 (RPO)。
- 最后，他们需要算出运行部署所需的 Site Recovery 组件需要多少服务器。

###### <a name="gather-on-premises-information"></a>收集本地信息
Contoso 可以使用 [Site Recovery 部署规划器](https://docs.microsoft.com/azure/site-recovery/site-recovery-deployment-planner)工具完成以下步骤：

- Contoso 可以使用该工具远程配置 VM，而不会对生产环境产生影响。 这有助于找出用于复制和故障转移的带宽和存储要求。
- Contoso 无需在本地安装任何 Site Recovery 组件即可运行此工具。
- 此工具将收集有关兼容的和不兼容 VM、每个 VM 的磁盘以及每个磁盘数据变化的信息。 它还可确定成功复制和故障转移所需的网络带宽要求，以及所需的 Azure 基础结构。
- Contoso 需要确保然后在匹配 Site Recovery 配置服务器最低要求的 Windows Server 计算机上运行规划器工具。 配置服务器是复制本地 VMware VM 所需的 Site Recovery 计算机。


###### <a name="identify-site-recovery-requirements"></a>确定 Site Recovery 需求

除要复制的 VM 外，Site Recovery 还需要多个组件，用于进行 VMware 迁移。

组件 | **详细信息**
--- | ---
**配置服务器** | 通常，VMware VM 使用 OVF 模板进行设置。<br/><br/> 配置服务器组件在本地和 Azure 之间协调通信并管理数据复制。
**进程服务器** | 默认安装在配置服务器上。<br/><br/> 进程服务器组件接收复制数据，通过缓存、压缩和加密对其进行优化，然后将数据发送到 Azure 存储。<br/><br/> 进程服务器还会将 Azure Site Recovery 移动服务安装在要复制的 VM 上，并在本地计算机上执行自动发现。<br/><br/> 缩放后的部署需要其他独立进程服务器才能处理大量的复制流量。
**移动服务** | 移动服务代理安装在将使用 Site Recovery 迁移的每个 VMware VM 上。  

Contoso 需要了解如何根据容量注意事项部署这些组件。

组件 | **容量需求**
--- | ---
**最大每日更改率** | 单个进程服务器可以处理多达 2 TB 的每日更改率。 由于 VM 只能使用一个进程服务器，因此复制的 VM 支持的最大每日数据更改率为 2 TB。
**最大吞吐量** | 标准 Azure 存储帐户每秒最多可以处理 20,000 个请求，因此要复制的 VM 中的每秒输入/输出操作数 (IOPS) 应保持在此限制内。 例如，如果 VM 有 5 个磁盘，每个磁盘在 VM 上生成 120 IOPS（8K 大小），则处于 Azure 的单磁盘 IOPS 限制 (500) 内。<br/><br/> 注意：所需的存储帐户数等于源计算机总 IOPS 除以 20,000。 在 Azure 中，一个复制的计算机只能属于一个存储帐户。
**配置服务器** | 根据 Contoso 总共复制 100=200 VM 的估计和[配置服务器大小调整要求](../site-recovery/site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server)，Contoso 估计需要如下所示的配置服务器计算机：<br/><br/> CPU：16 个 vCPU（2 个插槽 * 8 个核心 @ 2.5 GHz）<br/><br/> 内存：32 GB<br/><br/> 缓存磁盘：1 TB<br/><br/> 数据更改率：1-2 TB。<br/><br/> 除大小调整要求外，Contoso 还需要确保配置服务器处于最佳位置，即应位于与将迁移的 VM 相同的网络和 LAN 区段中。
**进程服务器** | Contoso 将部署独立的专用进程服务器，使其能够复制 100-200 个 VM：<br/><br/> CPU：16 个 vCPU（2 个插槽 * 8 个核心 @ 2.5 GHz）<br/><br/> 内存：32 GB<br/><br/> 缓存磁盘：1 TB<br/><br/> 数据更改率：1-2 TB。<br/><br/> 进程服务器将努力工作，并且在此情况下，它们应位于可以处理复制所需的磁盘 I/O、网络流量和 CPU 的 ESXi 主机。 为此，Contoso 将考虑使用专用主机。 
**网络** | Contoso 检查了当前的站点到站点 VPN 基础结构，并决定实现 Azure ExpressRoute。 此实现至关重要，因为它可降低延迟，并将带宽提高到 Contoso 的美国东部 2 的主要 Azure 区域。<br/><br/> **监视**：Contoso 需要密切监视源自进程服务器的数据流。 如果数据使网络带宽过载，Contoso 将考虑[限制进程服务器带宽](../site-recovery/site-recovery-plan-capacity-vmware.md#control-network-bandwidth)。
**Azure 存储** | 对于迁移，Contoso 还需创建适当类型和数量的目标 Azure 存储帐户。  Site Recovery 将 VM 数据复制到 Azure 存储。<br/><br/> Site Recovery 可复制到标准或高级 (SSD) 存储帐户。<br/><br/> 若要确定存储，Contoso 必须检查[存储限制](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)，并随着时间的推移考虑预期的增长和使用量的增加。 鉴于速度和迁移优先级，Contoso 决定使用高级存储帐户。<br/><br/> 迁移过程期间，他们将创建并重复使用多个存储帐户。
Contoso 已决定对要部署到 Azure 的所有 VM 使用托管磁盘。  所需的 IOPS 将确定磁盘为标准版 (HDD) 还是高级版 (SSD)。<br/>.<br/>


#### <a name="data-migration-service"></a>数据迁移服务 

Azure 数据库迁移服务 (DMS) 是一项完全托管的服务，支持从多个数据库源到 Azure 数据平台的无缝迁移，并且最小化停机时间。

- DMS 可集成现有工具和服务的功能。 它利用数据迁移助手 (DMA) 生成评估报告，提出有关数据库兼容性和任何必需的修改的建议。
- DMS 通过有助于在迁移前解决潜在问题的智能评估，使用简单的自我引导式迁移过程。
- DMS 可从多个源大规模迁移到目标 Azure 数据库。
- DMS 提供从 SQL Server 2005 到 SQL Server 2017 的支持。

DMS 不是唯一的 Microsoft 数据库迁移工具。 获取[工具和服务比较](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/)。

###### <a name="using-dms-at-scale"></a>大规模使用 DMS

从 SQL Server 进行迁移时，Contoso 将使用 DMS。  

- 预配 DMS 时，Contoso 需要确保它大小正确，并且已设为针对数据迁移优化性能。 Contoso 将选择“具有 4 个 vCore 的业务关键层”选项，从而使服务能够利用多个 vCPU 进行并行化并加快数据传输。

    ![DMS 缩放](./media/contoso-migration-scale/dms.png)

- Contoso 的另一个缩放策略是，数据迁移期间，暂时将 Azure SQL 或 MySQL 数据库目标实例纵向扩展到高级层 SKU。 使用低级别 SKU 时，这能将可能影响数据传输活动的数据库限制降到最低。



##### <a name="using-other-tools"></a>使用其他工具

除 DMS 外，Contoso 还可使用其他工具和服务来识别 VM 信息。

- 他们具有可帮助手动迁移的脚本。 这些脚本在 GitHub 存储库中提供。
- 此外，还可使用多种[合作伙伴工具](https://azure.microsoft.com/migration/partners/)进行迁移。


## <a name="phase-3-optimize"></a>第 3 阶段：优化

Contoso 将资源移到 Azure 后，他们需要对其进行简化来提高性能，并使用成本管理工具实现 ROI 最大化。 鉴于 Azure 是按需付费服务，因此了解系统表现并确保其大小合适对 Contoso 至关重要。


### <a name="azure-cost-management"></a>Azure 成本管理

为充分利用其云投资，Contoso 将利用免费的 Azure 成本管理工具。

- Microsoft 子公司 Cloudyn 构建的此许可解决方案，使 Contoso 能够透明准确地管理云支出。  它提供了许多用于监视、分配和降低云成本的工具。
- Azure 成本管理提供简单的仪表板报表，帮助成本分配、报销和退款。
- 成本管理通过确定可供 Contoso 稍后管理和调整的未充分利用的资源，帮助优化云支出。
- [详细了解](https://docs.microsoft.com/azure/cost-management/overview) Azure 成本管理。

    
![成本管理](./media/contoso-migration-scale/cost-management.png)  
    
 
### <a name="native-tools"></a>本机工具

Contoso 还将使用脚本来查找未使用的资源。

- 大型迁移期间，常常有残留的信息（例如产生费用的虚拟硬盘 (VHD)），但这些信息对公司没有任何价值。 脚本在 GitHub 存储库中提供。
- Contoso 将利用 Microsoft IT 部门完成的工作，并考虑实现 Azure 资源优化 (ARO) 工具包。
- Contoso 可以将包含预配置 runbook 和计划的 Azure 自动化帐户部署到其订阅中，并开始省钱。 启用或创建计划后，Azure 资源优化将自动在订阅中进行，包括对新资源的优化。
- 这可提供分散式自动化功能，以降低成本。 功能包括：
    - 基于较低 CPU 自动暂停 Azure VM。
    - 计划暂停或取消暂停 Azure VM。
    - 使用 Azure 标记，计划按升序和降序暂停或取消暂停 Azure VM。
    - 按需批量删除资源组。
- 在此 [GitHub 存储库](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit)中开始使用 ARO 工具包。

### <a name="partner-tools"></a>合作伙伴工具

可以利用合作伙伴工具（例如 [Hanu](https://hanu.com/insight/) 和 [Scalr]( https://www.scalr.com/cost-optimization/)）。


## <a name="phase-4-secure--manage"></a>阶段 4：安全和管理

在此阶段，Contos 使用 Azure 安全和管理资源来管理、保护和监视 Azure 中的云应用。 这些资源可帮助你在使用 Azure 门户中提供的产品时，运行安全且妥善管理的环境。 Contoso 可在迁移期间开始使用这些服务，并借助 Azure 混合支持，继续使用其中的许多服务，获得跨混合云一致的体验。


### <a name="security"></a>安全
Contos 依靠 Azure 安全中心跨混合云工作负载，进行统一的安全管理和高级威胁防护。

- 安全中心使你能全面了解并控制 Azure 中云应用的安全性。
- Contoso 能够启用具有适应性的威胁防护，快速检测威胁并采取应对措施，从而降低安全风险。

[详细了解](https://azure.microsoft.com/services/security-center/)安全中心。 


### <a name="monitoring"></a>监视

Contoso 需要了解新迁移的应用、基础结构和运行 Azure 的数据的运行状况和性能。 Contoso 将利用内置的 Azure 云监视工具，例如 Azure Monitor、Log Analytics 和 Application Insights。
 
- 使用上述工具，Contoso 能从各种来源轻松收集数据，并获得丰富见解。 例如，Contoso 可测量 VM 的 CPU 磁盘和内存利用率，查看多个 VM 之间的应用程序和网络依赖项，并跟踪应用程序性能。
- Contoso 将使用这些云监视工具来执行操作并将其与服务解决方案集成。
- [详细了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) Azure 监视。

### <a name="bcdr"></a>BCDR 

Contoso 需要适用于 Azure 资源的业务连续性和灾难恢复 (BCDR) 策略。

- Azure 提供[内置 BCDR 功能](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications)，以保护数据安全并使应用/服务正常运行。 
- 除内置功能外，Contoso 还希望确保其可以从故障中恢复，避免成本高昂的业务中断，满足符合性目标，并保护数据免受勒索软件和人为错误侵害。 要执行此操作
    - Contoso 将部署 Azure 备份作为 Azure 资源备份的经济解决方案。 由于 Azure 备份是内置服务，因此 Contoso 只需几个简单步骤即可设置云备份。
    - Contoso 将使用 Azure Site Recovery 设置 Azure VM 的灾难恢复，用于复制、故障转移及其指定的 Azure 区域之间的故障回复。  这可确保在主要区域中发生中断时，Contoso 所选的辅助区域中的 Azure VM 上运行的应用仍然可用。 [了解详细信息](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)。

 
## <a name="conclusion"></a>结束语

在本文中，Contoso 计划大规模迁移到 Azure。  他们将迁移过程分成 4 个阶段。 从评估和迁移，再到优化、安全性和迁移完成后的管理。 大多数情况下，都需要将迁移项目作为整个过程进行规划，而不是通过将其分解为对业务有意义的分类和数量，在系统内部迁移系统。 通过评估数据和应用分类，以及可以分解为一系列较小迁移的项目，这些小型迁移可以安全快速运行。  这些较小迁移的总和可以快速转变成指向 Azure 的大型成功迁移。
