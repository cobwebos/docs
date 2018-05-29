---
title: 使用 Azure SQL 数据仓库针对租户数据库运行分析查询 | Microsoft Docs
description: 使用从多个“Azure SQL 数据库”数据库提取的数据运行跨租户分析查询。
keywords: sql 数据库教程
services: sql-database
documentationcenter: ''
author: anumjs
manager: craigg
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/08/2017
ms.author: anjangsh
ms.openlocfilehash: dd51480f30187456104a0f9d72ec17082dc1e39e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
ms.locfileid: "33208050"
---
# <a name="explore-saas-analytics-with-azure-sql-database-sql-data-warehouse-data-factory-and-power-bi"></a>探索如何使用 Azure SQL 数据库、SQL 数据仓库、数据工厂和 Power BI 运行 SaaS 分析

本教程逐步讲解一个端到端的分析方案。 该方案演示，对租户数据运行分析如何使软件供应商做出明智的决策。 借助从每个租户数据库提取的数据，可以使用分析来获取租户行为的见解，包括租户如何使用示例 Wingtip Tickets SaaS 应用程序。 此方案涉及三个步骤： 

1.  从每个租户数据库**提取数据**到分析存储（在本例中为 Azure SQL 数据仓库）。
2.  **优化提取的数据**以进行分析处理。
3.  使用**商业智能**工具抽取有用的见解，以引导做出决策。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> - 创建要加载的租户分析存储。
> - 使用 Azure 数据工厂 (ADF) 将每个租户数据库中的数据提取到分析数据仓库。
> - 优化提取的数据（重新组织成星型架构）。
> - 查询分析数据仓库。
> - 使用 Power BI 进行数据可视化，以突出显示租户数据的趋势并提出改进建议。

![architectureOverView](media/saas-tenancy-tenant-analytics/adf_overview.png)

## <a name="analytics-over-extracted-tenant-data"></a>分析提取的租户数据

SaaS 应用程序在云中保存租户数据，这些数据可能非常庞大。 可通过此类数据深入了解应用程序的操作和使用情况，以及租户的行为。 这些见解可引导应用和平台中的功能开发、可用性改进和其他投资。

如果所有数据只是在一个多租户数据库中，则访问所有租户的数据就很简单。 但是，如果数据大量分散在几千个数据库中，则访问就会变得更复杂。 克服复杂性的方法之一是将数据提取到分析数据库或数据仓库进行查询。

本教程演示一个针对 Wingtip Tickets 应用程序的端到端分析方案。 首先，使用 [Azure 数据工厂 (ADF)](../data-factory/introduction.md) 作为业务流程工具，从每个租户数据库中提取门票销量和相关数据。 此数据将载入到分析存储中的临时表。 分析存储可以是 SQL 数据库或 SQL 数据仓库。 本教程使用 [SQL 数据仓库](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is)作为分析存储。

接下来，转换提取的数据，并将其载入到一组[星型架构](https://www.wikipedia.org/wiki/Star_schema)表。 这些表由一个中心事实数据表和相关的维度表组成：

- 星型架构中的中心事实数据表包含门票数据。
- 维度表包含有关会场、事件、客户和购买日期的数据。

将中心事实数据表与维度表相结合，可以实现高效的分析处理。 下图显示了本教程中使用的星型架构：
 
![architectureOverView](media/saas-tenancy-tenant-analytics/starschematables.JPG)

最后，查询星型架构表。 查询结果使用 Power BI 以可视形式显示，其中突出显示了租户的行为及其对应用程序的用法的见解。 使用此星型架构，运行查询来公开以下信息：

- 谁正在买票，从哪个会场买票。
- 门票销量的模式和趋势。
- 每个会场的相对受欢迎度。

本教程提供了基本示例，演示可从 Wingtip Tickets 数据中收集的见解。 例如，如果 Wingtip Tickets 供应商了解每个会场使用该服务的方式，则他们就能思考如何针对或多或少的活跃会场运用不同的服务计划。 

## <a name="setup"></a>设置

### <a name="prerequisites"></a>先决条件

> [!NOTE]
> 本教程使用 Azure 数据工厂中目前以受限预览版提供的功能（链接服务参数化）。 若要完成本教程，请在[此处](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxrVywox1_tHk9wgd5P8SVJUNlFINjNEOElTVFdMUEREMjVVUlJCUDdIRyQlQCN0PWcu)提供订阅 ID。 启用订阅后，我们会发送确认邮件。

若要完成本教程，请确保满足以下先决条件：
- 已部署 Wingtip Tickets SaaS Database Per Tenant 应用程序。 若要在五分钟内进行部署，请参阅[部署并探究 Wingtip SaaS 应用程序](saas-dbpertenant-get-started-deploy.md)。
- 已从 GitHub 下载 Wingtip Tickets SaaS Database Per Tenant 脚本和应用程序[源代码](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/)。 请参阅下载说明。 在提取 zip 文件的内容之前，请务必取消阻止该 zip 文件。
- 已安装 Power BI Desktop。 [下载 Power BI Desktop](https://powerbi.microsoft.com/downloads/)。
- 已预配其他租户批，具体请参阅[**有关预配租户的教程**](saas-dbpertenant-provision-and-catalog.md)。

### <a name="create-data-for-the-demo"></a>创建用于演示的数据

本教程探讨如何分析门票销量数据。 此步骤将为所有租户生成门票数据。 稍后的步骤将提取这些数据进行分析。 确保已按如前所述预配租户批，以便获得足够的数据来公开不同购票模式的范围。

1. 在 PowerShell ISE 中，打开“…\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1”，并设置以下值：
    - **$DemoScenario** = **1** 购买所有会场举行的活动的门票
2. 按 **F5** 运行脚本，并为所有会场创建购票历史记录。 针对 20 个租户，该脚本将生成数万张门票，整个过程可能需要 10 分钟或更长。

### <a name="deploy-sql-data-warehouse-data-factory-and-blob-storage"></a>部署 SQL 数据仓库、数据工厂和 Blob 存储 
在 Wingtip Tickets 应用中，租户的事务数据分散在多个数据库中。 Azure 数据工厂 (ADF) 用于在数据仓库中协调此数据的提取、加载和转换 (ELT)。 为最有效地将数据载入 SQL 数据仓库，ADF 会将数据提取到中间 Blob 文件，然后使用 [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading) 将数据载入数据仓库。   

此步骤将部署本教程中使用的其他资源：名为 _tenantanalytics_ 的 SQL 数据仓库、名为 _dbtodwload-\<user\>_ 的 Azure 数据工厂，以及名为 _wingtipstaging\<user\>_ 的 Azure 存储帐户。 存储帐户用于暂时保存提取的数据文件，然后这些文件将会载入数据仓库。 此步骤还会部署数据仓库架构，并定义用于协调 ELT 过程的 ADF 管道。
1. 在 PowerShell ISE 中，打开“…\Learning Modules\Operational Analytics\Tenant Analytics DW\Demo-TenantAnalyticsDW.ps1”，并设置：
    - **$DemoScenario** = **2** 部署租户分析数据仓库、Blob 存储和数据工厂 
1. 按 **F5** 运行演示脚本并部署 Azure 资源。 

现在，查看部署的 Azure 资源：
#### <a name="tenant-databases-and-analytics-store"></a>租户数据库和分析存储
使用 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 连接到 **tenants1-dpt-&lt;user&gt;** 和 **catalog-dpt-&lt;user&gt;** 服务器。 将 &lt;user&gt; 替换为部署应用时使用的值。 使用登录名 *developer* 和密码 *P@ssword1*。 有关更多指导，请参阅[简介教程](saas-dbpertenant-wingtip-app-overview.md)。

![从 SSMS 连接到 SQL 数据库服务器](media/saas-tenancy-tenant-analytics/ssmsSignIn.JPG)

在对象资源管理器中：

1. 展开 *tenants1-dpt-&lt;user&gt;* 服务器。
1. 展开“数据库”节点，查看租户数据库的列表。
1. 展开 *catalog-dpt-&lt;user&gt;* 服务器。
1. 确认是否能够看到包含以下对象的分析存储：
    1. **raw_Tickets**、**raw_Customers**、**raw_Events** 和 **raw_Venues** 表保存从租户数据库提取的原始数据。
    1. 星型架构表为 **fact_Tickets**、**dim_Customers**、**dim_Venues**、**dim_Events** 和 **dim_Dates**。
    1. 存储过程 **sp_transformExtractedData** 用于转换数据并将其载入星型架构表。

![DWtables](media/saas-tenancy-tenant-analytics/DWtables.JPG)

#### <a name="blob-storage"></a>Blob 存储
1. 在 [Azure 门户](https://ms.portal.azure.com)中，导航到用于部署应用程序的资源组。 确认是否已添加名为 **wingtipstaging\<user\>** 的存储帐户。

  ![DWtables](media/saas-tenancy-tenant-analytics/adf-staging-storage.PNG)

1. 单击 **wingtipstaging\<user\>** 存储帐户浏览现有的对象。
1. 单击“Blob”磁贴
1. 单击容器 **configfile**
1. 确认 **configfile** 是否包含名为 **TableConfig.json** 的 JSON 文件。 此文件包含源和目标表名称、列名称和跟踪器列名称。

#### <a name="azure-data-factory-adf"></a>Azure 数据工厂 (ADF)
在 [Azure 门户](https://ms.portal.azure.com)上的资源组中，确认是否已添加名为 _dbtodwload-\<user\>_ 的 Azure 数据工厂。 

 ![adf_portal](media/saas-tenancy-tenant-analytics/adf-data-factory-portal.png)

本部分探讨创建的数据工厂。 请遵循以下步骤启动数据工厂：
1. 在门户中，单击名为 **dbtodwload-\<user\>** 的数据工厂。
2. 单击“创作和监视”磁贴，在单独的选项卡中启动数据工厂设计器。 

## <a name="extract-load-and-transform-data"></a>提取、加载和转换数据
Azure 数据工厂用于协调数据的提取、加载和转换。 从本教程中，我们将从每个租户数据库的四个不同 SQL 视图提取数据：**rawTickets**、**rawCustomers**、**rawEvents** 和 **rawVenues**。 这些视图包含会场 ID，因此你可以区分数据仓库中每个会场的数据。 数据将载入到数据仓库中的相应临时表：**raw_Tickets**、**raw_customers**、**raw_Events** 和 **raw_Venue**. 然后，某个存储过程将转换原始数据，并填充星型架构表：**fact_Tickets**、**dim_Customers**、**dim_Venues**、**dim_Events** 和 **dim_Dates**。

在上一部分，我们部署并初始化了所需的 Azure 资源，包括数据工厂。 部署的数据工厂包含所需的管道、数据集、链接服务用于提取、加载和转换租户数据。 让我们进一步探讨这些对象，然后触发管道，以将数据从租户数据库移到数据仓库。

### <a name="data-factory-pipeline-overview"></a>数据工厂管道概述
本部分探讨数据工厂中创建的对象。 下图描绘了本教程中使用的 ADF 管道的总体工作流。 如果希望以后再探讨管道，并先查看结果，请跳转到下一部分**触发管道运行**。

![adf_overview](media/saas-tenancy-tenant-analytics/adf-data-factory.PNG)

在概述页中，切换到左侧面板中的“创作”选项卡，并观察是否已创建三个[管道](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities)和三个[数据集](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)。
![adf_author](media/saas-tenancy-tenant-analytics/adf_author_tab.JPG)

三个嵌套的管道为：SQLDBToDW、DBCopy 和 TableCopy。

**管道 1 - SQLDBToDW** 查找目录数据库中存储的租户数据库的名称（表名称：[__ShardManagement].[ShardsGlobal]），查找每个租户数据库，并执行 **DBCopy** 管道。 完成后，将执行提供的 **sp_TransformExtractedData** 存储过程架构。 此存储过程转换临时表中加载的数据，并填充星型架构表。

**管道 2 - DBCopy** 查找 Blob 存储中存储的配置文件中的源表和列的名称。  然后，针对以下四个表中的每一个运行 **TableCopy** 管道：TicketFacts、CustomerFacts、EventFacts 和 VenueFacts。 对所有 20 个数据库并行执行 **[Foreach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity)** 活动。 ADF 允许并行运行最多 20 个循环迭代。 请考虑为其他数据库创建多个管道。    

**管道 3 - TableCopy** 使用 SQL 数据库中的行版本号 (_rowversion_) 来识别已更改或更新的行。 此活动将会查找用于从源表提取行的起始和结束行版本。 每个租户数据库中存储的 **CopyTracker** 表跟踪在每次运行时从每个源表提取的最后一行。 新的或已更改的行将复制到数据仓库中的相应临时表：**raw_Tickets**、**raw_Customers**、**raw_Venues** 和 **raw_Events**。 最后，将在 **CopyTracker** 表中保存最后一个行版本，用作下次提取操作的初始行版本。 

还有三个参数化的链接服务将数据工厂链接到源 SQL 数据库、目标 SQL 数据仓库和中间 Blob 存储。 在“创作”选项卡中，单击“连接”浏览这些链接服务，如下图所示：

![adf_linkedservices](media/saas-tenancy-tenant-analytics/linkedservices.JPG)

对应于三个链接服务，有三个数据集引用了在管道活动中用作输入或输出的数据。 请浏览每个数据集，以观察所用的连接和参数。 _AzureBlob_ 指向包含源表和目标表，以及每个源中的跟踪器列的配置文件。
  
### <a name="data-warehouse-pattern-overview"></a>数据仓库模式概述
SQL 数据仓库用作分析存储，对租户数据执行聚合。 本示例中使用 PolyBase 将数据载入 SQL 数据仓库。 原始数据载入临时表，这些表中包含一个标识列，用于跟踪已转换为星型架构表的行。 下图显示了加载模式：![loadingpattern](media/saas-tenancy-tenant-analytics/loadingpattern.JPG)

本示例使用了渐变维度 (SCD) 类型 1 维度表。 每个维度具有一个使用标识列定义的代理键。 为了节省时间，日期维度表已预先填充，这也是一种最佳做法。 对于其他维度表，已使用 CREATE TABLE AS SELECT...(CTAS) 语句创建一个临时表，其中包含现有的已修改和未修改行，以及代理键。 这是使用 IDENTITY_INSERT=ON 实现的。 然后，使用 IDENTITY_INSERT=OFF 将新行插入表中。 为了方便回滚，现有维度表已重命名，并且临时表也已重命名，成为新的维度表。 在每次运行之前，会删除旧维度表。

维度表在事实数据表之前加载。 这种顺序可确保每次事实数据抵达之前，所有引用的维度已存在。 加载事实数据后，将会匹配每个对应维度的业务键，并将对应的代理键添加到每个事实。

转换操作的最后一个步骤删除随时可用于下次管道执行的临时数据。
   
### <a name="trigger-the-pipeline-run"></a>触发管道运行
遵循以下步骤，针对所有租户数据库运行完整的提取、加载和转换管道：
1. 在 ADF 用户界面的“创作”选项卡上，从左窗格中选择“SQLDBToDW”管道。
1. 单击“触发器”，并在下拉菜单中单击“立即触发”。 此操作会立即运行管道。 在生产场景中，请定义一个时间表用于运行管道，以按计划刷新数据。
  ![adf_trigger](media/saas-tenancy-tenant-analytics/adf_trigger.JPG)
1. 在“管道运行”页上单击“完成”。
 
### <a name="monitor-the-pipeline-run"></a>监视管道运行
1. 在 ADF 用户界面中，通过左侧菜单切换到“监视”选项卡。
1. 不断地单击“刷新”，直到 SQLDBToDW 管道的状态显示为“成功”。
  ![adf_monitoring](media/saas-tenancy-tenant-analytics/adf_monitoring.JPG)
1. 使用 SSMS 连接到数据仓库并查询星型架构表，验证数据是否已载入这些表中。

完成管道后，事实数据表会保存所有会场的门票销量数据，并且维度表中会填充相应的会场、活动和客户。

## <a name="data-exploration"></a>数据浏览

### <a name="visualize-tenant-data"></a>可视化租户数据

星型架构中的数据提供分析所需的所有门票销售数据。 以图形方式可视化数据能够更方便地查看大型数据集的趋势。 在本部分，我们将使用 **Power BI** 来处理和可视化数据仓库中的租户数据。

使用以下步骤连接到 Power BI，并导入前面创建的视图：

1. 启动 Power BI Desktop。
2. 在“开始”功能区上的菜单中，依次选择“获取数据”、“更多...” 从菜单中。
3. 在“获取数据”窗口中，选择“Azure SQL 数据库”。
4. 在数据库登录窗口中，输入服务器名称 (**catalog-dpt-&lt;User&gt;.database.windows.net**)。 为“数据连接模式”选择“导入”，单击“确定”。 

    ![sign-in-to-power-bi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. 在左窗格中选择“数据库”，输入 *developer* 作为用户名，输入 *P@ssword1* 作为密码。 单击“连接”。  

    ![database-sign-in](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. 在“导航器”窗格中的分析数据库下，选择以下星型架构表：**fact_Tickets**、**dim_Events**、**dim_Venues**、**dim_Customers** 和 **dim_Dates**。 然后选择“加载”。 

祝贺你！ 数据已成功载入 Power BI。 现在，请浏览有趣的可视化效果，以深入了解租户。 本教程将逐步讲解分析功能如何向 Wingtip Tickets 业务团队提供一些数据驱动的建议。 借助建议可以优化业务模型和客户体验。

首先，请分析门票销售数据，查看不同会场的服务使用差异。 在 Power BI 中选择下图所示的选项，绘制每个会场售出的门票总数的条形图。 （由于门票生成器中存在随机变化，你的结果可能与图中不同。）
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues-DW.PNG)

上面的绘图确认，每个会场售出的门票数有差异。 门票销量较大的会场对服务的使用程度比销量较小的会场要高。 此处也许可以根据不同的租户需求定制资源分配。

可以进一步分析数据，确定门票销量在各时间的变化。 在 Power BI 中选择下图所示的选项，绘制 60 天内每天售出的门票总数。
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate-DW.PNG)

上面的图表显示某些会场的门票销售高峰期。 这些峰值强化了这样一种印象：某些会场消耗的系统资源可能不成比例。 到目前为止，何时出现高峰并没有明显的模式。

接下来，让我们进一步调查这些销售高峰日期的基数。 开放售票后，这些高峰发生在什么时间？ 若要绘制每日售票量的图表，请在 Power BI 中选择下图所示的选项。

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay-DW.PNG)

此绘图显示，某些会场在售票的第一天就售出了大量的门票。 这些会场开放售票后，售票量似乎在短时间内出现剧增。 少数会场存在的这种活动喷发可能会影响其他会场的服务。

可以再次深化到数据，确定这些会场主办的所有活动是否都存在这种售票剧增。 在前面的绘图中可以看到，Contoso Concert Hall 售出了大量门票，Contoso 在某些日期也出现了售票高峰。 请体验不同的 Power BI 选项，绘制 Contoso Concert Hall 的累积门票销量，并专注于其每场活动的销量趁势。 是否所有活动都遵循相同的销售模式？ 请尝试生成如下所示的绘图。

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

此绘图显示 Contoso Concert Hall 举办的每场活动在不同时间的累积门票销量，从中可以看出，并非所有活动都出现了销量剧增的情况。 请体验不同的筛选选项，以浏览其他会场的销量趁势。

门票销售模式的见解可以引导 Wingtip Tickets 优化其业务模式。 Wingtip 也许可以不向所有租户收取相同的费用，而是推出具有不同性能级别的服务层。 可以根据更高的服务级别协议 (SLA)，向每日售出较多门票的大型会场提供更高的层。 这些会场可将数据库放在具有更高的数据库资源限制的池中。 每个服务层可以采用按小时售量分配，超出分配即会收取额外的费用。 定期出现销量喷发的大型会场将会受益于更高的层，而 Wingtip Tickets 可以更高效地将服务变现。

同时，某些 Wingtip Tickets 客户抱怨他们正在努力售出足够多的票证，以抵消服务费用。 通过这些见解，绩效不佳的会场也许能够找到促升门票销量的机会。 销量提高会增大服务的认知价值。 右键单击“fact_Tickets”并选择“新建度量值”。 针对名为 **AverageTicketsSold** 的新度量值输入以下表达式：

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

选择以下可视化选项绘制每个会场售出的门票百分比，以确定其相对成功度。

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues-DW.PNG)

上面的绘图显示，尽管大多数会场售出了 80% 以上的门票，但还有一些会场正在挣扎，力求将空座率降到一半以下。 尝试输入不同的合理值，以选择每个会场的最大或最小售出门票百分比。

## <a name="embedding-analytics-in-your-apps"></a>在应用中嵌入分析 
本教程重点介绍了跨租户的分析，旨在改善软件供应商对其租户的了解。 分析功能还能为租户提供见解，帮助他们更有效地自行管理业务。 

在 Wingtip Tickets 示例中，我们已提前发现，门票销量往往遵循可预测的模式。 这项见解可能有助于业绩不佳的会场大幅促升其门票销量。 也许他们可以利用机器学习技术来预测每场活动的门票销量。 还可为价格变化造成的影响建模，以便能够预测优惠折扣的影响。 可将 Power BI Embedded 集成到事件管理应用程序，以可视化预测的销量数据，包括在销量不佳的活动中打折对门票总销量和收入造成的影响。 使用 Power BI Embedded，甚至可以直接在可视化体验中对门票价格真正实施折扣。


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 部署填充了星型架构的 SQL 数据仓库，以进行租户分析。
> * 使用 Azure 数据工厂将每个租户数据库中的数据提取到分析数据仓库。
> * 优化提取的数据（重新组织成星型架构）。
> * 查询分析数据仓库。 
> * 使用 Power BI 可视化所有租户中的数据趋势。

祝贺你！

## <a name="additional-resources"></a>其他资源

- 其他[基于 Wingtip SaaS 应用程序编写的教程](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)。
