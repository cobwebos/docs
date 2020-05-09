---
title: 监视 Azure 存储 |Microsoft Docs
description: 了解如何监视 Azure 存储的性能和可用性。
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 5564634471045838dae3344dc883b6fdc203711e
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82722916"
---
# <a name="monitoring-azure-storage"></a>监视 Azure 存储

如果有依赖于 Azure 资源的关键应用程序和业务流程，则需要监视这些资源的可用性、性能和操作。 本文介绍 Azure 存储生成的监视数据，以及如何使用 Azure Monitor 的功能来分析此数据的警报。

> [!NOTE]
> Azure Monitor 中的 Azure 存储日志提供公共预览版，可用于所有公有云区域中的预览版测试。 若要注册预览版，请参阅[此页](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。  此预览版启用常规用途 v1 和常规用途 v2 存储帐户中的 blob （包括 Azure Data Lake Storage Gen2）、文件、队列、表、高级存储帐户的日志。 不支持经典存储帐户。

## <a name="monitor-overview"></a>监视器概述

每个存储资源的 Azure 门户中的 "**概述**" 页包含资源使用情况的简要视图，包括其请求和每小时计费。 这是有用的信息，但只提供少量的监视数据。 其中的某些数据是自动收集的，并且在创建存储资源后即可进行分析。 可以使用某些配置启用其他类型的数据收集。

## <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？
Azure 存储使用[Azure Monitor](../../azure-monitor/overview.md) ，这是 azure 中的一个完整的堆栈监视服务，它可提供一组完整的功能来监视 azure 资源以及其他云中和本地的资源，从而创建监视数据。 

首先介绍如何[Azure Monitor 来监视 Azure 资源，](../../azure-monitor/insights/monitor-azure-resource.md)其中介绍了以下内容：

- 说明是 Azure Monitor？
- 与监视相关的成本
- 监视 Azure 中收集的数据
- 配置数据收集
- Azure 中用于分析和警报监视数据的标准工具

以下各节将介绍从 Azure 存储中收集的特定数据，并提供有关使用 Azure tools 配置数据收集和分析此数据的示例。

## <a name="monitoring-data-from-azure-storage"></a>监视 Azure 存储中的数据

Azure 存储会收集与其他 Azure 资源相同的监视数据，如[监视 Azure 资源](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)中的数据中所述。 有关 Azure 存储创建的日志和指标的详细参考信息，请参阅[Azure 存储监视数据参考](monitor-storage-reference.md)。

Azure Monitor 中的指标和日志仅支持 Azure 资源管理器存储帐户。 Azure Monitor 不支持经典存储帐户。 如果要在经典存储帐户上使用指标或日志，则需要迁移到 Azure 资源管理器存储帐户。 请参阅[迁移到 Azure 资源管理器](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)。

如果需要，可以继续使用经典指标和日志。 事实上，经典指标和日志与 Azure Monitor 中的指标和日志同时提供。 此支持将保持不变，直到 Azure 存储在旧指标和日志中结束服务。 

### <a name="logs-in-azure-monitor-preview"></a>Azure Monitor （预览版）中的日志

仅在针对服务终结点发出请求时才会创建日志条目。 例如，如果存储帐户的 blob 终结点中存在活动，而在其表或队列终结点中没有活动，则仅创建与 blob 服务有关的日志。 Azure 存储日志包含有关成功和失败的存储服务请求的详细信息。 可以使用该信息监视各个请求和诊断存储服务问题。 将最大程度地记录请求。

#### <a name="logging-authenticated-requests"></a>记录经过身份验证的请求

 将记录以下类型的已经过身份验证的请求：

- 成功的请求
- 失败的请求，包括超时、限制、网络、授权和其他错误
- 使用共享访问签名 (SAS) 或 OAuth 的请求，包括失败和成功的请求。
- 对分析数据的请求（ **$logs**容器中的经典日志数据和 **$metric**表中的类指标数据）

不会记录存储服务本身发出的请求，如创建或删除日志。 [存储记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)和[存储日志格式](monitor-storage-reference.md)主题中记录了记录数据的完整列表。

#### <a name="logging-anonymous-requests"></a>记录匿名请求

 将记录以下类型的匿名请求：

- 成功的请求
- 服务器错误
- 客户端和服务器的超时错误
- 失败的 GET 请求，错误代码为 304（未修改）

不会记录所有其他失败的匿名请求。 [存储记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)和[存储日志格式](monitor-storage-reference.md)主题中记录了记录数据的完整列表。

## <a name="configuration"></a>配置

平台指标和活动日志会自动收集，但你必须创建诊断设置以收集资源日志，或将其转发到 Azure Monitor 之外。 有关使用 Azure 门户、CLI 或 PowerShell 创建诊断设置的详细过程，请参阅[创建诊断设置以在 Azure 中收集平台日志和指标](../../azure-monitor/platform/diagnostic-settings.md)。

创建诊断设置时，需要选择要为其启用日志的存储类型（blob、队列、表、文件）。 如果在 Azure 门户中创建诊断设置，则可以从列表中选择资源。 如果使用 PowerShell 或 Azure CLI，则需要使用存储类型的资源 ID。 可以通过打开存储帐户的 "**属性**" 页来查找 Azure 门户中的资源 ID。

您还必须指定要为其收集日志的操作类别。 下表列出了 Azure 存储的类别：

| 类别 | 说明 |
|:---|:---|
| StorageRead | 对 blob 的读取操作。  |
| StorageWrite | Blob 上的写入操作。 |
| StorageDelete | 对 blob 的删除操作。 |

## <a name="analyzing-metric-data"></a>分析指标数据

可以使用指标资源管理器，通过其他 Azure 服务中的指标分析 Azure 存储的指标。 从 " **Azure Monitor** " 菜单中选择 "**指标**"，打开指标资源管理器。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../../azure-monitor/platform/metrics-getting-started.md)。 

以下示例演示如何查看帐户级别的**事务**。

![在 Azure 门户中访问指标的屏幕截图](./media/monitor-storage/access-metrics-portal.png)

对于支持维度的指标，可使用所需的维度值筛选指标。 下面的示例演示了如何通过选择“API 名称”维度的值，在特定操作上查看帐户级别的“事务”********。

![在 Azure 门户中访问包含维度的指标的屏幕截图](./media/monitor-storage/access-metrics-portal-with-dimension.png)

有关 Azure 存储支持的维度的完整列表，请参阅[度量值维度](monitor-storage-reference.md#metrics-dimensions)。

Azure 存储的所有指标位于以下命名空间中：

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices

有关所有 Azure Monitor 支持指标（包括 Azure 存储）的列表，请参阅[Azure Monitor 支持的指标](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)。


### <a name="access-metrics"></a>访问指标

> [!TIP]
> 若要查看 Azure CLI 或 .NET 示例，请选择下面相应的选项卡。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>列出指标定义

可以列出存储帐户的指标定义，或单个存储服务（如 blob、文件、表或队列服务）。 使用[AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0) cmdlet。

在此示例中，将`<resource-ID>`占位符替换为整个存储帐户的资源 id，或者替换为单个存储服务（如 blob、文件、表或队列服务）的资源 id。 可以在 Azure 门户的存储帐户的 "**属性**" 页中找到这些资源 id。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="read-metric-values"></a>读取指标值

你可以读取存储帐户的帐户级别指标值，或者读取单个存储服务（如 blob、文件、表或队列服务）。 使用[AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0) cmdlet。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-account-level-metric-definition"></a>列出帐户级别指标定义

可以列出存储帐户的指标定义，或单个存储服务（如 blob、文件、表或队列服务）。 使用[az monitor 度量列表-定义](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions)命令。
 
在此示例中，将`<resource-ID>`占位符替换为整个存储帐户的资源 id，或者替换为单个存储服务（如 blob、文件、表或队列服务）的资源 id。 可以在 Azure 门户的存储帐户的 "**属性**" 页中找到这些资源 id。

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>读取帐户级别的指标值

你可以读取存储帐户的指标值，或者读取单个存储服务（如 blob、文件、表或队列服务）。 使用[az monitor 公制 list](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)命令。

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor 提供[.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)来读取指标定义和值。 [示例代码](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)演示如何通过不同的参数来使用 SDK。 对于存储指标，需使用 `0.18.0-preview` 或更高版本。
 
在这些示例中，将`<resource-ID>`占位符替换为整个存储帐户的资源 id，或者替换为单个存储服务（如 blob、文件、表或队列服务）的资源 id。 可以在 Azure 门户的存储帐户的 "**属性**" 页中找到这些资源 id。

将该`<subscription-ID>`变量替换为你的订阅的 ID。  有关`<tenant-ID>`如何获取`<application-ID>`、和`<AccessKey>`的值的指南，请参阅[如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/)。 

#### <a name="list-account-level-metric-definition"></a>列出帐户级别指标定义

下面的示例演示如何在帐户级别列出指标定义：

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

#### <a name="read-account-level-metric-values"></a>读取帐户级别的指标值

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

#### <a name="read-multi-dimensional-metric-values"></a>读取多维指标值

对于多维指标，如果需要读取基于特定维度值的指标数据，则需定义元数据筛选器。

以下示例演示如何根据支持多维的指标读取指标数据：

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
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

## <a name="analyzing-log-data"></a>分析日志数据

可以在存储帐户中以 blob 的形式访问资源日志，也可以通过日志分析查询访问资源日志。

有关这些日志中显示的字段的详细参考信息，请参阅[Azure 存储监视数据参考](monitor-storage-reference.md)。

> [!NOTE]
> Azure Monitor 中的 Azure 存储日志提供公共预览版，可用于所有公有云区域中的预览版测试。 若要注册预览版，请参阅[此页](https://www.microsoft.com)。  此预览版启用常规用途 v1 和常规用途 v2 存储帐户中的 blob （包括 Azure Data Lake Storage Gen2）、文件、队列、表、高级存储帐户的日志。 不支持经典存储帐户。

### <a name="access-logs-in-a-storage-account"></a>在存储帐户中访问日志

日志显示为存储到目标存储帐户中的容器的 blob。 数据在单个 blob 内收集并存储为以行分隔的 JSON 有效负载。 Blob 的名称遵循以下命名约定：

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

下面是一个示例：

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="access-logs-in-event-hub"></a>在事件中心访问日志

发送到事件中心的日志不存储为文件，但可以验证事件中心是否收到了日志信息。 在 Azure 门户中，导航到事件中心，然后验证**传入消息**数是否大于零。 

![审核日志](media/monitor-storage/event-hub-log.png)

你可以使用安全信息和事件管理和监视工具来访问和读取发送到事件中心的日志数据。 有关详细信息，请参阅[如何对发送到事件中心的监视数据执行哪些操作？](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)。

### <a name="access-logs-in-log-analytics-workspace"></a>Log Analytics 工作区中的访问日志

您可以通过使用 Azure Monitor 日志查询来访问发送到 Log Analytics 工作区的日志。

请参阅[Azure Monitor 中的 Log Analytics 入门](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。

数据存储在下表中。

| 表 | 说明 |
|:---|:---|
|StorageBlobLogs | 描述 blob 存储中的活动的日志。 |
|StorageFileLogs | 描述文件共享中的活动的日志。 |
|StorageQueueLogs | 描述队列中活动的日志。|
|StorageTableLogs| 描述表中的活动的日志。|

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Azure Monitor 中的 Azure 存储 Log Analytics 查询

可以在 "**日志搜索**" 搜索栏中输入一些查询，以帮助监视 Azure 存储帐户。 这些查询使用[新语言](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

下面是一些可用于帮助你监视 Azure 存储帐户的查询。

* 列出过去3天内10个最常见的错误。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* 列出在过去3天内导致大多数错误的前10个操作。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* 列出过去3天内具有最长的端到端延迟的前10个操作。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* 列出导致过去3天发生服务器端限制错误的所有操作。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* 列出过去3天内使用匿名访问的所有请求。

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* 创建在过去3天内使用的操作饼图。
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>常见问题解答

**Azure 存储是否支持托管磁盘或非托管磁盘的指标？**

否，Azure 计算支持磁盘上的指标。 有关详细信息，请参阅此[文章](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)。

## <a name="next-steps"></a>后续步骤

- [Azure 存储监视数据参考](monitor-storage-reference.md)，了解 Azure 存储空间创建的日志和指标。
- 有关监视 Azure 资源的详细信息，请[Azure Monitor 监视 azure 资源](../../azure-monitor/insights/monitor-azure-resource.md)。
- [Azure 存储指标迁移](./storage-metrics-migration.md)

