---
title: 通过将 Contoso 应用迁移到 Azure Web 应用和 Azure SQL 数据库对其进行重构 | Microsoft Docs
description: 了解 Contoso 如何通过将本地应用迁移到 Azure Web 应用和 Azure SQL Server 数据库来重新托管它。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: raynew
ms.openlocfilehash: 39444b20dfefd947abb2f2bc00a9945398996dd0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47040527"
---
# <a name="contoso-migration-refactor-an-on-premises-app-to-an-azure-web-app-and-azure-sql-database"></a>Contoso 迁移：将本地应用重构到 Azure Web 应用和 Azure SQL 数据库

本文演示了 Contoso 如何在 Azure 中重构 SmartHotel360 应用。 他们将应用前端 VM 迁移到 Azure Web 应用，将应用数据库迁移到 Azure SQL 数据库。

本文档是系列文章中的其中一篇，目的是展示虚拟公司 Contoso 如何将本地资源迁移到 Microsoft Azure 云。 该系列介绍了背景信息，同时提供了很多应用场景来描述如何设置迁移基础结构、评估本地资源是否适合迁移以及如何运行不同类型的迁移。 应用场景越来越复杂。 我们将不断添加更多的文章。

**文章** | **详细信息** | **Status**
--- | --- | ---
[文章 1：概述](contoso-migration-overview.md) | 简要介绍 Contoso 的迁移策略、文章系列和所使用的示例应用。 | 可用
[文章 2：部署 Azure 基础结构](contoso-migration-infrastructure.md) | 介绍 Contoso 如何装备其本地和 Azure 基础结构进行迁移。 所有迁移文章共用同一个基础结构。 | 可用
[文章 3：访问本地资源](contoso-migration-assessment.md)  | 展示 Contoso 如何评估 VMware 上运行的本地双层 SmartHotel 应用。 Contoso 使用 [Azure Migrate](migrate-overview.md) 服务评估应用 VM，使用 [数据库迁移助手](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)评估应用 SQL Server 数据库。 | 可用
[文章 4：将应用重新托管到 Azure VM 和 SQL 托管实例](contoso-migration-rehost-vm-sql-managed-instance.md) | 演示 Contoso 如何将 SmartHotel 应用直接迁移到 Azure。 Contoso 使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 迁移应用前端 VM，使用 [Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/dms-overview)将应用数据库迁移到 SQL 托管实例。 | 可用
[文章 5：将应用重新托管到 Azure VM](contoso-migration-rehost-vm.md) | 展示 Contoso 如何在仅使用 Site Recovery 的情况下迁移 SmartHotel 应用 VM。 | 可用
[文章 6：将应用重新托管到 Azure VM 和 SQL Server AlwaysOn 可用性组](contoso-migration-rehost-vm-sql-ag.md) | 展示 Contoso 如何迁移 SmartHotel 应用。 Contoso 使用 Site Recovery 来迁移应用 VM，同时使用数据库迁移服务将应用数据库迁移到受 AlwaysOn 可用性组保护的 SQL Server 群集。 | 可用
[文章 7：将 Linux 应用重新托管到 Azure VM](contoso-migration-rehost-linux-vm.md) | 展示 Contoso 如何使用 Site Recovery 将 Linux osTicket 应用直接迁移到 Azure VM | 可用
[文章 8：将 Linux 应用重新托管到 Azure VM 和 Azure MySQL 服务器](contoso-migration-rehost-linux-vm-mysql.md) | 演示 Contoso 如何使用 Site Recovery 将 Linux osTicket 应用迁移到 Azure VM，以及如何使用 MySQL 工作台将应用数据库迁移到 Azure MySQL 服务器实例。 | 可用
文章 9：将应用重构到 Azure Web 应用和 Azure SQL 数据库 | 演示 Contoso 如何将 SmartHotel 应用迁移到 Azure Web 应用，并将应用数据库迁移到 Azure SQL Server 实例 | 本文
[文章 10：将 Linux 应用重构到 Azure Web 应用和 Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | 演示 Contoso 如何将 Linux 应用 osTicket 迁移到多个站点中的 Azure Web 应用，并集成 GitHub 以实现持续交付。 他们将应用数据库迁移到 Azure MySQL 实例。 | 可用
[文章 11：重构 Azure DevOps Services 上的 TFS](contoso-migration-tfs-vsts.md) | 展示 Contoso 如何通过将其本地 Team Foundation Server (TFS) 部署迁移到 Azure 中的 Azure DevOps Services 来迁移该部署。 | 可用
[文章 12：基于 Azure 容器和 Azure SQL 数据库重构应用](contoso-migration-rearchitect-container-sql.md) | 展示 Contoso 如何将其 SmartHotel 应用迁移并重新架构到 Azure。 他们将应用 Web 层重新架构为 Windows 容器，将应用数据库重新架构到 Azure SQL 数据库中。 | 可用
[文章 13：在 Azure 中重新生成应用](contoso-migration-rebuild.md) | 展示 Contoso 如何使用一系列 Azure 功能和服务（包括应用服务、Azure Kubernetes、Azure Functions、认知服务和 Cosmos DB）重新生成其 SmartHotel 应用。 | 可用

在本文中，Contoso 将 VMware VM 上运行的双层 Windows. VMware VM 上运行的 NET SmartHotel360 应用迁移到 Azure。 此应用作为开源应用提供，可在 [github](https://github.com/Microsoft/SmartHotel360) 上下载。

## <a name="business-drivers"></a>业务驱动因素

IT 领导团队与其业务合作伙伴密切协作，以了解合作伙伴希望在本次迁移中实现的目标：

- **满足业务增长**：Contoso 在不断壮大，这给本地系统和基础结构造成了压力。
- **增加效率**：Contoso 需要摒弃不必要的流程，简化开发人员和用户流程。  业务要求 IT 反应迅速，不浪费时间金钱，从而更快满足客户需求。
- **提高灵活性**：Contoso IT 需要对业务需求更加敏感。 它必须能够抢在市场变化之前作出反应，这样才能在全球经济中取得成功。  同时它不能阻碍发展，成为业务的绊脚石。
- **扩展**：随着业务成功发展，Contoso IT 必须提供能够同步成长的系统。
- **成本**：Contoso 希望将许可成本降至最低。

## <a name="migration-goals"></a>迁移目标

Contoso 云团队制定了本次迁移的目标。 这些目标用于确定最佳迁移方式。

**要求** | **详细信息**
--- | --- 
**应用** | Azure 中的该应用将如同现在一样重要。<br/><br/> 它应当具有与当前在 VMWare 中的性能相同的性能。<br/><br/> 团队不打算对此应用进行投资。 目前，管理员只是将应用安全地移动到云。<br/><br/> 团队希望停止支持当前用来运行此应用的 Windows Server 2008 R2。<br/><br/> 团队还希望从 SQL Server 2008 R2 迁移到一个现代化 PaaS 数据库平台，这将在最大程度上减少管理需求。<br/><br/> Contoso 希望尽可能利用其在 SQL Server 许可和软件保障方面的投资。<br/><br/> 此外，Contoso 还希望减少 Web 层上的单一故障点。
**限制** | 该应用包括在同一 VM 上运行的一个 ASP.NET 应用和一个 WCF 服务。 他们希望使用 Azure 应用服务将此拆分到两个 Web 应用中。 
**Azure** | Contoso 希望将应用迁移到 Azure，但不希望在 VM 上运行它。 Contoso 希望为 Web 层和数据层利用 Azure PaaS 服务。 
**DevOps** | Contoso 希望借助用于他们生成和发布管道的 Azure DevOps 来迁移到 DevOps 模型。

## <a name="solution-design"></a>解决方案设计

在确定目标和要求后，Contoso 将设计和审查部署解决方案，并确定迁移流程，包括将用于迁移的 Azure 服务。

### <a name="current-app"></a>当前应用

- SmartHotel360 本地应用跨两个 VM（WEBVM 和 SQLVM）进行分层。
- 这两个 VM 位于 VMware ESXi 主机 contosohost1.contoso.com（6.5 版）上
- VMware 环境由 VM 上运行的 vCenter Server 6.5 (**vcenter.contoso.com**) 托管。
- Contoso 有一个本地数据中心 (contoso-datacenter)，其中包含一个本地域控制器 (**contosodc1**)。
- 迁移完成后，Contoso 数据中心的本地 VM 将停止使用。


### <a name="proposed-solution"></a>建议的解决方案

- 对于应用的数据库层，Contoso 使用[此文章](https://docs.microsoft.com/azure/sql-database/sql-database-features)将 Azure SQL 数据库与 SQL Server 进行了比较。 Contoso 决定使用 Azure SQL 数据库，原因如下：
    - Azure SQL 数据库是一种关系数据库托管服务。 它在多个服务级别提供可预测的性能，几乎不需要管理。 优点包括在不停机的情况下实现动态可伸缩性、内置的智能优化以及全局可伸缩性和可用性。
    - Contoso 可以利用轻型数据迁移助手 (DMA) 来评估本地数据库并将其迁移到 Azure SQL。
    - 借助软件保障，Contoso 可以使用适用于 SQL Server 的 Azure 混合权益交换现有许可证，以获得 SQL 数据库的折扣价格。 这最多可以节省 30% 的费用。
    - SQL 数据库提供了许多安全功能，包括始终加密、动态数据掩码，以及行级安全性/威胁检测。
- 对于应用 Web 层，Contoso 已决定使用 Azure 应用服务。 使用此 PaaS 服务，只需要执行几项配置更改便可部署应用。 Contoso 将使用 Visual Studio 进行更改，并部署两个 Web 应用。 一个用于网站，一个用于 WCF 服务。
- 为了满足 DevOps 管道的要求，Contoso 已选择配合 Git 存储库使用 Azure DevOps 进行源代码管理 (SCM)。 自动化的生成和发布将用于生成代码，并将其部署到 Azure Web 应用。
  
### <a name="solution-review"></a>解决方案评审
Contoso 通过将利弊清单放置在一起来评估其建议的设计。

**注意事项** | **详细信息**
--- | ---
**优点** | SmartHotel360 应用代码不需要更改便可迁移到 Azure。<br/><br/> Contoso 可以使用适用于 SQL Server 和 Windows Server 的 Azure 混合权益利用其在软件保障方面的投资。<br/><br/> 迁移后，无需再为 Windows Server 2008 R2 提供支持。 [了解详细信息](https://support.microsoft.com/lifecycle)。<br/><br/> Contoso 可为应用的 Web 层配置多个实例，使其不再是单一故障点。<br/><br/> 数据库将不再依赖于老化的 SQL Server 2008 R2。<br/><br/> SQL 数据库支持相关技术要求。 Contoso 使用数据库迁移助手对本地数据库进行了评估，发现它是兼容的。<br/><br/> SQL 数据库具有内置的容错功能，Contoso 不需要设置此功能。 这将确保数据层不再是单一故障转移点。
**缺点** | 对于每个 Web 应用，Azure 应用服务仅支持一个应用部署。 这意味着必须预配两个 Web 应用（一个用于网站，一个用于 WCF 服务）。<br/><br/> 如果 Contoso 使用数据迁移助手而非数据迁移服务来迁移其数据库，则没有随时可用于大规模迁移数据库的基础结构。 Contoso 将需要构建另一个区域来确保当主区域不可用时能够进行故障转移。

## <a name="proposed-architecture"></a>建议的体系结构

![方案体系结构](media/contoso-migration-refactor-web-app-sql/architecture.png) 


### <a name="migration-process"></a>迁移过程

1. Contoso 将预配一个 Azure SQL 实例，并将 SmartHotel360 数据库迁移到其中。
2. Contoso 将预配和配置 Web 应用，并将 SmartHotel360 应用部署到其中。

    ![迁移过程](media/contoso-migration-refactor-web-app-sql/migration-process.png) 

### <a name="azure-services"></a>Azure 服务

**服务** | **说明** | **成本**
--- | --- | ---
[数据迁移助手 (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Contoso 将使用 DMA 来评估和检测可能影响其在 Azure 中数据库功能的兼容性问题。 DMA 评估 SQL 源和目标之间的功能奇偶一致性，并针对性能和可靠性提升基础建议。 | 它是可以免费下载的工具。
[Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/) | 一个智能的、完全托管的关系型云数据库服务。 | 成本取决于功能、吞吐量和大小。 [了解详细信息](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
[Azure 应用服务 - Web 应用](https://docs.microsoft.com/azure/app-service/app-service-web-overview) | 使用完全托管的平台，创建功能强大的云应用 | 成本取决于大小、位置和使用持续时间。 [了解详细信息](https://azure.microsoft.com/pricing/details/app-service/windows/)。
[Azure DevOps](https://docs.microsoft.com/azure/azure-portal/tutorial-azureportal-devops) | 提供一个用于应用开发的持续集成和持续部署 (CI/CD) 管道。 该管道首先会连接到用于管理应用程序代码的 Git 存储库，然后依次配置用于生成包和其他生成项目的生成系统，以及用于在开发、测试及生产环境中部署更改的发布管理系统。 

## <a name="prerequisites"></a>先决条件

若要运行此方案，Contoso 需要以下项目：

**要求** | **详细信息**
--- | ---
**Azure 订阅** | 在前面的文章中，Contoso 已创建了订阅。 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。<br/><br/> 如果创建的是免费帐户，则你是自己的订阅的管理员，可以执行所有操作。<br/><br/> 如果你使用现有订阅并且不是管理员，则需要请求管理员为你分配“所有者”或“参与者”权限。
**Azure 基础结构** | [了解](contoso-migration-infrastructure.md) Contoso 如何设置 Azure 基础结构。


## <a name="scenario-steps"></a>方案步骤

Contoso 按如下方式运行迁移：

> [!div class="checklist"]
> * 步骤 1：在 Azure 中预配 SQL 数据库实例**：Contoso 在 Azure 中预配 SQL 实例。 将应用网站迁移到 Azure 后，WCF 服务 Web 应用将指向此实例。
> * 步骤 2：使用 DMA 迁移数据库：Contoso 使用数据库迁移助手迁移应用数据库。
> * 步骤 3：预配 Web 应用：Contoso 将预配两个 Web 应用。
> * 步骤 4：设置 Azure DevOps：Contoso 将创建新的 Azure DevOps 项目，并导入 Git 存储库。
> * 步骤 5：配置连接字符串：Contoso 将配置连接字符串，这样使 Web 层 Web 应用、WCF 服务 Web 应用和 SQL 实例能够进行通信。
> * 步骤 6：设置生成和发布管道：最后一步，Contoso 设置生成和发布管道以创建应用，并将其部署到两个单独的 Azure Web 应用。


## <a name="step-1-provision-an-azure-sql-database"></a>步骤 1：预配 Azure SQL 数据库

1. Contoso 管理员选择在 Azure 中创建 SQL 数据库。 

    ![预配 SQL](media/contoso-migration-refactor-web-app-sql/provision-sql1.png)

2. 指定一个数据库名称以匹配在本地 VM 上运行的数据库 (**SmartHotel.Registration**)。 将数据库置于 ContosoRG 资源组中。 这是用于 Azure 中的生产资源的资源组。

    ![预配 SQL](media/contoso-migration-refactor-web-app-sql/provision-sql2.png)

3. 在主区域中设置一个新的 SQL Server 实例 (**sql-smarthotel-eus2**)。

    ![预配 SQL](media/contoso-migration-refactor-web-app-sql/provision-sql3.png)

4. 设置定价层以匹配其服务器和数据库需求。 选择使用 Azure 混合权益来节省资金，因为他们已有一个 SQL Server 许可证。
5. 对于大小，使用基于 V 核心的购买，并且针对其预期的要求设置限制。

    ![预配 SQL](media/contoso-migration-refactor-web-app-sql/provision-sql4.png)

6. 然后，创建数据库实例。

    ![预配 SQL](media/contoso-migration-refactor-web-app-sql/provision-sql5.png)

7. 创建实例后，打开数据库，并记下在使用数据库迁移助手进行迁移时需要的详细信息。

    ![预配 SQL](media/contoso-migration-refactor-web-app-sql/provision-sql6.png)


**需要更多帮助？**

- 在预配 SQL 数据库时[获取帮助](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)。
- [了解](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) V 核心资源限制。


## <a name="step-2-migrate-the-database-with-dma"></a>步骤 2：使用 DMA 迁移数据库

Contoso 管理员将使用 DMA 迁移 SmartHotel360 数据库。

### <a name="install-dma"></a>安装 DMA

1. 从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=53595)下载工具到本地 SQL Server VM SQLVM。
2. 在 VM 上运行安装程序 (DownloadMigrationAssistant.msi)。
3. 在“完成”页面上，先选择“启动 Microsoft 数据迁移助手”，再完成向导。

### <a name="migrate-the-database-with-dma"></a>使用 DMA 迁移数据库

1. 在 DMA 中，他们将创建一个新项目 (SmartHotelDB) 并选择“迁移” 
2. 选择“SQL Server”作为源服务器类型，并指定“Azure SQL 数据库”作为目标。 

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-1.png)

3. 在迁移详细信息中，添加 **SQLVM** 作为源服务器，并添加 **SmartHotel.Registration** 数据库。 

     ![DMA](media/contoso-migration-refactor-web-app-sql/dma-2.png)

4. 收到一个错误，看上去与身份验证有关。 但是，在调查后，发现问题是数据库名称中的句点 (.) 造成的。 他们决定使用名称 **SmartHotel-Registration** 预配一个新的 SQL 数据库以解决此问题。 再次运行 DMA 时，可以选择 **SmartHotel-Registration** 并继续运行向导。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-3.png)

5. 在“选择对象”中，选择数据库表，并生成一个 SQL 脚本。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-4.png)

6. 在 DMS 创建脚本后，单击“部署架构”。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-5.png)

7. DMA 确认部署成功。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-6.png)

8. 现在，开始迁移。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-7.png)

9. 在迁移完成后，Contoso 管理员可以验证数据库是否正在 Azure SQL 实例上运行。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-8.png)

10. 在 Azure 门户中删除多余的 SQL 数据库 **SmartHotel.Registration**。

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-9.png)


## <a name="step-3-provision-web-apps"></a>步骤 3：预配 Web 应用

迁移数据库后，Contoso 管理员现在可以预配两个 Web 应用。

1. 在门户中选择“Web 应用”。

    ![Web 应用](media/contoso-migration-refactor-web-app-sql/web-app1.png)

2. 提供应用名称 (**SHWEB-EUS2**)，在 Windows 上运行它，并将其放置在生产资源组 **ContosoRG** 中。 创建一个新的应用服务和计划。

    ![Web 应用](media/contoso-migration-refactor-web-app-sql/web-app2.png)

3. 预配 Web 应用后，重复此过程来为 WCF 服务创建一个 Web 应用 (**SHWCF-EUS2**)

    ![Web 应用](media/contoso-migration-refactor-web-app-sql/web-app3.png)

4. 完成后，浏览到应用地址来检查它们是否已成功创建。


## <a name="step-4-set-up-azure-devops"></a>步骤 4：设置 Azure DevOps


Contoso 需要为应用程序构建 DevOps 基础结构和管道。  为此，Contoso 管理员需创建新的 DevOps 项目、导入代码，然后设置生成和发布管道。

1.   在 Contoso Azure DevOps 帐户中，创建新项目 (ContosoSmartHotelRefactor)，然后选择“Git”用于版本控制。

    ![新建项目](./media/contoso-migration-refactor-web-app-sql/vsts1.png)
2. 导入当前保存应用代码的 Git 存储库。 该存储库位于某个[公共存储库](https://github.com/Microsoft/SmartHotel360-internal-booking-apps)中，可以下载它。

    ![下载应用代码](./media/contoso-migration-refactor-web-app-sql/vsts2.png)
    
3. 导入代码后，将 Visual Studio 连接到存储库，并使用团队资源管理器克隆代码。

    ![连接到项目](./media/contoso-migration-refactor-web-app-sql/devops1.png)

4. 将存储库克隆到开发人员计算机后，打开该应用的解决方案文件。 在该文件中，Web 应用和 WCF 服务各自具有独立的项目。

    ![解决方案文件](./media/contoso-migration-refactor-web-app-sql/vsts4.png)
    

## <a name="step-5-configure-connection-strings"></a>步骤 5：配置连接字符串

Contoso 管理员需要确保 Web 应用和数据库都能够进行通信。 为此，他们在代码和 Web 应用中配置连接字符串。

1. 在用于 WCF 服务的 Web 应用 (**SHWCF-EUS2**) >“设置” > “应用程序设置”中，添加一个名为 **DefaultConnection** 的新连接字符串。
2. 连接字符串是从 **SmartHotel-Registration** 数据库中拉取的，应当使用正确的凭据更新它。

    ![连接字符串](media/contoso-migration-refactor-web-app-sql/string1.png)

3. 使用 Visual Studio，他们将打开解决方案文件中的“SmartHotel.Registration.wcf”项目。 应当将 WCF 服务 SmartHotel.Registration.Wcf 的 web.config 文件的 **connectionStrings** 部分更新为连接字符串。

     ![连接字符串](media/contoso-migration-refactor-web-app-sql/string2.png)

4. 应当将 SmartHotel.Registration.Web 的 web.config 文件的 **client** 部分更改为指向 WCF 服务的新位置。 这是承载着服务终结点的 WCF Web 应用的 URL。

    ![连接字符串](media/contoso-migration-refactor-web-app-sql/strings3.png)

5. 在代码中进行更改后，管理员需要提交所做的更改。 在 Visual Studio 中使用“团队资源管理器”进行提交和同步。


## <a name="step-6-set-up-build-and-release-pipelines-in-azure-devops"></a>步骤 6：在 Azure DevOps 中设置生成和发布管道

Contoso 管理员现在配置 Azure DevOps 来执行生成和发布过程。

1. 在 Azure DevOps 中，单击“生成和发布” > “新建管道”。

    ![新建管道](./media/contoso-migration-refactor-web-app-sql/pipeline1.png)

2. 选择“Azure Repos Git”和相关的存储库。

    ![Git 和存储库](./media/contoso-migration-refactor-web-app-sql/pipeline2.png)

3. 在“选择模板”中，他们将为生成选择 ASP.NET 模板。

     ![ASP.NET 模板](./media/contoso-migration-refactor-web-app-sql/pipeline3.png)
    
4. 名称“ContosoSmartHotelRefactor-ASP.NET-CI”用于生成。 单击“保存和队列”。

     ![保存并排队](./media/contoso-migration-refactor-web-app-sql/pipeline4.png)

5. 这将启动第一个生成。 他们可单击生成号以监视进程。 完成后，可以看到进程反馈，单击“项目”可查看生成结果。

    ![审阅](./media/contoso-migration-refactor-web-app-sql/pipeline5.png)

6. “Drop”文件夹中包含生成结果。

    - 两个 zip 文件是包含应用的包。
    - 这些文件在用于部署到 Azure Web 应用的发布管道中使用

     ![项目](./media/contoso-migration-refactor-web-app-sql/pipeline6.png)

7. 单击“发布” > “+新建管道”。

    ![新建管道](./media/contoso-migration-refactor-web-app-sql/pipeline7.png)

8. 选择 Azure 应用服务部署模板。

    ![Azure 应用服务模板](./media/contoso-migration-refactor-web-app-sql/pipeline8.png)

9. 将发布管道命名为“ContosoSmartHotel360Refactor”，将 WCF Web 应用的名称 (SHWCF-EUS2) 指定为“阶段”的名称。

    ![环境](./media/contoso-migration-refactor-web-app-sql/pipeline9.png)

10. 在此阶段中，单击“1 个作业，1 个任务”，以配置 WCF 服务的部署。

    ![部署 WCF](./media/contoso-migration-refactor-web-app-sql/pipeline10.png)

11. 确认订阅已选定并授权，然后选择“应用服务名称”。

     ![选择应用服务](./media/contoso-migration-refactor-web-app-sql/pipeline11.png)

12. 在“管道”>“项目”中，选择“+添加项目”，并选择使用“ContosoSmarthotel360Refactor”管道生成。

     ![构建](./media/contoso-migration-refactor-web-app-sql/pipeline12.png)

15. 单击所选项目上的闪电符号以启用持续部署触发器。

     ![闪电符号](./media/contoso-migration-refactor-web-app-sql/pipeline13.png)

16. 持续部署触发器应已设置为“已启用”。

   ![已启用持续部署](./media/contoso-migration-refactor-web-app-sql/pipeline14.png) 

17. 现在，再次返回“1 个作业，1 个任务”阶段，然后单击“部署 Azure 应用服务”。

    ![部署应用服务](./media/contoso-migration-refactor-web-app-sql/pipeline15.png)

18. 在“选择文件或文件夹”中，找到生成期间创建的 SmartHotel.Registration.Wcf.zip 文件，然后单击“保存”。-sql

    ![保存 WCF](./media/contoso-migration-refactor-web-app-sql/pipeline16.png)

19. 单击“管道” > “阶段”以及“+添加”，为“SHWEB-EUS2”添加环境。 选择另一个 Azure 应用服务部署。

    ![添加环境](./media/contoso-migration-refactor-web-app-sql/pipeline17.png)

20. 重复此过程来将 Web 应用 (SmartHotel.Registration.Web) 文件发布到正确的 Web 应用。

    ![发布 Web 应用](./media/contoso-migration-refactor-web-app-sql/pipeline18.png)

21. 保存后，发布管道将如下所示。

     ![发布管道摘要](./media/contoso-migration-refactor-web-app-sql/pipeline19.png)

22. 返回“生成”，然后单击“触发器” > “启用持续集成”。 这将启用管道，从而当更改提交到代码时能够进行完整的生成和发布。

    ![启用持续集成](./media/contoso-migration-refactor-web-app-sql/pipeline20.png)

23. 单击“保存并排队”以运行完整管道。 将触发一个新生成，这反而会向 Azure 应用服务创建应用的第一个版本。

    ![保存管道](./media/contoso-migration-refactor-web-app-sql/pipeline21.png)

24. Contoso 管理员可以从 Azure DevOps 中按照生成和发布管道流程进行操作。 生成完成后，将启动发布。

    ![生成和发布应用](./media/contoso-migration-refactor-web-app-sql/pipeline22.png)

25. 管道完成后，这两个站点均已部署，并且应用已启动并正在联机运行。

    ![完成发布](./media/contoso-migration-refactor-web-app-sql/pipeline23.png)

此时，应用已成功迁移到 Azure。



## <a name="clean-up-after-migration"></a>迁移后的清理

在迁移后，Contoso 需要完成以下清理步骤：  

- 从 vCenter 清单中删除本地 VM。
- 从本地备份作业中删除 VM。
- 更新内部文档以显示 SmartHotel360 应用的新位置。 以在 Azure SQL 数据库中运行的形式显示数据库，以在两个 Web 应用中运行的形式显示前端。
- 查看与已停用的 VM 交互的所有资源，并更新所有相关设置或文档以反映新配置。


## <a name="review-the-deployment"></a>查看部署

Azure 显示已迁移的资源后，Contoso 需要积极行动、全面保护新的基础结构。

### <a name="security"></a>安全

- Contoso 需要确保其新的 SmartHotel-Registration 数据库是安全的。 [了解详细信息](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)。
- 尤其重要的是，Contoso 应更新 Web 应用以将 SSL 与证书配合使用。

### <a name="backups"></a>备份

- Contoso 需要审查 Azure SQL 数据库的备份要求。 [了解详细信息](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。
- Contoso 还需要了解如何管理 SQL 数据库备份和还原。 [详细了解](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)自动备份。
- Contoso 应考虑实现故障转移组来为数据库提供区域性故障转移。 [了解详细信息](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)。
- Contoso 需要考虑在主区域“美国东部 2”和“美国中部”区域中部署 Web 应用以提高恢复能力。 Contoso 可以对流量管理器进行配置以确保发生区域性故障时进行故障转移。

### <a name="licensing-and-cost-optimization"></a>许可和成本优化

- 部署所有资源后，Contoso 应当根据其[基础结构规划](contoso-migration-infrastructure.md#set-up-tagging)分配 Azure 标记。
- 所有许可成本都包括在 Contoso 使用的 PaaS 服务的成本中。 这将从企业协议中扣除。
- Contoso 将启用由 Microsoft 子公司 Cloudyn 许可的 Azure 成本管理。 该服务是一个多云成本管理解决方案，可帮助利用和管理 Azure 与其他云资源。  [详细了解](https://docs.microsoft.com/azure/cost-management/overview) Azure 成本管理。

## <a name="conclusion"></a>结束语

在本文中，Contoso 通过将应用前端 VM 迁移到两个 Azure Web 应用在 Azure 中重构了 SmartHotel360 应用。 应用数据库已迁移到 Azure SQL 数据库。






