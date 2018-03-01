---
title: "使用提取的数据运行跨租户分析 | Microsoft Docs"
description: "使用从多个“Azure SQL 数据库”数据库提取的数据运行跨租户分析查询。"
keywords: "sql 数据库教程"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 11/08/2017
ms.author: anjangsh; billgib; genemi
ms.openlocfilehash: 62f09a7ff353783b0f54202554d126bf59ee941a
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="cross-tenant-analytics-using-extracted-data"></a>使用提取的数据运行跨租户分析

本教程逐步讲解一个完整的分析方案。 该方案演示企业如何通过分析做出明智的决策。 基于从每个租户数据库提取的数据，通过诊断深入了解租户行为和应用程序使用情况。 此方案涉及三个步骤： 

1.  从每个租户数据库提取数据，然后加载到分析存储中。
2.  转换提取后的数据进行分析处理。
3.  使用“商业智能”工具抽取出有用的见解，以引导作出决策。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> - 创建要将数据提取到的租户分析存储。
> - 使用弹性作业将每个租户数据库中的数据提取到分析存储。
> - 优化提取的数据（重新组织成星型架构）。
> - 查询分析数据库。
> - 使用 Power BI 进行数据可视化，以突出显示租户数据的趋势并提出改进建议。

![architectureOverView](media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>脱机租户分析模式

多租户 SaaS 应用程序通常在云端存储有大量租户数据。 可通过此类数据深入了解应用程序的操作和使用情况，以及租户的行为。 这些见解可引导应用和平台中的功能开发、可用性改进和其他投资。

如果所有数据均位于一个多租户数据库中，则可轻松访问所有租户的数据。 但是，如果数据大量分散在几千个数据库中，访问就变得更加复杂。 为降低复杂度和尽量减少分析查询对事务数据的影响，一种方式是将数据提取到专门的分析数据库或数据仓库。

本教程将展示一个针对 Wingtip Tickets SaaS 应用程序的完整分析情景。 首先，使用弹性作业从租户数据库中提取数据，再将其加载到分析存储的临时表中。 分析存储可以是 SQL 数据库或 SQL 数据仓库。 对于大规模数据提取，建议使用 [Azure 数据仓库](../data-factory/introduction.md)。

接下来，将聚合的数据转换为一组[星型架构](https://www.wikipedia.org/wiki/Star_schema)表。 这些表由一个中心事实数据表和相关的维度表组成。  对于 Wingtip Tickets：

- 星型架构中的中心事实数据表包含门票数据。
- 维度表中显示场所、活动、客户和购买日期的相关数据。

将中心事实数据表与维度表相结合，可以实现高效的分析处理。 下图显示了本教程中使用的星型架构：
 
![architectureOverView](media/saas-tenancy-tenant-analytics/StarSchema.png)

最后，使用 PowerBI 查询分析存储，以突出显示租户行为及其 Wingtip Tickets 应用程序使用情况的相关信息。 运行查询以：
 
- 显示每个场所的相对受欢迎程度
- 突出显示不同活动的售票模式
- 显示售票后不同场所的相对活动成功情况

了解每位租户使用服务的方式，进而探索如何从服务中获利以及如何改进服务来让租户更加成功。 本教程提供一些基本示例，介绍可从租户数据中收集的见解类型。

## <a name="setup"></a>设置

### <a name="prerequisites"></a>先决条件

若要完成本教程，请确保满足以下先决条件：

- 已部署 Wingtip Tickets SaaS Database Per Tenant 应用程序。 若要在五分钟内进行部署，请参阅[部署和浏览 Wingtip SaaS 应用程序](saas-dbpertenant-get-started-deploy.md)
- 已从 GitHub 下载 Wingtip Tickets SaaS Database Per Tenant 脚本和应用程序[源代码](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/)。 请参阅下载说明。 在提取 zip 文件的内容之前，请务必取消阻止该 zip 文件。 有关下载和取消阻止 Wingtip Tickets SaaS 脚本的步骤，请参阅[常规指南](saas-tenancy-wingtip-app-guidance-tips.md)。
- 已安装 Power BI Desktop。 [下载 Power BI Desktop](https://powerbi.microsoft.com/downloads/)
- 已预配其他租户批，具体请参阅[**有关预配租户的教程**](saas-dbpertenant-provision-and-catalog.md)。
- 已创建作业帐户和作业帐户数据库。 请参阅[**架构管理教程**](saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent)中的相应步骤。

### <a name="create-data-for-the-demo"></a>创建用于演示的数据

在本教程中，将门票销售数据执行分析。 在当前步骤中，请为所有租户生成门票数据。  稍后将提取这些数据进行分析。 确保已按如前所述预配租户批，以便获得有意义的数据量。 数量够大的数据能够揭示不同购票模式的范围。

1. 在 PowerShell ISE 中打开 *…\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 并设置以下值：
    - **$DemoScenario** = **1** 购买所有会场举行的活动的门票
2. 按 **F5** 运行脚本，并创建每个会场举行的每个活动的购票历史记录。  该脚本会运行几分钟时间，以生成数万张门票。

### <a name="deploy-the-analytics-store"></a>部署分析存储
通常，有大量的事务数据库共同保存所有租户数据。 必须将众多事务数据库中的租户数据聚合到一个分析存储中。 使用聚合能够有效地查询数据。 本教程使用“Azure SQL 数据库”数据库来存储聚合的数据。

在以下步骤中部署名为 **tenantanalytics** 的分析存储。 此外，还要部署稍后将在本教程中填充的预定义表：
1. 在 PowerShell ISE 中打开 *…\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. 设置脚本中的 $DemoScenario 变量，使其与所选的分析存储匹配：
    - 若要使用不包含列存储的 SQL 数据库，请设置 **$DemoScenario** = **2**
    - 若要使用包含列存储的 SQL 数据库，请设置 **$DemoScenario** = **3**  
3. 按 **F5** 运行演示脚本（用于调用 *Deploy-TenantAnalytics<XX>.ps1* 脚本），以创建租户分析存储。 

部署应用程序并在其中填充所需的租户数据后，请使用 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 并指定登录名 *developer* 和密码 *P@ssword1*，来连接 **tenants1-dpt-&lt;User&gt;** 和 **catalog-dpt-&lt;User&gt;** 服务器。 有关更多指导，请参阅[简介教程](saas-dbpertenant-wingtip-app-overview.md)。

![architectureOverView](media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

在对象资源管理器中执行以下步骤：

1. 展开 *tenants1-dpt-&lt;User&gt;* 服务器。
2. 展开“数据库”节点，查看租户数据库的列表。
3. 展开 *catalog-dpt-&lt;User&gt;* 服务器。
4. 确认是否能够看到该分析存储和 jobaccount 数据库。

在 SSMS 对象资源管理器中展开分析存储节点，查看以下数据库项：

- 表 **TicketsRawData** 和 **EventsRawData** 保存从租户数据库提取的原始数据。
- 星型架构表为 **fact_Tickets**、**dim_Customers**、**dim_Venues**、**dim_Events** 和 **dim_Dates**。
- 存储过程用于在星型架构表中填充来自原始数据表的数据。

![architectureOverView](media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>数据提取 

### <a name="create-target-groups"></a>创建目标组 

在继续下一步之前，请确保已部署作业帐户和作业帐户数据库。 在接下来的一系列步骤中，将使用弹性作业从每个租户数据库提取数据，并将数据存储在分析存储中。 然后，第二个作业将数据分片，并将其存储到星型架构的表中。 这两个作业针对两个不同的目标组（即 **TenantGroup** 和 **AnalyticsGroup**）运行。 提取作业针对包含所有租户数据库的 TenantGroup 运行。 分片作业针对只包含分析存储的 AnalyticsGroup 运行。 使用以下步骤创建目标组：

1. 在 SSMS 中，连接到 catalog-dpt-&lt;User&gt; 中的 **jobaccount** 数据库。
2. 在 SSMS 中，打开 *…\Learning Modules\Operational Analytics\Tenant Analytics\ TargetGroups.sql* 
3. 修改脚本顶部的 @User 变量，将 <User> 替换为部署 Wingtip SaaS 应用时使用的用户值。
4. 按 **F5** 运行脚本，以创建两个目标组。

### <a name="extract-raw-data-from-all-tenants"></a>从所有租户提取原始数据

相比活动和会场数据而言，门票和客户数据发生大规模数据修改的频率可能更大。 因此，请考虑以高于活动和会场数据的提取频率单独提取门票和客户数据。 在本部分，定义并计划两个单独的作业：

- 提取门票和客户数据。
- 提取活动和会场数据。

每个作业提取自身的目标数据，并将其发布到分析存储。 有一个单独的作业会将提取的数据分片到分析星型架构。

1. 在 SSMS 中，连接到 catalog-dpt-&lt;User&gt; 服务器中的 **jobaccount** 数据库。
2. 在 SSMS 中，打开 *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*。
3. 修改脚本顶部的 @User，将 <User> 替换为部署 Wingtip SaaS 应用时使用的用户名 
4. 按 F5 运行脚本，以创建并运行从每个租户数据库提取门票和客户数据的作业。 该作业会将数据保存到分析存储中。
5. 查询 tenantanalytics 数据库中的 TicketsRawData 表，确保该表中已填充来自所有租户的门票信息。

![ticketExtracts](media/saas-tenancy-tenant-analytics/ticketExtracts.png)

重复上述步骤，但这一次请将 **\ExtractTickets.sql** 替换为步骤 2 中的 **\ExtractVenuesEvents.sql**。

成功运行该作业后，分析存储中的 EventsRawData 表中将会填充来自所有租户的新活动和会场信息。 

## <a name="data-reorganization"></a>数据重新组织

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>将提取的数据分片以填充星型架构表

下一步是将提取的原始数据分片成一组已针对分析查询进行优化的表。 将使用星型架构。 中心事实数据表保存各项门票销售记录。 其他表中填充了有关会场、活动和客户的数据。 另外还有一些时间维度表。 

在本教程部分，定义并运行一个用于将提取的原始数据与星型架构表中的数据合并的作业。 合并作业完成后，将删除原始数据，以便下一个租户数据提取作业可以填充表。

1. 在 SSMS 中，连接到 catalog-dpt-&lt;User&gt; 中的 **jobaccount** 数据库。
2. 在 SSMS 中，打开 *…\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*。
3. 按 **F5** 运行脚本，以定义一个调用分析存储中 sp_ShredRawExtractedData 存储过程的作业。
4. 请耐心等待，让作业成功运行。
    - 在 jobs.jobs_execution 表的 **Lifecycle** 列中检查作业状态。 确保作业的状态为 **Succeeded**，然后继续。 如果运行成功，将显示类似于以下图表所示的数据：

![分片](media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>数据浏览

### <a name="visualize-tenant-data"></a>可视化租户数据

星型架构表中的数据提供分析所需的所有门票销售数据。 若要更方便地查看大型数据集的趋势，需要以图形方式将数据可视化。  本部分介绍如何使用 **Power BI** 来处理和可视化已提取并已组织的租户数据。

使用以下步骤连接到 Power BI，并导入前面创建的视图：

1. 启动 Power BI Desktop。
2. 在“开始”功能区上的菜单中，依次选择“获取数据”、“更多...” 从菜单中。
3. 在“获取数据”窗口中，选择“Azure SQL 数据库”。
4. 在数据库登录窗口中，输入服务器名称 (catalog-dpt-&lt;User&gt;.database.windows.net)。 为“数据连接模式”选择“导入”，单击“确定”。 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. 在左窗格中选择“数据库”，输入 *developer* 作为用户名，输入 *P@ssword1* 作为密码。 单击“连接”。  

    ![databasesignin](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. 在“导航器”窗格中的分析数据库下，选择以下星型架构表：fact_Tickets、dim_Events、dim_Venues、dim_Customers 和 dim_Dates。 然后选择“加载”。 

祝贺你！ 数据已成功载入 Power BI。 现在，可以开始探索有趣的可视化效果，以帮助自己深入了解租户。 本教程接下来逐步讲解如何使用分析向 Wingtip Tickets 业务团队提供数据驱动的建议。 借助建议可以优化业务模型和客户体验。

首先，请分析门票销售数据，查看不同会场的服务使用差异。 在 Power BI 中选择以下选项，绘制每个会场售出的门票总数的条形图。 由于门票生成器中存在随机变化，你的结果可能与图中不同。
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

上面的绘图确认，每个会场售出的门票数有差异。 门票销量较大的会场对服务的使用程度比销量较小的会场要高。 此处也许可以根据不同的租户需求定制资源分配。

可以进一步分析数据，确定门票销量在各时间的变化。 在 Power BI 中选择以下选项，绘制 60 天内每天售出的门票总数。
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

上面的图表显示某些会场的门票销售高峰期。 这些峰值强化了这样一种印象：某些会场消耗的系统资源可能不成比例。 到目前为止，何时出现高峰并没有明显的模式。

接下来，需要进一步调查这些销售高峰日期的基数。 开放售票后，这些高峰发生在什么时间？ 若要绘制每日售票量的图表，请在 Power BI 中选择以下选项。

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

上面的绘图显示，某些会场在售票的第一天就售出了大量的门票。 这些会场开放售票后，售票量似乎在短时间内出现剧增。 少数会场存在的这种活动喷发可能会影响其他会场的服务。

可以再次深化到数据，确定这些会场主办的所有活动是否都存在这种售票剧增。 在前面的绘图中可以看到，Contoso Concert Hall 售出了大量门票，Contoso 在某些日期也出现了售票高峰。 请体验不同的 Power BI 选项，绘制 Contoso Concert Hall 的累积门票销量，并专注于其每场活动的销量趁势。 是否所有活动都遵循相同的销售模式？

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

上面的 Contoso Concert Hall 绘图显示，并非所有活动都出现销量剧增。 请体验不同的筛选选项，以查看其他会场的售量趁势。

门票销售模式的见解可以引导 Wingtip Tickets 优化其业务模式。 Wingtip 也许可以不向所有租户收取相同的费用，而是推出具有不同性能级别的服务层。 可以根据更高的服务级别协议 (SLA)，向每日售出较多门票的大型会场提供更高的层。 这些会场可将数据库放在具有更高的数据库资源限制的池中。 每个服务层可以采用按小时售量分配，超出分配即会收取额外的费用。 定期出现销量喷发的大型会场将会受益于更高的层，而 Wingtip Tickets 可以更高效地将服务变现。

同时，某些 Wingtip Tickets 客户抱怨他们正在努力售出足够多的票证，以抵消服务费用。 通过这些见解，绩效不佳的会场也许能够找到促升门票销量的机会。 销量提高会增大服务的认知价值。 右键单击“fact_Tickets”并选择“新建度量值”。 针对名为 **AverageTicketsSold** 的新度量值输入以下表达式：

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

选择以下可视化选项绘制每个会场售出的门票百分比，以确定其相对成功度。

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

上面的绘图显示，尽管大多数会场售出了 80% 以上的门票，但还有一些会场正在挣扎，力求将空座率降到一半以下。 尝试输入不同的合理值，以选择每个会场的最大或最小售出门票百分比。

经过前面的深入分析，我们发现门票销量往往遵循可预测的模式。 这项发现可让 Wingtip Tickets 帮助绩效不佳的会场通过推荐动态价格来促升门票销量。 此外，还可以让会场找到利用机器学习技术预测每项活动的门票销量的机会。 此外，可以预测优惠折扣产生的收入对门票销量造成的影响。 可将 Power BI Embedded 集成到某个事件管理应用程序中。 这种集成有助于可视化预测销量，以及不同折扣的效果。 该应用程序有助于设计可直接从分析界面应用的最佳折扣。

我们已从 WingTip 应用程序中观察到租户数据的趋势。 可以构思应用向 SaaS 应用程序供应商提议业务决策的其他方法。 这样，供应商便可以更好地迎合其租户的需求。 希望本教程已让你了解如何使用所需的工具针对租户数据执行分析，使公司能够更自信地做出数据驱动的决策。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> - 部署包含预定义星型架构表的租户分析数据库
> - 使用弹性作业从所有租户数据库提取数据
> - 将提取的数据合并到用于分析的星型架构表
> - 查询分析数据库 
> - 使用 Power BI 进行数据可视化，以观察租户数据的趋势 

祝贺你！

## <a name="additional-resources"></a>其他资源

- 其他[基于 Wingtip SaaS 应用程序编写的教程](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)。
- [弹性作业](sql-database-elastic-jobs-overview.md)。
