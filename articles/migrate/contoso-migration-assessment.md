---
title: 评估本地工作负荷是否适合将 Contoso 迁移到 Azure | Microsoft Docs
description: 了解 Contoso 通过 Azure 迁移和数据库迁移评估其本地计算机是否适合迁移到 Azure 的方式
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: fa6fb4ffe1eea98392b2199f379431b0dffc6774
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006560"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Contoso 迁移：评估本地工作负荷是否适合迁移到 Azure

本文介绍 Contoso 如何评估其准备迁移到 Azure 的本地 SmartHotel 应用。

我们提供一系列的文章展示虚拟公司 Contoso 如何将其本地资源迁移到 Microsoft Azure 云中，而本文档是该系列的第三篇。 该系列介绍了背景信息，同时提供一系列部署场景来描述如何设置迁移基础结构、评估本地资源是否适合迁移和运行不同类型的迁移。 应用场景越来越复杂，我们将逐渐添加其他文章进行讲解。

**文章** | **详细信息** | **Status**
--- | --- | ---
[文章 1：概述](contoso-migration-overview.md) | 简要介绍 Contoso 的迁移策略、文章系列和所使用的示例应用。 | 可用
[文章 2：部署 Azure 基础结构](contoso-migration-infrastructure.md) | 介绍 Contoso 如何装备其本地和 Azure 基础结构进行迁移。 所有迁移文章共用同一个基础结构。 | 可用
文章 3：评估要迁移到 Azure 的本地资源  | 展示 Contoso 如何评估 VMware 上运行的本地双层 SmartHotel 应用。 Contoso 使用 [Azure Migrate](migrate-overview.md) 服务评估应用 VM，使用 [数据库迁移助手](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)评估应用 SQL Server 数据库。 | 本文。
[文章 4：在 Azure VM 和 SQL 托管实例上重新托管应用](contoso-migration-rehost-vm-sql-managed-instance.md) | 演示 Contoso 如何将本地 SmartHotel 应用直接迁移到 Azure。 Contoso 使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 迁移应用前端 VM，使用 [Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/dms-overview)将应用数据库迁移到 SQL 托管实例。 | 可用
[文章 5：在 Azure VM 上重新托管应用](contoso-migration-rehost-vm.md) | 演示 Contoso 如何使用 Site Recovery 服务将 SmartHotel 应用 VM 迁移到 Azure VM。 | 可用
[文章 6：在 Azure VM 和 SQL Server AlwaysOn 可用性组上重新托管应用](contoso-migration-rehost-vm-sql-ag.md) | 展示 Contoso 如何迁移 SmartHotel 应用。 Contoso 使用 Site Recovery 来迁移应用 VM，同时使用数据库迁移服务将应用数据库迁移到受 AlwaysOn 可用性组保护的 SQL Server 群集。 | 可用
[文章 7：将 Linux 应用重新托管到 Azure VM](contoso-migration-rehost-linux-vm.md) | 展示 Contoso 如何使用 Site Recovery 将 Linux osTicket 应用直接迁移到 Azure VM | 可用
[文章 8：在 Azure VM 和 Azure MySQL 上重新托管 Linux 应用](contoso-migration-rehost-linux-vm-mysql.md) | 演示 Contoso 如何使用 Site Recovery 将 Linux osTicket 应用迁移到 Azure VM，以及如何使用 MySQL 工作台将应用数据库迁移到 Azure MySQL 服务器实例。 | 可用
[文章 9：基于 Azure Web 应用和 Azure SQL 数据库重构应用](contoso-migration-refactor-web-app-sql.md) | 演示 Contoso 如何将 SmartHotel 应用迁移到 Azure Web 应用，并将应用数据库迁移到 Azure SQL Server 实例 | 可用
[文章 10：基于 Azure Web 应用和 Azure MySQL 重构 Linux 应用](contoso-migration-refactor-linux-app-service-mysql.md) | 演示 Contoso 如何将 Linux osTicket 应用迁移到多个站点中的 Azure Web 应用，并与 GitHub 集成以便持续交付。 他们将应用数据库迁移到 Azure MySQL 实例。 | 可用
[文章 11：基于 VSTS 重构 TFS](contoso-migration-tfs-vsts.md) | 演示 Contoso 如何通过将本地 Team Foundation Server (TFS) 部署迁移到 Azure 中的 Visual Studio Team Services (VSTS) 来迁移该部署。 | 可用
[文章 12：在 Azure 容器和 Azure SQL 数据库上重塑应用架构](contoso-migration-rearchitect-container-sql.md) | 演示 Contoso 如何将其 SmartHotel 应用迁移并重新架构到 Azure。 他们将应用 Web 层重新架构为 Windows 容器以及 Azure SQL 数据库中的应用数据库。 | 可用
[文章 13：在 Azure 中重新生成应用](contoso-migration-rebuild.md) | 演示 Contoso 如何使用一系列 Azure 功能和服务（包括应用程序服务、Azure Kubernetes、Azure Functions、认知服务和 Cosmos DB）重新生成其 SmartHotel 应用。 | 可用


## <a name="overview"></a>概述

在考虑迁移到 Azure 时，Contoso 公司希望进行技术和财务方面的评估，以便确定其本地工作负荷是否适合迁移到云。 具体而言，Contoso 团队想要评估进行迁移时计算机和数据库的兼容性，并估算在 Azure 中运行其资源所需的容量和成本。

为了开始并且更好地了解涉及的技术，公司将对其两个本地应用进行评估，并在下表中予以概述。 请注意，该公司评估的是重新托管和重构应用以进行迁移的迁移方案。 有关重新托管和重构的详细信息，请参阅 [Contoso 迁移概述](contoso-migration-overview.md)。

**应用名称** | 平台 | **应用层** | **详细信息**
--- | --- | --- | ---
SmartHotel<br/><br/> 管理 Contoso 旅行要求 | 通过 SQL Server 数据库在 Windows 上运行 | 具有前端 ASP.NET 网站的两层应用在一个 VM (WEBVM) 上运行，SQL Server 在另一个 VM (SQLVM) 上运行 | VM 为 VMware，在 vCenter Server 托管的 ESXi 主机上运行。<br/><br/> 可以从 [GitHub](https://github.com/Microsoft/SmartHotel360) 下载示例应用。
OSTicket<br/><br/> Contoso 服务台应用 | 通过 MySQL PHP (LAMP) 在 Linux/Apache 上运行。 | 具有前端 PHP 网站的两层应用在一个 VM (OSTICKETWEB) 上运行，MySQL 数据库在另一个 VM (OSTICKETMYSQL) 上运行 | 客户服务应用使用此应用跟踪内部员工和外部客户的问题。<br/><br/> 可以从 [GitHub](https://github.com/osTicket/osTicket) 下载示例应用。

## <a name="current-architecture"></a>当前体系结构


下图展示了 Contoso 当前的本地体系结构。

![Contoso 体系结构](./media/contoso-migration-assessment/contoso-architecture.png)  

- Contoso 的一个主要数据中心位于美国东部的纽约市。
- 公司在美国还有 3 家当地分支机构。
- 主要数据中心通过城域光纤以太网连接到 Internet（速度 500 mbps）。
- 每家分支机构均在本地使用业务级连接实现联网，使用 IPSec VPN 隧道连接回主要数据中心。 这使得公司整个网络永久连接不掉线，同时优化了 Internet 的连接性。
- 主要数据中心通过 VMware 实现了完全的虚拟化处理。 公司有两台 ESXi 6.5 虚拟化主机，由 vCenter Server 6.5 进行托管。
- Contoso 使用 Active Directory 管理身份信息，并在内网上使用 DNS 服务器。
- 数据中心的域控制器在 VMware VM 上运行。 而当地分支机构的域控制器在物理服务器上运行。





## <a name="business-drivers"></a>业务驱动因素

IT 领导团队与业务合作伙伴密切协作，以了解本次迁移要实现的业务目标：

- **满足业务增长**：Contoso 在不断壮大，这给本地系统和基础结构造成了压力。
- **增加效率**：Contoso 需要摒弃不必要的流程，简化开发人员和用户流程。  业务要求 IT 反应迅速，不浪费时间金钱，从而更快满足客户需求。
- **提高灵活性**：Contoso IT 需要对业务需求更加敏感。 它必须能够抢在市场变化之前作出反应，这样才能在全球经济中取得成功。  同时它不能阻碍发展，成为业务的绊脚石。
- **扩展**：随着业务成功发展，Contoso IT 必须提供能够同步成长的系统。

## <a name="assessment-goals"></a>评估目标

Contoso 云团队制定了迁移评估的目标：

- 迁移后，Azure 中应用的功能应当能够与其当前在本地 VMWare 环境中的功能媲美。  迁移到云并非表示应用性能变得不太重要。
- Contoso 需要了解其应用程序和数据库与 Azure 要求的兼容性以及其在 Azure 中的托管选项。
- 应用移动到云后，应该将 Contoso 的数据库管理降至最低。  
- Contoso 不仅想要了解迁移选项，还想要了解移动到云后与基础结构相关的成本。

## <a name="assessment-tools"></a>评估工具
Contoso 使用 Microsoft 工具进行评估。 这些工具符合 Contoso 的目标，并能为其提供所有所需信息。

**技术** | **说明** | **成本**
--- | --- | ---
[数据迁移助手 (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | 公司将使用 DMA 评估和检测可能影响其在 Azure 中数据库功能的兼容性问题。 DMA 评估 SQL 源和目标之间的功能奇偶一致性，并针对性能和可靠性提升基础建议。 | 它是可以免费下载的工具。
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Contoso 将使用此服务评估其 VMware VM。 它评估计算机是否适合迁移，并对在 Azure 中运行时的大小和成本进行估算。  | 目前（2018 年 5 月），使用此服务不需付费。
[服务地图](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate 使用服务映射来显示要迁移的计算机之间的依赖关系。 |  服务映射是 Azure Log Analytics 的一部分。 它目前可以免费使用 180 天。

在此方案中，Contoso 下载并运行 DMA，以便评估其旅游应用的本地 SQL Server 数据库。 公司将使用带有依赖项映射功能的 Azure Migrate 来评估应用 VM，然后再迁移到 Azure。



## <a name="assessment-architecture"></a>评估体系结构


![迁移评估体系结构](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Contoso 是表示一家典型企业组织的虚构名称。
- Contoso 有一个本地数据中心 (contoso-datacenter)，其中包含本地域控制器（CONTOSODC1、CONTOSODC2）。
- VMware VM 位于运行 6.5 版本的 VMware ESXI 主机上。 主机：contosohost1、contosohost2
- VMware 环境由 VM 上运行的 vCenter Server 6.5 (venter) 托管。
- SmartHotel 旅行应用：
    - 应用程序层跨两个 VMware VM 分层：WEBVM 和 SQLVM。
    - 这两个 VM 位于 VMware ESXi 主机 contosohost1.contoso.com 上。
    - VM 运行包含 SP1 的 Windows Server 2008 R2 数据中心。
- VMware 环境由在 VM 上运行的 vCenter Server (**vcenter.contoso.com**) 托管。
- OSTicket 服务台应用：
    - 应用跨两个 VM 分层：OSTICKETWEB 和 OSTICKETMYSQL。
    - VM 在 Ubuntu Linux Server 16.04-LTS 上运行。
    - OSTICKETWEB VM 运行 Apache 2 和 PHP 7.0。
    - OSTICKETMYSQL VM 运行 MySQL 5.7.22。

![体系结构](./media/contoso-migration-assessment/architecture.png)


## <a name="prerequisites"></a>先决条件

在本评估中，Contoso（以及你）需要满足以下条件：

- 所有者或参与者拥有对 Azure 订阅或者 Azure 订阅中资源组的访问权限。
- 运行 5.5、6.0 或 6.5 版本的本地 vCenter Server。
- vCenter Server 中的一个只读帐户，或者创建该帐户的权限。
- 在 vCenter Server 上使用 .OVA 模板创建 VM 的权限。
- 至少一个运行 5.0 或更高版本的 ESXi 主机。
- 至少两个本地 VMware VM，一个 VM 运行一个 SQL Server 数据库。
- 在每个 VM 上安装 Azure Migrate 代理的权限。
- 这些 VM 应该有直接的 Internet 连接。
        - 可以仅限对[所需 URL](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites) 进行 Internet 访问。
        - 如果计算机未连接 internet，则需要在上面安装 [OMS 网关](../log-analytics/log-analytics-oms-gateway.md)。
- 运行 SQL Server 实例的 VM 的 FQDN（用于数据库评估）。
- 在 SQL Server VM 上运行的 Windows 防火墙应该允许在 TCP 端口 1433（默认）上进行外部连接，这样 DMA 才能够进行连接。


## <a name="assessment-overview"></a>评估概述

下面是 Contoso 进行评估的方式：


> [!div class="checklist"]
> * **步骤 1：下载并安装 DMA**：准备 DMA，用于对本地 SQL Server 数据库进行评估。
> * **步骤 2：使用 DMA 评估数据库**：运行并分析数据库评估。
> * **步骤 3：通过 Azure Migrate 进行 VM 评估准备**：设置本地帐户并调整 VMware 设置。
> * **步骤 4：使用 Azure Migrate 发现本地 VM**：创建 Azure Migrate 收集器 VM。 然后，运行收集器以发现用于评估的 VM。
> * **步骤 5：使用 Azure Migrate 进行依赖项分析准备**：在 VM 上安装 Azure Migrate 代理，以便了解在 VM 之间映射的依赖关系。
> * **步骤 6：使用 Azure Migrate 评估 VM**：检查依赖项、对 VM 分组，然后运行评估。 在评估就绪以后，对其进行分析并做好迁移准备。


## <a name="step-1-download-and-install-the-dma"></a>步骤 1：下载并安装 DMA

1. Contoso 从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=53595)下载 DMA。
    - 可以在能够连接到 SQL 实例的任何计算机上安装此助手。 不需在 SQL Server 计算机上运行它。
    - 不应在 SQL Server 主机上运行它。
2. 运行已下载的安装程序文件 (DownloadMigrationAssistant.msi) 即可开始安装。
3. 在“完成”页面上，先选择“启动 Microsoft 数据迁移助手”，再完成向导。

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel"></a>步骤 2：运行和分析针对 SmartHotel 的数据库评估

现在，Contoso 可以运行评估，分析其 SmartHotel 应用的本地 SQL Server。

1. 在数据迁移助手中，单击“新建”，选择“评估”，并为评估提供项目名称“SmartHotel”。
2. 选择“源服务器类型”作为“Azure 虚拟机上的 SQL Server”。

    ![选择源](./media/contoso-migration-assessment/dma-assessment-1.png)

    > [!NOTE]
      目前，DMA 不支持迁移到 SQL 托管实例所需的评估。 为了解决此问题，Contoso 将“Azure VM 上的 SQL Server”用作评估的假定目标。

3. 在“选择目标版本”中，选择 SQL Server 2017 作为目标版本。 需要选择此选项是因为这是 SQL 托管实例使用的版本。
4. 选择它以发现有关兼容性和新功能的信息：
    - “兼容性问题”会指出那些可能会妨碍迁移的更改，或者需要在迁移之前进行微小调整的更改。 它会始终告知任何当前使用功能已弃用的情况。 问题按兼容性级别进行组织。
    - “新功能的建议”会指出目标 SQL Server 平台中可以在迁移后用于数据库的新功能。 这些功能按“性能”、“安全性”和“存储”进行组织。

    ![选择目标](./media/contoso-migration-assessment/dma-assessment-2.png)

2. 在“连接到服务器”中，公司输入运行数据库的 VM 名称和凭据来进行访问。 需要启用“信任服务器证书”，确保可访问 SQL Server。 然后单击“连接”。

    ![选择目标](./media/contoso-migration-assessment/dma-assessment-3.png)

3. 在“添加源”中，添加需要评估的数据库，并单击“下一步”开始评估。
4. 评估已创建。

    ![创建评估](./media/contoso-migration-assessment/dma-assessment-4.png)

5. 在“查看结果”中，可查看评估结果。


### <a name="analyze-the-database-assessment"></a>分析数据库评估

会立即显示可用结果。 如果是修复问题，则需要单击“重新启动评估”来重新运行评估。

1. 在“兼容性问题”报告中，检查每个兼容性级别的任何问题。 兼容性级别会映射到 SQL Server 版本，如下所示：

    - 100：SQL Server 2008/Azure SQL 数据库
    - 110：SQL Server 2012/Azure SQL 数据库
    - 120：SQL Server 2014/Azure SQL 数据库
    - 130：SQL Server 2016/Azure SQL 数据库
    - 140：SQL Server 2017/Azure SQL 数据库

    ![兼容性问题](./media/contoso-migration-assessment/dma-assessment-5.png)

2. 在“功能建议”报告中，Contoso 在迁移后可以查看此评估建议的性能、安全性和存储功能。 建议的功能多种多样，其中包括：内存中 OLTP 和列存储、Stretch Database、Always Encrypted、动态数据掩码、透明数据加密 (TDE)。

    ![功能建议](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > 我们建议 Contoso 为所有 SQL Server 数据库[启用 TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017)，这在数据库位于云时尤为重要。 应仅在迁移后启用 TDE。 如果已启用 TDE，则需要将证书或非对称密钥移到目标服务器的 master 数据库。 [了解详细信息](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)。

2. 可用 JSON 或 CSV 格式导出评估。

请注意：如果运行更大规模的评估，则可以：

- 并发运行多个评估，然后打开“所有评估”页来查看评估的状态。
- [将评估合并到一个 SQL Server 数据库中](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database)。
- [将评估合并到一个 PowerBI 报表中](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017)。


## <a name="step-3-prepare-for-vm-assessment-with-azure-migrate"></a>步骤 3：使用 Azure Migrate 进行 VM 评估准备

Contoso 需要创建可供 Azure Migrate 用来自动发现待评估 VM 的 VMware 帐户、验证自己是否有权创建 VM、记下需要打开的端口，以及设置统计信息设置级别。

### <a name="set-up-a-vmware-account"></a>设置 VMware 帐户

 VM 发现需要 vCenter 中的只读帐户，具有以下属性：

- 用户类型：至少为只读用户。
- 权限：数据中心对象 –> 传播到子对象，角色=只读。
- 详细信息：用户在数据中心级别分配，可以访问数据中心的所有对象。
- 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）。

### <a name="verify-permissions-to-create-a-vm"></a>验证是否有权创建 VM

Contoso 通过导入 .OVA 格式的文件来验证自己是否有权创建 VM。 [了解详细信息](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1)。

### <a name="verify-ports"></a>验证端口

Contoso 评估使用依赖项映射。 此功能要求在需要评估的 VM 上安装代理。 代理需要能够从每个 VM 的 TCP 端口 443 连接到 Azure。 [详细了解](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid)连接要求。


### <a name="set-statistics-settings"></a>设置统计信息设置

在开始部署前，Contoso 必须将 vCenter Server 的统计设置设为级别 3。 请注意：

- 设置级别以后，需等待至少一天，然后才能运行评估。 否则，可能会出现异常。
- 如果级别高于 3，可以正常评估，但会出现以下情况：
    - 不会收集磁盘和网络的性能数据。
    - 对于存储，Azure Migrate 建议使用 Azure 中的标准磁盘，其大小与本地磁盘一样。
    - 对于网络，只要有一个本地网络适配器，就应在 Azure 中有一个对应的网络适配器。
    - 对于计算，Azure Migrate 会查看 VM 核心数和内存大小，并会建议 Azure VM 使用相同的配置。 如果有多个合格的 Azure VM 大小，建议选择成本最低的那一个。
- [详细了解](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)级别 3 对应的大小。

公司级别设置如下：

1. 公司在 vSphere Web 客户端中，打开 vCenter Server 实例。
2. 在“管理” > “设置” > “常规”中，单击“编辑”。
3. 在“统计信息”中，将统计信息级别设置为“级别 3”。

    ![vCenter 统计信息级别](./media/contoso-migration-assessment/vcenter-statistics-level.png)



## <a name="step-4-discover-vms"></a>步骤 4：发现 VM

为了发现 VM，Contoso 创建 Azure Migrate 项目。 公司下载和设置收集器 VM，并运行收集器发现其本地 VM。

### <a name="create-a-project"></a>创建一个项目

1. 在 [Azure 门户](https://portal.azure.com)中，搜索“Azure Migrate”，并创建一个项目 (ContosoMigration)。
2. 公司指定项目名称和 Azure 订阅，并且创建新的 Azure 资源组“ContosoFailoverRG”。 请注意：

    - 只能在“美国中西部”或“美国东部”区域创建一个 Azure Migrate 项目。
    - 可以针对任意目标位置来计划迁移。
    - 项目位置仅用于存储从本地 VM 中收集的元数据。

    ![Azure Migrate](./media/contoso-migration-assessment/project-1.png)


### <a name="download-the-collector-appliance"></a>下载收集器设备

Azure Migrate 会创建一个称作收集器设备的本地 VM。 此 VM 可发现本地 VMware VM，并将其相关元数据发送到 Azure Migrate 服务。 为了设置收集器设备，Contoso 下载 .OVA 模板，并将其导入到本地 vCenter Server 以创建 VM。

1. 在 Azure Migrate 项目 >“入门” > “发现和评估” > “发现计算机”中，下载 .OVA 模板文件。
2. 复制项目 ID 和密钥。 这些都是配置收集器所必需的。

    ![下载 .ova 文件](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>验证收集器设备

在部署 VM 前，Contoso 检查 .OVA 文件的安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令，生成 OVA 的哈希：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 生成的哈希应与这些设置匹配（版本 1.0.9.12）

**算法** | **哈希值**
--- | ---
MD5 | d0363e5d1b377a8eb08843cf034ac28a
SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

### <a name="create-the-collector-appliance"></a>创建收集器设备

现在，Contoso 可将下载的文件导入到 vCenter Server 并且预配配置服务器 VM。

1. 在 vSphere 客户端控制台中，单击“文件” > “部署 OVF 模板”。

    ![部署 OVF](./media/contoso-migration-assessment/vcenter-wizard.png)

2. 在“部署 OVF 模板向导”>“源”中，指定 OVA 文件的位置。
3. 在“名称和位置”中，为收集器 VM 指定一个友好名称，以及待托管 VM 的库存位置。 同时指定要在其上运行收集器设备的主机或群集。
5. 在“存储”中，指定存储位置；在“磁盘格式”中，指定所需的存储预配方式。
7. 在“网络映射”中，指定收集器 VM 要连接到的网络。 网络需要与 Internet 建立连接才能向 Azure 发送元数据。
8. 查看设置，并选择“部署后打开”> “完成”。 在创建设备以后，系统会发出一条消息，确认已成功完成。

### <a name="run-the-collector-to-discover-vms"></a>运行收集器以发现 VM。

现在，运行收集器以发现 VM。 请注意，此收集器目前仅支持使用“英语(美国)”作为操作系统语言和收集器界面语言。

1. 在 vSphere 客户端控制台>“打开控制台”中，为收集器 VM 指定语言、时区和密码首选项。
2. 在桌面上，单击“运行收集器”快捷方式。

    ![收集器快捷方式](./media/contoso-migration-assessment/collector-shortcut.png)

4. 在 Azure Migrate 收集器>“设置先决条件”中，接受许可条款并阅读第三方信息。
5. 收集器会检查 VM 是否可以访问 Internet、时间是否已同步，，以及收集器服务是否正在运行（此服务默认安装在 VM 上）。 它还会安装 VMWare PowerCLI。

    > [!NOTE]
    > 我们假定此 VM 可以直接访问 Internet，不需使用代理。

    ![验证先决条件](./media/contoso-migration-assessment/collector-verify-prereqs.png)


5. 在“指定 vCenter Server 详细信息”中，指定 vCenter Server 名称 (FQDN) 或 IP 地址，以及用于发现的只读凭据。
7. 选择 VM 发现的范围。 收集器只能发现指定范围内的 VM。 可将范围设置为特定文件夹、数据中心或群集。 它不应包含超过 1500 台 VM。

    ![连接到 vCenter](./media/contoso-migration-assessment/collector-connect-vcenter.png)

6. 在“指定迁移项目”中，指定从门户复制的 Azure Migrate 项目 ID 和密钥。 可在项目“概述”页>“发现计算机”中再次获取这些信息。  

    ![连接到 Azure](./media/contoso-migration-assessment/collector-connect-azure.png)

7. 在“查看收集进度”中，Contoso 可以监视发现过程，并检查从 VM 中收集的元数据是否在范围内。 收集器提供一个近似的发现时间。

    ![正在进行收集](./media/contoso-migration-assessment/collector-collection-process.png)



### <a name="verify-vms-in-the-portal"></a>在门户中验证 VM

收集完成以后，Contoso 会检查 VM 是否显示在门户中。

1. 在 Azure Migrate 项目>“管理” > “计算机”中，检查需要发现的 VM 是否出现。

    ![已发现的计算机](./media/contoso-migration-assessment/discovery-complete.png)

3. 请注意，这些计算机目前尚未安装 Azure Migrate 代理。 Contoso 需要安装代理以查看依赖项。

    ![已发现的计算机](./media/contoso-migration-assessment/machines-no-agent.png)



## <a name="step-5-prepare-for-dependency-analysis"></a>步骤 5：进行依赖项分析准备

为了查看 Contoso 想要访问的 VM 之间的依赖关系，公司在应用 VM 上下载并安装代理。 Contoso 为其所有 VM 上的 Windows 和 Linux 应用执行此操作。

### <a name="take-a-snapshot"></a>生成快照

通过在代理安装前生成快照，可在修改前保留 VM 副本。

![计算机快照](./media/contoso-migration-assessment/snapshot-vm.png)


### <a name="download-and-install-the-vm-agents"></a>下载并安装 VM 代理

1. 在“计算机”页上，选择计算机，然后在“依赖项”列中“要求安装”。
2. 在“发现计算机”页上，执行以下操作：
    - 为每个 Windows VM 下载 MMA 和 Dependency Agent
    - 为每个 Linux VM 下载 MMA 和 Dependency Agent
3. 现在，复制工作区 ID 和密钥。 安装 MMA 时需要以下内容。

    ![代理下载](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>在 Windows VM 上安装代理

在每个 VM 上运行安装。

#### <a name="install-the-mma-on-windows-vms"></a>在 Windows VM 上安装 MMA

1. 双击已下载的代理。
2. 在“目标文件夹”中，保留默认安装文件夹，然后单击“下一步”。
2. 在“代理安装选项”中，选择“将代理连接到 Azure Log Analytics” > “下一步”。

    ![MMA 安装](./media/contoso-migration-assessment/mma-install.png)

5. 在“Azure Log Analytics”中，粘贴从门户复制的工作区 ID 和密钥。

    ![MMA 安装](./media/contoso-migration-assessment/mma-install2.png)

6. 在“准备安装”中，现在可以安装 MMA。

#### <a name="install-the-dependency-agent-on-windows-vms"></a>在 Windows VM 上安装 Dependency Agent

1. 双击已下载的 Dependency Agent。
2. 接受许可条款，并等待安装完成。

    ![依赖关系代理](./media/contoso-migration-assessment/dependency-agent.png)


### <a name="install-the-agents-on-linux-vms"></a>在 Linux VM 上安装代理

在每个 VM 上运行安装。

#### <a name="install-the-mma-on-linux-vms"></a>在 Linux VM 上安装 MMA

1. 使用 sudo apt-get install python-ctypeslib 在每个 VM 上安装 python ctypes 库。
2. 应运行命令，以 root 身份安装 MMA 代理。  要获取 root 权限，请运行以下命令并输入根密码 sudo -i。
3. 现在，安装 MMA 代理。
    - 将正确的工作区 ID 和密钥插入到命令中。
    - 这些命令适用于 64 位。
    - 在 OMS 门户>“连接的源”选项卡>“设置”中，可找到“工作区 ID”和“主键”。
    - 运行以下命令下载 OMS 代理，验证校验并安装/载入代理。

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
    ```



#### <a name="install-the-dependency-agent-on-linux-vms"></a>在 Linux VM 上安装 Dependency Agent

安装 MMA 后，Contoso 可以在 Linux VM 上安装 Dependency Agent。

1. 使用 InstallDependencyAgent-Linux64.bin（具有自解压二进制文件的 Shell 脚本）在 Linux 计算机上安装 Dependency Agent。 使用 sh 来运行文件或将执行权限添加到文件本身。

2. 以 root 身份安装 Linux Dependency Agent：

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```


## <a name="step-6-run-and-analyze-the-vm-assessment"></a>步骤 6：运行和分析 VM 评估

Contoso 现在可以验证计算机依赖关系并创建一个组。 然后，为组运行评估。

### <a name="verify-dependencies-and-create-a-group"></a>验证依赖关系并创建一个组


1. 为了分析计算机，公司单击“查看依赖项”。

    ![查看计算机依赖项](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. SQLVM 的依赖关系映射显示了以下详细信息：

    - 在指定的时段（默认为一小时）内在 SQLVM 上有活动的网络连接运行的进程组/进程
    - 与所有依赖关系计算机的入站（客户端）和出站（服务器）TCP 连接。
    - 安装了 Azure Migrate 代理的依赖关系计算机显示为单独的框
    - 没有安装代理的计算机会显示端口和 IP 地址信息。

3. 对于安装了代理的计算机 (WEBVM)，公司单击计算机框以查看更多信息，包括 FQDN、操作系统和 MAC 地址。

    ![查看组依赖项](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. 现在，选择要添加到组的 VM（SQLVM 和 WEBVM）。  可以使用 CTRL+单击选择多个 VM。
5. 单击“创建组”，然后指定名称 (smarthotelapp)。

> [!NOTE]
    > 若要查看更细致的依赖关系，可以展开时间范围。 可以选择一个具体的时段，或者选择开始日期和结束日期。


### <a name="run-an-assessment"></a>运行评估


1. 在“组” 页上，打开组 (smarthotelapp)，并单击“创建评估”。

    ![创建评估](./media/contoso-migration-assessment/run-vm-assessment.png)

2. 评估会显示在“管理” > “评估”页中。

Contoso 使用默认评估设置，但也可以自定义设置。 [了解详细信息](how-to-modify-assessment.md)。



### <a name="analyze-the-vm-assessment"></a>分析 VM 评估

Azure Migrate 评估包括以下信息：本地 VM 是否兼容 Azure、建议的适用于 Azure VM 的大小，以及估算的每月 Azure 成本。

![评估报告](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>查看置信度分级

![评估屏幕](./media/contoso-migration-assessment/assessment-display.png)

评估的置信度分为 1 星到 5 星（1 星表示置信度最低，5 星表示置信度最高）。
- 为评估分配置信度时，会考虑到进行评估计算时所需数据点的可用性。
- 此分级可以用来评估 Azure Migrate 提供的大小建议的可靠性。
- 进行“基于性能的大小调整”时，可以使用置信度分级，因为 Azure Migrate 可能没有足够的数据点来进行基于使用率的大小调整。 对于“按本地大小调整”，置信度分级始终为 5 星，因为 Azure Migrate 有进行 VM 大小调整所需的所有数据点。
- 提供评估的置信度分级时，会考虑到可用数据点的百分比：

   **数据点的可用性** | **置信度分级**
   --- | ---
   0%-20% | 1 星
   21%-40% | 2 星
   41%-60% | 3 星
   61%-80% | 4 星
   81%-100% | 5 星

#### <a name="verify-readiness"></a>验证就绪性

![评估就绪](./media/contoso-migration-assessment/azure-readiness.png)  

评估报表显示的信息汇总在表中。 请注意，若要显示基于性能的大小调整情况，Azure Migrate 需要以下信息。 如果此信息无法收集，大小评估可能不准确。

- CPU 和内存的使用率数据。
- 每个附加到 VM 的磁盘的读/写 IOPS 和吞吐量。
- 每个附加到 VM 的网络适配器的网络流入/流出量信息。


**设置** | **指示** | **详细信息**
--- | --- | ---
**Azure VM 就绪** | 指示 VM 是否已做好迁移准备 | 可能的状态：</br><br/>- Azure 已就绪<br/><br/>- 各项条件已准备就绪 <br/><br/>- 尚未做好 Azure 迁移准备<br/><br/>- 就绪性未知<br/><br/> 如果 VM 未就绪，我们会显示某些修正步骤。
**Azure VM 大小** | 对于已就绪的 VM，我们会建议一个 Azure VM 大小。 | 大小建议取决于评估属性：<br/><br/>- 如果使用了基于性能的大小调整，则在大小调整时，会考虑 VM 的性能历史记录。<br/><br/>- 如果使用了“按本地”，则大小调整取决于本地 VM 大小，不考虑使用率数据。
**建议的工具** | 由于我们的计算机运行代理，Azure Migrate 会查看在计算机中运行的进程，确定该计算机是否为数据库计算机。
**VM 信息** | 报表会显示本地 VM 的设置，包括操作系统、启动类型、磁盘和存储信息。


#### <a name="review-monthly-cost-estimates"></a>查看每月估算的成本

此视图显示在 Azure 中运行 VM 的总计算和存储成本以及每台计算机的详细信息。

![评估就绪](./media/contoso-migration-assessment/azure-costs.png)

- 成本估算是根据计算机的大小建议进行计算的。
- 将会对于组中的所有 VM 合计计算和存储的每月估算费用。


## <a name="clean-up-after-assessment"></a>评估后进行清理

- 评估完成后，Contoso 将保留 Azure 迁移设备以用于将来的测评。
- 将关闭 VM VMware。 在评估其他 VM 时，会重新启动它。
- 公司将在 Azure 中保留 Contoso 迁移项目。  它当前部署在 ContosoFailoverRG 资源组中（位于美国东部 Azure 区域）。
-  收集器 VM 有 180 天的评估许可证。 如果此许可证过期，公司需要重新下载并设置收集器。


## <a name="conclusion"></a>结束语

在此方案中，Contoso 使用 DMA 工具评估其 SmartHotel 应用数据库，并使用 Azure Migrate 服务评估本地 VM。 然后，公司查看评估情况，确保本地资源已做好迁移到 Azure 的准备。

## <a name="next-steps"></a>后续步骤

在本系列的下一篇文章中，Contoso 通过直接迁移在 Azure 中重新托管其 SmartHotel 应用。 Contoso 使用 Azure Site Recovery 迁移应用前端 WEBVM，并使用数据库迁移服务将应用数据库迁移到 Azure SQL 托管实例。 [开始](contoso-migration-rehost-vm-sql-managed-instance.md)此部署。
