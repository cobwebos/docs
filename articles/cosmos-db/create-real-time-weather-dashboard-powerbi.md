---
title: 使用 Azure 宇宙数据库、Azure 分析服务和 Power BI 创建实时仪表板
description: 了解如何使用 Azure Cosmos DB 和 Azure 分析服务在 Power BI 中创建实时天气仪表板。
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "70376588"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>使用 Azure Cosmos DB 和 Power BI 创建实时仪表板

本文介绍使用 Azure Cosmos DB 和 Azure 分析服务在 Power BI 中创建实时天气仪表板所需的步骤。 Power BI 仪表板将显示图表，以显示有关区域温度和降雨量的实时信息。

## <a name="reporting-scenarios"></a>报告方案

有多种方法可以设置 Azure Cosmos DB 中存储的数据的报告仪表板。 根据陈旧要求和数据大小，下表描述了每种方案的报告设置：


|方案 |设置 |
|---------|---------|
|1. 生成临时报告（不刷新）    |  [使用导入模式为 BI Azure 宇宙数据库连接器供电](powerbi-visualize.md)       |
|2. 生成定期刷新的专案报告   |  [使用导入模式为 BI Azure Cosmos DB 连接器供电（计划定期刷新）](powerbi-visualize.md)       |
|3. 报告大型数据集（< 10 GB）     |  通过增量刷新为 BI Azure 宇宙数据库连接器供电       |
|4. 对大型数据集进行实时报告    |  使用直接查询和 Azure 分析服务（Azure Cosmos DB 连接器）为 BI Azure 分析服务连接器供电       |
|5. 使用聚合报告实时数据     |  [电源 BI 火花连接器与直接查询 + Azure 数据块 + 宇宙 DB 火花连接器。](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. 报告大型数据集上具有聚合的实时数据   |  使用直接查询 + Azure 分析服务 + Azure 数据块 + 宇宙 DB Spark 连接器为 BI Azure 分析服务连接器供电。       |

可以使用 Azure Cosmos DB Power BI 连接器轻松设置方案 1 和 2。 本文介绍了方案 3 和 4 的设置。

### <a name="power-bi-with-incremental-refresh"></a>使用增量刷新为 BI 供电

Power BI 具有可配置增量刷新的模式。 此模式无需创建和管理 Azure 分析服务分区。 可以设置增量刷新，以便仅筛选大型数据集中的最新更新。 但是，此模式仅适用于数据集限制为 10 GB 的 Power BI 高级服务。

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>电源 BI Azure 分析连接器 + Azure 分析服务

Azure 分析服务提供完全托管的平台，作为在云中托管企业级数据模型的服务。 可以从 Azure Cosmos DB 加载到 Azure 分析服务中。 为了避免一直查询整个数据集，可以将数据集细分为 Azure 分析服务分区，这些分区可以以不同频率独立刷新。

## <a name="power-bi-incremental-refresh"></a>电源 BI 增量刷新

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>将天气数据引入 Azure 宇宙数据库

设置引入管道以将[天气数据](https://catalog.data.gov/dataset/local-weather-archive)加载到 Azure Cosmos DB。 您可以设置 Azure[数据工厂 （ADF）](../data-factory/connector-azure-cosmos-db.md)作业，以便使用 HTTP 源和 Cosmos DB 接收器定期将最新的天气数据加载到 Azure Cosmos DB 中。


### <a name="connect-power-bi-to-azure-cosmos-db"></a>将 POWER BI 连接到 Azure 宇宙数据库

1. **将 Azure Cosmos 帐户连接到 Power BI** - 打开 Power BI 桌面并使用 Azure Cosmos DB 连接器选择正确的数据库和容器。

   ![Azure Cosmos DB Power BI 连接器](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **配置增量刷新**- 使用 Power BI 文章按照[增量刷新](/power-bi/service-premium-incremental-refresh)中的步骤为数据集配置增量刷新。 添加**RangeStart**和**RangeEnd**参数，如以下屏幕截图所示：

   ![配置范围参数](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   由于数据集具有文本形式的日期列，因此应转换**RangeStart**和**RangeEnd**参数以使用以下筛选器。 在 **"高级编辑器"** 窗格中，修改查询添加以下文本以根据 RangeStart 和 RangeEnd 参数筛选行：

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   根据源数据集中存在哪些列和数据类型，可以相应地更改 RangeStart 和 RangeEnd 字段

   
   |properties  |数据类型  |“筛选器”  |
   |---------|---------|---------|
   |_ts     |   Numeric      |  [_ts] >持续时间.总秒数（范围开始 - #datetime（1970，1，1，0，0））和[_ts] <持续时间。总秒数（范围结束 - #datetime（1970，1，0，0））       |
   |日期（例如：- 2019-08-19）     |   String      | [文档.date]>日期时间.到文本（范围开始，"yyyy-MM-dd"）和[文档.date] <日期时间。到文本（Range 结束，"yyyy-MM-dd"）        |
   |日期（例如：- 2019-08-11 12：00：00）   |  String       |  [文档.date]>日期时间.至文本（范围开始），yyyy-mm-dd HH：mm：ss"）和[文档.date] <日期时间。到Text（Rangeend，"yyy-mm-dd HH：mm：ss"）       |


1. **定义刷新策略**- 通过导航到表**的上下文**菜单上的**增量刷新**选项卡来定义刷新策略。 将刷新策略设置为**每天**刷新并存储上个月的数据。

   ![定义刷新策略](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   忽略警告，指出*无法确认 M 查询已折叠*。 Azure Cosmos DB 连接器折叠筛选器查询。

1. **加载数据并生成报告**- 通过使用之前加载的数据，创建图表以报告温度和降雨量。

   ![加载数据并生成报告](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **将报表发布到 Power BI 高级**- 由于增量刷新仅是高级功能，因此发布对话框只允许选择高级容量上的工作区。 首次刷新可能需要更长时间来导入历史数据。 后续数据刷新要快得多，因为它们使用增量刷新。


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>电源 BI Azure 分析连接器 + Azure 分析服务 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>将天气数据引入 Azure 宇宙数据库 

设置引入管道以将[天气数据](https://catalog.data.gov/dataset/local-weather-archive)加载到 Azure Cosmos DB。 您可以设置 Azure 数据工厂 （ADF） 作业，以便使用 HTTP 源和 Cosmos DB 接收器定期将最新的天气数据加载到 Azure Cosmos DB 中。

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>将 Azure 分析服务连接到 Azure 宇宙帐户

1. **创建新的 Azure 分析服务群集** - 创建 Azure[分析服务实例](../analysis-services/analysis-services-create-server.md)，该实例位于与 Azure Cosmos 帐户和 Databricks 群集相同的区域中。

1. **在可视化工作室** -  中创建新的分析服务表格项目[安装 SQL 服务器数据工具 （SSDT），](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)并在可视化工作室中创建分析服务表格项目。

   ![创建 Azure 分析服务项目](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   选择**集成工作区**实例，并将兼容性级别设置为 SQL **Server 2017 / Azure 分析服务 （1400）**

   ![Azure 分析服务表格模型设计器](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **添加 Azure Cosmos 数据库数据源**- 导航到**模型**> **数据源** > **新数据源**，并添加 Azure Cosmos DB 数据源，如下图所示：

   ![添加宇宙数据库数据源](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   通过提供**帐户 URI、****数据库名称**和**容器名称**连接到 Azure Cosmos DB。 现在，您可以看到 Azure Cosmos 容器中的数据导入到 Power BI 中。

   ![预览 Azure 宇宙数据库数据](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **构造分析服务模型**- 打开查询编辑器，执行所需的操作以优化加载的数据集：

   * 仅提取与天气相关的列（温度和降雨量）

   * 从表中提取月份信息。 如下一节所述，此数据在创建分区时非常有用。

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

   此外，将温度列的数据类型更改为十进制，以确保这些值可以在 Power BI 中绘制。

1. **创建 Azure 分析分区**- 在 Azure 分析服务中创建分区，将数据集划分为逻辑分区，这些分区可以独立刷新，频率不同。 在此示例中，您将创建两个分区，将数据集划分为最近一个月的数据和其他所有内容。

   ![创建分析服务分区](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   在 Azure 分析服务中创建以下两个分区：

   * **最近一个月** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **历史** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **将模型部署到 Azure 分析服务器**- 右键单击 Azure 分析服务项目并选择 **"部署**"。 在 **"部署服务器"属性**窗格中添加服务器名称。

   ![部署 Azure 分析服务模型](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **配置分区刷新和合并**- Azure 分析服务允许独立处理分区。 由于我们希望使用最新数据不断更新 **"最近月份"** 分区，因此将刷新间隔设置为 5 分钟。 不需要刷新历史分区中的数据。 此外，您需要编写一些代码，以将最近月分区合并到历史分区并创建新的最近月份分区。


## <a name="connect-power-bi-to-analysis-services"></a>将 POWER BI 连接到分析服务

1. **使用 Azure 分析服务数据库连接器连接到 Azure 分析服务器**- 选择**实时模式**并连接到 Azure 分析服务实例，如以下屏幕截图所示：

   ![从 Azure Analysis Services 获取数据](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **加载数据并生成报告**- 通过使用之前加载的数据，创建图表以报告温度和降雨量。 由于您要创建实时连接，因此应在上一步骤中部署的 Azure 分析服务模型中的数据上执行查询。 新数据加载到 Azure Cosmos DB 后，温度图表将在五分钟内更新。

   ![加载数据并生成报告](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>后续步骤

* 有关 Power BI 的详细信息，请参阅 [Power BI 入门](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)。

* [将 Qlik Sense 连接到 Azure Cosmos DB 并可视化数据](visualize-qlik-sense.md)