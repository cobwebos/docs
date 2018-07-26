---
title: 在 Azure 中重新生成 Contoso 本地应用 | Microsoft Docs
description: 了解 Contoso 如何使用 Azure 应用服务、Kubernetes 服务、CosmosDB、Azure Functions 和认知服务在 Azure 中重新生成应用。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: raynew
ms.openlocfilehash: 0d195d5fbede3100c0474ae9614a880cfb3acb19
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004993"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Contoso 迁移：在 Azure 中重新生成本地应用

本文演示了 Contoso 如何在 Azure 中迁移并重新生成其 SmartHotel 应用。 他们将应用的前端 VM 迁移到 Azure 应用服务 Web 应用。 应用后端是使用微服务生成的，这些微服务部署到由 Azure Kubernetes 服务 (AKS) 管理的容器。 站点与 Azure Functions 进行交互，提供宠物照片功能。 

本文档是系列文章中的其中一篇，目的是展示虚拟公司 Contoso 如何将本地资源迁移到 Microsoft Azure 云。 该系列介绍了背景信息，同时提供了很多应用场景来描述如何设置迁移基础结构、评估本地资源是否适合迁移以及如何运行不同类型的迁移。 应用场景越来越复杂，我们将逐渐添加其他文章进行讲解。

**文章** | **详细信息** | **Status**
--- | --- | ---
[文章 1：概述](contoso-migration-overview.md) | 简要介绍 Contoso 的迁移策略、文章系列和所使用的示例应用。 | 可用
[文章 2：部署 Azure 基础结构](contoso-migration-infrastructure.md) | 介绍 Contoso 如何装备其本地和 Azure 基础结构进行迁移。 所有迁移文章共用同一个基础结构。 | 可用
[文章 3：访问本地资源](contoso-migration-assessment.md)  | 展示 Contoso 如何评估 VMware 上运行的本地双层 SmartHotel 应用。 Contoso 使用 [Azure Migrate](migrate-overview.md) 服务评估应用 VM，使用 [数据库迁移助手](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)评估应用 SQL Server 数据库。 | 可用
[文章 4：在 Azure VM 和 SQL 托管实例上重新托管应用](contoso-migration-rehost-vm-sql-managed-instance.md) | 演示 Contoso 如何将 SmartHotel 应用直接迁移到 Azure。 Contoso 使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 迁移应用前端 VM，使用 [Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/dms-overview)将应用数据库迁移到 SQL 托管实例。 | 可用
[文章 5：在 Azure VM 上重新托管应用](contoso-migration-rehost-vm.md) | 展示 Contoso 如何在仅使用 Site Recovery 的情况下迁移 SmartHotel 应用 VM。 | 可用
[文章 6：将应用重新托管到 Azure VM 和 SQL Server AlwaysOn 可用性组](contoso-migration-rehost-vm-sql-ag.md) | 展示 Contoso 如何迁移 SmartHotel 应用。 Contoso 使用 Site Recovery 来迁移应用 VM，同时使用数据库迁移服务将应用数据库迁移到受 AlwaysOn 可用性组保护的 SQL Server 群集。 | 可用
[文章 7：将 Linux 应用重新托管到 Azure VM](contoso-migration-rehost-linux-vm.md) | 展示 Contoso 如何使用 Site Recovery 将 Linux osTicket 应用直接迁移到 Azure VM | 可用
[文章 8：在 Azure VM 和 Azure MySQL 服务器上重新托管 Linux 应用](contoso-migration-rehost-linux-vm-mysql.md) | 演示 Contoso 如何使用 Site Recovery 将 Linux osTicket 应用迁移到 Azure VM，以及如何使用 MySQL 工作台将应用数据库迁移到 Azure MySQL 服务器实例。 | 可用
[文章 9：基于 Azure Web 应用和 Azure SQL 数据库重构应用](contoso-migration-refactor-web-app-sql.md) | 演示 Contoso 如何将 SmartHotel 应用迁移到 Azure Web 应用，并将应用数据库迁移到 Azure SQL Server 实例 | 可用
[文章 10：将 Linux 应用重构到 Azure Web 应用和 Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | 演示 Contoso 如何将 Linux 应用 osTicket 迁移到多个站点中的 Azure Web 应用，并集成 GitHub 以实现持续交付。 他们将应用数据库迁移到 Azure MySQL 实例。 | 可用
[文章 11：基于 VSTS 重构 TFS](contoso-migration-tfs-vsts.md) | 展示 Contoso 如何通过将本地 Team Foundation Server (TFS) 部署迁移到 Azure 中的 Visual Studio Team Services (VSTS) 来迁移该部署。 | 可用
[文章 12：将应用重新架构到 Azure 容器和 SQL 数据库](contoso-migration-rearchitect-container-sql.md) | 展示 Contoso 如何将其 SmartHotel 应用迁移并重新架构到 Azure。 他们将应用 Web 层重新架构为 Windows 容器，将应用数据库重新架构到 Azure SQL 数据库中。 | 可用
文章 13：在 Azure 中重新生成应用 | 展示 Contoso 如何使用一系列 Azure 功能和服务（包括应用服务、Azure Kubernetes、Azure Functions、认知服务和 Cosmos DB）重新生成其 SmartHotel 应用。 | 本文。

在本文中，Contoso 将 VMware VM 上运行的双层 Windows. NET SmartHotel 应用迁移到 Azure。 此应用作为开源应用提供，可在 [github](https://github.com/Microsoft/SmartHotel360) 上下载。

## <a name="business-drivers"></a>业务驱动因素

IT 领导团队与其业务合作伙伴密切协作，以了解合作伙伴希望在本次迁移中实现的目标：

- **应对业务增长**：Contoso 正在发展壮大。 他们希望在其网站上为客户提供独特的体验。
- **敏捷性**：Contoso 必须能够抢在市场变化之前做出反应，这样才能在全球经济环境中取得成功。 
- **扩展**：随着业务成功发展，Contoso IT 必须提供能够同步成长的系统。
- **成本**：Contoso 希望将许可成本降至最低。

## <a name="migration-goals"></a>迁移目标

Contoso 云团队制定了本次迁移的应用要求。 这些要求用于确定最佳迁移方式：
 - Azure 中的该应用将如同现在一样重要。 它应具有良好的性能并能轻松缩放。
 - 应用不应使用 IaaS 组件。 所有内容都应生成为使用 PaaS 或无服务器服务。
 - 所有应用版本都应在云服务中运行，并且容器应当位于云中的专用企业级容器注册表中。
 - 在真实世界中，用于宠物照片的 API 服务应当是准确可靠的，因为应用做出的决策在酒店中必须得到尊重。 应当允许经授权进入的任何宠物停留在酒店中。

## <a name="solution-design"></a>解决方案设计

在确定其目标和要求后，Contoso 设计并审查部署解决方案，确定迁移流程，包括将用于迁移的 Azure 服务。

### <a name="current-app"></a>当前应用

- SmartHotel 本地应用跨两个 VM（WEBVM 和 SQLVM）进行分层。
- 这两个 VM 位于 VMware ESXi 主机 contosohost1.contoso.com（6.5 版）上
- VMware 环境由 VM 上运行的 vCenter Server 6.5 (**vcenter.contoso.com**) 托管。
- Contoso 有一个本地数据中心 (contoso-datacenter)，其中包含一个本地域控制器 (**contosodc1**)。
- 迁移完成后，Contoso 数据中心的本地 VM 将停止使用。


### <a name="proposed-architecture"></a>建议的体系结构

- 应用的前端将在主区域中部署为 Azure 应用服务 Web 应用。
- 一个 Azure 函数将提供宠物照片上传功能，站点将与此功能进行交互。
- 该宠物照片函数利用认知服务视觉 API 和 CosmosDB。
- 站点的后端是使用微服务生成的。 这些微服务将部署到在 Azure Kubernetes 服务 (AKS) 上管理的容器中。
- 容器将使用 VSTS 进行生成，并推送到 Azure 容器注册表 (ACR)。
- 现在，Contoso 将使用 Visual Studio 手动部署 Web 应用和函数代码。
- 将使用一个 PowerShell 脚本部署微服务，该脚本调用 Kubernetes 命令行工具。

    ![方案体系结构](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>解决方案评审
Contoso 通过将利弊清单放置在一起来评估其建议的设计。

**注意事项** | **详细信息**
--- | ---
**优点** | 为端到端部署使用 PaaS 和无服务器解决方案可以显著降低 Contoso 必须提供的管理时间。<br/><br/> 迁移到微服务体系结构可以使 Contoso 能够随时间推移轻松扩展他们的解决方案。<br/><br/> 可以在联机状态下添加新功能，不会破坏任何现有的解决方案代码库。<br/><br/> Web 应用将配置有多个实例，不存在单一故障点。<br/><br/> 将启用自动缩放，以便应用可以处理不同的流量大小。<br/><br/> 迁移到 PaaS 服务后，Contoso 可以停用在 Windows Server 2008 R2 操作系统上运行的过时解决方案。<br/><br/> CosmosDB 具有内置的容错功能，这不需要 Contoso 进行配置。 这意味着数据层不再是单一故障转移点。
**缺点** | 容器比其他迁移选项更加复杂。 学习曲线对于 Contoso 来说可能是一个问题。  它们引入了新一级的复杂性以提供更大的价值（而无论该曲线如何）。<br/><br/> Contoso 的运营团队需要努力理解用于应用的 Azure、容器和微服务并为其提供支持。<br/><br/> Contoso 还没有完全为整个解决方案实现 DevOps。 他们需要考虑将服务部署到 AKS、函数和应用服务。



### <a name="migration-process"></a>迁移流程

1. Contoso 预配 ACR、AKS 和 CosmosDB。
2. 他们为部署预配基础结构，包括 Azure Web 应用、存储帐户、函数和 API。 
3. 在基础结构就位后，他们将使用 VSTS 生成微服务容器映像，VSTS 将这些映像推送到 ACR。
4. Contoso 将使用一个 PowerShell 脚本将这些微服务部署到 ASK。
5. 最后，他们将部署 Azure 函数和 Web 应用。

    ![迁移流程](./media/contoso-migration-rebuild/migration-process.png) 

### <a name="azure-services"></a>Azure 服务

**服务** | **说明** | **成本**
--- | --- | ---
[AKS](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | 简化 Kubernetes 管理、部署和操作。 提供完全托管的 Kubernetes 容器业务流程服务。  | AKS 是一项免费服务。  只需要为使用的虚拟机、关联的存储和网络资源付费。 [了解详细信息](https://azure.microsoft.com/pricing/details/kubernetes-service/)。
[Azure Functions](https://azure.microsoft.com/services/functions/) | 通过事件驱动型无服务器计算体验加快开发速度。 按需缩放。  | 只需要为使用的资源付费。 计划按每秒资源消耗和执行次数收费。 [了解详细信息](https://azure.microsoft.com/pricing/details/functions/)。
[Azure 容器注册表](https://azure.microsoft.com/services/container-registry/) | 用于存储所有类型的容器部署的映像。 | 成本取决于功能、存储和使用持续时间。 [了解详细信息](https://azure.microsoft.com/pricing/details/container-registry/)。
[Azure 应用服务](https://azure.microsoft.com/services/app-service/containers/) | 快速生成、部署和缩放在任何平台上运行的企业级 Web 应用、移动应用和 API 应用。 | 应用服务计划按秒收费。 [了解详细信息](https://azure.microsoft.com/pricing/details/app-service/windows/)。

## <a name="prerequisites"></a>先决条件

要运行此方案，你（和 Contoso）需具备以下项：

**要求** | **详细信息**
--- | ---
**Azure 订阅** | 在本系列的第 1 篇文章中执行评估时，应该既已创建订阅。 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。<br/><br/> 如果创建的是免费帐户，则你是自己的订阅的管理员，可以执行所有操作。<br/><br/> 如果你使用现有订阅并且不是管理员，则需要请求管理员为你分配“所有者”或“参与者”权限。
**Azure 基础结构** | [了解](contoso-migration-infrastructure.md) Contoso 如何设置 Azure 基础结构。
**开发人员先决条件** | Contoso 要求开发人员工作站上具有以下工具：<br/><br/> - [Visual Studio 2017 Community Edition：版本 15.5](https://www.visualstudio.com/)<br/><br/> 启用 .NET 工作负荷。<br/><br/> [Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> 设置为使用 Windows 容器的 [Docker CE (Windows 10) 或 Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/)。



## <a name="scenario-steps"></a>方案步骤

Contoso 按如下方式运行迁移：

> [!div class="checklist"]
> * **步骤 1：预配 AKS 和 ACR**：Contoso 使用 PowerShell 预配托管 AKS 群集和 Azure 容器注册表
> * **步骤 2：生成 Docker 容器**：使用 VSTS 为 Docker 容器设置 CI，并将容器推送到 ACR。
> * **步骤 3：部署后端微服务**：部署后端微服务将利用的基础结构的其余部分。
> * **步骤 4：部署前端基础结构**：部署前端基础结构，包括宠物照片的 blob 存储、Cosmos DB 和视觉 API。
> * **步骤 5：迁移后端**：部署微服务并在 AKS 上运行以迁移后端。
> * **步骤 6：发布前端**：将 SmartHotel 应用发布到 Azure 应用服务以及宠物服务将调用的 Function App。



## <a name="step-1-provision-an-aks-cluster-and-acr"></a>步骤 1：预配 AKS 群集和 ACR

Contoso 运行部署脚本来使用 AKS 和 Azure 容器注册表创建托管 Kubernetes 群集。

- 本部分的说明中使用的是 **SmartHotel360-Azure-backend** 存储库。
- **SmartHotel360-Azure-backend** GitHub 存储库包含用于部署的本部分的所有软件。

他们如下所述进行预配：

1. 在开始之前，Contoso 确保在他们使用的开发计算机上安装所有必备软件。 
2. 使用 Git 将存储库克隆到本地的开发计算机。

    **git clone https://github.com/Microsoft/SmartHotel360-Azure-backend.git**

3.  Contoso 使用 Visual Studio Code 打开文件夹，转到 **/deploy/k8s** 目录，其中包含脚本 **gen-aks-env.ps1**。
4. 运行脚本来使用 AKS 和容器注册表创建托管 Kubernetes 群集。

    ![AKS](./media/contoso-migration-rebuild/aks1.png)
 
5.  打开文件后，将 $location 参数更新为 **eastus2** 并保存该文件。

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

6. 单击“查看” > “集成终端”以在 Code 中打开集成终端。

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

7. 在 PowerShell 集成终端中，使用 Connect-AzureRmAccount 命令登录到 Azure。 [详细了解](https://docs.microsoft.com/powershell/azure/get-started-azureps)如何开始使用 PowerShell。

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

8. 在 Azure CLI 中进行身份验证：运行 **az login** 命令并根据说明使用 Web 浏览器进行身份验证。 [详细了解](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)使用 Azure CLI 进行登录。

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

9. 运行以下命令，并传递资源组名称 ContosoRG、AKS 群集 smarthotel-aks-eus2 的名称以及新的注册表名称。

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```

    ![AKS](./media/contoso-migration-rebuild/aks6.png)

10. Azure 创建另一个资源组，使其包含 AKS 群集的资源。

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

11. 部署完成后，Contoso 安装 **kubectl** 命令行工具。 Azure CloudShell 上已安装了此工具。

    **az aks install-cli**

12. 通过运行 **kubectl get nodes** 命令，验证到群集的连接。 在自动创建的资源组中，节点与 VM 同名。

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

13. 运行以下命令来启动 Kubernetes 仪表板： 

    **az aks browse --resource-group ContosoRG --name smarthotelakseus2**

14. 仪表板中将打开一个浏览器选项卡。 这是使用 Azure CLI 的隧道连接。 

    ![AKS](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-build-a-docker-container"></a>步骤 2：生成 Docker 容器

### <a name="create-a-vsts-and-build"></a>创建 VSTS 并生成

Contoso 创建 VSTS 项目，配置 CI 生成来创建容器，然后将其推送到 ACR。 本部分中的说明中使用的是 [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) 存储库。

1. 从 visualstudio.com 中，创建一个新帐户 (**contosodevops360.visualstudio.com**) 并将其配置为使用 Git。

2. 创建一个新项目 (**smarthotelrefactor**) 并使用 Git 进行版本控制，使用 Agile 来管理工作流。

    ![VSTS](./media/contoso-migration-rebuild/vsts1.png) 


3. 导入 GitHub 存储库。

    ![VSTS](./media/contoso-migration-rebuild/vsts2.png)
    
4. 在“生成和发布”中，使用 VSTS Git 作为源基于导入的 **smarthotel** 存储库创建一个新定义。 

    ![VSTS](./media/contoso-migration-rebuild/vsts3.png)

6. 选择使用一个空管道开始。

    ![VSTS](./media/contoso-migration-rebuild/vsts4.png)  

7. 对于生成定义，选择“托管 Linux 预览版”。

    ![VSTS](./media/contoso-migration-rebuild/vsts5.png) 
 
8. 在“第 1 阶段”中，添加一个 **Docker Compose** 任务。 此任务生成 Docker Compose。

    ![VSTS](./media/contoso-migration-rebuild/vsts6.png) 

9. 重复操作并添加另一个 **Docker Compose** 任务。 此操作将容器推送到 ACR。

     ![VSTS](./media/contoso-migration-rebuild/vsts7.png) 

8. 选择（要生成的）第一个任务，为生成配置 Azure 订阅、授权和 ACR。 

    ![VSTS](./media/contoso-migration-rebuild/vsts8.png)

9. 指定存储库的 **src** 文件夹中的 **docket-compose.yaml** 文件的路径。 选择生成服务映像，并包括最新标记。 当操作更改为**生成服务映像**时，VSTS 任务的名称变为**自动生成服务**

    ![VSTS](./media/contoso-migration-rebuild/vsts9.png)

10. 现在，Contoso 配置第二个（要推送的）Docker 任务。 选择订阅和 **smarthotelacreus2** ACR。 

    ![VSTS](./media/contoso-migration-rebuild/vsts10.png)

11. 再次输入 docker-compose.yaml 文件的文件路径，选择“推送服务映像”并包括最新标记。 当操作更改为**推送服务映像**时，VSTS 任务的名称变为**自动推送服务**

    ![VSTS](./media/contoso-migration-rebuild/vsts11.png)

12. 配置 VSTS 任务后，Contoso 保存生成定义，然后启动生成过程。

    ![VSTS](./media/contoso-migration-rebuild/vsts12.png)

13. 单击生成作业来检查进度。

    ![VSTS](./media/contoso-migration-rebuild/vsts13.png)

14. 在生成完成后，ACR 会显示新的存储库，其中填充了微服务使用的容器。

    ![VSTS](./media/contoso-migration-rebuild/vsts14.png)


## <a name="step-3-deploy-back-end-microservices"></a>步骤 3：部署后端微服务

创建 AKS 群集并生成 Docker 映像后，Contoso 现在部署后端微服务将利用的基础结构的其余部分。

- 本部分中的说明中使用的是 [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) 存储库。
- 在 **/deploy/k8s/arm** 文件夹中，有单个用来创建所有项的脚本。 

他们如下所述进行部署：

1. Contoso 通过键入以下命令使用 deploy.cmd 文件在 ContosoRG 资源组和 EUS2 区域中部署 Azure 资源：

    **.\deploy azuredeploy ContosoRG -c eastus2**

    ![部署后端](./media/contoso-migration-rebuild/backend1.png)

2. 捕获每个数据库的连接字符串，供稍后使用。

    ![部署后端](./media/contoso-migration-rebuild/backend2.png)

## <a name="step-4-deploy-front-end-infrastructure"></a>步骤 4：部署前端基础结构

Contoso 需要部署将由前端应用使用的基础结构。 他们创建 blob 存储容器，用以存储宠物图像；创建 Cosmos 数据库，用以存储包含宠物信息的文档；并创建用于网站的视觉 API。 

针对本部分的说明中使用的是 [SmartHotel-public-web](https://github.com/Microsoft/SmartHotel360-public-web) 存储库。

### <a name="create-storage-containers"></a>创建存储容器

1.  在 Azure 门户中，Contoso 打开所创建的存储帐户，然后单击“Blob”。
2.  创建公共访问级别设置为容器的一个新容器 (**Pets**)。 用户会将他们的宠物照片上传到此容器。

    ![存储 blob](./media/contoso-migration-rebuild/blob1.png)

3. 创建名为 **settings** 的另一个新容器。 一个包含前端应用设置的文件将放置在此容器中。

    ![存储 blob](./media/contoso-migration-rebuild/blob2.png)

4. 将存储帐户的访问详细信息捕获到一个文本文件中供将来参考。

    ![存储 blob](./media/contoso-migration-rebuild/blob2.png)

## <a name="provision-a-cosmos-database"></a>预配 Cosmos 数据库

Contoso 预配一个 Cosmos 数据库来用于宠物信息。

1. 在 Azure 市场中创建一个 **Azure Cosmos DB**。

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos1.png)

2. 指定一个名称 (**contosomarthotel**)，选择 SQL API 并将其放置在主区域“美国东部 2”中的生产资源组 ContosoRG 中。

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos2.png)

3. 向数据库中添加一个具有默认容量和吞吐量的新集合。

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos3.png)


4. 记下数据库的连接信息供将来参考。 

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos4.png)


## <a name="provision-computer-vision"></a>预配计算机视觉

Contoso 预配计算机视觉 API。 函数将调用该 API 来评估用户上传的照片。

1. 在 Azure 市场中创建一个**计算机视觉**实例。

     ![计算机视觉](./media/contoso-migration-rebuild/vision1.png)

2. 在主区域“美国东部 2”中的生产资源组 ContosoRG 中预配 API (**smarthotelpets**)。

    ![计算机视觉](./media/contoso-migration-rebuild/vision2.png)

3. 将该 API 的连接字符串保存到一个文本文件供以后参考。

     ![计算机视觉](./media/contoso-migration-rebuild/vision3.png)

## <a name="step-5-deploy-the-back-end-services-in-azure"></a>步骤 5：在 Azure 中部署后端服务

现在，Contoso 需要部署 NGINX 入口控制器来允许到服务器的入站流量，然后将微服务部署到 AKS 群集。

本部分中的说明中使用的是 [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) 存储库。



1. 使用 Visual Studio Code 更新 **/deploy/k8s/config_loal.yml** 文件。 更新他们在前面记下的信息中的各种数据库连接。

     ![部署微服务](./media/contoso-migration-rebuild/deploy-micro.png)

    > [!NOTE]
    > 部分配置设置（例如 Active Directory B2C）未包括在本文中。 存储库中提供了有关这些设置的更多信息。

2. deploy.ps1 文件删除所有群集内容（出口和入口控制器除外）并部署微服务。 如下所示运行该文件：

  ```
  .\deploy.ps1 -configFile .\conf_local.yml -dockerUser smarthotelacreus2  -dockerPassword [passwordhere] -registry smarthotelacreus2.azurecr.io -imageTag latest -deployInfrastructure $false -buildImages $false -pushImages $false
  ```

3. 运行以下命令来检查服务状态：

    ```
    kubectl get services
    ```
4. 打开 Kubernetes 仪表板来审查部署：

    ```
    az aks browse --resource-group ContosoRG --name smarthotelakseus2
    ```


## <a name="step-6-publish-the-frontend"></a>步骤 6：发布前端

在这最后一个步骤中，Contoso 将 SmartHotel 应用发布到 Azure 应用服务以及宠物服务调用的 Function App。

本部分的说明使用的是 [SmartHotel-public-web](https://github.com/Microsoft/SmartHotel360-public-web)  存储库。

### <a name="set-up-web-app-to-use-contoso-resources"></a>将 Web 应用设置为使用 Contoso 资源

1. Contoso 使用 Git 将存储库克隆到本地的开发计算机：

    **git clone https://github.com/Microsoft/SmartHotel360-public-web.git**

2. 在 Visual Studio 中，打开文件夹以显示存储库中的所有文件。

    ![发布前端](./media/contoso-migration-rebuild/front-publish1.png)

3. 使所需的配置更改成为默认设置。

    - 当 Web 应用启动时，它会查找 **SettingsUrl** 应用设置。
    - 此变量必须包含某个配置文件的 URL。
    - 默认情况下，使用的设置是一个公共终结点。

4. 更新 **/config-sample.json/sample.json** 文件。 这是使用公共终结点时用于 Web 的配置文件。

    - 使用 AKS API 终结点、存储帐户和 Cosmos 数据库的值编辑 **urls** 和 **pets_config** 部分。 
    - URL 应当与 Contoso 将创建的新 Web 应用的 DNS 名称匹配。
    - 对于 Contoso，这是 **smarthotelcontoso.eastus2.cloudapp.azure.com**。

    ![发布前端](./media/contoso-migration-rebuild/front-publish2.png)

5. 更新文件后，将其重命名为 **smarthotelsettingsurl**，并将其上传到之前创建的存储 blob 中。

     ![发布前端](./media/contoso-migration-rebuild/front-publish3.png)

6. 单击文件以获取 URL。 当应用开始拉取配置文件时将使用此 URL。

    ![发布前端](./media/contoso-migration-rebuild/front-publish4.png)

7. 将 **appsettings.Production.json** 文件中的 **SettingsUrl** 更新为新文件的 URL。

    ![发布前端](./media/contoso-migration-rebuild/front-publish5.png)


### <a name="deploy-the-website-to-the-azure-app-service"></a>将网站部署到 Azure 应用服务

现在，Contoso 可以发布其网站。


1. 在 Visual Studio 2017 中启用 Application Insights 监视。 为此，在解决方案资源管理器中选择 **PublicWeb** 项目，并搜索“添加 Application Insights”。 向部署基础结构时创建的 Application Insight 实例注册应用。

    ![发布网站](./media/contoso-migration-rebuild/deploy-website1.png)

2. 在 Visual Studio 中，将 PublicWeb 项目连接到 App Insights，然后更新以显示它已配置。
 
    ![发布网站](./media/contoso-migration-rebuild/deploy-website2.png)

3. 在 Visual Studio 中，创建并发布其 Web 应用。

    ![发布网站](./media/contoso-migration-rebuild/deploy-website3.png)

5. 指定一个应用名称，并将其放置在主区域“美国东部 2”中的生产资源组 **ContosoRG** 中。

    ![发布网站](./media/contoso-migration-rebuild/deploy-website4.png)

### <a name="deploy-the-function-to-azure"></a>将函数部署到 Azure

1. 使用 Visual Studio 创建并发布函数。 为此，右键单击“PetCheckerFunction” > “发布”。 然后创建一个新的应用服务函数。

     ![部署函数](./media/contoso-migration-rebuild/function1.png)

2. 指定名称 **smarthotelpetchecker**，将其放置在 ContosoRG 资源组中，并配置一个新的应用服务计划。

     ![部署函数](./media/contoso-migration-rebuild/function2.png)

3. 选择存储帐户并创建函数。

    ![部署函数](./media/contoso-migration-rebuild/function3.png)

4. 部署将首先将函数应用预配到 Azure。 在“发布”中，Contoso 添加存储的应用设置、Cosmos 数据库和计算机视觉 API。

    ![部署函数](./media/contoso-migration-rebuild/function4.png)

5. 为了首先在本地运行函数，更新 **PetCheckerFunction/local.settings.json** 中的设置。

    ![部署函数](./media/contoso-migration-rebuild/function5.png)

6. 部署函数后，它将出现在 Azure 门户中，状态为“正在运行”。

    ![部署函数](./media/contoso-migration-rebuild/function6.png)


7. 浏览到应用来测试“宠物检查程序 AI”是否按预期工作，网址为 [http://smarthotel360public.azurewebsites.net/Pets](http://smarthotel360public.azurewebsites.net/Pets)。
8. 单击头像来上传照片。

    ![部署函数](./media/contoso-migration-rebuild/function7.png)

9. 他们要检查的第一张照片是一只小狗的照片。

    ![部署函数](./media/contoso-migration-rebuild/function8.png)

10. 应用返回一条接受消息。

    ![部署函数](./media/contoso-migration-rebuild/function9.png)




## <a name="review-the-deployment"></a>查看部署

Azure 显示已迁移的资源后，Contoso 需要积极行动、全面保护新的基础结构。

### <a name="security"></a>安全性

- Contoso 必须确保其新数据库是安全的。 [了解详细信息](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)。
- 需要将应用更新为将 SSL 与证书配合使用。 容器实例应当重新部署为在端口 443 上进行应答。
- 他们应当考虑使用密钥保管库来保护其 Service Fabric 应用的机密。 [了解详细信息](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)。

### <a name="backups-and-disaster-recovery"></a>备份和灾难恢复

- Contoso 需要审查 Azure SQL 数据库的备份要求。 [了解详细信息](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。
- 他们应当考虑实现 SQL 故障转移组来为数据库提供区域性故障转移。 [了解详细信息](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)。
- 他们可以利用 ACR 高级 SKU 的异地复制。 [了解详细信息](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

### <a name="licensing-and-cost-optimization"></a>许可和成本优化

- 部署所有资源后，Contoso 应当根据其[基础结构规划](contoso-migration-infrastructure.md#set-up-tagging)分配 Azure 标记。
- 所有许可成本都包括在 Contoso 使用的 PaaS 服务的成本中。 这将从企业协议中扣除。
- Contoso 将启用由 Microsoft 子公司 Cloudyn 许可的 Azure 成本管理。 该服务是一个多云成本管理解决方案，可帮助利用和管理 Azure 与其他云资源。  [详细了解](https://docs.microsoft.com/azure/cost-management/overview) Azure 成本管理。

## <a name="conclusion"></a>结束语

在本文中，Contoso 在 Azure 中重新生成 SmartHotel 应用。 他们在 Azure 应用服务 Web 应用中重新生成本地应用前端 VM。 他们使用微服务生成应用后端，这些微服务部署到由 Azure Kubernetes 服务 (AKS) 管理的容器。 他们通过一个宠物照片应用增强了应用功能。




