---
title: 评估需要使用 DMA 和 Azure Migrate 迁移到 Azure 的本地工作负荷 | Microsoft Docs
description: 了解如何准备 Azure，以便使用数据迁移助手 (DMA) 和 Azure Migrate 服务来迁移本地计算机。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 33e31c47a6125ac363410a9a78e9c9310c74d51e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="scenario-1-assess-on-premises-workloads-for-migration-to-azure"></a>方案 1：评估要迁移到 Azure 的本地工作负荷

在考虑迁移到 Azure 的时候，Contoso 公司希望进行技术和财务方面的评估，以便确定其本地工作负荷是否适合迁移到云。 具体而言，他们想要评估进行迁移时计算机和数据库的兼容性，并估算在 Azure 中运行其资源所需的容量和成本。

为了进行试验并更好地了解所涉及的技术，他们打算评估和迁移一个小型的本地旅游应用。 此应用是一个双层应用，在一个 VM 上运行 Web 应用，在另一个 VM 上运行 SQL Server 数据库。 应用程序部署在 VMware 中，环境由 vCenter Server 托管。 他们将使用数据迁移助手 (DMA) 和 Azure Migrate 服务进行评估。

**技术** | **说明** | **成本**
--- | --- | ---
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | DMA 评估和检测可能会影响 Azure 中数据库功能的兼容性问题。 此外，它还评估 SQL Server 源和目标之间的功能奇偶一致性，并为目标环境提供性能和可靠性方面的改进建议。 | 它是可以免费下载的工具。 
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | 此服务用于评估要迁移到 Azure 的本地计算机。 它评估计算机是否适合迁移，并对在 Azure 中运行时的大小和成本进行估算。 目前，Azure Migrate 服务可以评估要迁移到 Azure 的本地 VMware VM。 | 目前（2018 年 4 月），使用此服务不需付费。
[服务地图](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate 使用服务映射来显示要迁移的计算机之间的依赖关系。 |  服务映射是 Azure Log Analytics 的一部分。 它目前可以免费使用 180 天。 

在此方案中，我们将下载并运行 DMA，以便评估旅游应用的本地 SQL Server 数据库。 我们将使用带有依赖关系映射功能的 Azure Migrate 来评估应用 VM，然后再将其迁移到 Azure。

> [!NOTE]
> 就此方案来说，数据库的评估目标将是“Azure VM 上的 SQL Server”。 但是，在下一方案文章中，我们将运行目标为 Azure SQL 托管实例的迁移。 我们将使用此方法，因为 DMA 目前不支持以 Azure SQL 托管实例 为目标的评估。

## <a name="architecture"></a>体系结构

在此方案中，我们将设置 

 ![迁移评估体系结构](./media/migrate-scenarios-assessment/migration-assessment-architecture.png)

在本方案中：
- Contoso 有一个本地数据中心 (**contoso-datacenter**)，带有一个本地域控制器 ( **contosodc1**)。
- 内部旅游应用跨两个 VM（**WEBVM** 和 **SQLVM**）进行分层，位于 VMware ESXi 主机 **contosohost1.contoso.com** 上。
- VMware 环境由在 VM 上运行的 vCenter Server (**vcenter.contoso.com**) 托管。




## <a name="prerequisites"></a>先决条件

下面是部署此方案所需的项：

- 运行 5.5、6.0 或 6.5 版本的本地 vCenter Server。
- vCenter Server 中的一个只读帐户，或者创建该帐户的权限。 
- 在 vCenter Server 上使用 .OVA 模板创建 VM 的权限。
- 至少一个运行 5.0 或更高版本的 ESXi 主机。
- 至少两个本地 VMware VM，一个 VM 运行一个 SQL Server 数据库。
- 你应该有权在每个 VM 上安装 Azure Migrate 代理。
- 这些 VM 应该有直接的 Internet 连接。
        - 可以仅限对[所需 URL](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites) 进行 Internet 访问。
        - 如果计算机未连接到 Internet，则需在计算机上下载并安装 [OMS 网关](../log-analytics/log-analytics-oms-gateway.md)。
- 运行 SQL Server 实例的 VM 的 FQDN（用于数据库评估）。
- 在 SQL Server VM 上运行的 Windows 防火墙应该允许在 TCP 端口 1433（默认）上进行外部连接，这样 DMA 才能够进行连接。


## <a name="scenario-overview"></a>方案概述

下面是我们将要执行的步骤：


> [!div class="checklist"]
> * **步骤 1：准备 Azure**。 只需一个 Azure 订阅。
> * **步骤 2：下载并安装 DMA**：准备 DMA，以便对本地 SQL Server 数据库进行评估。
> * **步骤 3：评估数据库**：运行和分析数据库评估。
> * **步骤 4：通过 Azure Migrate 进行 VM 评估准备**：设置本地帐户并调整 VMware 设置。
> * **步骤 5：发现本地 VM**：创建 Azure Migrate 收集器 VM。 然后，运行收集器以发现用于评估的 VM。
> * **步骤 6：为依赖关系分析做准备**：在 VM 上安装 Azure Migrate 代理，以便了解在 VM 之间映射的依赖关系。
> * **步骤 7：评估 VM**：检查依赖关系、对 VM 分组，然后运行评估。 在评估就绪以后，请对其进行分析，做好迁移准备。


## <a name="step-1-prepare-azure"></a>步骤 1：准备 Azure

如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

- 如果创建的是免费帐户，则你是自己的订阅的管理员，可以执行所有操作。
- 如果使用的是现有的订阅，而你不是管理员，则需请求管理员为你分配订阅（或用于此方案的资源组）的“所有者”或“参与者”权限。


## <a name="step-2-download-and-install-the-dma"></a>步骤 2：下载并安装 DMA

1. 从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=53595)下载 DMA。
    - 可以在能够连接到 SQL 实例的任何计算机上安装此助手。 不需在 SQL Server 计算机上运行它。
    - 不应在 SQL Server 主机上运行它。
2. 双击已下载的安装程序文件 (DownloadMigrationAssistant.msi) 即可开始安装。
3. 在“完成”页上，确保选中“启动 Microsoft 数据迁移助手”，然后单击“完成”。

## <a name="step-3-run-and-analyze-the-database-assessment"></a>步骤 3：运行和分析数据库评估

针对指定的目标，通过运行评估来分析源 SQL Server 实例。

1. 在“新建”中选择“评估”，然后为评估提供一个项目名称。
2. 在“源服务器类型”中选择“SQL Server”。 在“目标服务器类型”中，选择“Azure 虚拟机上的 SQL Server”。

    ![选择源](./media/migrate-scenarios-assessment/dma-assessment-1.png)

    > [!NOTE]
      目前，DMA 不支持迁移到 SQL 托管实例所需的评估。 为了解决此问题，我们将“Azure VM 上的 SQL Server”用作评估的假定目标。

1.  在“选择目标版本”中，指定要在 Azure 中运行的 SQL Server 的目标版本，以及要在评估中发现的内容：
    - “兼容性问题”会指出那些可能会妨碍迁移的更改，或者需要在迁移之前进行微小调整的更改。 它还会指出目前使用的任何已弃用的功能。 问题按兼容性级别进行组织。 
    - “新功能的建议”会指出目标 SQL Server 平台中可以在迁移后用于数据库的新功能。 这些功能按“性能”、“安全性”和“存储”进行组织。 

    ![选择目标](./media/migrate-scenarios-assessment/dma-assessment-2.png)

2. 在“连接到服务器”中，请指定运行 SQL Server 实例的计算机的名称、身份验证类型以及连接详细信息。 然后单击“连接”。

    ![选择目标](./media/migrate-scenarios-assessment/dma-assessment-3.png)
    
3. 在“添加源”中，选择要评估的数据库，然后单击“添加”。
4. 此时会创建名称已指定的评估。

    ![创建评估](./media/migrate-scenarios-assessment/dma-assessment-4.png)

5.  单击“下一步”，开始评估。
6. 在“查看结果”中，会看到已启用的评估测试的结果。


### <a name="analyze-the-database-assessment"></a>分析数据库评估

结果一旦生成，就会显示在助手中。 

1. 在“兼容性问题”报表中，检查数据库的每个兼容性级别是否存在问题，如果有，如何修正。 兼容性级别会映射到 SQL Server 版本，如下所示：
    - 100：SQL Server 2008/Azure SQL 数据库
    - 110：SQL Server 2012/Azure SQL 数据库
    - 120：SQL Server 2014/Azure SQL 数据库
    - 130：SQL Server 2016/Azure SQL 数据库
    - 140：SQL Server 2017/Azure SQL 数据库

    ![兼容性问题](./media/migrate-scenarios-assessment/dma-assessment-5.png)

2. 在“功能建议”中，查看此评估建议的应该在迁移后配置的性能、安全性和存储功能。 建议的功能多种多样，其中包括：内存中 OLTP 和列存储、Stretch Database、Always Encrypted、动态数据掩码、透明数据加密。

    ![功能建议](./media/migrate-scenarios-assessment/dma-assessment-6.png)

3. 如果修复了任何问题，请单击“重启评估”来重新运行评估。 
4. 单击“导出报表”可以获取 JSON 或 CSV 格式的评估报表。

如果运行更大规模的评估，请执行以下操作：

- 可以并发运行多个评估，然后打开“所有评估”页来查看评估的状态。
- 可以[将评估合并到一个 SQL Server 数据库中](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database)。
- 可以[将评估合并到一个 PowerBI 报表中](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017)。


## <a name="step-4-prepare-for-vm-assessment-with-azure-migrate"></a>步骤 4：使用 Azure Migrate 为 VM 评估做准备

创建可供 Azure Migrate 用来自动发现要评估的 VM 的 VMware 帐户、验证自己是否有权创建 VM、记下必须打开的端口，以及设置统计信息设置级别。

### <a name="set-up-a-vmware-account"></a>设置 VMware 帐户

 需要在 vCenter 中有一个只读帐户。 如果还没有此类帐户，请创建具有以下属性的 VMware 帐户：

- 用户类型：至少为只读用户。
- 权限：数据中心对象 –> 传播到子对象，角色=只读。
- 详细信息：用户在数据中心级别分配，可以访问数据中心的所有对象。
- 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）。

### <a name="verify-permissions-to-create-a-vm"></a>验证是否有权创建 VM

通过导入 .OVA 格式的文件来检查自己是否有权创建 VM。 [了解详细信息](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1)。

### <a name="verify-ports"></a>验证端口

在此方案中，我们将配置依赖关系映射。 此功能要求在进行评估的 VM 上安装一个代理。 该代理需要能够从每个 VM 的 TCP 端口 443 连接到 Azure。 [详细了解](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid)连接要求。


### <a name="set-statistics-settings"></a>设置统计信息设置

开始部署之前，应将 vCenter Server 的统计信息设置设定为级别 3。 请注意：
- 设置该级别以后，需等待至少一天，然后才能运行评估。 否则，可能会出现异常。
- 如果级别高于 3，可以正常评估，但会出现以下情况：
    - 不会收集磁盘和网络的性能数据。
    - 对于存储，Azure Migrate 建议使用 Azure 中的标准磁盘，其大小与本地磁盘一样。
    - 对于网络，只要有一个本地网络适配器，就应在 Azure 中有一个对应的网络适配器。
    - 对于计算，Azure Migrate 会查看 VM 核心数和内存大小，并会建议 Azure VM 使用相同的配置。 如果有多个合格的 Azure VM 大小，建议选择成本最低的那一个。
   
    
[详细了解](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)级别 3 对应的大小。

将级别设置如下：

1. 在 vSphere Web 客户端中，打开 vCenter Server 实例。
2. 选择“管理”选项卡，然后在“设置”下单击“常规”。
3. 单击“编辑”，然后在“统计信息”中将统计信息级别设置为“级别 3”。

    ![vCenter 统计信息级别](./media/migrate-scenarios-assessment/vcenter-statistics-level.png)



## <a name="step-5-discover-vms"></a>步骤 5：发现 VM

创建 Azure Migrate 项目，下载并设置收集器 VM。 然后，运行收集器以发现 VM。

### <a name="create-a-project"></a>创建一个项目

1. 登录到 [Azure 门户](https://portal.azure.com)，然后单击“创建资源”。
2. 搜索“Azure Migrate”。 在搜索结果中选择“Azure Migrate”，然后单击“创建”。
3. 指定项目名称和 Azure 订阅。
4. 创建新的资源组。
5. 指定项目的位置，然后单击“创建”。

    - 只能在“美国中西部”或“美国东部”区域创建一个 Azure Migrate 项目。
    - 可以针对任意目标位置来计划迁移。
    - 项目位置仅用于存储从本地 VM 中收集的元数据。

    ![Azure Migrate](./media/migrate-scenarios-assessment/project-1.png)


    

### <a name="download-the-collector-appliance"></a>下载收集器设备

Azure Migrate 会创建一个称作收集器设备的本地 VM。 此 VM 可发现本地 VMware VM，并将其相关元数据发送到 Azure Migrate 服务。 若要设置收集器设备，请下载一个 .OVA 文件，并将其导入到本地 vCenter 服务器以创建 VM。

1. 在 Azure Migrate 项目中，单击“开始” > “发现和评估” > “发现计算机”。
2. 在“发现计算机”中，单击“下载”以下载 .OVA 文件。
3. 在“复制项目凭据”中，复制项目 ID 和密钥。 在配置收集器时要使用这些信息。

    ![下载 .ova 文件](./media/migrate-scenarios-assessment/download-ova.png) 

### <a name="verify-the-collector-appliance"></a>验证收集器设备

在部署 .OVA 文件之前请检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令以生成 OVA 的哈希：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 生成的哈希应与这些设置匹配（版本 1.0.9.7）。
    
    **算法** | **哈希值**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c
    

### <a name="create-the-collector-appliance"></a>创建收集器设备

将下载的文件导入 vCenter Server。

1. 在 vSphere 客户端控制台中，单击“文件” > “部署 OVF 模板”。

    ![部署 OVF](./media/migrate-scenarios-assessment/vcenter-wizard.png) 

2. 在“部署 OVF 模板向导”>“源”中，指定 .OVA 文件的位置，然后单击“下一步”。
3. 在“OVF 模板详细信息”中，单击“下一步”。 在“最终用户许可协议”中单击“接受”，接受许可协议的条款，然后单击“下一步”。
4. 在“名称和位置”中，为收集器 VM 指定一个友好名称，以及要托管 VM 的库存位置，然后单击“下一步”。 指定要在其上运行收集器应用程序的主机或群集。
5. 在“存储”中指定要为设备存储文件的位置，然后单击“下一步”。
6. 在“磁盘格式”中指定需要以何种方式预配存储。
7. 在“网络映射”中，指定收集器 VM 要连接到的网络。 网络需要与 Internet 建立连接才能向 Azure 发送元数据。 
8. 在“准备完成”中查看设置，选择“部署后打开”，然后单击“完成”。

在创建设备以后，系统会发出一条消息，确认已成功完成。

### <a name="run-the-collector-to-discover-vms"></a>运行收集器以发现 VM。

在开始之前，请注意，此收集器目前仅支持使用“英语(美国)”作为操作系统语言和收集器界面语言。

1. 在 vSphere 客户端控制台中，右键单击“VM”>“打开控制台”。
2. 提供设备的语言、时区和密码首选项。
3. 在桌面上，单击“运行收集器”快捷方式。

    ![收集器快捷方式](./media/migrate-scenarios-assessment/collector-shortcut.png) 
    
4. 在 Azure Migrate 收集器中，打开“设置必备组件”。
    - 接受许可条款，并阅读第三方信息。
    - 收集器会检查 VM 是否可以访问 Internet、时间是否已同步，，以及收集器服务是否正在运行（此服务默认安装在 VM 上）。 它还会检查 VMWare PowerCLI 是否已安装。 
    
    > [!NOTE]
    > 我们假定此 VM 可以直接访问 Internet，不需使用代理。

    ![验证先决条件](./media/migrate-scenarios-assessment/collector-verify-prereqs.png)
    

5. 在“指定 vCenter Server 详细信息”中，执行以下操作：
    - 指定 vCenter 服务器的名称 (FQDN) 或 IP 地址。
    - 在“用户名”和“密码”中，指定收集器用来发现 vCenter Server 上的 VM 的只读帐户凭据。
    - 在“选择范围”中，选择 VM 发现的范围。 收集器只能发现指定范围内的 VM。 可将范围设置为特定文件夹、数据中心或群集。 它不应包含超过 1500 台 VM。 

    ![连接到 vCenter](./media/migrate-scenarios-assessment/collector-connect-vcenter.png)

6. 在“指定迁移项目”中，指定从门户复制的 Azure Migrate 项目 ID 和密钥。 如果未复制这些信息，请从收集器 VM 中打开 Azure 门户。 在项目“概述”页中，单击“发现计算机”，然后复制结果。  

    ![连接到 Azure](./media/migrate-scenarios-assessment/collector-connect-azure.png)

7. 在“查看收集进度”中，监视发现过程，并检查从 VM 中收集的元数据是否在范围内。 收集器提供一个近似的发现时间。

    ![正在进行收集](./media/migrate-scenarios-assessment/collector-collection-process.png)
   


### <a name="verify-vms-in-the-portal"></a>在门户中验证 VM

收集完成以后，请检查 VM 是否显示在门户中。

1. 在 Azure Migrate 项目中，单击“管理” > “计算机”。
2. 检查想要发现的 VM 是否出现在页面中。

    ![已发现的计算机](./media/migrate-scenarios-assessment/discovery-complete.png)

3. 请注意，这些计算机目前尚未安装 Azure Migrate 代理。 需安装这些代理，然后才能查看依赖关系。
    
    ![已发现的计算机](./media/migrate-scenarios-assessment/machines-no-agent.png)



## <a name="step-6-prepare-for-dependency-analysis"></a>步骤 6：为依赖关系分析做准备

若要查看需评估的 VM 之间的依赖关系，请下载代理并将其安装在 Web 应用 VM（WEBVM 和 SQLVM）上。

### <a name="take-a-snapshot"></a>生成快照

如果需要在修改 VM 之前获得其副本，请在安装代理之前生成一个快照。

![计算机快照](./media/migrate-scenarios-assessment/snapshot-vm.png) 


### <a name="download-and-install-the-vm-agents"></a>下载并安装 VM 代理

1.  在 Azure Migrate 项目的“计算机”页面中，选择该计算机，然后在“依赖关系”列中单击“需要安装”。
2.  在每个 VM 的“发现计算机”页中，下载并安装 Microsoft Monitoring Agent (MMA) 和依赖关系代理。
3.  复制工作区 ID 和密钥。 在安装 MMA 时需要这些。

    ![代理下载](./media/migrate-scenarios-assessment/download-agents.png) 



#### <a name="install-the-mma"></a>安装 MMA

1. 双击下载的代理。
2. 在“欢迎”页面上，单击“下一步”。 在“许可条款”页面上，单击“我接受”以接受许可协议。
3. 在“目标文件夹”中，保留默认安装文件夹，然后单击“下一步”。 
4. 在“代理安装选项”中，选择“将代理连接到 Azure Log Analytics” > “下一步”。 

    ![MMA 安装](./media/migrate-scenarios-assessment/mma-install.png) 
5. 在 **Azure Log Analytics** 中，粘贴从门户复制的工作区 ID 和密钥。 单击“资源组名称” 的 Azure 数据工厂。
    ![MMA 安装](./media/migrate-scenarios-assessment/mma-install2.png) 

6. 在“准备安装”中，安装 MMA。



#### <a name="install-the-dependency-agent"></a>安装依赖项代理

1.  双击下载的依赖关系代理。
2.  在“许可条款”页面上，单击“我同意接受许可协议”。
3.  在“正在进行安装”页中，等待安装完成。 然后单击“下一步”。

    ![依赖关系代理](./media/migrate-scenarios-assessment/dependency-agent.png) 


       
## <a name="step-7-run-and-analyze-the-vm-assessment"></a>步骤 7：运行和分析 VM 评估

验证计算机依赖关系并创建一个组。 然后，运行评估。

### <a name="verify-dependencies-and-create-a-group"></a>验证依赖关系并创建一个组。

1.  在“计算机”页面中，对于要分析的 VM，请单击“查看依赖关系”。

    ![查看计算机依赖项](./media/migrate-scenarios-assessment/view-machine-dependencies.png) 

2. SQLVM 的依赖关系映射显示了以下详细信息：

    - 在指定的时段（默认为一小时）内在 SQLVM 上有活动的网络连接运行的进程组/进程
    - 与所有依赖关系计算机的入站（客户端）和出站（服务器）TCP 连接。
    - 安装了 Azure Migrate 代理的依赖关系计算机显示为单独的框
    - 没有安装代理的计算机会显示端口和 IP 地址信息。
    
 3. 对于安装了代理的计算机 (WEBVM)，请单击计算机框以查看更多信息，包括 FQDN、操作系统、MAC 地址。 

    ![查看组依赖项](./media/migrate-scenarios-assessment/sqlvm-dependencies.png)

4. 现在，请选择要添加到组的 VM（SQLVM 和 WEBVM）。  使用 CTRL+单击可选择多个 VM。
5. 单击“创建组”，然后指定名称 (smarthotelapp)。

> [!NOTE]
    > 若要查看更细致的依赖关系，可以展开时间范围。 可以选择一个具体的时段，或者选择开始日期和结束日期。 


### <a name="run-an-assessment"></a>运行评估


1. 在“组”页中，打开组 (smarthotelapp) 
2. 单击“创建评估”。

    ![创建评估](./media/migrate-scenarios-assessment/run-vm-assessment.png)

3. 评估会显示在“管理” > “评估”页中。


### <a name="modify-assessment-settings"></a>修改评估设置

在本教程中，我们使用了默认的评估设置，但你可以自定义设置，如下所示：

1. 在迁移项目“评估”页面上，选择评估，然后单击“编辑属性”。
2. 根据下表修改属性：

    **设置** | **详细信息** | **默认**
    --- | --- | ---
    **目标位置** | 要迁移到的 Azure 位置 | 无默认设置。
    **存储冗余** | Azure VM 要在迁移后使用的存储冗余类型。 | 默认值为[本地冗余存储 (LRS)](../storage/common/storage-redundancy-lrs.md)。 Azure Migrate 仅支持基于托管磁盘的评估，而托管磁盘仅支持 LRS，因此只有 LRS 选项。 
    **“大小调整”条件** | Azure Migrate 用来对 Azure 的 VM 进行大小调整的条件。 可执行基于性能的大小调整或将 VM 的大小设置为本地大小，而不用考虑性能历史记录。 | 默认选项为基于性能的大小调整。
    **性能历史记录** | 评估 VM 性能时要考虑的时长。 此属性仅在“大小调整”条件为基于性能调整大小时才适用。 | 默认时长为一天。
    **百分位使用率** | 进行适当大小调整时要考虑的性能样本集的百分位值。 此属性仅在“大小调整”条件为基于性能调整大小时才适用。  | 默认值为第 95 个百分位。
    **定价层** | 可指定目标 Azure VM 的[定价层（基本/标准）](../virtual-machines/windows/sizes-general.md)。 例如，如果打算迁移生产环境，则可考虑“标准”层，其提供的 VM 延迟较低但成本可能较高。 而如果使用开发-测试环境，则可考虑“基本”层，其 VM 延迟较高，但成本较低。 | 默认使用[标准](../virtual-machines/windows/sizes-general.md)层。
    **舒适因子** | Azure Migrate 在评估期间会考虑到缓冲（舒适因子）。 该缓冲应用到 VM 的机器使用率数据（CPU、内存、磁盘和网络）上。 舒适因子考虑到季节性使用特点、短期性能历史记录，以及未来使用量可能会增加等问题。<br/><br/> 例如，一个使用率为 20% 的 10 核 VM 通常相当于一个 2 核 VM。 但是，如果舒适因子为 2.0x，则结果就变成一个 4 核 VM。 | 默认设置是1.3x。
    **产品** | 你注册到的 [Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 | [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)是默认设置。
    **货币** | 计费货币。 | 默认为美元。
    **折扣 (%)** | 基于 Azure 优惠获得的任何特定订阅的折扣。 | 默认设置是 0%。
    **Azure 混合权益** | 指定你是否具有软件保证以及是否有资格享受 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 若设为“是”，Windows VM 采用非 Windows Azure 价格。 | 默认值为“是”。

3. 单击“保存”，更新评估设置。


### <a name="analyze-the-vm-assessment"></a>分析 VM 评估

Azure Migrate 评估包括以下信息：本地 VM 是否兼容 Azure、建议的适用于 Azure VM 的大小，以及估算的每月 Azure 成本。 

![评估报告](./media/migrate-scenarios-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>查看置信度分级

![评估屏幕](./media/migrate-scenarios-assessment/assessment-display.png)

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

![评估就绪](./media/migrate-scenarios-assessment/azure-readiness.png)  

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

![评估就绪](./media/migrate-scenarios-assessment/azure-costs.png) 

- 成本估算是根据计算机的大小建议进行计算的。
- 将会对于组中的所有 VM 合计计算和存储的每月估算费用。 


## <a name="conclusion"></a>结束语

此方案完成了以下操作：

> [!div class="checklist"]
> * 使用 DMA 工具评估本地数据库。
> * 使用 Azure Migrate 服务通过依赖关系映射评估本地 VM。
> * 查看评估情况，确保本地资源已做好迁移到 Azure 的准备。

## <a name="next-steps"></a>后续步骤

让我们继续下一方案，完成从本地 VM 到 Azure 的直接迁移。



