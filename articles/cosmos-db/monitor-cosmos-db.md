---
title: 监视 Azure Cosmos DB | Microsoft Docs
description: 了解如何监视 Azure Cosmos DB 的性能和可用性。
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: 5056762dab18ae23980c7d3b3ebfbb3c3014fa56
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798699"
---
# <a name="monitoring-azure-cosmos-db"></a>监视 Azure Cosmos DB

如果你有关键应用程序和业务流程依赖于 Azure 资源，则需要监视这些资源的可用性、性能和操作。 本文介绍 Azure Cosmos 数据库生成的监视数据，以及如何使用 Azure Monitor 的功能在此数据的基础上进行分析和发出警报。

## <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？
Azure Cosmos DB 使用 [Azure Monitor](../azure-monitor/overview.md) 创建监视数据，后者是 Azure 中的一项完整堆栈监视服务，它提供了一组完整的功能来监视 Azure 资源以及其他云中和本地的资源。

如果你尚不熟悉如何监视 Azure 服务，请从[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/insights/monitor-azure-resource.md)一文开始，其中介绍了以下内容：

- 说明是 Azure Monitor？
- 与监视相关的成本
- 监视 Azure 中收集的数据
- 配置数据集合
- Azure 中用于分析监视数据并就其发出警报的标准工具

本文的以下各部分介绍如何从 Azure Cosmos DB 收集特定数据，并提供了有关使用 Azure 工具配置数据收集和分析此数据的示例。

## <a name="azure-monitor-for-azure-cosmos-db"></a>适用于 Azure Cosmos DB 的 Azure Monitor

适用于 Azure Cosmos DB 的 Azure Monitor 基于 [Azure Monitor 的工作簿功能](../azure-monitor/platform/workbooks-overview.md)，并使用为以下部分中所述的 Cosmos DB 收集的相同监视数据。 使用 Azure Monitor 以统一的交互式体验查看所有 Azure Cosmos DB 资源的总体性能、故障、容量和操作运行状况，并利用 Azure Monitor 的其他功能进行详细分析和发出警报。 若要了解详细信息，请参阅文章[探究适用于 Azure Cosmos DB 的 Azure Monitor](../azure-monitor/insights/cosmosdb-insights-overview.md)。

> [!NOTE]
> 创建容器时，请确保不创建名称相同但大小写不同的两个容器。 这是因为 Azure 平台的某些部分不区分大小写，这可能会对具有此类名称的容器导致遥测和操作混乱/冲突。

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>查看 Azure Cosmos DB 的操作级别指标

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 从左侧导航栏中选择“监视器”，然后选择“指标”。

   ![Azure Monitor 中的“指标”窗格](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. 从“指标”窗格中选择一个资源，然后选择所需的订阅和资源组。  对于“资源类型”，请选择“Azure Cosmos DB 帐户”，接着选择你的现有 Azure Cosmos 帐户之一，然后选择“应用”。

   ![选择 Cosmos DB 帐户以查看指标](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. 接下来，可以从可用指标的列表中选择一个指标。 可以选择特定于请求单位、存储、延迟、可用性、Cassandra 和其他方面的指标。 若要详细了解此列表中的所有可用指标，请参阅[按类别列出的指标](monitor-cosmos-db-reference.md)一文。 在此示例中，让我们选择“请求单位”和“平均”作为聚合值。

   除了这些详细信息之外，还可以选择指标的时间范围和时间粒度。 最多可以查看过去 30 天的指标。  在你应用筛选器后，系统会根据筛选器显示一个图表。 可以查看所选时间段内每分钟消耗的平均请求单位数。  

   ![从 Azure 门户中选择一个指标](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>向指标添加筛选器

还可以按特定 CollectionName、DatabaseName、OperationType、Region 和 StatusCode 筛选指标和显示的图表。 若要筛选指标，请选择“添加筛选器”，并选择所需的属性（例如 OperationType），然后选择值（如 Query）。 然后，图表中会显示查询操作在所选时间段内消耗的请求单位数。 不会记录通过存储过程执行的操作，因此 OperationType 指标下不会显示这些操作。

![添加筛选器以选择指标粒度](./media/monitor-cosmos-db/add-metrics-filter.png)

可以使用“应用拆分”选项对指标进行分组。 例如，可按操作类型将请求单位分组，并一次性查看所有操作的图表，如下图所示：

![添加应用拆分筛选器](./media/monitor-cosmos-db/apply-metrics-splitting.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>从 Azure Cosmos DB 收集的监视数据

Azure Cosmos DB 会收集与[来自 Azure 资源的监视数据](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)中所述的其他 Azure 资源相同类型的监视数据。 有关 Azure Cosmos DB 创建的日志和指标的详细参考，请参阅 [Azure Cosmos DB 监视数据参考](monitor-cosmos-db-reference.md)。

Azure 门户中每个 Azure Cosmos 数据库的“概述”页都提供数据库使用情况的简要视图，其中包括该数据库的请求和每小时计费。 这些信息非常有用，但只提供少量监视数据。 创建数据库后，系统会立即自动收集其中部分数据并使其可用于分析；同时，你可以使用某项配置启用其他数据收集。

![概述页](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>分析指标数据

Azure Cosmos DB 为处理指标提供了自定义体验。 有关使用此体验和分析不同 Azure Cosmos DB 方案的详细信息，请参阅[从 Azure Monitor 监视和调试 Azure Cosmos DB 指标](cosmos-db-azure-monitor-metrics.md)。

可以从“Azure Monitor”菜单中打开“指标”，使用指标资源管理器根据来自其他 Azure 服务的指标为 Azure Cosmos DB 分析指标 。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../azure-monitor/platform/metrics-getting-started.md)。 Azure Cosmos DB 的所有指标都位于命名空间“Cosmos DB 标准指标”中。 向图表添加筛选器时，可以对这些指标使用以下维度：

- CollectionName
- DatabaseName
- OperationType
- 区域
- StatusCode

## <a name="analyzing-log-data"></a>分析日志数据
Azure Monitor 日志中的数据存储在表中，每个表都具有自己独有的属性集。 Azure Cosmos DB 将数据存储在以下表中。

| 表 | 说明 |
|:---|:---|
| AzureDiagnostics | 由多个服务用于存储资源日志的公用表。 Azure Cosmos DB 中的资源日志可使用 `MICROSOFT.DOCUMENTDB` 进行识别。   |
| AzureActivity    | 用于存储活动日志中所有记录的公用表。 


> [!IMPORTANT]
> 在 Azure Cosmos DB 菜单中选择“日志”时，Log Analytics 随即打开，其查询范围设置为当前 Azure Cosmos 数据库。 这意味着日志查询只包含来自该资源的数据。 如果要运行的查询包含其来自他数据库或其他 Azure 服务的数据，请从“Azure Monitor”菜单中选择“日志” 。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](../azure-monitor/log-query/scope.md)。

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure Monitor 中的 Azure Cosmos DB Log Analytics 查询

可在“日志搜索”搜索栏中输入下面这些查询，以帮助监视 Azure Cosmos 容器。 这些查询使用[新语言](../log-analytics/log-analytics-log-search-upgrade.md)。

以下是可用于帮助监视 Azure Cosmos 数据库的查询。

* 若要查询指定时间段内来自 Azure Cosmos DB 的所有诊断日志，请执行以下操作：

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests"

    ```

* 查询已按资源分组的所有操作：

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* 查询已按资源分组的所有用户活动：

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>以编程方式监视 Azure Cosmos DB

门户中提供的帐户级指标（如帐户存储使用率和总请求数）不可通过 SQL API 获得。 但是，可以使用 SQL API 在集合级别检索使用情况数据。 若要检索集合级别的数据，请执行以下操作：

* 若要使用 REST API，请[对集合执行 GET](https://msdn.microsoft.com/library/mt489073.aspx)。 集合的配额和使用情况信息将返回到响应中的 x-ms-resource-quota 和 x-ms-resource-usage 标头中。

* 要使用 .NET SDK，请使用 [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) 方法，它将返回 [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx)，其中包含大量使用情况属性，例如 **CollectionSizeUsage**、**DatabaseUsage**、**DocumentUsage** 等。

若要访问其他指标，请使用 [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)。 可以通过调用以下命令来检索可用的指标定义：

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

用于检索各个指标的查询使用以下格式：

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>后续步骤

- 有关 Azure Cosmos DB 创建的日志和指标的参考，请参阅 [Azure Cosmos DB 监视数据参考](monitor-cosmos-db-reference.md)。
- 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../azure-monitor/insights/monitor-azure-resource.md)。
