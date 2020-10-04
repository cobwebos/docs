---
title: 监视 Azure 队列存储 |Microsoft Docs
description: 了解如何监视 Azure 队列存储的性能和可用性。 监视 Azure 队列存储数据、了解配置以及分析指标和日志数据。
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 3fe99543b821810b1479f1e504098d81fd20c534
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711164"
---
# <a name="monitoring-azure-queue-storage"></a>监视 Azure 队列存储

如果你有依赖 Azure 资源的关键应用程序和业务流程，则需要监视这些资源的可用性、性能和操作。 本文介绍 Azure 队列存储生成的监视数据，以及如何使用 Azure Monitor 的功能来分析此数据的警报。

> [!NOTE]
> Azure Monitor 中的 Azure 存储日志目前为公共预览版，可在所有公有云区域中进行预览测试。 若要注册预览版，请参阅[此页](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。 此预览版启用 blob 的日志 (包括 Azure Data Lake Storage Gen2) 、文件、队列和表。 此功能适用于使用 Azure 资源管理器部署模型创建的所有存储帐户。 请参阅 [存储帐户概述](../common/storage-account-overview.md)。

## <a name="monitor-overview"></a>Monitor 概述

每个队列存储资源的 Azure 门户中的 " **概述** " 页包括资源使用情况的简要视图，例如请求和每小时计费。 这些信息非常有用，但只提供少量监视数据。 其中的某些数据是自动收集的，并且可在创建资源后立即进行分析。 你可以使用某些配置启用其他数据收集类型。

## <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？
Azure 队列存储使用 [Azure Monitor](../../azure-monitor/overview.md)，这是 Azure 中的一种完整的堆栈监视服务来创建监视数据。 Azure Monitor 提供了一整套用于监视 Azure 资源以及其他云和本地资源的功能。 

可先阅读文章[使用 Azure Monitor 监视 Azure 资源](../../azure-monitor/insights/monitor-azure-resource.md)，其中介绍了以下内容：

- 说明是 Azure Monitor？
- 与监视相关的成本
- 监视 Azure 中收集的数据
- 配置数据收集
- Azure 中用于分析监视数据并就其发出警报的标准工具

本文中的以下各部分将介绍从 Azure 存储收集的特定数据。 其中的示例演示了如何配置数据收集并通过 Azure 工具分析这些数据。

## <a name="monitoring-data"></a>监视数据

Azure 队列存储可收集与其他 Azure 资源相同的监视数据，如 [监视 Azure 资源的数据](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)中所述。 

有关 Azure 队列存储创建的指标和日志指标的详细信息，请参阅 [Azure 队列存储监视数据参考](monitor-queue-storage-reference.md) 。

Azure Monitor 中的指标和日志仅支持 Azure 资源管理器存储帐户。 Azure Monitor 不支持经典存储帐户。 如果要使用经典存储帐户上的指标或日志，则需要迁移到 Azure 资源管理器存储帐户。 请参阅[迁移到 Azure 资源管理器](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)。

如果需要，可以继续使用经典指标和日志。 实际上，经典指标和日志可与 Azure Monitor 中的指标和日志同时使用。 在 Azure 存储终止旧指标和日志的服务之前，支持范围保持不变。

## <a name="collection-and-routing"></a>集合和路由

平台指标和活动日志是自动收集的，但可以使用诊断设置将其路由到其他位置。 必须创建诊断设置以收集资源日志。 

若要使用 Azure 门户、Azure CLI 或 PowerShell 创建诊断设置，请参阅 [创建诊断设置以在 Azure 中收集平台日志和指标](../../azure-monitor/platform/diagnostic-settings.md)。 

若要查看创建诊断设置的 Azure 资源管理器模板，请参阅 [Azure 存储的诊断设置](https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings#diagnostic-setting-for-azure-storage)。

创建诊断设置时，请选择要为其启用日志的存储类型，如 blob、队列、表或文件。 对于队列存储，请选择 " **队列**"。 

如果在 Azure 门户中创建诊断设置，则可以从列表中选择资源。 如果使用 PowerShell 或 Azure CLI，则需要使用队列存储终结点的资源 ID。 通过打开存储帐户的“属性”页，可在 Azure 门户中找到资源 ID。

您还必须指定要为其收集日志的下列操作之一。 

| 类别 | 说明 |
|:---|:---|
| StorageRead | 对象上的读取操作。 |
| StorageWrite | 对象上的写入操作。 |
| StorageDelete | 对象上的删除操作。 |

## <a name="analyzing-metrics"></a>分析指标

你可以使用指标资源管理器通过其他 Azure 服务中的指标分析 Azure 存储的指标。 从 Azure Monitor 菜单中选择“指标”，可打开指标资源管理器 。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../../azure-monitor/platform/metrics-getting-started.md)。 

以下示例演示了如何查看帐户级别的事务。

![在 Azure 门户中访问指标的屏幕截图](./media/monitor-queue-storage/access-metrics-portal.png)

对于支持维度的指标，可使用所需的维度值筛选指标。 以下示例演示了如何通过选择“API 名称”维度的值，在特定操作上查看帐户级别的“事务” 。

![在 Azure 门户中访问包含维度的指标的屏幕截图](./media/monitor-queue-storage/access-metrics-portal-with-dimension.png)

有关 Azure 存储支持的维度的完整列表，请参阅[指标维度](monitor-queue-storage-reference.md#metrics-dimensions)。

Azure 队列存储的指标位于以下命名空间中： 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/queueServices

有关包括 Azure 队列存储的所有 Azure Monitor 支持指标的列表，请参阅 [Azure Monitor 支持的指标](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)。


### <a name="accessing-metrics"></a>访问指标

> [!TIP]
> 若要查看 Azure CLI 或 .NET 示例，请选择此处列出的相应选项卡。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>列出指标定义

可以列出存储帐户或队列存储服务的指标定义。 请使用 [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition) cmdlet。

在此示例中，将 `<resource-ID>` 占位符替换为整个存储帐户的资源 id 或队列存储服务的资源 id。  你可以在 Azure 门户中存储帐户的“属性”页上找到这些资源 ID。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>读取指标值

你可以读取存储帐户或队列存储服务的帐户级别指标值。 使用 [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric) cmdlet。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>列出帐户级指标定义

可以列出存储帐户或队列存储服务的指标定义。 使用 [az monitor metrics list-definitions](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) 命令。
 
在此示例中，将 `<resource-ID>` 占位符替换为整个存储帐户的资源 id 或队列存储服务的资源 id。 你可以在 Azure 门户中存储帐户的“属性”页上找到这些资源 ID。

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>读取帐户级指标值

你可以读取存储帐户或队列存储服务的指标值。 请使用 [az monitor metrics list](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list) 命令。

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor 提供 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)，用于读取指标定义和值。 [示例代码](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)演示如何通过不同的参数来使用 SDK。 对于存储指标，需使用 `0.18.0-preview` 或更高版本。
 
在这些示例中，将 `<resource-ID>` 占位符替换为整个存储帐户或队列存储服务的资源 ID。 你可以在 Azure 门户中存储帐户的“属性”页上找到这些资源 ID。

将 `<subscription-ID>` 占位符值替换为你的订阅 ID。 要查看有关如何获取 `<tenant-ID>`、`<application-ID>` 和 `<AccessKey>` 值的指南，请参阅[使用门户创建可访问资源的 Azure AD 应用程序和服务主体](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/)。 

#### <a name="list-the-account-level-metric-definition"></a>列出帐户级指标定义

以下示例演示如何列出帐户级别的指标定义：

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="reading-account-level-metric-values"></a>正在读取帐户级别的指标值

以下示例演示如何读取帐户级别的 `UsedCapacity` 数据：

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="reading-multidimensional-metric-values"></a>读取多维指标值

对于多维指标，如果需要读取基于特定维度值的指标数据，则需定义元数据筛选器。

以下示例演示如何根据支持多维的指标读取指标数据：

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for queue storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

---

## <a name="analyzing-logs"></a>分析日志

可以将资源日志作为存储帐户中的队列、事件数据或日志分析查询来访问。

有关这些日志中显示的字段的详细参考信息，请参阅 [Azure 队列存储监视数据参考](monitor-queue-storage-reference.md)。

> [!NOTE]
> Azure Monitor 中的 Azure 存储日志目前为公共预览版，可在所有公有云区域中进行预览测试。 若要注册预览版，请参阅[此页](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。 此预览版为常规用途 v1 和常规用途 v2 存储帐户中的 Blob（包括 Azure Data Lake Storage Gen2）、文件、队列、表和高级存储帐户启用日志。 经典存储帐户不受支持。

仅在针对服务终结点发出请求时才会创建日志条目。 例如，如果存储帐户在其队列终结点中具有活动，而在其表或 blob 终结点中没有活动，则仅创建与队列服务相关的日志。 Azure 存储日志包含有关成功和失败的存储服务请求的详细信息。 可以使用该信息监视各个请求和诊断存储服务问题。 将最大程度地记录请求。

### <a name="log-authenticated-requests"></a>记录经过身份验证的请求

 将记录以下类型的经过身份验证的请求：

- 成功的请求
- 失败的请求，包括超时、限制、网络、授权和其他错误
- 使用共享访问签名 (SAS) 或 OAuth 的请求，包括失败和成功的请求
- 对分析数据（$logs 容器中的经典日志数据和 $metric 表中的类指标数据）的请求 

队列存储服务本身发出的请求，如日志创建或删除，则不记录。 若要查看所记录数据的完整列表，请参阅[存储记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)和[存储日志格式](monitor-queue-storage-reference.md)。

### <a name="log-anonymous-requests"></a>记录匿名请求

 记录以下类型的匿名请求：

- 成功的请求
- 服务器错误
- 客户端和服务器的超时错误
- 失败的 GET 请求，错误代码为 304（未修改）

不会记录所有其他失败的匿名请求。 若要查看所记录数据的完整列表，请参阅[存储记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)和[存储日志格式](monitor-queue-storage-reference.md)。

### <a name="accessing-logs-in-a-storage-account"></a>访问存储帐户中的日志

日志显示为存储到目标存储帐户中的容器的 blob。 数据作为按行分隔的 JSON 有效负载进行收集并存储在单个 blob 中。 Blob 的名称遵循以下命名约定：

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/queueServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

下面是一个示例：

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>访问事件中心中的日志

发送到事件中心的日志并没有存储为文件，但你可以验证事件中心是否收到了日志信息。 在 Azure 门户中，请转到事件中心，然后验证“传入消息”计数是否大于零。 

![审核日志](media/monitor-queue-storage/event-hub-log.png)

你可以使用安全信息和事件管理以及监视工具来访问和读取发送到事件中心的日志数据。 有关详细信息，请参阅[可对发送到事件中心的监视数据执行什么操作？](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)。

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>访问 Log Analytics 工作区中的日志

你可以使用 Azure Monitor 日志查询来访问发送到 Log Analytics 工作区的日志。

有关详细信息，请参阅 [Azure Monitor 中的 Log Analytics 入门](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。

数据存储在 **StorageQueueLogs** 表中。  

#### <a name="sample-kusto-queries"></a>示例 Kusto 查询

您可以在 **日志搜索** 栏中输入一些查询来帮助您监视队列存储。 这些查询使用[新语言](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

> [!IMPORTANT]
> 从存储帐户资源组菜单中选择 **日志** 时，会打开 Log Analytics 并将查询范围设置为当前资源组。 这意味着日志查询只包含来自该资源组的数据。 如果要运行的查询包含来自其他资源或来自其他 Azure 服务的数据，请从 " **Azure Monitor** " 菜单中选择 "**日志**"。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](/azure/azure-monitor/log-query/scope/)。

使用以下查询可帮助你监视 Azure 存储帐户：

* 列出最近三天内 10 个最常见的错误。

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* 列出最近三天内导致大部分错误的前 10 个操作。

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* 列出最近三天内端到端延迟最长的前 10 个操作。

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* 列出最近三天内导致服务器端限制错误的所有操作。

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* 列出最近三天内使用匿名访问的所有请求。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* 创建最近三天内使用的操作的饼图。
    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>常见问题解答

**Azure 存储是否支持托管磁盘或非托管磁盘的指标？**

否。 Azure 计算支持磁盘上的指标。 有关详细信息，请参阅 [托管和非托管磁盘的每个磁盘指标](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)。

## <a name="next-steps"></a>后续步骤

- 有关 Azure 队列存储创建的日志和指标的参考，请参阅 [Azure 队列存储监视数据参考](monitor-queue-storage-reference.md)。
- 要了解如何监视 Azure 资源，请参阅[使用 Azure Monitor 监视 Azure 资源](../../azure-monitor/insights/monitor-azure-resource.md)。
- 要详细了解指标迁移，请参阅 [Azure 存储指标迁移](../common/storage-metrics-migration.md)。
