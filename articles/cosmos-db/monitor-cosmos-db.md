---
title: 监视 Azure 宇宙数据库 |微软文档
description: 了解如何监视 Azure Cosmos DB 的性能和可用性。
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: db9e86706ecd4e5b2526e1d801dda45ed6b345c6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887242"
---
# <a name="monitoring-azure-cosmos-db"></a>监视 Azure 宇宙数据库
当依赖于 Azure 资源的关键应用程序和业务流程时，需要监视这些资源的可用性、性能和操作。 本文介绍了 Azure Cosmos 数据库生成的监视数据，以及如何使用 Azure 监视器的功能来分析和警报此数据。

## <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？
Azure Cosmos DB 使用[Azure 监视器](../azure-monitor/overview.md)创建监视数据，Azure 监视器是 Azure 中的完整堆栈监视服务，除了其他云和本地的资源外，还提供一整套功能来监视 Azure 资源。 

如果您还不熟悉监视 Azure 服务，则从[使用 Azure 监视器监视 Azure 资源](../azure-monitor/insights/monitor-azure-resource.md)一文开始，该文章描述了以下内容：

- 说明是 Azure Monitor？
- 与监视相关的成本
- 监视在 Azure 中收集的数据
- 配置数据收集
- Azure 中用于分析和警报监视数据的标准工具

以下各节通过描述从 Azure Cosmos DB 收集的数据，并提供用于使用 Azure 工具配置数据收集和分析此数据的示例，从而构建本文。

## <a name="azure-monitor-for-cosmos-db-preview"></a>用于宇宙数据库的 Azure 监视器（预览）
[Azure Cosmos DB 的 Azure 监视器](../azure-monitor/insights/cosmosdb-insights-overview.md)基于 Azure[监视器的工作簿功能](../azure-monitor/app/usage-workbooks.md)，并使用以下各节中所述为 Cosmos DB 收集的相同监视数据。 使用此工具查看统一交互式体验中所有 Azure Cosmos DB 资源的总体性能、故障、容量和操作运行状况，并利用 Azure 监视器的其他功能进行详细分析和警报。 

![宇宙 DB 的 Azure 监视器](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>查看 Azure 宇宙 DB 的操作级别指标

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 在左侧导航栏中选择“监视”，然后选择“指标”。********

   ![Azure Monitor 中的“指标”窗格](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. 在“指标”窗格中选择一个资源，然后选择所需的订阅和资源组。**************** 对于“资源类型”，请选择“Azure Cosmos DB 帐户”，选择一个现有的 Azure Cosmos 帐户，然后选择“应用”。************

   ![选择 Cosmos DB 帐户以查看指标](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. 接下来，可以从可用指标列表中选择一个指标。 可以选择特定于请求单位、存储、延迟、可用性、Cassandra 等的指标。 要详细了解此列表中的所有可用指标，请参阅[按类别的指标](monitor-cosmos-db-reference.md)。 在此示例中，让我们选择“请求单位”和“平均”作为聚合值。********

   除这些详细信息外，还可以选择指标的“时间范围”和“时间粒度”。******** 可以查看过去最长 30 天的指标。  应用筛选器后，系统会根据该筛选器显示图表。 可以查看所选时间段内每分钟消耗的平均请求单位数。  

   ![从 Azure 门户中选择指标](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>向指标添加筛选器

还可以按 **CollectionName**、**DatabaseName**、**OperationType**、**Region** 和 **StatusCode** 筛选指标及显示的图表。 若要筛选指标，请选择“添加筛选器”，选择所需的属性（例如 **OperationType**），然后选择一个值（例如 **Query**）。**** 然后，图表中会显示查询操作在所选时间段内消耗的请求单位数。 不会记录通过存储过程执行的操作，因此 OperationType 指标下不会显示这些操作。

![添加筛选器以选择指标粒度](./media/monitor-cosmos-db/add-metrics-filter.png)

可以使用“应用拆分”选项将指标分组。**** 例如，可按操作类型将请求单位分组，并一次性查看所有操作的图表，如下图所示：

![添加“应用拆分”筛选器](./media/monitor-cosmos-db/apply-metrics-splitting.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>监视从 Azure 宇宙数据库收集的数据

Azure Cosmos DB 收集与其他 Azure 资源相同的监视数据，这在监视[Azure 资源中描述](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)的数据。 有关 Azure Cosmos DB 创建的日志和指标的详细信息，请参阅[Azure Cosmos DB 监视数据引用](monitor-cosmos-db-reference.md)。

每个 Azure Cosmos 数据库的 Azure 门户中的 **"概述"** 页包括数据库使用情况的简要视图，包括其请求和每小时计费使用情况。 这是有用的信息，但可用的监视数据只有少量。 其中一些数据会自动收集，并在创建数据库后立即可供分析，同时可以使用某些配置启用其他数据收集。

![概述页](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>分析指标数据

Azure Cosmos DB 提供了用于处理指标的自定义体验。 有关使用此体验和分析不同的 Azure Cosmos DB 方案的详细信息，请参阅[Azure 监视器中的监视和调试 Azure 宇宙数据库指标](cosmos-db-azure-monitor-metrics.md)。

您可以使用指标资源管理器使用来自其他 Azure 服务的指标分析 Azure Cosmos DB 的指标，从 Azure**监视器**菜单中打开**指标**。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../azure-monitor/platform/metrics-getting-started.md)。 Azure Cosmos DB 的所有指标都在命名空间**Cosmos DB 标准指标中**。 将筛选器添加到图表时，可以将以下维度与以下指标一起使用：

- CollectionName
- DatabaseName
- OperationType
- 区域
- StatusCode


## <a name="analyzing-log-data"></a>分析日志数据
Azure 监视器日志中的数据存储在每个表具有其自己的唯一属性集的表中。 Azure Cosmos DB 将数据存储在下表中。

| 表 | 说明 |
|:---|:---|
| AzureDiagnostics | 多个服务用于存储资源日志的通用表。 Azure Cosmos DB 的资源日志可以使用`MICROSOFT.DOCUMENTDB`标识。   |
| AzureActivity    | 存储活动日志中所有记录的常见表。 


> [!IMPORTANT]
> 从 Azure Cosmos DB 菜单中选择 **"日志"** 时，将打开日志分析，查询范围设置为当前 Azure Cosmos 数据库。 这意味着日志查询只包含来自该资源的数据。 如果希望运行的查询包含其他数据库或其他 Azure 服务的数据，请从“Azure Monitor”**** 菜单中选择“日志”****。 请查看 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](../azure-monitor/log-query/scope.md)了解详细信息。

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure 宇宙 DB 日志分析查询在 Azure 监视器中

下面是一些查询，您可以在**日志搜索**栏中输入这些查询，以帮助您监视 Azure Cosmos 容器。 这些查询使用[新语言](../log-analytics/log-analytics-log-search-upgrade.md)。

以下是可用于帮助监视 Azure Cosmos 数据库的查询。

* 若要查询指定时间段内来自 Azure Cosmos DB 的所有诊断日志，请执行以下操作：

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"

    ```

* 查询最近记录的 10 个事件：

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* 查询已按操作类型分组的所有操作：

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* 要查询所有操作，请按资源分组：

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* 查询已按资源分组的所有用户活动：

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
* 将所有超过 100 个 R 的查询与**DataPlane 请求**和**查询 RunTime 统计信息**中的数据联接。

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
    | project activityId_g, requestCharge_s
    | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
    ) on $left.activityId_g == $right.activityId_g
    | order by requestCharge_s desc
    | limit 100
    ```

* 查询哪些操作花费的时间超过 3 毫秒：

    ```Kusto
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* 查询哪些代理正在运行操作：

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* 查询何时执行了长时间运行的操作：

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
    ```
    
* 要获取分区密钥统计信息以评估数据库帐户的前 3 个分区之间的偏差，请进行以下计算：

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
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

- 有关 Azure Cosmos DB 创建的日志和指标的引用，请参阅[Azure Cosmos DB 监视数据引用](monitor-cosmos-db-reference.md)。
- 有关监视 Azure 资源的详细信息，请参阅[通过 Azure Monitor 监视 Azure 资源](../azure-monitor/insights/monitor-azure-resource.md)。
