---
title: 基于 Azure 容器和 Azure SQL 数据库重新架构 Contoso 应用 | Microsoft Docs
description: 了解 Contoso 如何基于 Azure Windows 容器和 Azure SQL 数据库重新架构应用。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 243b19d19fbce11d77f60bc2eccc4d1c58c85b80
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892178"
---
# <a name="contoso-migration-rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Contoso 迁移：将本地应用重新架构到 Azure 容器和 Azure SQL 数据库

本文演示 Contoso 如何将其 SmartHotel360 应用迁移到 Azure 中并重新架构。 Contoso 将应用前端 VM 迁移到 Azure Windows 容器，将应用数据库迁移到 Azure SQL 数据库。

本文档是系列文章中的其中一篇，目的是展示虚拟公司 Contoso 如何将本地资源迁移到 Microsoft Azure 云。 该系列介绍了背景信息，同时提供了很多应用场景来描述如何设置迁移基础结构、评估本地资源是否适合迁移以及如何运行不同类型的迁移。 应用场景越来越复杂。 我们会不断添加更多的文章。

**文章** | **详细信息** | **Status**
--- | --- | ---
[文章 1：概述](contoso-migration-overview.md) | 概述文章系列、Contoso 的迁移策略和该系列所用的示例应用。 | 可用
[文章 2：部署 Azure 基础结构](contoso-migration-infrastructure.md) | Contoso 准备其本地和 Azure 基础结构进行迁移。 该系列的所有迁移文章共用同一个基础结构。 | 可用
[文章 3：评估要迁移到 Azure 的本地资源](contoso-migration-assessment.md)  | Contoso 评估 VMware 上运行的本地 SmartHotel360 应用。 Contoso 使用 Azure Migrate 服务评估应用 VM，并使用数据迁移助手评估应用 SQL Server 数据库。 | 可用
[文章 4：在 Azure VM 和 SQL 数据库托管实例上重新托管应用](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso 将其本地 SmartHotel360 应用直接迁移到 Azure。 Contoso 使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 迁移应用前端 VM。 Contoso 使用 [Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/dms-overview)将应用数据库迁移到 Azure SQL 数据库托管实例。 | 可用   
[文章 5：在 Azure VM 上重新托管应用](contoso-migration-rehost-vm.md) | Contoso 使用 Site Recovery 服务将其 SmartHotel360 应用 VM 迁移到 Azure VM。 | 可用
[文章 6：在 Azure VM 和 SQL Server AlwaysOn 可用性组中重新托管应用](contoso-migration-rehost-vm-sql-ag.md) | Contoso 迁移 SmartHotel360 应用。 Contoso 使用 Site Recovery 迁移应用 VM。 它使用数据库迁移服务将应用数据库迁移到受 AlwaysOn 可用性组保护的 SQL Server 群集。 | 可用 
[文章 7：在 Azure VM 上重新托管 Linux 应用](contoso-migration-rehost-linux-vm.md) | Contoso 使用 Azure Site Recovery 将 Linux osTicket 应用直接迁移到 Azure VM | 可用
[文章 8：在 Azure VM 和 Azure MySQL 上重新托管 Linux 应用](contoso-migration-rehost-linux-vm-mysql.md) | Contoso 使用 Azure Site Recovery 将 Linux osTicket 应用迁移到 Azure VM，并使用 MySQL 工作台将应用数据库迁移到 Azure MySQL 服务器实例。 | 可用
[文章 9：基于 Azure Web 应用和 Azure SQL 数据库重构应用](contoso-migration-refactor-web-app-sql.md) | Contoso 将 SmartHotel360 应用迁移到 Azure Web 应用，并使用数据库迁移助手将应用数据库迁移到 Azure SQL Server 实例 | 可用
[文章 10：基于 Azure Web 应用和 Azure MySQL 重构 Linux 应用](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso 使用 Azure 流量管理器将其 Linux osTicket 应用迁移到多个 Azure 区域上的 Azure Web 应用，并与 GitHub 集成以实现持续交付。 Contoso 将应用数据库迁移到 Azure Database for MySQL 实例。 | 可用 
[文章 11：在 Azure DevOps Services 上重构 TFS](contoso-migration-tfs-vsts.md) | Contoso 将其本地 Team Foundation Server 部署迁移到 Azure 中的 Azure DevOps Services。 | 可用
文章 12：在 Azure 容器和 Azure SQL 数据库上重塑应用架构 | Contoso 将其 SmartHotel 应用迁移到 Azure。 然后，它将应用 Web 层重新架构为 Azure Service Fabric 中运行的 Windows 容器，以及具有 Azure SQL 数据库的数据库。 | 本文
[文章 13：在 Azure 中重新生成应用](contoso-migration-rebuild.md) | Contoso 使用一系列 Azure 功能和服务（包括 Azure 应用服务、Azure Kubernetes 服务 (AKS)、Azure Functions、Azure 认知服务和 Azure Cosmos DB）重新生成其 SmartHotel 应用。 | 可用 
[文章 14：到 Azure 的大规模迁移](contoso-migration-scale.md) | 尝试过组合迁移后，Contoso 准备大规模整体迁移到 Azure。 | 可用

在本文中，Contoso 将在 VMware VM 上运行的两层 Windows WPF、XAML 窗体 SmartHotel360 应用迁移到 Azure。 此应用作为开源应用提供，可在 [github](https://github.com/Microsoft/SmartHotel360) 上下载。

## <a name="business-drivers"></a>业务驱动因素

Contoso IT 领导团队与其业务合作伙伴密切协作，以了解合作伙伴希望在本次迁移中实现的目标：

- **满足业务增长**：Contoso 在不断壮大，这给本地系统和基础结构造成了压力。
- **提高效率**：Contoso 需要删除不必要的过程，为开发者和用户简化流程。  业务要求 IT 反应迅速，不浪费时间金钱，从而更快满足客户需求。
- **提高灵活性**：Contoso IT 需要对业务需求更加敏感。 它必须能够抢在市场变化之前作出反应，这样才能在全球经济中取得成功。  同时它不能阻碍发展，成为业务的绊脚石。
- **缩放**：随着业务成功发展，Contoso IT 必须提供能够同步成长的系统。
- **成本**：Contoso 希望将许可成本降至最低。

## <a name="migration-goals"></a>迁移目标

Contoso 云团队制定了本次迁移的目标。 这些目标用于确定最佳迁移方式。

**目标** | **详细信息**
--- | --- 
**应用要求** | Azure 中的该应用将如同现在一样重要。<br/><br/> 它应当具有与当前在 VMWare 中的性能相同的性能。<br/><br/> Contoso 希望停止支持当前用来运行该应用的 Windows Server 2008 R2，并愿意继续在该应用上进行投资。<br/><br/> Contoso 希望从 SQL Server 2008 R2 迁移到一个现代化 PaaS 数据库平台，这将最大程度地减少管理需求。<br/><br/> Contoso 希望尽可能利用其在 SQL Server 许可和软件保障方面的投资。<br/><br/> Contoso 希望能够纵向扩展应用 Web 层。
**限制** | 该应用包括在同一 VM 上运行的一个 ASP.NET 应用和一个 WCF 服务。 Contoso 希望使用 Azure 应用服务将此拆分到两个 Web 应用中。 
**Azure 要求** | Contoso 希望将应用移动到 Azure，并在容器中运行它以延长应用寿命。 Contoso 不希望完全从头开始在 Azure 中实现应用。 
**DevOps** | Contoso 希望借助用于代码生成和发布管道的 Azure DevOps Services 来迁移到 DevOps 模型。

## <a name="solution-design"></a>解决方案设计

在确定目标和要求后，Contoso 设计并审查部署解决方案，确定迁移流程，包括 Contoso 将用于迁移的 Azure 服务。

### <a name="current-app"></a>当前应用

- SmartHotel360 本地应用跨两个 VM（WEBVM 和 SQLVM）进行分层。
- 这两个 VM 位于 VMware ESXi 主机 contosohost1.contoso.com（6.5 版）上
- VMware 环境由 VM 上运行的 vCenter Server 6.5 (**vcenter.contoso.com**) 托管。
- Contoso 有一个本地数据中心 (contoso-datacenter)，其中包含一个本地域控制器 (**contosodc1**)。
- 迁移完成后，Contoso 数据中心的本地 VM 将停止使用。


### <a name="proposed-architecture"></a>建议的体系结构

- 对于应用的数据库层，Contoso 使用[此文章](https://docs.microsoft.com/azure/sql-database/sql-database-features)将 Azure SQL 数据库与 SQL Server 进行了比较。 Contoso 决定使用 Azure SQL 数据库，原因有以下几个：
    - Azure SQL 数据库是一种关系数据库托管服务。 它在多个服务级别提供可预测的性能，几乎不需要管理。 优点包括在不停机的情况下实现动态可伸缩性、内置的智能优化以及全局可伸缩性和可用性。
    - Contoso 利用轻型数据迁移助手 (DMA) 来评估本地数据库并将其迁移到 Azure SQL。
    - 借助软件保障，Contoso 可以使用 SQL Server 的 Azure 混合权益交换现有许可证，以获得 SQL 数据库的折扣价格。 这最多可以节省 30% 的费用。
    - SQL 数据库提供了许多安全功能，包括始终加密、动态数据掩码，以及行级安全性/威胁检测。
- 对于应用 Web 层，Contoso 已决定使用 Azure DevOps Services 将其转换为 Windows 容器。
    - Contoso 将使用 Azure Service Fabric 部署应用，并从 Azure 容器注册表 (ACR) 中拉取 Windows 容器映像。
    - 用于对应用进行扩展以包括情绪分析功能的一个原型将实现为 Service Fabric 中的另一个服务并连接到 Cosmos DB。  这将从推文读取信息并将其显示在应用上。
- 为了实施 DevOps 管道，Contoso 将配合 Git 存储库使用 Azure DevOps 进行源代码管理 (SCM)。  将使用自动生成和发行来生成代码，并将其部署到 Azure 容器注册表和 Azure Service Fabric。

    ![方案体系结构](./media/contoso-migration-rearchitect-container-sql/architecture.png) 

  
### <a name="solution-review"></a>解决方案评审
Contoso 通过将利弊清单放置在一起来评估其建议的设计。

**注意事项** | **详细信息**
--- | ---
**优点** | 要想迁移到 Azure Service Fabric，需要对 SmartHotel360 应用代码进行更改。 但是，工作量很小，可以使用 Service Fabric SDK 工具进行更改。<br/><br/> 迁移到 Service Fabric 后，Contoso 今后能够快速开始开发可添加到应用程序中的微服务，且不会给原始代码库带来风险。<br/><br/> Windows 容器提供了与一般容器相同的优点。 它们提高了敏捷性、可移植性和控制能力。<br/><br/> Contoso 可以利用适用于 SQL Server 和 Windows Server 的 Azure 混合权益利用其在软件保障方面的投资。<br/><br/> 迁移后，不再需要支持 Windows Server 2008 R2。 [了解详细信息](https://support.microsoft.com/lifecycle)。<br/><br/> Contoso 可为应用的 Web 层配置多个实例，使其不再是单一故障点。<br/><br/> 他们不再依赖于老化的 SQL Server 2008 R2。<br/><br/> SQL 数据库支持 Contoso 的技术要求。 Contoso 管理员使用数据库迁移助手对本地数据库进行了评估，发现它是兼容的。<br/><br/> SQL 数据库具有内置的容错功能，Contoso 不需要设置此功能。 这将确保数据层不再是单一故障转移点。
**缺点** | 容器比其他迁移选项更加复杂。 容器的学习曲线对于 Contoso 来说可能是一个问题。  它们引入了新一级的复杂性以提供更大的价值（而无论该曲线如何）。<br/><br/> Contoso 的运营团队需要努力理解用于应用的 Azure、容器和微服务并为其提供支持。<br/><br/> 如果 Contoso 使用数据迁移助手而非数据迁移服务来迁移其数据库，则没有随时可用于大规模迁移数据库的基础结构。



### <a name="migration-process"></a>迁移过程

1. Contoso 预配用于 Windows 的 Azure Service Fabric 群集。
2. 它将预配一个 Azure SQL 实例，并将 SmartHotel360 数据库迁移到其中。
3. Contoso 使用 Service Fabric SDK 工具将 Web 层 VM 转换为 Docker 容器。
4. 连接 Service Fabric 群集和 ACR，并使用 Azure Service Fabric 部署应用。

    ![迁移过程](./media/contoso-migration-rearchitect-container-sql/migration-process.png) 

### <a name="azure-services"></a>Azure 服务

**服务** | **说明** | **成本**
--- | --- | ---
[数据迁移助手 (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | 评估和检测可能会影响 Azure 中数据库功能的兼容性问题。 DMA 评估 SQL 源和目标之间的功能奇偶一致性，并针对性能和可靠性提升基础建议。 | 它是可以免费下载的工具。
[Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/) | 提供一个智能的、完全托管的关系型云数据库服务。 | 成本取决于功能、吞吐量和大小。 [了解详细信息](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
[Azure 容器注册表](https://azure.microsoft.com/services/container-registry/) | 用于存储所有类型的容器部署的映像。 | 成本取决于功能、存储和使用持续时间。 [了解详细信息](https://azure.microsoft.com/pricing/details/container-registry/)。
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | 构建和运行始终可用且可缩放的分布式应用 | 成本取决于计算节点的大小、位置和持续时间。 [了解详细信息](https://azure.microsoft.com/pricing/details/service-fabric/)。
[Azure DevOps](https://docs.microsoft.com/azure/azure-portal/tutorial-azureportal-devops) | 提供一个用于应用开发的持续集成和持续部署 (CI/CD) 管道。 该管道首先会连接到用于管理应用程序代码的 Git 存储库，然后依次配置用于生成包和其他生成项目的生成系统，以及用于在开发、测试及生产环境中部署更改的发布管理系统。

## <a name="prerequisites"></a>先决条件

若要运行此方案，Contoso 需具备以下项：

**要求** | **详细信息**
--- | ---
**Azure 订阅** | 在前面的系列文章中，Contoso 已创建订阅。 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。<br/><br/> 如果创建的是免费帐户，则你是自己的订阅的管理员，可以执行所有操作。<br/><br/> 如果你使用现有订阅并且不是管理员，则需要请求管理员为你分配“所有者”或“参与者”权限。
**Azure 基础结构** | [了解](contoso-migration-infrastructure.md) Contoso 事先如何设置 Azure 基础结构。
**开发人员先决条件** | Contoso 要求开发人员工作站上具有以下工具：<br/><br/> - [Visual Studio 2017 Community Edition：版本 15.5](https://www.visualstudio.com/)<br/><br/> - 启用 .NET 工作负荷。<br/><br/> - [Git](https://git-scm.com/)<br/><br/> - [Service Fabric SDK v 3.0 或更高版本](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)<br/><br/> 设置为使用 Windows 容器的 - [Docker CE (Windows 10) 或 Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/)。



## <a name="scenario-steps"></a>方案步骤

Contoso 按如下方式运行迁移：

> [!div class="checklist"]
> * **步骤 1：在 Azure 中预配 SQL 数据库实例**：Contoso 在 Azure 中预配 SQL 实例。 将前端 Web VM 迁移到 Azure 容器后，具有应用 Web 前端的容器实例将指向此数据库。
> * **步骤 2：创建 Azure 容器注册表 (ACR)**：Contoso 为 Docker 容器映像预配企业容器注册表。
> * **步骤 3：预配 Azure Service Fabric**：预配 Service Fabric 群集。
> * **步骤 4：管理 Service Fabric 证书**：Contoso 设置证书，使 Azure DevOps Services 能够访问群集。
> * **步骤 5：使用 DMA 迁移数据库**：使用数据库迁移助手迁移应用数据库。
> * **步骤 6：设置 Azure DevOps Services**：Contoso 在 Azure DevOps Services 中设置新项目，并将代码导入 Git 存储库。
> * **步骤 8：转换应用**：Contoso 使用 Azure DevOps 和 SDK 工具将应用转换为容器。
> * **步骤 8：设置生成和发布**：Contoso 设置生成和发布管道，以创建应用并将其发布到 ACR 和 Service Fabric 群集。
> * **步骤 9：扩展应用**：在应用公开后，Contoso 扩展该应用以利用 Azure 功能，并使用管道将其重新发布到 Azure。



## <a name="step-1-provision-an-azure-sql-database"></a>步骤 1：预配 Azure SQL 数据库

Contoso 管理员预配 Azure SQL 数据库。

1. 他们选择在 Azure 中创建 **SQL 数据库**。 

    ![预配 SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

2. 指定一个数据库名称以匹配在本地 VM 上运行的数据库 (**SmartHotel.Registration**)。 将数据库置于 ContosoRG 资源组中。 这是用于 Azure 中的生产资源的资源组。

    ![预配 SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

3. 在主区域中设置一个新的 SQL Server 实例 (**sql-smarthotel-eus2**)。

    ![预配 SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

4. 设置定价层以匹配服务器和数据库需求。 选择使用 Azure 混合权益来节省资金，因为他们已有一个 SQL Server 许可证。
5. 对于大小，使用基于 V 核心的购买，并且针对预期的要求设置限制。

    ![预配 SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql4.png)

6. 然后，创建数据库实例。

    ![预配 SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql5.png)

7. 创建实例后，打开数据库，并记下在使用数据库迁移助手进行迁移时需要的详细信息。

    ![预配 SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql6.png)


**需要更多帮助？**

- 在预配 SQL 数据库时[获取帮助](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)。
- [了解](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) V 核心资源限制。



## <a name="step-2-create-an-acr-and-provision-an-azure-container"></a>步骤 2：创建 ACR 并预配 Azure 容器

Azure 容器是从 Web VM 使用导出的文件创建的。 容器存放在 Azure 容器注册表 (ACR) 中。


1. Contoso 管理员在 Azure 门户中创建容器注册表。

     ![容器注册表](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

2. 为注册表提供一个名称 (**contosoacreus2**)，将其置于主区域中，并将其置于用于基础结构资源的资源组中。 为管理员用户启用访问权限，将其设置为一个高级 SKU，以便他们可以利用异地复制。

    ![容器注册表](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)  


## <a name="step-3-provision-azure-service-fabric"></a>步骤 3：预配 Azure Service Fabric

SmartHotel360 容器将在 Azure Service Fabric 群集中运行。 Contoso 管理员按如下所述创建 Service Fabric 群集：

1. 从 Azure 市场创建一个 Service Fabric 资源

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

2. 在“基本信息”中，提供群集的唯一 DS 名称，以及用于访问本地 VM 的凭据。 将资源放置在主区域“美国东部 2”中的生产资源组 (**ContosoRG**) 中。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png) 

3. 在“节点类型配置”中，输入节点类型名称、持续性设置、VM 大小和应用终结点。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png) 


4. 在“创建密钥保管库”中，在其基础结构资源组中创建一个新的密钥保管库来存放证书。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png) 


5. 在“访问策略”中，启用对虚拟机的访问权限来部署 Key Vault。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric5.png) 

6. 为证书指定名称。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric6.png) 

7. 在摘要页中，复制用来下载证书的链接。 需要使用此证书来连接到 Service Fabric 群集。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric7.png) 

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric8.png) 

8. 在通过验证后，预配群集。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric9.png) 

9. 在“证书导入向导”中，将下载的证书导入开发计算机中。 该证书用来向群集证明身份。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric10.png) 

10. 预配群集后，连接到 Service Fabric 群集资源管理器。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric11.png) 

11. 需要选择正确的证书。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric12.png) 

12. Service Fabric 资源管理器将加载，并且 Contoso 管理员可以管理群集。

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric13.png) 


## <a name="step-4-manage-service-fabric-certificates"></a>步骤 4：管理 Service Fabric 证书

Contoso 需要提供群集证书，使 Azure DevOps Services 能够访问群集。 Contoso 管理员对此进行设置。

1. 打开 Azure 门户，并浏览到 KeyVault。
2. 打开证书，并复制在预配过程中创建的证书的指纹。

    ![复制指纹](./media/contoso-migration-rearchitect-container-sql/cert1.png)
 
3. 将指纹复制到某个文本文件供稍后参考。
4. 现在，添加一个在群集上用作管理客户端证书的客户端证书。 这样，Azure DevOps Services 便可连接到群集，以便在发布管道中部署应用。 为此，可在门户中打开 KeyVault，并选择“证书” > “生成/导入”。

    ![生成客户端证书](./media/contoso-migration-rearchitect-container-sql/cert2.png)

5. 输入证书的名称，并在“使用者”中提供 X.509 可分辨名称。

     ![证书名称](./media/contoso-migration-rearchitect-container-sql/cert3.png)

6. 创建证书后，在本地下载 PFX 格式的证书。

     ![下载证书](./media/contoso-migration-rearchitect-container-sql/cert4.png)

7. 现在，返回到 KeyVault 中的证书列表，并复制刚刚创建的客户端证书的指纹。 将指纹保存在文本文件中。

     ![客户端证书指纹](./media/contoso-migration-rearchitect-container-sql/cert5.png)

8. 对于 Azure DevOps Services 部署，需要确定证书的 Base64 值。 可以使用 PowerShell 在本地开发人员工作站上执行此检查。 将输出粘贴到某个文本文件供稍后使用。

    ```powershell
        [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("C:\path\to\certificate.pfx")) 
    ```

     ![Base64 值](./media/contoso-migration-rearchitect-container-sql/cert6.png)

9. 最后，将新证书添加到 Service Fabric 群集。 为此，可在门户中打开该群集，并单击“安全性”。

     ![添加客户端证书](./media/contoso-migration-rearchitect-container-sql/cert7.png)

10. 单击“添加” > “管理客户端”，并粘贴新客户端证书的指纹。 然后单击“添加”。 此过程最长需要 15 分钟。

     ![添加客户端证书](./media/contoso-migration-rearchitect-container-sql/cert8.png)

## <a name="step-5-migrate-the-database-with-dma"></a>步骤 5：使用 DMA 迁移数据库

现在，Contoso 管理员可以使用 DMA 迁移 SmartHotel360 数据库。

### <a name="install-dma"></a>安装 DMA

1. 从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=53595)下载工具到本地 SQL Server VM SQLVM。
2. 在 VM 上运行安装程序 (DownloadMigrationAssistant.msi)。
3. 在“完成”页面上，先选择“启动 Microsoft 数据迁移助手”，再完成向导。

### <a name="configure-the-firewall"></a>配置防火墙

为了连接到 Azure SQL 数据库，Contoso 管理员设置了一个允许访问的防火墙规则。

1. 在数据库的“防火墙和虚拟网络”属性中，允许访问 Azure 服务，为本地 SQL Server VM 的客户端 IP 地址添加一个规则。
2. 创建一个服务器级防火墙规则。

    ![防火墙](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

需要更多帮助？

[了解](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)如何为 Azure SQL 数据库创建和管理防火墙规则。

### <a name="migrate"></a>迁移

Contoso 管理员现在迁移数据库。

1. 在 DMA 中，创建一个新项目 (**SmartHotelDB**) 并选择“迁移” 
2. 选择“SQL Server”作为源服务器类型，并指定“Azure SQL 数据库”作为目标。 

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-1.png)

3. 在迁移详细信息中，添加 **SQLVM** 作为源服务器，并添加 **SmartHotel.Registration** 数据库。 

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-2.png)

4. 收到一个错误，看上去与身份验证有关。 但是，在调查后，发现问题是数据库名称中的句点 (.) 造成的。 他们决定使用名称 **SmartHotel-Registration** 预配一个新的 SQL 数据库以解决此问题。 再次运行 DMA 时，可以选择 **SmartHotel-Registration** 并继续运行向导。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-3.png)

5. 在“选择对象”中，选择数据库表，并生成一个 SQL 脚本。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-4.png)

6. 在 DMS 创建脚本后，单击“部署架构”。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-5.png)

7. DMA 确认部署成功。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-6.png)

8. 现在，开始迁移。

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-7.png)

9. 在迁移完成后，Contoso 可以验证数据库是否正在 Azure SQL 实例上运行。

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-8.png)

10. 在 Azure 门户中删除多余的 SQL 数据库 **SmartHotel.Registration**。

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-9.png)


## <a name="step-6-set-up-azure-devops-services"></a>步骤 6：设置 Azure DevOps Services

Contoso 需要为应用程序构建 DevOps 基础结构和管道。  为此，Contoso 管理员需创建新的 Azure DevOps 项目、导入其代码，然后生成并发布管道。

1.   在 Contoso Azure DevOps 帐户中创建新项目 (ContosoSmartHotelRearchitect)，然后选择“Git”用于版本控制。
![新建项目](./media/contoso-migration-rearchitect-container-sql/vsts1.png)

2. 导入当前保存应用代码的 Git 存储库。 该存储库位于某个[公共存储库](https://github.com/Microsoft/SmartHotel360-internal-booking-apps)中，可以下载它。

    ![下载应用代码](./media/contoso-migration-rearchitect-container-sql/vsts2.png)

3. 导入代码后，将 Visual Studio 连接到存储库，并使用团队资源管理器克隆代码。

4. 将存储库克隆到开发人员计算机后，打开该应用的解决方案文件。 在该文件中，Web 应用和 WCF 服务各自具有独立的项目。

    ![解决方案文件](./media/contoso-migration-rearchitect-container-sql/vsts4.png)

## <a name="step-7-convert-the-app-to-a-container"></a>步骤 7：将应用转换为容器

该本地应用是一个传统的三层应用：

- 它包含 WebForms 和连接到 SQL Server 的一个 WCF 服务。
- 它使用实体框架与 SQL 数据库中的数据相集成，通过一个 WCF 服务公开该数据。
- WebForms 应用程序与该 WCF 服务进行交互。

Contoso 管理员使用 Visual Studio 和 SDK 工具将应用转换为容器，如下所述：


1. 使用 Visual Studio，查看本地存储库的 **SmartHotel360-internal-booking-apps\src\Registration** 目录中已打开的解决方案文件 (SmartHotel.Registration.sln)。  此时将显示两个应用。 Web 前端 SmartHotel.Registration.Web 和 WCF 服务应用 SmartHotel.Registration.WCF。

    ![容器](./media/contoso-migration-rearchitect-container-sql/container2.png)


2. 右键单击 Web 应用 >“添加” > “容器业务流程协调程序支持”。
3. 在“添加容器业务流程协调程序支持”中，选择“Service Fabric”。

    ![容器](./media/contoso-migration-rearchitect-container-sql/container3.png)
    
4. Contoso 为 SmartHotel.Registration.WCF 应用重复此过程。
5. 现在，检查解决方案发生了哪些变化。

    - 新的应用是 **SmartHotel.RegistrationApplication/**
    - 该应用包含两个服务：SmartHotel.Registration.WCF 和 SmartHotel.Registration.Web。

    ![容器](./media/contoso-migration-rearchitect-container-sql/container4.png)

6. Visual Studio 创建了 Docker 文件，将所需的映像拉取到本地的开发人员计算机。

    ![容器](./media/contoso-migration-rearchitect-container-sql/container5.png)

7. Visual Studio 创建并打开清单文件 (**ServiceManifest.xml**)。 此文件告诉 Service Fabric 将容器部署到 Azure 时如何配置容器。

    ![容器](./media/contoso-migration-rearchitect-container-sql/container6.png)

8. 另一个清单文件 (**ApplicationManifest.xml) 包含容器的配置应用程序。

    ![容器](./media/contoso-migration-rearchitect-container-sql/container7.png)

9. 打开 **ApplicationParameters/Cloud.xml** 文件，并更新连接字符串，以将应用连接到 Azure SQL 数据库。 可在 Azure 门户中的数据库中找到连接字符串。

    ![连接字符串](./media/contoso-migration-rearchitect-container-sql/container8.png)

10. 提交更新的代码并推送到 Azure DevOps Services。

    ![提交](./media/contoso-migration-rearchitect-container-sql/container9.png)

## <a name="step-8-build-and-release-pipelines-in-azure-devops-services"></a>步骤 8：在 Azure DevOps Services 中生成和发布管道

Contoso 管理员现在配置 Azure DevOps Services 来执行生成和发布过程，以实施 DevOps 工作。

1. 在 Azure DevOps Services 中，单击“生成和发布” > “新建管道”。

    ![新建管道](./media/contoso-migration-rearchitect-container-sql/pipeline1.png)

2. 选择“Azure DevOps Services Git”和相关的存储库。

    ![Git 和存储库](./media/contoso-migration-rearchitect-container-sql/pipeline2.png)

3. 在“选择模板”中，选择 Docker 支持的结构。

     ![Fabric 和 Docker](./media/contoso-migration-rearchitect-container-sql/pipeline3.png)
    
4. 将“操作标记映像”更改为“生成映像”，并将任务配置为使用预配的 ACR。

     ![注册表](./media/contoso-migration-rearchitect-container-sql/pipeline4.png)

5. 在“推送映像”任务中，配置要推送到 ACR 的映像，并选择包含最新的标记。
6. 在“触发器”中启用持续集成，并添加主分支。

    ![触发器](./media/contoso-migration-rearchitect-container-sql/pipeline5.png)

7. 单击“保存并排队”以开始生成。
8. 生成成功后，转到发布管道。 在 Azure DevOps Services 中单击“发布” > “新管道”。

    ![发布管道](./media/contoso-migration-rearchitect-container-sql/pipeline6.png)    

9. 选择“Azure Service Fabric 部署”模板，并命名阶段 (SmartHotelSF)。

    ![环境](./media/contoso-migration-rearchitect-container-sql/pipeline7.png)

10. 提供管道名称 (ContosoSmartHotel360Rearchitect)。 对于阶段，单击“1 个工作，1 个任务”，以配置 Service Fabric 部署。

    ![阶段和任务](./media/contoso-migration-rearchitect-container-sql/pipeline8.png)

11. 现在，单击“新建”以添加新的群集连接。

    ![新建连接](./media/contoso-migration-rearchitect-container-sql/pipeline9.png)

12. 在“添加 Service Fabric 服务连接”中，配置连接和身份验证设置，以供 Azure DevOps Services 在部署应用时使用。 可在 Azure 门户中找到群集终结点。Contoso 添加了 **tcp://** 作为前缀。
13. 收集的证书信息已输入到“服务器证书指纹”和“客户端证书”。

    ![证书](./media/contoso-migration-rearchitect-container-sql/pipeline10.png)

13. 单击“管道”>“添加项目”。

     ![项目](./media/contoso-migration-rearchitect-container-sql/pipeline11.png)

14. 选择项目，并选择使用最新版本的生成管道。

     ![构建](./media/contoso-migration-rearchitect-container-sql/pipeline12.png)

15. 请注意，项目中的闪电符号已选中。

     ![项目状态](./media/contoso-migration-rearchitect-container-sql/pipeline13.png)

16. 另请注意，已启用持续部署触发器。

   ![已启用持续部署](./media/contoso-migration-rearchitect-container-sql/pipeline14.png) 

17. 单击“保存” > “创建发布”。

    ![发布](./media/contoso-migration-rearchitect-container-sql/pipeline15.png)

18. 部署完成后，SmartHotel360 现在将运行 Service Fabric。

    ![发布](./media/contoso-migration-rearchitect-container-sql/publish4.png)

19. 为连接到应用，Contoso 将流量定向到 Service Fabric 节点前面的 Azure 负载均衡器的公共 IP 地址。

    ![发布](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-9-extend-the-app-and-republish"></a>步骤 9：扩展应用并重新发布

当 SmartHotel360 应用和数据库在 Azure 中运行后，Contoso 希望扩展应用。

- Contoso 的开发人员在开发将在 Service Fabric 群集上运行的新 .NET Core 应用程序的原型。
- 该应用将用来从 CosmosDB 拉取情绪数据。
- 该数据将采用推文形式，使用无服务器 Azure 函数以及认知服务文本分析 API 进行处理。

### <a name="provision-azure-cosmos-db"></a>预配 Azure Cosmos DB

Contoso 管理员首先预配 Azure Cosmos 数据库。

1. 从 Azure 市场创建 Azure Cosmos DB 资源。

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend1.png)

2. 提供数据库名称 (**contososmarthotel**)，选择 SQL API，并将资源放置在主区域“美国东部 2”中的生产资源组中。

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend2.png)

3. 在“开始使用”中，选择“数据资源管理器”，然后添加一个新集合。
4. 在“添加集合”中，提供 ID 并设置存储容量和吞吐量。

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend3.png)

5. 在门户中，打开新数据库 >“集合” > “文档”并单击“新建文档”。
6. 将以下 JSON 代码粘贴到文档窗口中。 这是单条推文形式的示例数据。

    ```json
    {
            "id": "2ed5e734-8034-bf3a-ac85-705b7713d911",
            "tweetId": 927750234331580911,
            "tweetUrl": "https://twitter.com/status/927750237331580911",
            "userName": "CoreySandersWA",
            "userAlias": "@CoreySandersWA",
            "userPictureUrl": "",
            "text": "This is a tweet about #SmartHotel360",
            "language": "en",
            "sentiment": 0.5,
            "retweet_count": 1,
            "followers": 500, 
            "hashtags": [
                ""
            ]
    }
    ```

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend4.png)

7. 找到 Cosmos DB 终结点和身份验证密钥。 这些在应用中用来连接到集合。 在数据库中，单击“密钥”，将 URI 和主密钥复制到记事本。

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend5.png)

### <a name="update-the-sentiment-app"></a>更新情绪应用

预配 Cosmos DB 后，Contoso 管理员可以配置应用以连接到它。

1. 在 Visual Studio 中，在解决方案资源管理器中打开文件 ApplicationModern\ApplicationParameters\cloud.xml。

    ![情绪应用](./media/contoso-migration-rearchitect-container-sql/sentiment1.png)

2. 填写以下两个参数：

   ```xml
   <Parameter Name="SentimentIntegration.CosmosDBEndpoint" Value="[URI]" />
   ```
   
   ```xml
   <Parameter Name="SentimentIntegration.CosmosDBAuthKey" Value="[Key]" />
   ```

    ![情绪应用](./media/contoso-migration-rearchitect-container-sql/sentiment2.png)

### <a name="republish-the-app"></a>重新发布应用

扩展应用后，Contoso 管理员使用管道将其重新发布到 Azure。

1. 将代码提交并推送到 Azure DevOps Services。 这会启动生成和发布管道。

2. 完成生成和部署后，SmartHotel360 现在将运行 Service Fabric。 Servie Fabric 管理控制台现在显示了三个服务。

    ![重新发布](./media/contoso-migration-rearchitect-container-sql/republish3.png)

3. 现在，可以单击各个服务来查看 SentimentIntegration 应用是否已启动且正在运行。

    ![重新发布](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>迁移后的清理

在迁移后，Contoso 需要完成以下清理步骤：  

- 从 vCenter 清单中删除本地 VM。
- 从本地备份作业中删除 VM。
- 更新内部文档以显示 SmartHotel360 应用的新位置。 以在 Azure SQL 数据库中运行的形式显示数据库，以在 Service Fabric 中运行的形式显示前端。
- 查看与已停用的 VM 交互的所有资源，并更新所有相关设置或文档以反映新配置。


## <a name="review-the-deployment"></a>查看部署

Azure 显示已迁移的资源后，Contoso 需要积极行动、全面保护新的基础结构。

### <a name="security"></a>安全

- Contoso 管理员需要确保其新的 **SmartHotel-Registration** 数据库是安全的。 [了解详细信息](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)。
- 尤其重要的是，他们应当更新容器以将 SSL 与证书配合使用。
- 他们应当考虑使用密钥保管库来保护其 Service Fabric 应用的机密。 [了解详细信息](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)。

### <a name="backups"></a>备份

- Contoso 需要审查 Azure SQL 数据库的备份要求。 [了解详细信息](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。
- Contoso 管理员应考虑实现故障转移组来为数据库提供区域性故障转移。 [了解详细信息](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)。
- 他们可以利用 ACR 高级 SKU 的异地复制。 [了解详细信息](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)。
- 当用于容器的 Web 应用可用时，Contoso 需要考虑在主区域“美国东部 2”和“美国中部”区域中部署 Web 应用。 Contoso 管理员可以配置流量管理器，以确保发生区域性故障时进行故障转移。
- Cosmos DB 自动备份。 [阅读](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore)此过程以了解详细信息。

### <a name="licensing-and-cost-optimization"></a>许可和成本优化

- 部署所有资源后，Contoso 应根据[基础结构规划](contoso-migration-infrastructure.md#set-up-tagging)分配 Azure 标记。
- 所有许可成本都包括在 Contoso 使用的 PaaS 服务的成本中。 这将从企业协议中扣除。
- Contoso 将启用由 Microsoft 子公司 Cloudyn 许可的 Azure 成本管理。 该服务是一个多云成本管理解决方案，可帮助利用和管理 Azure 与其他云资源。  [详细了解](https://docs.microsoft.com/azure/cost-management/overview) Azure 成本管理。

## <a name="conclusion"></a>结束语

在本文中，Contoso 通过将应用前端 VM 迁移到 Service Fabric 在 Azure 中重构了 SmartHotel360 应用。 应用数据库已迁移到 Azure SQL 数据库。





