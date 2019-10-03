---
title: 使用 Azure Cosmos DB、Azure Analysis Services 和 Power BI 创建实时仪表板
description: 了解如何使用 Azure Cosmos DB 和 Azure Analysis Services 在 Power BI 中创建实时天气仪表板。
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376588"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>使用 Azure Cosmos DB 和 Power BI 创建实时仪表板

本文介绍使用 Azure Cosmos DB 和 Azure Analysis Services 在 Power BI 中创建实时天气仪表板所需的步骤。 Power BI 仪表板将显示图表，以显示有关区域温度和降雨量的实时信息。

## <a name="reporting-scenarios"></a>报告方案

有多种方法可以为存储在 Azure Cosmos DB 中的数据设置报表仪表板。 根据过期要求和数据大小，下表描述了每种方案的报表设置：


|应用场景 |安装 |
|---------|---------|
|1.生成即席报表（无刷新）    |  [Power BI 具有导入模式 Azure Cosmos DB 连接器](powerbi-visualize.md)       |
|2.定期刷新生成即席报表   |  [Power BI Azure Cosmos DB 连接器与导入模式（计划定期刷新）](powerbi-visualize.md)       |
|3.报告大型数据集（< 10 GB）     |  通过增量刷新 Power BI Azure Cosmos DB 连接器       |
|4.报告大型数据集的实时时间    |  直接查询 + Azure Analysis Services Power BI Azure Analysis Services 连接器（Azure Cosmos DB 连接器）       |
|5.用聚合报告实时数据     |  [Power BI Spark 连接器与直接查询 + Azure Databricks + Cosmos DB Spark 连接器。](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6.报告包含大型数据集聚合的实时数据   |  通过直接查询 + Azure Analysis Services + Azure Databricks + Cosmos DB Spark 连接器 Power BI Azure Analysis Services 连接器。       |

使用 Azure Cosmos DB Power BI 连接器可以轻松设置方案1和2。 本文介绍方案3和4的设置。

### <a name="power-bi-with-incremental-refresh"></a>增量刷新的 Power BI

Power BI 具有可在其中配置增量刷新的模式。 此模式无需创建和管理 Azure Analysis Services 分区。 增量刷新可设置为仅筛选大型数据集中的最新更新。 但是，此模式仅适用于数据集限制为 10 GB 的 Power BI Premium 服务。

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure 分析连接器 + Azure Analysis Services

Azure Analysis Services 提供完全托管的平台即服务，该服务在云中托管企业级的数据模型。 可以从 Azure Cosmos DB 将大规模数据集加载到 Azure Analysis Services 中。 若要避免始终查询整个数据集，可以将数据集细分为 Azure Analysis Services 分区，这些分区可以在不同的频率单独刷新。

## <a name="power-bi-incremental-refresh"></a>Power BI 增量刷新

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>将天气数据引入 Azure Cosmos DB

设置一个引入管道，将[天气数据](https://catalog.data.gov/dataset/local-weather-archive)加载到 Azure Cosmos DB。 可以设置[Azure 数据工厂（ADF）](../data-factory/connector-azure-cosmos-db.md)作业，使用 HTTP 源和 Cosmos DB 接收器定期将最新天气数据加载到 Azure Cosmos DB 中。


### <a name="connect-power-bi-to-azure-cosmos-db"></a>将 Power BI 连接到 Azure Cosmos DB

1. **将 Azure Cosmos 帐户连接到 Power BI** -打开 Power BI Desktop 并使用 Azure Cosmos DB 连接器选择正确的数据库和容器。

   ![Azure Cosmos DB Power BI 连接器](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **配置增量刷新**-按照 Power BI 一文中的[增量刷新](/power-bi/service-premium-incremental-refresh)中的步骤操作，为数据集配置增量刷新。 添加**RangeStart**和**RangeEnd**参数，如以下屏幕截图所示：

   ![配置范围参数](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   由于数据集具有文本格式的日期列，因此应将**RangeStart**和**RangeEnd**参数转换为使用以下筛选器。 在 "**高级编辑器**" 窗格中，修改查询，添加以下文本以基于 RangeStart 和 RangeEnd 参数筛选行：

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   根据源数据集中存在的列和数据类型，可以相应地更改 "RangeStart" 和 "RangeEnd" 字段

   
   |属性  |数据类型  |筛选  |
   |---------|---------|---------|
   |_ts     |   Numeric      |  [_ts] > Duration. Timespan.totalseconds （RangeStart-#datetime （1970，1，1，0，0，0））和 [_ts] < #datetime （1970，1，1，0，0，0）））       |
   |Date （例如：-2019-08-19）     |   String      | [Document. 日期] > ToText （RangeStart，"yyyy-mm-dd"）和 [Document. date] < ToText （RangeEnd，"yyyy-mm-dd"）        |
   |Date （例如：-2019-08-11 12:00:00）   |  String       |  [Document. date] > ToText （RangeStart，"yyyy-mm-dd HH： mm： ss"）和 [Document. 日期] < ToText （RangeEnd，"yyyy-mm-dd HH： mm： ss"）       |


1. **定义刷新策略**-定义刷新策略，方法是导航到表**上下文**菜单上的 "**增量刷新**" 选项卡。 将刷新策略设置为**每天**刷新并存储上个月的数据。

   ![定义刷新策略](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   忽略警告，指出*无法确认是否折叠了 M 查询*。 Azure Cosmos DB 连接器折叠筛选器查询。

1. **加载数据并生成报表**-通过使用之前加载的数据，创建用于报告温度和降雨量的图表。

   ![加载数据并生成报告](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. 将**报表发布到 Power BI 高级版**-由于增量刷新是一项仅限高级版的功能，因此 "发布" 对话框仅允许选择高级容量中的工作区。 第一次刷新可能需要更长的时间才能导入历史记录数据。 后续的数据刷新速度要快得多，因为它们使用增量刷新。


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure 分析连接器 + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>将天气数据引入 Azure Cosmos DB 

设置一个引入管道，将[天气数据](https://catalog.data.gov/dataset/local-weather-archive)加载到 Azure Cosmos DB。 可以设置 Azure 数据工厂（ADF）作业，使用 HTTP 源和 Cosmos DB 接收器定期将最新天气数据加载到 Azure Cosmos DB 中。

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>将 Azure Analysis Services 连接到 Azure Cosmos 帐户

1. **创建新的 Azure Analysis Services 群集** - 在 azure Cosmos 帐户和 Databricks 群集所在的同一区域中[创建 azure Analysis Services 的实例](../analysis-services/analysis-services-create-server.md)。

1. **在 visual studio** -  中创建新的 Analysis Services 表格项目[安装 SQL Server Data Tools （SSDT）](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)并在 visual studio 中创建 Analysis Services 表格项目。

   ![创建 Azure Analysis Services 项目](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   选择**集成的工作区**实例，并将兼容级别设置为**SQL Server 2017/Azure Analysis Services （1400）**

   ![Azure Analysis Services 表格模型设计器](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **添加 Azure Cosmos DB 数据源**-导航到 "**模型**> **数据源** > " "**新建数据源**" 并添加 Azure Cosmos DB 数据源，如以下屏幕截图所示：

   ![添加 Cosmos DB 数据源](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   通过提供**帐户 URI**、**数据库名称**和**容器名称**连接到 Azure Cosmos DB。 你现在可以看到将 Azure Cosmos 容器中的数据导入 Power BI。

   ![预览 Azure Cosmos DB 数据](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **构造 Analysis Services 模型**-打开查询编辑器，执行所需的操作来优化已加载的数据集：

   * 仅提取与天气相关的列（温度和降雨量）

   * 从表中提取月信息。 此数据可用于创建分区，如下一节中所述。

   * 将温度列转换为数字

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

   此外，将温度列的数据类型更改为 Decimal，以确保可以在 Power BI 中绘制这些值。

1. **创建 Azure 分析分区**-在 Azure Analysis Services 中创建分区，以将数据集划分为可独立和不同频率刷新的逻辑分区。 在此示例中，将创建两个分区，将数据集划分为最近的月份的数据和其他内容。

   ![创建 analysis services 分区](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   在 Azure Analysis Services 中创建以下两个分区：

   * **最新月** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **记录** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. 将**模型部署到 Azure Analysis Server** -右键单击 Azure Analysis Services 项目，然后选择 "**部署**"。 在 "**部署服务器属性**" 窗格中添加服务器名称。

   ![部署 Azure Analysis Services 模型](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **配置分区刷新和合并**-Azure Analysis Services 允许独立处理分区。 由于我们希望使用最新的数据持续更新**最新的月份**分区，因此请将刷新间隔设置为5分钟。 不需要在历史分区中刷新数据。 此外，还需要编写一些代码，将最新的月分区合并到历史分区，并创建新的最新月份分区。


## <a name="connect-power-bi-to-analysis-services"></a>将 Power BI 连接到 Analysis Services

1. **使用 Azure Analysis Services 数据库连接器连接到 Azure Analysis Server** -选择**实时模式**并连接到 Azure Analysis Services 实例，如以下屏幕截图所示：

   ![从 Azure Analysis Services 获取数据](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **加载数据并生成报告**-通过使用之前加载的数据，创建用于报告温度和降雨量的图表。 由于创建的是实时连接，因此应该对在上一步中部署的 Azure Analysis Services 模型中的数据执行查询。 将新数据加载到 Azure Cosmos DB 后，温度图将在五分钟内进行更新。

   ![加载数据并生成报告](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>后续步骤

* 有关 Power BI 的详细信息，请参阅 [Power BI 入门](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)。

* [连接 Qlik sense 感知，Azure Cosmos DB 和可视化数据](visualize-qlik-sense.md)