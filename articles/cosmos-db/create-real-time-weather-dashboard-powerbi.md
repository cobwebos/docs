---
title: 使用 Azure Cosmos DB、Azure Analysis Services 和 Power BI 创建实时仪表板
description: 了解如何使用 Azure Cosmos DB 和 Azure Analysis Services 在 Power BI 中创建实时天气仪表板。
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 681929928e6e6b28c7950c8aeeadc8b181491f46
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804123"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>使用 Azure Cosmos DB 和 Power BI 创建实时仪表板

本文介绍使用 Azure Cosmos DB 和 Azure Analysis Services 在 Power BI 中创建实时天气仪表板所需执行的步骤。 Power BI 仪表板将会显示图表，以便显示有关区域中的气温和降水量的实时信息。

## <a name="reporting-scenarios"></a>报告方案

可通过多种方式基于 Azure Cosmos DB 中存储的数据设置报告仪表板。 下表根据数据的过时要求和大小描述了每种方案的报告设置：


|方案 |设置 |
|---------|---------|
|1.生成即席报表（不刷新）    |  [采用导入模式的 Power BI Azure Cosmos DB 连接器](powerbi-visualize.md)       |
|2.生成定期刷新的即席报表   |  [采用导入模式的 Power BI Azure Cosmos DB 连接器（按计划定期刷新）](powerbi-visualize.md)       |
|3.针对大型数据集 (< 10 GB) 进行报告     |  支持增量刷新的 Power BI Azure Cosmos DB 连接器       |
|4.针对大型数据集实时报告    |  支持直接查询的 Power BI Azure Analysis Services 连接器 + Azure Analysis Services（Azure Cosmos DB 连接器）       |
|5.针对包含聚合值的实时数据进行报告     |  [支持直接查询的 Power BI Spark 连接器 + Azure Databricks + Cosmos DB Spark 连接器。](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6.针对大型数据集中包含聚合值的实时数据进行报告   |  支持直接查询的 Power BI Azure Analysis Services 连接器 + Azure Analysis Services + Azure Databricks + Cosmos DB Spark 连接器。       |

可以使用 Azure Cosmos DB Power BI 连接器轻松设置方案 1 和 2。 本文介绍方案 3 和 4 的设置。

### <a name="power-bi-with-incremental-refresh"></a>支持增量刷新的 Power BI

Power BI 提供一种可以配置增量刷新的模式。 采用此模式便无需创建和管理 Azure Analysis Services 分区了。 可将增量刷新设置为仅筛选大型数据集中的最新更新。 但是，此模式仅适用于数据集大小上限为 10 GB 的 Power BI Premium 服务。

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis 连接器 + Azure Analysis Services

Azure Analysis Services 提供一个完全托管的平台即服务，用于在云中承载企业级数据模型。 可将巨量数据集从 Azure Cosmos DB 载入 Azure Analysis Services。 为了避免总要查询整个数据集，可将数据集细分到 Azure Analysis Services 分区中，而这些分区可按照不同的频率独立刷新。

## <a name="power-bi-incremental-refresh"></a>Power BI 增量刷新

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>将天气数据引入 Azure Cosmos DB

设置一个引入管道，以将[天气数据](https://catalog.data.gov/dataset/local-weather-archive)加载到 Azure Cosmos DB 中。 可以设置一个 [Azure 数据工厂 (ADF)](../data-factory/connector-azure-cosmos-db.md) 作业，以使用 HTTP 源和 Cosmos DB 接收器定期将最新的天气数据载入 Azure Cosmos DB。


### <a name="connect-power-bi-to-azure-cosmos-db"></a>将 Power BI 连接到 Azure Cosmos DB

1. **将 Azure Cosmos 帐户连接到 Power BI** - 打开 Power BI Desktop，使用 Azure Cosmos DB 连接器选择适当的数据库和容器。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png" alt-text="Azure Cosmos DB Power BI 连接器":::

1. **配置增量刷新** - 遵循[使用 Power BI 进行增量刷新](/power-bi/service-premium-incremental-refresh)一文中的步骤，为数据集配置增量刷新。 按以下屏幕截图中所示添加 **RangeStart** 和 **RangeEnd** 参数：

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png" alt-text="Azure Cosmos DB Power BI 连接器" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   根据源数据集中存在的列和数据类型，可以相应地更改 RangeStart 和 RangeEnd 字段

   
   |属性  |数据类型  |筛选器  |
   |---------|---------|---------|
   |_ts     |   Numeric      |  [_ts] > Duration.TotalSeconds(RangeStart - #datetime(1970, 1, 1, 0, 0, 0)) and [_ts] < Duration.TotalSeconds(RangeEnd - #datetime(1970, 1, 1, 0, 0, 0)))       |
   |Date（例如：- 2019-08-19）     |   String      | [Document.date]> DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd")        |
   |Date（例如：- 2019-08-11 12:00:00）   |  String       |  [Document.date]> DateTime.ToText(RangeStart," yyyy-mm-dd HH:mm:ss") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-mm-dd HH:mm:ss")       |


1. **定义刷新策略** - 导航到表的**上下文**菜单中的“增量刷新”选项卡来定义刷新策略。  将刷新策略设置为**每日**刷新并存储上个月的数据。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png" alt-text="Azure Cosmos DB Power BI 连接器":::

   忽略警告“无法确认是否要折叠 M 查询”。  Azure Cosmos DB 连接器将折叠筛选器查询。

1. **加载数据并生成报表** - 使用前面加载的数据，创建用于报告气温和降水量的图表。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="Azure Cosmos DB Power BI 连接器":::

1. **将报表发布到 Power BI Premium** - 由于增量刷新是仅在 Premium 版本中提供的功能，发布对话框仅允许选择 Premium 容量中的工作区。 由于需要导入历史数据，首次刷新所需的时间可能较长。 后续的数据刷新速度要快得多，因为使用了增量刷新。


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis 连接器 + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>将天气数据引入 Azure Cosmos DB 

设置一个引入管道，以将[天气数据](https://catalog.data.gov/dataset/local-weather-archive)加载到 Azure Cosmos DB 中。 可以设置一个 Azure 数据工厂 (ADF) 作业，以使用 HTTP 源和 Cosmos DB 接收器定期将最新的天气数据载入 Azure Cosmos DB。

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>将 Azure Analysis Services 连接到 Azure Cosmos 帐户

1. **创建新的 Azure Analysis Services 群集**  -  在 Azure Cosmos 帐户和 Databricks 群集所在的同一区域中[创建 Azure Analysis Services 的实例](../analysis-services/analysis-services-create-server.md)。

1. **在 Visual Studio 中创建新的 Analysis Services 表格项目**  -   [安装 SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017&preserve-view=true)，并在 Visual Studio 中创建一个 Analysis Services 表格项目。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png" alt-text="Azure Cosmos DB Power BI 连接器":::

   选择“集成式工作区”实例，并将“兼容性级别”设置为“SQL Server 2017 / Azure Analysis Services (1400)”  

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png" alt-text="Azure Cosmos DB Power BI 连接器":::

1. **添加 Azure Cosmos DB 数据源** - 导航到“模型”> “数据源” > “新建数据源”，并按以下屏幕截图中所示添加 Azure Cosmos DB 数据源：   

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/add-data-source.png" alt-text="Azure Cosmos DB Power BI 连接器":::

   提供**帐户 URI**、**数据库名称**和**容器名称**以连接到 Azure Cosmos DB。 现在可以看到，Azure Cosmos 容器中的数据已导入到 Power BI 中。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png" alt-text="Azure Cosmos DB Power BI 连接器":::

1. **构建 Analysis Services 模型** - 打开查询编辑器，执行所需的操作以优化加载的数据集：

   * 仅提取天气相关的列（气温和降水量）

   * 从表中提取月份信息。 根据下一部分所述创建分区时，此数据非常有用。

   * 将气温列转换为数字

   生成的 M 表达式如下所示：

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   此外，将气温列的数据类型更改为“Decimal”，以确保可以在 Power BI 中绘出这些值。

1. **创建 Azure Analysis 分区** - 在 Azure Analysis Services 中创建分区，以将数据集划分到可按不同的频率独立刷新的逻辑分区。 在此示例中，将创建两个分区，将数据集划分为最近的月份的数据和其他内容。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png" alt-text="Azure Cosmos DB Power BI 连接器" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **历史** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **将模型部署到 Azure Analysis Server** - 右键单击 Azure Analysis Services 项目并选择“部署”。  在“部署服务器属性”窗格中添加服务器名称。 

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png" alt-text="Azure Cosmos DB Power BI 连接器":::

1. **配置分区刷新与合并** - Azure Analysis Services 允许独立处理分区。 由于我们希望使用最新的数据持续更新“最近月份”分区，因此请将刷新间隔设置为 5 分钟。  可以使用 [REST API](../analysis-services/analysis-services-async-refresh.md)、 [Azure 自动化](../analysis-services/analysis-services-refresh-azure-automation.md)或 [逻辑应用](../analysis-services/analysis-services-refresh-logic-app.md)来刷新数据。 不需要刷新历史分区中的数据。 此外，还需要编写一些代码将“最近月份”分区合并到历史分区，并创建一个新的“最近月份”分区。

## <a name="connect-power-bi-to-analysis-services"></a>将 Power BI 连接到 Analysis Services

1. **使用 Azure Analysis Services 数据库连接器连接到 Azure Analysis Server** - 选择“实时模式”并连接到 Azure Analysis Services 实例，如以下屏幕截图中所示：

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png" alt-text="Azure Cosmos DB Power BI 连接器":::

1. **加载数据并生成报表** - 使用前面加载的数据，创建用于报告气温和降水量的图表。 由于创建的是实时连接，因此应该针对在上一步骤中部署的 Azure Analysis Services 模型中的数据执行查询。 在将新数据载入 Azure Cosmos DB 后的五分钟内，气温图表将会更新。

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="Azure Cosmos DB Power BI 连接器":::

## <a name="next-steps"></a>后续步骤

* 有关 Power BI 的详细信息，请参阅 [Power BI 入门](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)。

* [将 Qlik Sense 连接到 Azure Cosmos DB 并可视化数据](visualize-qlik-sense.md)
