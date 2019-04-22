---
title: 在 Azure 中重新生成 Contoso 本地应用 | Microsoft Docs
description: 了解 Contoso 如何使用 Azure 应用服务、Kubernetes 服务、CosmosDB、Azure Functions 和认知服务在 Azure 中重新生成应用。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 74c33d73f15c4edf63a02ea5c9a0cdcad88bb68c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59049739"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Contoso 迁移：在 Azure 中重新生成本地应用

本文演示了 Contoso 如何在 Azure 中迁移并重新生成 SmartHotel360 应用。 Contoso 将应用的前端 VM 迁移到 Azure 应用服务 Web 应用。 应用后端是使用微服务生成的，这些微服务部署到由 Azure Kubernetes 服务 (AKS) 管理的容器。 站点与 Azure Functions 进行交互，提供宠物照片功能。 

本文档是系列文章中的其中一篇，目的是展示虚拟公司 Contoso 如何将本地资源迁移到 Microsoft Azure 云。 该系列介绍了背景信息，同时提供了很多应用场景来描述如何设置迁移基础结构、评估本地资源是否适合迁移以及如何运行不同类型的迁移。 应用场景越来越复杂。 我们将不断添加更多的文章。


**文章** | **详细信息** | **Status**
--- | --- | ---
[文章 1：概述](contoso-migration-overview.md) | 简要介绍 Contoso 的迁移策略、文章系列和所使用的示例应用。 | 可用
[文章 2：部署 Azure 基础结构](contoso-migration-infrastructure.md) | 介绍 Contoso 如何装备其本地和 Azure 基础结构进行迁移。 所有迁移文章共用同一个基础结构。 | 可用
[文章 3：访问本地资源](contoso-migration-assessment.md)  | 展示 Contoso 如何评估 VMware 上运行的本地双层 SmartHotel360 应用。 Contoso 使用 [Azure Migrate](migrate-overview.md) 服务评估应用 VM，使用 [数据库迁移助手](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)评估应用 SQL Server 数据库。 | 可用
[文章 4：在 Azure VM 和 SQL 托管实例上重新托管应用](contoso-migration-rehost-vm-sql-managed-instance.md) | 演示 Contoso 如何将 SmartHotel360 应用直接迁移到 Azure。 Contoso 使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 迁移应用前端 VM，使用 [Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/dms-overview)将应用数据库迁移到 SQL 托管实例。 | 可用
[文章 5：在 Azure VM 上重新托管应用](contoso-migration-rehost-vm.md) | 展示 Contoso 如何在仅使用 Site Recovery 的情况下迁移 SmartHotel360 应用 VM。 | 可用
[文章 6：将应用重新托管到 Azure VM 和 SQL Server AlwaysOn 可用性组](contoso-migration-rehost-vm-sql-ag.md) | 展示 Contoso 如何迁移 SmartHotel360 应用。 Contoso 使用 Site Recovery 来迁移应用 VM，同时使用数据库迁移服务将应用数据库迁移到受 AlwaysOn 可用性组保护的 SQL Server 群集。 | 可用
[文章 7：在 Azure VM 上重新托管 Linux 应用](contoso-migration-rehost-linux-vm.md) | 展示 Contoso 如何使用 Site Recovery 将 Linux osTicket 应用直接迁移到 Azure VM | 可用
[文章 8：在 Azure VM 和 Azure MySQL 服务器上重新托管 Linux 应用](contoso-migration-rehost-linux-vm-mysql.md) | 演示 Contoso 如何使用 Site Recovery 将 Linux osTicket 应用迁移到 Azure VM，以及如何使用 MySQL 工作台将应用数据库迁移到 Azure MySQL 服务器实例。 | 可用
[文章 9：基于 Azure Web 应用和 Azure SQL 数据库重构应用](contoso-migration-refactor-web-app-sql.md) | 演示 Contoso 如何将 SmartHotel360 应用迁移到 Azure Web 应用，并将应用数据库迁移到 Azure SQL Server 实例 | 可用
[文章 10：将 Linux 应用重构到 Azure Web 应用和 Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | 演示 Contoso 如何将 Linux 应用 osTicket 迁移到多个站点中的 Azure Web 应用，并集成 GitHub 以实现持续交付。 他们将应用数据库迁移到 Azure MySQL 实例。 | 可用
[文章 11：在 Azure DevOps Services 上重构 TFS](contoso-migration-tfs-vsts.md) | 展示 Contoso 如何通过将本地 Team Foundation Server (TFS) 部署迁移到 Azure 中的 Azure DevOps Services 来迁移该部署。 | 可用
[文章 12：将应用重新架构到 Azure 容器和 SQL 数据库](contoso-migration-rearchitect-container-sql.md) | 展示 Contoso 如何将其 SmartHotel 应用迁移并重新架构到 Azure。 他们将应用 Web 层重新架构为 Windows 容器，将应用数据库重新架构到 Azure SQL 数据库中。 | 可用
文章 13：在 Azure 中重新生成应用 | 展示 Contoso 如何使用一系列 Azure 功能和服务（包括应用服务、Azure Kubernetes、Azure Functions、认知服务和 Cosmos DB）重新生成其 SmartHotel 应用。 | 本文
[文章 14：到 Azure 的大规模迁移](contoso-migration-scale.md) | 尝试过组合迁移后，Contoso 准备大规模整体迁移到 Azure。 | 可用

在本文中，Contoso 将 VMware VM 上运行的双层 Windows. VMware VM 上运行的 NET SmartHotel360 应用迁移到 Azure。 此应用作为开源应用提供，可在 [github](https://github.com/Microsoft/SmartHotel360-Backend) 上下载。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="business-drivers"></a>业务驱动因素

IT 领导团队与其业务合作伙伴密切协作，以了解合作伙伴希望在本次迁移中实现的目标：

- **满足业务增长**：Contoso 在不断发展，并且想要为 Contoso 网站上的客户提供独特的体验。
- **敏捷性**：Contoso 必须能够抢在市场变化之前做出反应，这样才能在全球经济环境中取得成功。 
- **缩放**：随着业务成功发展，Contoso IT 团队必须提供能够同步成长的系统。
- **成本**：Contoso 希望将许可成本降至最低。

## <a name="migration-goals"></a>迁移目标

Contoso 云团队制定了本次迁移的应用要求。 这些要求用于确定最佳迁移方式：
 - Azure 中的该应用将如同现在一样重要。 它应具有良好的性能并能轻松缩放。
 - 应用不应使用 IaaS 组件。 所有内容都应生成为使用 PaaS 或无服务器服务。
 - 所有应用版本都应在云服务中运行，并且容器应当位于云中的专用企业级容器注册表中。
 - 在真实世界中，用于宠物照片的 API 服务应当是准确可靠的，因为应用做出的决策在酒店中必须得到尊重。 应当允许经授权进入的任何宠物停留在酒店中。
 - 为了满足 DevOps 管道的要求，Contoso 将配合 Git 存储库使用 Azure DevOps 进行源代码管理 (SCM)。  自动化的生成和发布将用于生成代码，并将其部署到 Azure Web 应用、Azure Functions 和 AKS。
 - 后端的微服务和前端的网站需要不同的 CI/CD 管道。
 - 后端服务与前端 Web 应用具有不同的发布周期。  为满足此要求，两者将部署两个不同的 DevOps 管道。
 - Contoso 在部署所有前端网站时需获得管理批准，并且 CI/CD 管道必须提供此批准。


## <a name="solution-design"></a>解决方案设计

在确定目标和要求后，Contoso 将设计和审查部署解决方案，并确定迁移流程，包括将用于迁移的 Azure 服务。

### <a name="current-app"></a>当前应用

- SmartHotel360 本地应用跨两个 VM（WEBVM 和 SQLVM）进行分层。
- 这两个 VM 位于 VMware ESXi 主机 contosohost1.contoso.com（6.5 版）上
- VMware 环境由 VM 上运行的 vCenter Server 6.5 (**vcenter.contoso.com**) 托管。
- Contoso 有一个本地数据中心 (contoso-datacenter)，其中包含一个本地域控制器 (**contosodc1**)。
- 迁移完成后，Contoso 数据中心的本地 VM 将停止使用。


### <a name="proposed-architecture"></a>建议的体系结构

- 应用的前端将在主 Azure 区域中部署为 Azure 应用服务 Web 应用。
- 一个 Azure 函数将提供宠物照片上传功能，站点将与此功能进行交互。
- 该宠物照片函数利用认知服务视觉 API 和 CosmosDB。
- 站点的后端是使用微服务生成的。 这些微服务将部署到在 Azure Kubernetes 服务 (AKS) 上管理的容器中。
- 容器将使用 Azure DevOps 进行生成，并推送到 Azure 容器注册表 (ACR)。
- 现在，Contoso 将使用 Visual Studio 手动部署 Web 应用和函数代码
- 将使用一个 PowerShell 脚本部署微服务，该脚本调用 Kubernetes 命令行工具。

    ![方案体系结构](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>解决方案评审

Contoso 通过将利弊清单放置在一起来评估其建议的设计。

**注意事项** | **详细信息**
--- | ---
**优点** | 为端到端部署使用 PaaS 和无服务器解决方案可以显著降低 Contoso 必须提供的管理时间。<br/><br/> 迁移到微服务体系结构可以使 Contoso 能够随时间推移轻松扩展解决方案。<br/><br/> 可以在联机状态下添加新功能，不会破坏任何现有的解决方案代码库。<br/><br/> Web 应用将配置有多个实例，不存在单一故障点。<br/><br/> 将启用自动缩放，以便应用可以处理不同的流量大小。<br/><br/> 迁移到 PaaS 服务后，Contoso 可以停用在 Windows Server 2008 R2 操作系统上运行的过时解决方案。<br/><br/> CosmosDB 具有内置的容错功能，这不需要 Contoso 进行配置。 这意味着数据层不再是单一故障转移点。
**缺点** | 容器比其他迁移选项更加复杂。 学习曲线对于 Contoso 来说可能是一个问题。  它们引入了新一级的复杂性以提供更大的价值（而无论该曲线如何）。<br/><br/> Contoso 的运营团队需要努力理解用于应用的 Azure、容器和微服务并为其提供支持。<br/><br/> Contoso 还没有完全为整个解决方案实现 DevOps。 Contoso 需要考虑将服务部署到 AKS、函数和应用程序服务。



### <a name="migration-process"></a>迁移过程

1. Contoso 预配 ACR、AKS 和 CosmosDB。
2. 他们为部署预配基础结构，包括 Azure Web 应用、存储帐户、函数和 API。 
3. 在基础结构就位后，他们将使用 Azure DevOps 生成微服务容器映像，Azure DevOps 将这些映像推送到 ACR。
4. Contoso 将使用一个 PowerShell 脚本将这些微服务部署到 ASK。
5. 最后，他们将部署 Azure 函数和 Web 应用。

    ![迁移过程](./media/contoso-migration-rebuild/migration-process.png) 

### <a name="azure-services"></a>Azure 服务

**服务** | **说明** | **成本**
--- | --- | ---
[AKS](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | 简化 Kubernetes 管理、部署和操作。 提供完全托管的 Kubernetes 容器业务流程服务。  | AKS 是一项免费服务。  只需要为使用的虚拟机、关联的存储和网络资源付费。 [了解详细信息](https://azure.microsoft.com/pricing/details/kubernetes-service/)。
[Azure Functions](https://azure.microsoft.com/services/functions/) | 通过事件驱动型无服务器计算体验加快开发速度。 按需缩放。  | 只需要为使用的资源付费。 计划按每秒资源消耗和执行次数收费。 [了解详细信息](https://azure.microsoft.com/pricing/details/functions/)。
[Azure 容器注册表](https://azure.microsoft.com/services/container-registry/) | 用于存储所有类型的容器部署的映像。 | 成本取决于功能、存储和使用持续时间。 [了解详细信息](https://azure.microsoft.com/pricing/details/container-registry/)。
[Azure 应用服务](https://azure.microsoft.com/services/app-service/containers/) | 快速生成、部署和缩放在任何平台上运行的企业级 Web 应用、移动应用和 API 应用。 | 应用服务计划按秒收费。 [了解详细信息](https://azure.microsoft.com/pricing/details/app-service/windows/)。

## <a name="prerequisites"></a>必备组件

对于此方案，Contoso 需具备以下项：

**要求** | **详细信息**
--- | ---
**Azure 订阅** | 在前面的文章中，Contoso 已创建订阅。 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。<br/><br/> 如果创建的是免费帐户，则你是自己的订阅的管理员，可以执行所有操作。<br/><br/> 如果你使用现有订阅并且不是管理员，则需要请求管理员为你分配“所有者”或“参与者”权限。
**Azure 基础结构** | [了解](contoso-migration-infrastructure.md) Contoso 如何设置 Azure 基础结构。
**开发人员先决条件** | Contoso 要求开发人员工作站上具有以下工具：<br/><br/> - [Visual Studio 2017 Community Edition：版本 15.5](https://www.visualstudio.com/)<br/><br/> 启用 .NET 工作负荷。<br/><br/> [Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> 设置为使用 Windows 容器的 [Docker CE (Windows 10) 或 Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/)。



## <a name="scenario-steps"></a>方案步骤

Contoso 按如下方式运行迁移：

> [!div class="checklist"]
> * **步骤 1：预配 AKS 和 ACR**：Contoso 使用 PowerShell 预配托管 AKS 群集和 Azure 容器注册表
> * **步骤 2：生成 Docker 容器**：使用 Azure DevOps 为 Docker 容器设置 CI，并将容器推送到 ACR。
> * **步骤 3：部署后端微服务**：部署后端微服务将利用的基础结构的其余部分。
> * **步骤 4：部署前端基础结构**：部署前端基础结构，包括存储宠物照片的 blob 存储、Cosmos DB 和视觉 API。
> * **步骤 5：迁移后端**：部署微服务并在 AKS 上运行以迁移后端。
> * **步骤 6：发布前端**：将 SmartHotel360 应用发布到 Azure 应用服务以及宠物服务将调用的 Function App。



## <a name="step-1-provision-back-end-resources"></a>步骤 1：预配后端资源

Contoso 管理员运行部署脚本来使用 AKS 和 Azure 容器注册表 (ACR) 创建托管 Kubernetes 群集。

- 本部分的说明中使用的是 **SmartHotel360-Azure-backend** 存储库。
- **SmartHotel360-Azure-backend** GitHub 存储库包含用于部署的本部分的所有软件。

### <a name="prerequisites"></a>必备组件

1. 在开始之前，Contoso 管理员确保他们使用的部署在开发计算机上安装中的所有必备软件。
2. 使用 Git: git 克隆**https://github.com/Microsoft/SmartHotel360-Azure-backend.git**将存储库克隆到本地的开发计算机


### <a name="provision-aks-and-acr"></a>预配 AKS 和 ACR

Contoso 管理员预配以下项：

1.  使用 Visual Studio Code 打开文件夹，转到 /deploy/k8s 目录，其中包含脚本 gen-aks-env.ps1。
2. 运行脚本来使用 AKS 和 ACR 创建托管 Kubernetes 群集。

    ![AKS](./media/contoso-migration-rebuild/aks1.png)
 
3.  打开文件后，将 $location 参数更新为 **eastus2** 并保存该文件。

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

4. 单击“查看” > “集成终端”以在 Code 中打开集成终端。

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

5. 在 PowerShell 集成终端中，他们在登录到 Azure 中使用连接 AzAccount 命令。 [详细了解](https://docs.microsoft.com/powershell/azure/get-started-azureps)如何开始使用 PowerShell。

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

6. 在 Azure CLI 中进行身份验证：运行 **az login** 命令并根据说明使用 Web 浏览器进行身份验证。 [详细了解](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)使用 Azure CLI 进行登录。

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

7. 运行以下命令，并传递资源组名称 ContosoRG、AKS 群集 smarthotel-aks-eus2 的名称以及新的注册表名称。

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```
    ![AKS](./media/contoso-migration-rebuild/aks6.png)

8. Azure 创建另一个资源组，使其包含 AKS 群集的资源。

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

9. 部署完成后，安装 kubectl 命令行工具。 Azure CloudShell 上已安装了此工具。

    **az aks install-cli**

10. 通过运行 **kubectl get nodes** 命令，验证到群集的连接。 在自动创建的资源组中，节点与 VM 同名。

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

11. 运行以下命令来启动 Kubernetes 仪表板： 

    **az aks browse --resource-group ContosoRG --name smarthotelakseus2**

12. 仪表板中将打开一个浏览器选项卡。 这是使用 Azure CLI 的隧道连接。 

    ![AKS](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-configure-the-back-end-pipeline"></a>步骤 2：配置后端管道

### <a name="create-an-azure-devops-project-and-build"></a>创建 Azure DevOps 项目和版本

Contoso 创建 Azure DevOps 项目，配置 CI 生成来创建容器，然后将其推送到 ACR。 本部分中的说明中使用的是 [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) 存储库。

1. 从 visualstudio.com 中，创建一个新组织 (contosodevops360.visualstudio.com) 并将其配置为使用 Git。

2. 创建一个新项目 (SmartHotelBackend) 并使用 Git 进行版本控制，使用 Agile 来管理工作流。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts1.png) 


3. 导入 [GitHub 存储库](https://github.com/Microsoft/SmartHotel360-Backend)。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts2.png)
    
4. 在“管道”中，单击“生成”，并在存储库中通过将 Azure Repos Git 作为源使用来创建新的管道。 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts3.png)

6. 选择使用一个空作业开始。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts4.png)  

7. 对于生成管道，选择“托管 Linux 预览版”。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts5.png) 
 
8. 在“第 1 阶段”中，添加一个 **Docker Compose** 任务。 此任务生成 Docker Compose。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts6.png) 

9. 重复操作并添加另一个 **Docker Compose** 任务。 此操作将容器推送到 ACR。

     ![Azure DevOps](./media/contoso-migration-rebuild/vsts7.png) 

8. 选择（要生成的）第一个任务，为生成配置 Azure 订阅、授权和 ACR。 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts8.png)

9. 指定存储库的 src 文件夹中的 docker-compose.yaml 文件的路径。 选择生成服务映像，并包括最新标记。 当操作更改为“生成服务映像”时，Azure DevOps 任务的名称变为“自动生成服务”

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts9.png)

10. 现在，配置第二个（要推送的）Docker 任务。 选择订阅和 **smarthotelacreus2** ACR。 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts10.png)

11. 再次输入 docker-compose.yaml 文件的文件路径，选择“推送服务映像”并包括最新标记。 当操作更改为“推送服务映像”时，Azure DevOps 任务的名称变为“自动推送服务”

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts11.png)

12. 配置 Azure DevOps 任务后，Contoso 保存生成管道，然后启动生成过程。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts12.png)

13. 单击生成作业来检查进度。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts13.png)

14. 在生成完成后，ACR 会显示新的存储库，其中填充了微服务使用的容器。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts14.png)


### <a name="deploy-the-back-end-infrastructure"></a>部署后端基础结构

创建 AKS 群集并生成 Docker 映像后，Contoso 管理员现在部署后端微服务将利用的基础结构的其余部分。

- 本部分中的说明中使用的是 [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) 存储库。
- 在 **/deploy/k8s/arm** 文件夹中，有单个用来创建所有项的脚本。 

他们如下所述进行部署：

1. 打开开发人员命令提示，并为 Azure 订阅使用命令 az 登录。
2. 通过键入以下命令使用 deploy.cmd 文件在 ContosoRG 资源组和 EUS2 区域中部署 Azure 资源：

    .\deploy.cmd azuredeploy ContosoRG -c eastus2

    ![部署后端](./media/contoso-migration-rebuild/backend1.png)

2. 在 Azure 门户中捕获每个数据库的连接字符串，供稍后使用。

    ![部署后端](./media/contoso-migration-rebuild/backend2.png)

### <a name="create-the-back-end-release-pipeline"></a>创建后端发布管道

现在，Contoso 管理员执行以下操作：

- 部署 NGINX 入口控制器，以允许入站流量发送到服务。
- 将微服务部署到 AKS 群集。
- 第一步是使用 Azure DevOps 将连接字符串更新至微服务。 然后，配置新的 Azure DevOps 发布管道以部署微服务。
- 本部分中的说明中使用的是 [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) 存储库。
- 注意，部分配置设置（例如 Active Directory B2C）未包括在本文中。 存储库中提供了有关这些设置的更多信息。

创建管道：

1. 通过使用 Visual Studio，用前面记下的数据库连接信息更新 /deploy/k8s/config_local.yml 文件。

    ![DB 连接](./media/contoso-migration-rebuild/back-pipe1.png)

2. 打开 Azure DevOps，并在 SmartHotel360 项目的“版本”中，单击“+新建管道”。

    ![新建管道](./media/contoso-migration-rebuild/back-pipe2.png)

3. 单击“空作业”启动不带模板的管道。
4. 提供阶段和管道名称。

      ![阶段名称](./media/contoso-migration-rebuild/back-pipe4.png)

      ![管道名称](./media/contoso-migration-rebuild/back-pipe5.png)

5. 添加项目。

     ![添加项目](./media/contoso-migration-rebuild/back-pipe6.png)

6. 选择 Git 作为源类型，并指定 SmartHotel360 应用的项目、源和主分支。

    ![项目设置](./media/contoso-migration-rebuild/back-pipe7.png)

7. 单击任务链接。

    ![任务链接](./media/contoso-migration-rebuild/back-pipe8.png)

8. 添加一个新的 Azure PowerShell 任务，以便在 Azure 环境中运行 PowerShell 脚本。

    ![Azure 中的 PowerShell](./media/contoso-migration-rebuild/back-pipe9.png)

9. 为任务选择 Azure 订阅，并从 Git 存储库中选择 deploy.ps1 脚本。

    ![运行脚本](./media/contoso-migration-rebuild/back-pipe10.png)


10. 将参数添加到脚本。 脚本将删除所有群集内容（入口和入口控制器除外），并部署微服务。

    ![脚本参数](./media/contoso-migration-rebuild/back-pipe11.png)

11. 将首选的 Azure PowerShell 版本设置为最新版本，并保存管道。
12. 移回“发布”页，然后手动创建新的发布。

    ![新发布](./media/contoso-migration-rebuild/back-pipe12.png)

13. 单击创建的发布，并单击“操作”中的“部署”。

      ![部署发布](./media/contoso-migration-rebuild/back-pipe13.png)  

14. 部署完成后，使用 Azure Cloud Shell：kubectl get services 运行以下命令来检查服务状态。


## <a name="step-3-provision-front-end-services"></a>步骤 3：预配前端服务

Contoso 管理员需要部署将由前端应用使用的基础结构。 他们创建 blob 存储容器，用以存储宠物图像；创建 Cosmos 数据库，用以存储包含宠物信息的文档；并创建用于网站的视觉 API。 

针对本部分的说明中使用的是 [SmartHotel360-public-web](https://github.com/Microsoft/SmartHotel360-public-web) 存储库。

### <a name="create-blob-storage-containers"></a>创建 blob 存储容器

1.  在 Azure 门户中，打开所创建的存储帐户，然后单击“Blob”。
2.  创建公共访问级别设置为容器的一个新容器 (**Pets**)。 用户会将他们的宠物照片上传到此容器。

    ![存储 blob](./media/contoso-migration-rebuild/blob1.png)

3. 创建名为 **settings** 的另一个新容器。 一个包含前端应用设置的文件将放置在此容器中。

    ![存储 blob](./media/contoso-migration-rebuild/blob2.png)

4. 将存储帐户的访问详细信息捕获到一个文本文件中供将来参考。

    ![存储 blob](./media/contoso-migration-rebuild/blob2.png)

### <a name="provision-a-cosmos-database"></a>预配 Cosmos 数据库

Contoso 管理员预配一个 Cosmos 数据库来用于宠物信息。

1. 在 Azure 市场中创建一个 **Azure Cosmos DB**。

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos1.png)

2. 指定一个名称 (**contosomarthotel**)，选择 SQL API 并将其放置在主区域“美国东部 2”中的生产资源组 ContosoRG 中。

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos2.png)

3. 向数据库中添加一个具有默认容量和吞吐量的新集合。

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos3.png)


4. 记下数据库的连接信息供将来参考。 

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos4.png)


### <a name="provision-computer-vision"></a>预配计算机视觉

Contoso 管理员预配计算机视觉 API。 函数将调用该 API 来评估用户上传的照片。

1. 在 Azure 市场中创建一个**计算机视觉**实例。

     ![计算机视觉](./media/contoso-migration-rebuild/vision1.png)

2. 在主区域“美国东部 2”中的生产资源组 ContosoRG 中预配 API (**smarthotelpets**)。

    ![计算机视觉](./media/contoso-migration-rebuild/vision2.png)

3. 将该 API 的连接字符串保存到一个文本文件供以后参考。

     ![计算机视觉](./media/contoso-migration-rebuild/vision3.png)


### <a name="provision-the-azure-web-app"></a>预配 Azure Web 资源

Contoso 管理员使用 Azure 门户预配 Web 应用。


1. 在门户中选择“Web 应用”。

    ![Web 应用](media/contoso-migration-rebuild/web-app1.png)

2. 提供应用名称 (smarthotelcontoso)，在 Windows 上运行它，并将其放置在生产资源组 ContosoRG 中。 创建用于应用程序监视的新 Application Insights 实例……

    ![Web 应用名称](media/contoso-migration-rebuild/web-app2.png)

3. 完成后，浏览到应用地址来检查是否已成功创建。

4. 现在，在 Azure 门户中，为代码创建过渡槽。 管道将部署到此槽。 这可确保只有管理员发布后的代码才能投入生产。

    ![Web 应用过渡槽](media/contoso-migration-rebuild/web-app3.png)



### <a name="provision-the-azure-function-app"></a>预配 Azure Function 应用

Contoso 管理员在 Azure 门户中预配 Function App。

1. 选择 Function App。

    ![创建 Function App](./media/contoso-migration-rebuild/function-app1.png)

2. 提供应用名称 (smarthotelpetchecker)。 将应用放置在生产资源组 ContosoRG中。将托管位置设为“消耗计划”然后将应用放置在美国东部 2 区域中。 将创建新的存储帐户，以及用于监视 Application Insights 实例。

    ![函数应用设置](./media/contoso-migration-rebuild/function-app2.png)


3. 部署应用后，浏览到应用地址来检查是否已成功创建。


## <a name="step-4-set-up-the-front-end-pipeline"></a>步骤 4：设置前端管道

Contoso 管理员为前端站点创建两个不同的项目。 

1. 在 Azure DevOps，创建一个项目 SmartHotelFrontend。

    ![前端项目](./media/contoso-migration-rebuild/function-app1.png)

2. 将 [SmartHotel360 前端](https://github.com/Microsoft/SmartHotel360-public-web.git) Git 存储库导入到新项目中。
3. 对于 Function App，创建另一个 Azure DevOps 项目 (SmartHotelPetChecker)，并将 [PetChecker](https://github.com/Microsoft/SmartHotel360-PetCheckerFunction ) Git 存储库导入到此项目中。

### <a name="configure-the-web-app"></a>配置 Web 应用

现在，Contoso 管理员将 Web 应用配置为使用 Contoso 资源。

1. 连接到 Azure DevOps 项目，并将存储库克隆到本地开发计算机。
2. 在 Visual Studio 中，打开文件夹以显示存储库中的所有文件。

    ![存储库文件](./media/contoso-migration-rebuild/configure-webapp1.png)

3. 更新所需的配置更改。

    - 当 Web 应用启动时，它会查找 **SettingsUrl** 应用设置。
    - 此变量必须包含指向某个配置文件的 URL。
    - 默认情况下，使用的设置是一个公共终结点。

4.  更新 /config-sample.json/sample.json 文件。

    - 这是使用公共终结点时用于 Web 的配置文件。
    - 使用 AKS API 终结点、存储帐户和 Cosmos 数据库的值编辑 urls 和 pets_config 部分。
    - URL 应当与 Contoso 将创建的新 Web 应用的 DNS 名称匹配。
    - 对于 Contoso，这是 **smarthotelcontoso.eastus2.cloudapp.azure.com**。

    ![Json 设置](./media/contoso-migration-rebuild/configure-webapp2.png)

5. 更新文件后，将其重命名为 smarthotelsettingsurl，并将其上传到之前创建的 blob 存储中。

    ![重命名和上传](./media/contoso-migration-rebuild/configure-webapp3.png)

6. 单击文件以获取 URL。 当应用拉取配置文件时将使用此 URL。

    ![应用 URL](./media/contoso-migration-rebuild/configure-webapp4.png)

7. 将 appsettings.Production.json 文件中的 SettingsURL 更新为新文件的 URL。

    ![更新 URL](./media/contoso-migration-rebuild/configure-webapp5.png)

### <a name="deploy-the-website-to-the-azure-app-service"></a>将网站部署到 Azure 应用服务

Contoso 管理员现在可以发布该网站。


1. 打开 Azure DevOps，并在 SmartHotelFrontend 项目的“版本和发布”中，单击“+新建管道”。
2. 选择 Azure DevOps Git 作为源。
3. 选择 ASP.NET Core 模板。
4. 审查管道，并确认“发布 Web 项目”和“压缩已发布项目”已选中。

    ![管道设置](./media/contoso-migration-rebuild/vsts-publishfront2.png)

5. 在“触发器”中启用持续集成，并添加主分支。 这可确保每次当解决方案有新的代码提交到主分支中时，生成管道就会启动。

    ![持续集成](./media/contoso-migration-rebuild/vsts-publishfront3.png)

6. 单击“保存并排队”以开始生成。
7. 在生成完成后，使用 Azure 应用服务部署配置发布管道。
8. 提供阶段名称“暂存”。

    ![环境名称](./media/contoso-migration-rebuild/vsts-publishfront4.png)

9. 添加项目，并选择刚配置的版本。

     ![添加项目](./media/contoso-migration-rebuild/vsts-publishfront5.png)

10. 单击项目上的闪电符号，并启用连续部署。

    ![连续部署](./media/contoso-migration-rebuild/vsts-publishfront6.png)
11. 在“环境”中，单击“暂存”下的“1 个作业、1 个任务”。
12. 选择订阅和应用名称后，打开“部署 Azure 应用服务”任务。 部署配置为使用“暂存”部署槽位。 这会在此槽位中自动生成供评审和批准的代码。

     ![槽位](./media/contoso-migration-rebuild/vsts-publishfront7.png)

13. 在“管道”中，添加新的阶段。

    ![新的环境](./media/contoso-migration-rebuild/vsts-publishfront8.png)

14. 选择“使用槽位部署 Azure 应用服务”并将环境命名为“Prod”。
15. 单击“1 个作业、2 个任务”，然后选择订阅、应用服务名称和“暂存”槽位。

    ![环境名称](./media/contoso-migration-rebuild/vsts-publishfront10.png)

16. 从管道中删除“将 Azure 应用服务部署到槽位中”。 它是由前面的步骤放置在这里的。

    ![从管道中删除](./media/contoso-migration-rebuild/vsts-publishfront11.png)

13. 保存管道。 在管道中，单击“后期部署条件”。

    ![后期部署](./media/contoso-migration-rebuild/vsts-publishfront12.png)

14. 启用“后期部署审批”，并将开发主管添加为审批者。

    ![后期部署审批](./media/contoso-migration-rebuild/vsts-publishfront13.png)

15. 在生成管道中，手动启动生成。 这会触发新的发布管道，该发布管道会将站点部署到过渡槽。 对于 Contoso，该槽的 URL 是 https://smarthotelcontoso-staging.azurewebsites.net/。
16. 生成完成后，并且发布将部署到槽，Azure DevOps 向开发主管发送电子邮件以供审批。
17. 开发主管单击“查看审批”，并可在 Azure DevOps 门户中批准或拒绝请求。

    ![审批邮件](./media/contoso-migration-rebuild/vsts-publishfront14.png)

16. 主管作出评论，并进行批准。 此操作将启动“暂存”槽与“生产”槽的交换，并将生成移动到生产中。

    ![批准和交换](./media/contoso-migration-rebuild/vsts-publishfront15.png)

17. 该管道完成交换。

    ![完成交换](./media/contoso-migration-rebuild/vsts-publishfront16.png)

18. 团队通过访问 https://smarthotelcontoso.azurewebsites.net/ 检查生产槽以验证 Web 应用是否在生产。


### <a name="deploy-the-petchecker-function-app"></a>部署 PetChecker Function App

Contoso 管理员将对应用作出如下部署。

1. 通过连接到 Azure DevOps 项目，将存储库克隆到本地开发计算机。
2. 在 Visual Studio 中，打开文件夹以显示存储库中的所有文件。
3. 打开 src/PetCheckerFunction/local.settings.json 文件，并为存储、Cosmos 数据库和计算机视觉 API 添加应用设置。

    ![部署函数](./media/contoso-migration-rebuild/function5.png)

4. 提交代码，并将其同步到 Azure DevOps，从而推送更改。
5. 添加一个新的生成管道，并为源选择 Azure DevOps Git。
6. 选择 ASP.NET Core (.NET Framework) 模板。
7. 接受模板的默认设置。
8. 在“触发器”中，选择“启用持续集成”，然后单击“保存并排队”以启动生成。
9. 生成成功后，生成发布管道，从而添加“使用槽位部署 Azure 应用服务”。
10. 将环境命名为“生产”，并选择订阅。 将“应用类型”设为“Function App”，并将应用服务名称设为 smarthotelpetchecker。

    ![函数应用](./media/contoso-migration-rebuild/petchecker2.png)

11. 添加项目 Build。

    ![项目](./media/contoso-migration-rebuild/petchecker3.png)

12. 启用“持续部署触发器”，然后单击“保存”。
13. 单击“对新版本进行排队”运行完整的 CI/CD 管道。
14. 部署函数后，它将出现在 Azure 门户中，状态为“正在运行”。

    ![部署函数](./media/contoso-migration-rebuild/function6.png)


7. 浏览到应用以测试“宠物检查器”应用是否按预期工作，网址为 [http://smarthotel360public.azurewebsites.net/Pets](http://smarthotel360public.azurewebsites.net/Pets)。
8. 单击头像来上传照片。

    ![部署函数](./media/contoso-migration-rebuild/function7.png)

9. 他们要检查的第一张照片是一只小狗的照片。

    ![部署函数](./media/contoso-migration-rebuild/function8.png)

10. 应用返回一条接受消息。

    ![部署函数](./media/contoso-migration-rebuild/function9.png)






## <a name="review-the-deployment"></a>查看部署

Azure 显示已迁移的资源后，Contoso 现在需要积极行动、全面保护新的基础结构。

### <a name="security"></a>安全

- Contoso 必须确保新数据库是安全的。 [了解详细信息](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)。
- 需要将应用更新为将 SSL 与证书配合使用。 容器实例应当重新部署为在端口 443 上进行应答。
- Contoso 应当考虑使用 KeyVault 来保护其 Service Fabric 应用的机密。 [了解详细信息](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)。

### <a name="backups-and-disaster-recovery"></a>备份和灾难恢复

- Contoso 需要审查 Azure SQL 数据库的备份要求。 [了解详细信息](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。
- Contoso 应当考虑实现 SQL 故障转移组来为数据库提供区域性故障转移。 [了解详细信息](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)。
- Contoso 可以利用 ACR 高级 SKU 的异地复制。 [了解详细信息](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)。
- Cosmos DB 自动备份。 Contoso 可[通过此处](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore)了解此过程的详细信息。

### <a name="licensing-and-cost-optimization"></a>许可和成本优化

- 部署所有资源后，Contoso 应当根据其[基础结构规划](contoso-migration-infrastructure.md#set-up-tagging)分配 Azure 标记。
- 所有许可成本都包括在 Contoso 使用的 PaaS 服务的成本中。 这将从企业协议中扣除。
- Contoso 将启用由 Microsoft 子公司 Cloudyn 许可的 Azure 成本管理。 该服务是一个多云成本管理解决方案，可帮助利用和管理 Azure 与其他云资源。  [详细了解](https://docs.microsoft.com/azure/cost-management/overview) Azure 成本管理。

## <a name="conclusion"></a>结束语

在本文中，Contoso 在 Azure 中重新生成 SmartHotel360 应用。 在 Azure 应用服务 Web 应用中重新生成本地应用前端 VM。 应用后端是使用微服务生成的，这些微服务部署到由 Azure Kubernetes 服务 (AKS) 管理的容器。 Contoso 通过一个宠物照片应用增强了应用功能。




