---
title: 将 Contoso Linux 服务台应用重构到 Azure 应用服务和 Azure MySQL | Microsoft Docs
description: 了解 Contoso 如何通过以下方式重构本地 Linux 应用：将该应用迁移到 Azure 应用服务（将 GitHub 用于 Web 层）和 Azure SQL 数据库。
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 5981c708abdaa12a662075cc5bf5aae14ccc35c2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002154"
---
# <a name="contoso-migration-refactor-a-contoso-linux-service-desk-app-to-the-azure-app-service-and-azure-mysql"></a>Contoso 迁移：将 Contoso Linux 服务台应用重构到 Azure 应用服务和 Azure MySQL

本文展示了 Contoso 如何通过以下方式重构其本地双层 Linux 服务台应用 (osTicket)：将该应用迁移到集成了 GitHub 的 Azure 应用服务和 Azure MySQL。

本文档是展示虚构公司 Contoso 如何将其本地资源迁移到 Microsoft Azure 云的一系列文章中的第十篇。 该系列包括背景信息和说明如何设置迁移基础结构及运行不同类型的迁移的方案。 应用场景越来越复杂，我们将逐渐添加其他文章进行讲解。

**文章** | **详细信息** | **Status**
--- | --- | ---
[文章 1：概述](contoso-migration-overview.md) | 简要介绍 Contoso 的迁移策略、文章系列和所使用的示例应用。 | 可用
[文章 2：部署 Azure 基础结构](contoso-migration-infrastructure.md) | 介绍 Contoso 如何装备其本地和 Azure 基础结构进行迁移。 所有的 Contoso 迁移方案共用同一个基础结构。 | 可用
[文章 3：访问本地资源](contoso-migration-assessment.md)  | 展示 Contoso 如何评估 VMware 上运行的本地双层 SmartHotel 应用。 公司使用 [Azure Migrate](migrate-overview.md) 服务评估应用 VM，使用 [Azure 数据库迁移助手](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)评估应用 SQL Server 数据库。 | 可用
[文章 4：在 Azure VM 和 SQL 托管实例上重新托管](contoso-migration-rehost-vm-sql-managed-instance.md) | 演示 Contoso 如何将 SmartHotel 应用迁移到 Azure。 他们使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 迁移应用 Web VM，使用 [Azure 数据库迁移](https://docs.microsoft.com/azure/dms/dms-overview)服务将应用数据库迁移到 SQL 托管实例。 | 可用
[文章 5：在 Azure VM 上重新托管](contoso-migration-rehost-vm.md) | 展示 Contoso 如何使用 Site Recovery 服务将其 SmartHotel 迁移到 Azure IaaS VM。
[文章 6：在 Azure VM 和 SQL Server 可用性组上重新托管](contoso-migration-rehost-vm-sql-ag.md) | 展示 Contoso 如何迁移 SmartHotel 应用。 公司使用 Site Recovery 迁移应用 VM，同时使用数据库迁移服务将应用数据库迁移到 SQL Server 可用性组。 | 可用
[文章 7：将 Linux 应用重新托管到 Azure VM](contoso-migration-rehost-linux-vm.md) | 展示 Contoso 如何使用 Azure Site Recovery 将其 osTicket Linux 应用迁移到 Azure IaaS VM。
[文章 8：在 Azure VM 和 Azure MySQL 服务器上重新托管 Linux 应用](contoso-migration-rehost-linux-vm-mysql.md) | 演示 Contoso 如何迁移 osTicket Linux 应用。 他们使用 Site Recovery 进行 VM 迁移，使用 MySQL Workbench 迁移到 Azure MySQL 服务器实例。 | 可用
[文章 9：基于 Azure Web 应用和 Azure SQL 数据库重构应用](contoso-migration-refactor-web-app-sql.md) | 演示 Contoso 如何将 SmartHotel 应用迁移到基于 Azure 容器的 Web 应用，并将应用数据库迁移到 Azure SQL Server。 | 可用
文章 10：基于 Azure Web 应用和 Azure MySQL Server 重构 Linux 应用 | 展示 Contoso 如何使用 PHP 7.0 Docker 容器将 Linux 应用 osTicket 迁移到 Azure 应用服务。 部署的代码库迁移到 GitHub。 应用数据库迁移到 Azure MySQL。 | 本文。
[文章 11：基于 VSTS 重构 TFS](contoso-migration-tfs-vsts.md) | 展示 Contoso 如何通过将本地 Team Foundation Server (TFS) 部署迁移到 Azure 中的 Visual Studio Team Services (VSTS) 来迁移该部署。 | 可用
[文章 12：基于 Azure 容器和 Azure SQL 数据库重构应用](contoso-migration-rearchitect-container-sql.md) | 展示 Contoso 如何将其 SmartHotel 应用迁移并重新架构到 Azure。 他们将应用 Web 层重新架构为 Windows 容器，将应用数据库重新架构到 Azure SQL 数据库中。 | 可用
[文章 13：在 Azure 中重新生成应用](contoso-migration-rebuild.md) | 展示 Contoso 如何使用一系列 Azure 功能和服务（包括应用服务、Azure Kubernetes、Azure Functions、认知服务和 Cosmos DB）重新生成其 SmartHotel 应用。 | 可用

在本文中，Contoso 将双层 Linux Apache MySQL PHP (LAMP) 服务支持应用 (osTicket) 迁移到 Azure。 如果想要使用此开放源代码应用，可从 [GitHub](https://github.com/osTicket/osTicket) 下载。



## <a name="business-drivers"></a>业务驱动因素

IT 领导团队与其业务合作伙伴密切协作，以了解其想要实现的目标：

- **应对业务增长**：Contoso 正在发展壮大，并且在扩展新市场。 他们需要更多的客户服务代理。 
- **扩展**：构建的解决方案应当使 Contoso 能够随业务扩展添加更多的客户服务代理。
- **提高恢复能力**：过去，系统发生的问题仅影响内部用户。 对于其新的业务模型，外部用户也将受影响，Contoso 要求应用始终正常运行。

## <a name="migration-goals"></a>迁移目标

Contoso 云团队已确定此迁移的目标，以便确定最佳迁移方法：

- 应用程序应能够缩放，以超越当前的本地容量和性能。  Contoso 将移动应用程序以利用 Azure 的按需缩放功能。
- Contoso 希望将应用程序代码库移动到一个持续交付管道。  当应用更改推送到 GitHub 时，他们希望在无需操作人员执行任务的情况下部署这些更改。
- 应用程序必须具备增长和故障转移能力，从而具有弹性和恢复能力。 他们希望将应用部署在两个不同的 Azure 区域中，并将其设置为自动缩放。
- Contoso 希望，将应用移动到云之后，可以最大程度地减少数据库管理员任务。

## <a name="solution-design"></a>解决方案设计
在确定其目标和要求后，Contoso 设计并审查部署解决方案，确定迁移流程，包括将用于迁移的 Azure 服务。


## <a name="current-architecture"></a>当前体系结构

- 应用分层到两个 VM（OSTICKETWEB 和 OSTICKETMYSQL）中。
- 这两个 VM 位于 VMware ESXi 主机 **contosohost1.contoso.com**（6.5 版）上。
- VMware 环境由 VM 上运行的 vCenter Server 6.5 (**vcenter.contoso.com**) 托管。
- Contoso 有一个本地数据中心 (contoso-datacenter)，其中包含一个本地域控制器 (**contosodc1**)。

![当前体系结构](./media/contoso-migration-refactor-linux-app-service-mysql/current-architecture.png) 


## <a name="proposed-architecture"></a>建议的体系结构

在确定其当前的体系结构、目标和迁移要求后，Contoso 设计一个部署解决方案，确定迁移流程，包括将用于迁移的 Azure 服务。

- OSTICKETWEB 上的 Web 层应用将通过在两个 Azure 区域中构建 Azure 应用服务进行迁移。 将使用 PHP 7.0 Docker 容器实现适用于 Linux 的 Azure 应用服务。
- 应用代码将移动到 GitHub，并且 Azure Web 应用将配置为使用 GitHub 实现持续交付。
- Azure 应用服务器将同时部署在主区域（美国东部 2）和次要区域（美国中部）。
- 在两个区域中，都将在 Azure Web 应用的前面设置流量管理器。
- 流量管理器将配置为优先级模式，以强制流量经过”美国东部 2”。
- 如果“美国东部 2”中的 Azure 应用服务器脱机，则用户可以访问“美国中部”内故障转移后的应用。
- 应用数据库将使用 MySQL Workbench 工具迁移到 Azure MySQL PaaS 服务。 本地数据库将在本地备份，并直接还原到 Azure MySQL。
- 数据库将位于主区域“美国东部 2”中生产网络 (VNET-PROD-EUS2) 的数据库子网 (PROD-DB-EUS2) 中：
- 因为他们迁移的是生产工作负荷，所以 Azure 资源将位于在生产资源组 **ContosoRG** 中。
- 流量管理器资源将部署在 Contoso 的基础结构资源组 **ContosoInfraRG** 中。
- 迁移完成后，Contoso 数据中心的本地 VM 将停止使用。


![方案体系结构](./media/contoso-migration-refactor-linux-app-service-mysql/proposed-architecture.png) 


## <a name="migration-process"></a>迁移过程

Contoso 将按如下方式完成迁移进程：

1. 第一步，Contoso 设置 Azure 基础结构，包括预配 Azure 应用服务、设置流量管理器，以及预配 Azure MySQL 实例。
2. 准备好 Azure 后，他们使用 MySQL Workbench 迁移数据库。 
3. 当数据库在 Azure 中运行后，他们为 Azure 应用服务设置具有持续交付能力的 GitHub 专用存储库，并使用 osTicket 应用加载该存储库。
4. 在 Azure 门户中，他们将应用从 GitHub 加载到运行 Azure 应用服务的 Docker 容器。 
5. 他们微调 DNS 设置，并为应用配置自动缩放。

![迁移过程](./media/contoso-migration-refactor-linux-app-service-mysql/migration-process.png) 


### <a name="azure-services"></a>Azure 服务

**服务** | **说明** | **成本**
--- | --- | ---
[Azure 应用服务](https://azure.microsoft.com/services/app-service/) | 该服务使用 Azure PaaS 服务为网站运行和缩放应用程序。  | 定价基于实例大小和所需的功能。 [了解详细信息](https://azure.microsoft.com/pricing/details/app-service/windows/)。
[流量管理器](https://azure.microsoft.com/services/traffic-manager/) | 一个负载均衡器，它使用 DNS 将用户定向到 Azure 或外部网站和服务。 | 定价基于收到的 DNS 查询数以及所监视的终结点数。 | [了解详细信息](https://azure.microsoft.com/pricing/details/traffic-manager/)。
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) | 数据库基于开放源代码 MySQL 服务器引擎。 它提供完全托管的可供企业使用的社区 MySQL 数据库即服务，用于应用开发和部署。 | 定价基于计算、存储和备份要求。 [了解详细信息](https://azure.microsoft.com/pricing/details/mysql/)。

 
## <a name="prerequisites"></a>先决条件

如果你（和 Contoso）想要运行此方案，应做好以下准备。

**要求** | **详细信息**
--- | ---
**Azure 订阅** | 在学习本系列前几篇文章期间，应已创建一个订阅。 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。<br/><br/> 如果创建的是免费帐户，则你是自己的订阅的管理员，可以执行所有操作。<br/><br/> 如果你使用现有订阅并且不是管理员，则需要请求管理员为你分配“所有者”或“参与者”权限。<br/><br/> 如需更加细化的权限，请查看[此文](../site-recovery/site-recovery-role-based-linked-access-control.md)。 
**Azure 基础结构** | Contoso 按照[用于迁移的 Azure 基础结构](contoso-migration-infrastructure.md)中所述设置其 Azure 基础结构。



## <a name="scenario-steps"></a>方案步骤

下面是 Azure 完成迁移的步骤：

> [!div class="checklist"]
> * **步骤 1：预配 Azure 应用服务**：Contoso 将在主区域和次要区域中预配 Web 应用。
> * **步骤 2：设置流量管理器**：他们在 Web 应用之前设置流量管理器，用于对流量进行路由和负载均衡。
> * **步骤 3：预配 MySQL**：在 Azure 中，Contoso 预配 Azure MySQL 数据库的实例。
> * **步骤 4：迁移数据库**：他们使用 MySQL Workbench 迁移数据库。 
> * **步骤 5：设置 GitHub**：Contoso 为应用网站/代码设置本地 GitHub 存储库。
> * **步骤 6：部署 Web 应用**：Contoso 从 GitHub 部署 Web 应用。




## <a name="step-1-provision-azure-app-services"></a>步骤 1：预配 Azure 应用服务

Contoso 使用 Azure 应用服务预配两个 Web 应用（每个区域中一个）。

1. 他们从 Azure 市场在主区域“美国东部 2”中创建一个 Web 应用资源 (**osticket-eus2**)。
2. 他们将该资源放置在生产资源组 **ContosoRG** 中。

    ![Azure 应用](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app1.png) 

3. 他们使用标准大小在主区域中创建一个新的应用服务计划 (**APP-SVP-EUS2**)。

     ![Azure 应用](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app2.png) 
    
4. Contoso 选择了带 PHP 7.0 运行时堆栈（这是一个 Docker 容器）的 Linux OS。

    ![Azure 应用](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app3.png) 

5. 他们为“美国中部”区域创建另一个 Web 应用 (**osticket-cus**) 和应用服务计划。

    ![Azure 应用](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app4.png) 


**需要更多帮助？**

- 了解 [Azure 应用服务 Web 应用](https://docs.microsoft.com/azure/app-service/app-service-web-overview)。
- 了解 [Linux 上的 Azure 应用服务](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)。


## <a name="step-2-set-up-traffic-manager"></a>步骤 2：设置流量管理器

Contoso 将流量管理器设置为将入站 Web 请求定向到在 osTicket Web 层上运行的 Web 应用。

1. Contoso 从 Azure 市场创建一个流量管理器资源 (**osticket.trafficmanager.net**)。 他们使用优先级路由方式，使“美国东部 2”成为主站点。 他们将该资源放置在其基础结构资源组 (**ContosoInfraRG**) 中。 请注意，流量管理器是全局性的，不绑定到特定位置

    ![流量管理器](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager1.png) 

2. 现在，Contoso 为流量管理器配置终结点。 他们将“美国东部 2”Web 应用添加为主站点 (**osticket-eus2**)，将“美国中部”应用添加为次要站点 (**osticket-cus**)。

    ![流量管理器](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager2.png) 

3. 添加终结点后，他们可以对其进行监视。

    ![流量管理器](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager3.png)

**需要更多帮助？**

- 了解[流量管理器](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)。
- 了解[将流量路由到优先终结点](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-priority-routing-method)。
 
## <a name="step-3-provision-azure-database-for-mysql"></a>步骤 3：设置 Azure Database for MySQL

Contoso 在主要美国东部 2 区域中设置 MySQL 数据库实例。

1. 在 Azure 门户中，创建 Azure Database for MySQL 资源。 

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-1.png)

2. 为 Azure 数据库添加名称 contosoosticket。 将数据库添加到生产资源组 ContosoRG，并为其指定凭据。
3. 本地 MySQL 数据库版本为 5.7，因此，他们选择此版本以便获得兼容性。 他们使用满足其数据库需求的默认大小。

     ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-2.png)

4. 对于“备份冗余选项”，Contoso 选择使用“异地冗余”。 通过此选项，发生中断时可在其次要美国中部区域中还原数据库。 仅在设置数据库时，才能配置此选项。

    ![冗余](./media/contoso-migration-refactor-linux-app-service-mysql/db-redundancy.png)

4. Contoso 设置连接安全性。 在数据库 >“连接安全性”中，他们设置防火墙规则来允许数据库访问 Azure 服务。
5. 他们将本地工作站客户端 IP 地址添加到开始和结束 IP 地址。 这允许 Web 应用访问 MySQL 数据库，以及执行迁移的数据库客户端。

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-3.png)



## <a name="step-4-migrate-the-database"></a>步骤 4：迁移数据库

Contoso 将使用备份和还原通过 MySQL 工具迁移数据库。 安装 MySQL Workbench，从 OSTICKETMYSQL 备份数据库，然后将其还原到 Azure Database for MySQL 服务器。

### <a name="install-mysql-workbench"></a>安装 MySQL Workbench

1. Contoso 检查[必备项并下载 MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool)。
2. 根据[安装说明](https://dev.mysql.com/doc/workbench/en/wb-installing.html)安装适用于 Windows 的 MySQL Workbench。 安装它们的计算机必须可供 OSTICKETMYSQL VM 和 Azure 通过 Internet 进行访问。
3. 在 MySQL Workbench 中，创建到 OSTICKETMYSQL 的 MySQL 连接。 

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench1.png)

4. 将数据库作为“osticket”导出到本地自包含文件。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench2.png)

5. 在本地备份数据库后，创建到 Azure Database for MySQL 实例的连接。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench3.png)

6. 现在，Contoso 可从自包含文件的 Azure MySQL 实例中导入（还原）数据库。 将为该实例创建新架构 (osticket)。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench4.png)

7. 还原数据后，可以使用 Workbench 查询数据，并且数据将显示在 Azure 门户中。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench5.png)

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench6.png)

8. 最后，Contoso 需要更新 Web 应用上的数据库信息。 在 MySQL 实例上，他们打开“连接字符串”。 

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench7.png)

9. 在字符串列表中，他们找到 Web 应用设置，并单击以复制它们。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench8.png)

10. 他们打开记事本窗口并将该字符串粘贴到新文件中，更新该字符串以匹配 osticket 数据库、MySQL 实例和凭据设置。

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench9.png)

11. Contoso 可以从 Azure 门户中 MySQL 实例的“概述”中验证服务器名称和登录名。

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench10.png)


## <a name="step-5-set-up-github"></a>步骤 5：设置 GitHub

Contoso 创建一个新的专用 GitHub 存储库，并设置到 Azure MySQL 中的 osTicket 数据库的连接。 然后，他们通过应用加载 Azure Web 应用。  

1.  他们浏览到 OsTicket 软件公共 GitHub 存储库，并将其分支到 Contoso GitHub 帐户。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github1.png)

2. 在分支后，他们将被导航到 **include** 文件夹，并查找 **ost-config.php** 文件。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github2.png)


3. 文件将在浏览器中打开，然后他们对其进行编辑。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github3.png)

4. 在编辑器中，Contoso 更新数据库详细信息，具体而言是 **DBHOST** and **DBUSER**。 

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github4.png)

5. 然后，他们提交更改。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github5.png)

6. 对于每个 Web 应用（**osticket-eus2** 和 **osticket-cus**），Contoso 在 Azure 门户中修改**应用程序设置**。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github6.png)

7. 他们输入名为 **osticket** 的连接字符串，并将该字符串从记事本复制到**值区域**中。 他们从字符串旁边的下拉列表中选择 **MySQL**，然后保存设置。

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github7.png)

## <a name="step-6-configure-the-web-apps"></a>步骤 6：配置 Web 应用

作为迁移过程的最后一步，Contoso 通过 osTicket 网站配置 Web 应用。



1. 在主 Web 应用 (**osticket-eus2**) 中，打开“部署选项”并将源设置为 **GitHub**。

    ![配置应用](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app1.png)

2. 他们选择部署选项。

    ![配置应用](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app2.png)

3. 设置选项后，配置在 Azure 门户中显示为挂起。

    ![配置应用](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app3.png)

4. 更新配置并将 osTicket Web 应用从 GitHub 加载到运行 Azure 应用服务的 Docket 容器后，站点将显示为“活动”。

    ![配置应用](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app4.png)

5. 然后，Contoso 为第二个 Web 应用 (**osticket-cus**) 重复上述步骤。
6. 配置站点后，可以通过流量管理器配置文件访问它。 DNS 名称是 osTicket 应用的新位置。 [了解详细信息](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)。

    ![配置应用](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app5.png)
    
7. Contoso 需要一个易于记忆的 DNS 名称。 他们在其域控制器上的 DNS 中创建一个指向流量管理器名称的别名记录 (CNAME) **osticket.contoso.com**。

    ![配置应用](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app6.png)

8. 他们将 **osticket-eus2** 和 **osticket-cus** Web 应用都配置为允许自定义主机名。

    ![配置应用](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app7.png)

### <a name="set-up-autoscaling"></a>设置自动缩放

最后，他们为应用设置自动缩放。 这可以确保，随着代理使用应用，应用实例可以根据业务需求增大或减小。 

1. 在应用服务 **APP-SRV-EUS2** 中，Contoso 打开“缩放单位”。
2. 他们通过单个规则配置新的自动缩放设置，当用于当前实例的 CPU 百分比高于 70% 达 10 分钟时，该规则将实例计数增加 1。

    ![自动缩放](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale1.png)

3. 他们在 **APP-SRV-CUS** 上配置相同的设置以确保当应用故障转移到次要区域时应用相同的行为。 唯一的差异是他们将实例限制设置为 1，因为这仅用于故障转移。

   ![自动缩放](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale2.png)

##  <a name="clean-up-after-migration"></a>迁移后的清理

在迁移完成后，osTicket 应用重构为在 Azure Web 应用中运行，且使用一个专用 GitHub 存储库实现持续交付。 应用在两个区域中运行以增强恢复能力。 迁移到 PaaS 平台后，osTicket 数据库在 Azure database for MySQL 中运行。

现在，Contoso 需要执行以下操作： 
- 从 vCenter 清单中删除 VMware VM。
- 从本地备份作业中删除本地 VM。
- 更新内部文档显示新的位置和 IP 地址。 
- 查看与本地 VM 交互的任何资源，更新任何相关设置或文档以反映新配置。
- 将监视重新配置为指向 osticket trafficmanager.net URL，以跟踪应用是否已启动并正在运行。

## <a name="review-the-deployment"></a>查看部署

现在，在应用正在运行的情况下，Contoso 需要完全操作和保护其新的基础结构。

### <a name="security"></a>“安全”

Contoso 安全团队审查应用来确定是否存在任何安全问题。 他们发现没有为 osTicket 应用与 MySQL 数据库实例之间的通信配置 SSL。 他们将需要进行此操作，以确保数据库流量不被攻击。 [了解详细信息](https://docs.microsoft.com/azure/mysql/howto-configure-ssl)。

### <a name="backups"></a>备份

- osTicket Web 应用不包含状态数据，因此无需进行备份。
- 他们无需为数据库配置备份。 Azure Database for MySQL 会自动创建并存储服务器备份。 他们选择为数据库使用异地冗余，因此，该数据库富有弹性且生产就绪。 备份可以用来将服务器还原到某个时间点。 [了解详细信息](https://docs.microsoft.com/azure/mysql/concepts-backup)。


### <a name="licensing-and-cost-optimization"></a>许可和成本优化

- PaaS 部署不存在许可问题。
- Contoso 将启用由 Microsoft 子公司 Cloudyn 许可的 Azure 成本管理。 该服务是一个多云成本管理解决方案，可帮助利用和管理 Azure 与其他云资源。  [详细了解](https://docs.microsoft.com/azure/cost-management/overview) Azure 成本管理。



