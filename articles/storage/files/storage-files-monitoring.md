---
title: 监视 Azure 文件 |Microsoft Docs
description: 了解如何监视 Azure 文件的性能和可用性。 监视 Azure 文件数据、了解配置以及分析指标和日志数据。
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 4b2f819edd875130c57d487536691b4588dcc71f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91772662"
---
# <a name="monitoring-azure-files"></a>监视 Azure 文件

如果你有依赖 Azure 资源的关键应用程序和业务流程，则需要监视这些资源的可用性、性能和操作。 本文介绍 Azure 文件生成的监视数据，以及如何使用 Azure Monitor 的功能来分析此数据的警报。

> [!NOTE]
> Azure Monitor 中的 Azure 存储日志目前为公共预览版，可在所有公有云区域中进行预览测试。 若要注册预览版，请参阅[此页](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。 此预览版启用 blob 的日志 (包括 Azure Data Lake Storage Gen2) 、文件、队列和表。 此功能适用于使用 Azure 资源管理器部署模型创建的所有存储帐户。 请参阅 [存储帐户概述](../common/storage-account-overview.md)。

## <a name="monitor-overview"></a>Monitor 概述

每个 Azure 文件资源的 Azure 门户中的 " **概述** " 页包括资源使用情况的简要视图，例如请求和每小时计费。 这些信息非常有用，但只提供少量监视数据。 其中的某些数据是自动收集的，并且可在创建资源后立即进行分析。 你可以使用某些配置启用其他数据收集类型。

## <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？
Azure 文件使用 [Azure Monitor](../../azure-monitor/overview.md)，这是 azure 中的一个完整的堆栈监视服务来创建监视数据。 Azure Monitor 提供了一整套用于监视 Azure 资源以及其他云和本地资源的功能。 

请从 [通过 Azure Monitor 监视 Azure 资源](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)一文开始，其中介绍了以下内容：

- 说明是 Azure Monitor？
- 与监视相关的成本
- 监视 Azure 中收集的数据
- 配置数据收集
- Azure 中用于分析监视数据并就其发出警报的标准工具

以下各节介绍了从 Azure 文件收集的特定数据。 其中的示例演示了如何配置数据收集并通过 Azure 工具分析这些数据。

## <a name="monitoring-data"></a>监视数据

Azure 文件收集与其他 Azure 资源相同的监视数据，如 [监视 Azure 资源的数据](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)中所述。 

有关 Azure 文件创建的指标和日志指标的详细信息，请参阅 [Azure 文件监视数据参考](storage-files-monitoring-reference.md) 。

Azure Monitor 中的指标和日志仅支持 Azure 资源管理器存储帐户。 Azure Monitor 不支持经典存储帐户。 如果要使用经典存储帐户上的指标或日志，则需要迁移到 Azure 资源管理器存储帐户。 请参阅[迁移到 Azure 资源管理器](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)。

## <a name="collection-and-routing"></a>集合和路由

平台指标和活动日志是自动收集的，但可以使用诊断设置将其路由到其他位置。 必须创建诊断设置以收集资源日志。 

> [!NOTE]
> Azure Monitor 中的 Azure 存储日志目前为公共预览版，可在所有公有云区域中进行预览测试。 若要注册预览版，请参阅[此页](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。 此预览版为常规用途 v1 和常规用途 v2 存储帐户中的 Blob（包括 Azure Data Lake Storage Gen2）、文件、队列、表和高级存储帐户启用日志。 经典存储帐户不受支持。

若要使用 Azure 门户、Azure CLI 或 PowerShell 创建诊断设置，请参阅 [创建诊断设置以在 Azure 中收集平台日志和指标](../../azure-monitor/platform/diagnostic-settings.md)。 

若要查看创建诊断设置的 Azure 资源管理器模板，请参阅 [Azure 存储的诊断设置](https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings#diagnostic-setting-for-azure-storage)。

创建诊断设置时，请选择要为其启用日志的存储类型，如 blob、队列、表或文件。 对于 Azure 文件，请选择 " **文件**"。 

如果在 Azure 门户中创建诊断设置，则可以从列表中选择资源。 如果使用 PowerShell 或 Azure CLI，需要使用 Azure 文件终结点的资源 ID。 通过打开存储帐户的“属性”页，可在 Azure 门户中找到资源 ID。

您还必须指定要为其收集日志的下列操作之一。 

| 类别 | 说明 |
|:---|:---|
| StorageRead | 对象上的读取操作。 |
| StorageWrite | 对象上的写入操作。 |
| StorageDelete | 对象上的删除操作。 |

若要获取记录的 SMB 和 REST 操作的列表，请参阅 [存储记录的操作和状态消息](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) 和 [Azure 文件监视数据引用](storage-files-monitoring-reference.md)。

## <a name="analyzing-metrics"></a>分析指标

你可以使用指标资源管理器通过其他 Azure 服务中的指标分析 Azure 存储的指标。 从 Azure Monitor 菜单中选择“指标”，可打开指标资源管理器 。 有关使用此工具的详细信息，请参阅 [Azure 指标资源管理器入门](../../azure-monitor/platform/metrics-getting-started.md)。 

对于支持维度的指标，可使用所需的维度值筛选指标。  有关 Azure 存储支持的维度的完整列表，请参阅[指标维度](storage-files-monitoring-reference.md#metrics-dimensions)。 Azure 文件的指标位于以下命名空间中： 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/fileServices

有关包括 Azure 文件的所有 Azure Monitor 支持指标的列表，请参阅 [Azure Monitor 支持的指标](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsfileservices)。

### <a name="accessing-metrics"></a>访问指标

> [!TIP]
> 若要查看 Azure CLI 或 .NET 示例，请选择此处列出的相应选项卡。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>列出指标定义

可以列出存储帐户或 Azure 文件服务的指标定义。 请使用 [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition) cmdlet。

在此示例中，将 `<resource-ID>` 占位符替换为整个存储帐户的资源 id 或 Azure 文件服务的资源 id。  你可以在 Azure 门户中存储帐户的“属性”页上找到这些资源 ID。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>读取指标值

你可以读取存储帐户或 Azure 文件服务的帐户级别指标值。 使用 [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric) cmdlet。

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>列出帐户级指标定义

可以列出存储帐户或 Azure 文件服务的指标定义。 使用 [az monitor metrics list-definitions](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) 命令。
 
在此示例中，将 `<resource-ID>` 占位符替换为整个存储帐户的资源 id 或 Azure 文件服务的资源 id。 你可以在 Azure 门户中存储帐户的“属性”页上找到这些资源 ID。

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>读取帐户级指标值

你可以读取存储帐户或 Azure 文件服务的指标值。 请使用 [az monitor metrics list](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list) 命令。

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor 提供 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)，用于读取指标定义和值。 [示例代码](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)演示如何通过不同的参数来使用 SDK。 对于存储指标，需使用 `0.18.0-preview` 或更高版本。
 
在这些示例中，将 `<resource-ID>` 占位符替换为整个存储帐户或 Azure 文件服务的资源 ID。 你可以在 Azure 门户中存储帐户的“属性”页上找到这些资源 ID。

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

#### <a name="reading-account-level-metric-values"></a>读取帐户级别指标值

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
        // Resource ID for Azure Files
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default";
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

你可以将资源日志作为存储帐户中的 blob 以及事件数据进行访问，也可以通过 Log Analytics 查询访问资源日志。

若要获取记录的 SMB 和 REST 操作的列表，请参阅 [存储记录的操作和状态消息](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) 和 [Azure 文件监视数据引用](storage-files-monitoring-reference.md)。

> [!NOTE]
> Azure Monitor 中的 Azure 存储日志目前为公共预览版，可在所有公有云区域中进行预览测试。 若要注册预览版，请参阅[此页](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。 此预览版为常规用途 v1 和常规用途 v2 存储帐户中的 Blob（包括 Azure Data Lake Storage Gen2）、文件、队列、表和高级存储帐户启用日志。 经典存储帐户不受支持。

仅在针对服务终结点发出请求时才会创建日志条目。 例如，如果存储帐户在其文件终结点中具有活动，而在其表或队列终结点中没有活动，则仅创建与文件服务相关的日志。 Azure 存储日志包含有关成功和失败的存储服务请求的详细信息。 可以使用该信息监视各个请求和诊断存储服务问题。 将最大程度地记录请求。

### <a name="log-authenticated-requests"></a>记录经过身份验证的请求

 将记录以下类型的经过身份验证的请求：

- 成功的请求
- 失败的请求，包括超时、限制、网络、授权和其他错误
- 使用共享访问签名 (SAS) 或 OAuth 的请求，包括失败和成功的请求
- 对分析数据（$logs 容器中的经典日志数据和 $metric 表中的类指标数据）的请求 

不会记录 Azure 文件服务本身发出的请求，如日志创建或删除。 有关记录的 SMB 和 REST 请求的完整列表，请参阅 [存储记录的操作和状态消息](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) 和 [Azure 文件监视数据参考](storage-files-monitoring-reference.md)。

### <a name="log-anonymous-requests"></a>记录匿名请求

 记录以下类型的匿名请求：

- 成功的请求
- 服务器错误
- 客户端和服务器的超时错误
- 失败的 GET 请求，错误代码为 304（未修改）

不会记录所有其他失败的匿名请求。 有关记录的 SMB 和 REST 请求的完整列表，请参阅 [存储记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) 和 [Azure 文件监视数据参考](storage-files-monitoring-reference.md)。

### <a name="accessing-logs-in-a-storage-account"></a>访问存储帐户中的日志

日志显示为存储到目标存储帐户中的容器的 blob。 数据作为按行分隔的 JSON 有效负载进行收集并存储在单个 blob 中。 Blob 的名称遵循以下命名约定：

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

下面是一个示例：

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>访问事件中心内的日志

发送到事件中心的日志并没有存储为文件，但你可以验证事件中心是否收到了日志信息。 在 Azure 门户中，请转到事件中心，然后验证“传入消息”计数是否大于零。 

![审核日志](media/storage-files-monitoring/event-hub-log.png)

你可以使用安全信息和事件管理以及监视工具来访问和读取发送到事件中心的日志数据。 有关详细信息，请参阅[可对发送到事件中心的监视数据执行什么操作？](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)。

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>访问 Log Analytics 工作区中的日志

你可以使用 Azure Monitor 日志查询来访问发送到 Log Analytics 工作区的日志。 数据存储在 **StorageFileLogs** 表中。 

有关详细信息，请参阅 [Azure Monitor 中的 Log Analytics 入门](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。

#### <a name="sample-kusto-queries"></a>示例 Kusto 查询

下面是一些可以在 **日志搜索** 栏中输入以帮助你监视 Azure 文件的查询。 这些查询使用[新语言](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

> [!IMPORTANT]
> 从存储帐户资源组菜单中选择 **日志** 时，会打开 Log Analytics 并将查询范围设置为当前资源组。 这意味着日志查询只包含来自该资源组的数据。 如果要运行的查询包含来自其他资源或来自其他 Azure 服务的数据，请从 " **Azure Monitor** " 菜单中选择 "**日志**"。 有关详细信息，请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](/azure/azure-monitor/log-query/scope/)。

使用以下查询来帮助你监视 Azure 文件共享：

- 查看过去一周的 SMB 错误

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- 创建在过去一周内的 SMB 操作饼图

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- 在过去一周内查看 REST 错误

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- 在上一周创建 REST 操作饼图

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

若要查看 Azure 文件的列名和说明的列表，请参阅 [StorageFileLogs](https://docs.microsoft.com/azure/azure-monitor/reference/tables/storagefilelogs)。

有关如何编写查询的详细信息，请参阅 [教程： Log Analytics 查询入门](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。

## <a name="alerts"></a>警报

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 它们允许你确定和解决你的系统中的问题，然后客户就会注意到它们。 你可以对 [指标](/azure/azure-monitor/platform/alerts-metric-overview)、 [日志](/azure/azure-monitor/platform/alerts-unified-log)和 [活动日志](/azure/azure-monitor/platform/activity-log-alerts)设置警报。 

下表列出了一些要监视的示例方案和用于警报的适当指标：

| 方案 | 用于警报的指标 |
|-|-|
| 文件共享受到限制。 | 度量值：事务<br>维度名称：响应类型 <br>维度名称：文件共享 (高级文件共享仅)  |
| 文件共享大小为容量的80%。 | 指标：文件容量<br>维度名称：文件共享 (高级文件共享仅)  |
| 文件共享出口在一天内超过 500 GiB。 | 指标：出口<br>维度名称：文件共享 (高级文件共享仅)  |

### <a name="how-to-create-alerts-for-azure-files"></a>如何创建 Azure 文件的警报

1. 在 Azure 门户 中转到自己的存储帐户。 

2. 单击 " **警报** "，然后单击 " **+ 新建警报规则**"。

3. 单击 " **编辑资源**"，选择 **文件资源类型** ，然后单击 " **完成**"。 

4. 单击 " **选择条件** " 并为警报提供以下信息： 

    - **指标**
    - **维度名称**
    - **警报逻辑**

5. 单击 " **选择操作组** "，并将操作组 (电子邮件、短信等 ) 添加到警报，方法是选择现有操作组或创建新的操作组。

6. 填写警报 **详细信息** ，如 **警报规则名称**、 **描述**和 **严重性**。

7. 单击“创建警报规则”以创建警报。

> [!NOTE]  
> 如果创建了警报，但该警报太干扰，请调整阈值和警报逻辑。

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>如何创建文件共享受到限制时的警报

1. 在 Azure 门户 中转到自己的存储帐户。
2. 在 " **监视** " 部分中，单击 " **警报**"，然后单击 " **+ 新建警报规则**"。
3. 单击“编辑资源”，为存储帐户选择“文件资源类型”，然后单击“完成”。 例如，如果存储帐户名称为，则 `contoso` 选择 `contoso/file` 资源。
4. 单击“选择条件”以添加条件。
5. 你将看到存储帐户支持的信号列表，请选择“事务”指标。
6. 在“配置信号逻辑”边栏选项卡上，单击“维度名称”下拉列表，然后选择“响应类型”。
7. 单击“维度值”下拉列表，并选择“SuccessWithThrottling”（对于 SMB）或“ClientThrottlingError”（对于 REST）。

   > [!NOTE]
   > 如果 SuccessWithThrottling 或 ClientThrottlingError 维度值未列出，则意味着资源尚未受到限制。 若要添加维度值，请单击 "**维度值**" 下拉箭头旁边的 "**添加自定义值**"，键入 " **SuccessWithThrottling** " 或 " **ClientThrottlingError**"，然后单击 **"确定"** ，然后重复步骤 #7。

8. 单击“维度名称”下拉列表并选择“文件共享”。
9. 单击“维度值”下拉列表，并选择要对其发出警报的文件共享。

   > [!NOTE]
   > 如果文件共享是标准文件共享，请选择“所有当前值和将来值”。 “维度值”下拉列表不会列出文件共享，因为每共享指标不可用于标准文件共享。 如果存储帐户中的任何文件共享受到限制，则会触发标准文件共享的限制警报，并且警报不会识别哪个文件共享受到限制。 因为每共享指标不可用于标准文件共享，所以建议为每个存储帐户使用一个文件共享。

10. 定义“警报参数”（阈值、运算符、聚合粒度和评估频率），然后单击“完成”。

    > [!TIP]
    > 如果你使用的是静态阈值，并且文件共享当前受到限制，则可通过指标图表来确定合理的阈值。 如果使用的是动态阈值，则指标图表将显示基于最新数据计算出的阈值。

11. 单击“选择操作组”，通过选择现有操作组或创建新的操作组，将一个操作组（电子邮件、短信等）添加到警报中。
12. 填写警报 **详细信息** ，如 **警报规则名称**、* * 说明和 **严重性**。
13. 单击“创建警报规则”以创建警报。

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>如何在 Azure 文件共享大小为容量的80% 时创建警报

1. 在 Azure 门户 中转到自己的存储帐户。
2. 在 " **监视** " 部分中，单击 " **警报** "，然后单击 " **+ 新建警报规则**"。
3. 单击“编辑资源”，为存储帐户选择“文件资源类型”，然后单击“完成”。 例如，如果存储帐户名称为，则 `contoso` 选择 `contoso/file` 资源。
4. 单击“选择条件”以添加条件。
5. 你会看到存储帐户支持的信号列表，请选择 " **文件容量** " 指标。
6. 在 " **配置信号逻辑** " 边栏选项卡上，单击 " **维度名称** " 下拉箭头，然后选择 " **文件共享**"。
7. 单击“维度值”下拉列表，并选择要对其发出警报的文件共享。

   > [!NOTE]
   > 如果文件共享是标准文件共享，请选择“所有当前值和将来值”。 “维度值”下拉列表不会列出文件共享，因为每共享指标不可用于标准文件共享。 标准文件共享的警报基于存储帐户中的所有文件共享。 因为每共享指标不可用于标准文件共享，所以建议为每个存储帐户使用一个文件共享。

8. 输入 **阈值** （以字节为单位）。 例如，如果文件共享大小为 100 TiB，并且你希望在文件共享大小为容量80% 时收到警报，则阈值为87960930222080。
9. 定义其余的 **警报参数** (聚合粒度和计算频率) 并单击 " **完成**"。
10. 单击“选择操作组”，通过选择现有操作组或创建新的操作组，将一个操作组（电子邮件、短信等）添加到警报中。
11. 填写警报 **详细信息** ，如 **警报规则名称**、* * 说明和 **严重性**。
12. 单击“创建警报规则”以创建警报。

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>如果 Azure 文件共享出口在一天内超过 500 GiB，如何创建警报

1. 在 Azure 门户 中转到自己的存储帐户。
2. 在“监视”部分中单击“警报”，然后单击“+ 新建警报规则”。 
3. 单击“编辑资源”，为存储帐户选择“文件资源类型”，然后单击“完成”。 例如，如果存储帐户名称为“contoso”，则选择“contoso/文件”资源。
4. 单击“选择条件”以添加条件。
5. 你会看到存储帐户支持的信号列表，请选择 **出口** 指标。
6. 在 " **配置信号逻辑** " 边栏选项卡上，单击 " **维度名称** " 下拉箭头，然后选择 " **文件共享**"。
7. 单击“维度值”下拉列表，并选择要对其发出警报的文件共享。

   > [!NOTE]
   > 如果文件共享是标准文件共享，请选择“所有当前值和将来值”。 “维度值”下拉列表不会列出文件共享，因为每共享指标不可用于标准文件共享。 标准文件共享的警报基于存储帐户中的所有文件共享。 因为每共享指标不可用于标准文件共享，所以建议为每个存储帐户使用一个文件共享。

8. 为 "阈值" 输入 **536870912000** 字节。 
9. 单击 " **聚合粒度** " 下拉箭头，然后选择 " **24 小时**"。
10. 选择 **评估频率** ，并 **单击 "完成**"。
11. 单击“选择操作组”，通过选择现有操作组或创建新的操作组，将一个操作组（电子邮件、短信等）添加到警报中。
12. 填写警报 **详细信息** ，如 **警报规则名称**、* * 说明和 **严重性**。
13. 单击“创建警报规则”以创建警报。

## <a name="next-steps"></a>后续步骤

- [Azure 文件监视数据引用](storage-files-monitoring.md)
- [利用 Azure Monitor 监视 Azure 资源](../../azure-monitor/insights/monitor-azure-resource.md)
- [Azure 存储指标迁移](../common/storage-metrics-migration.md)
- [规划 Azure 文件部署](https://docs.microsoft.com/azure/storage/files/storage-files-planning)
- [如何部署 Azure 文件](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)
- [排查 Windows 上的 Azure 文件问题](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems)
- [排查 Linux 上的 Azure 文件问题](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-linux-file-connection-problems)
