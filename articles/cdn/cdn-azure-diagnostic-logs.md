---
title: 诊断日志
titleSuffix: Azure Content Delivery Network
description: 客户可为 Azure CDN 启用日志分析。
services: cdn
author: asudbring
manager: KumudD
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: allensu
ms.openlocfilehash: dbaba67a163bb0f948de5ba2ebbdba5497ad5ff9
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116975"
---
# <a name="diagnostic-logs---azure-content-delivery-network"></a>诊断日志-Azure 内容分发网络

使用 Azure 诊断日志，可以查看核心分析并将其保存到一个或多个目标，包括：

* Azure 存储帐户
* Log Analytics 工作区
* Azure 事件中心

此功能在所有定价层的 CDN 终结点上提供。 

使用诊断日志可以将基本使用情况指标从 CDN 终结点导出到不同种类的源，以便以自定义方式使用它们。 您可以执行以下类型的数据导出：

* 将数据导出到 Blob 存储、导出为CSV，并在 Excel 中生成图形。
* 将数据导出到事件中心，并关联到其他 Azure 服务的数据。
* 将数据导出到 Azure Monitor 日志，并在自己的 Log Analytics 工作区中查看数据

需要 Azure CDN 配置文件才能执行以下步骤。 继续之前，请参阅[创建 Azure CDN 配置文件和终结点](cdn-create-new-endpoint.md)。

## <a name="enable-logging-with-the-azure-portal"></a>使用 Azure 门户启用日志记录

按照以下步骤为你的 Azure CDN 终结点启用日志记录：

1. 登录 [Azure 门户](https://portal.azure.com)。 

2. 在 Azure 门户中，导航到**All resources**  ->  **你的 cdn 配置文件**中的所有资源

2. 选择要为其启用诊断日志的 CDN 终结点：

    :::image type="content" source="./media/cdn-diagnostics-log/02_browse-to-diagnostics-logs.png" alt-text="选择 "CDN 终结点"。" border="true":::

3. 在 "**监视**" 部分选择 "**诊断日志**"：

    :::image type="content" source="./media/cdn-diagnostics-log/03_diagnostics-logs-options.png" alt-text="选择 "诊断日志"。" border="true":::

### <a name="enable-logging-with-azure-storage"></a>使用 Azure 存储启用日志记录

若要使用存储帐户来存储日志，请执行以下步骤：

 >[!NOTE] 
 >若要完成这些步骤，需要一个存储帐户。 有关详细信息，请参阅：**[创建 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)**。
    
1. 对于 "**诊断设置名称**"，请输入诊断日志设置的名称。
 
2. 选择“存档到存储帐户”****，然后选择“CoreAnalytics”****。 

3. 对于“保留期(天数)”****，请选择保留天数。 如果保留期为 0 天，则会无限期存储日志。 

4. 选择日志的订阅和存储帐户。

    :::image type="content" source="./media/cdn-diagnostics-log/04_diagnostics-logs-storage.png" alt-text="诊断日志-存储。" border="true":::

3. 选择“保存” 。

### <a name="send-to-log-analytics"></a>发送到 Log Analytics

若要对日志使用 Log Analytics，请执行以下步骤：

>[!NOTE] 
>需要使用 log analytics 工作区才能完成这些步骤。 有关详细信息，请参阅：**[在 Azure 门户中创建 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)**。
    
1. 对于 "**诊断设置名称**"，请输入诊断日志设置的名称。

2. 选择 "**发送到 Log Analytics**"，然后选择 " **CoreAnalytics**"。 

3. 为日志选择 "订阅" 和 "Log Analytics" 工作区。

   :::image type="content" source="./media/cdn-diagnostics-log/05-la-workspace.png" alt-text="诊断日志-Log Analytics。" border="true":::

4. 选择“保存” 。

### <a name="stream-to-an-event-hub"></a>流式传输到事件中心

若要将事件中心用于日志，请执行以下步骤：

>[!NOTE] 
>需要使用事件中心才能完成这些步骤。 有关详细信息，请参阅：**[快速入门：使用 Azure 门户创建事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)**。
    
1. 对于 "**诊断设置名称**"，请输入诊断日志设置的名称。

2. 选择 "**流式传输到事件中心**"，然后选择 " **CoreAnalytics**"。 

3. 选择日志的订阅和事件中心命名空间。

   :::image type="content" source="./media/cdn-diagnostics-log/06-eventhub-namespace.png" alt-text="诊断日志-事件中心。" border="true":::

4. 选择“保存” 。


## <a name="enable-logging-with-powershell"></a>使用 PowerShell 启用日志记录

以下示例演示了如何通过 Azure PowerShell Cmdlet 启用诊断日志。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>在存储帐户中启用诊断日志

1. 登录到 Azure PowerShell：

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. 若要在存储帐户中启用诊断日志，请输入以下命令。 将变量替换为值：

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Categories CoreAnalytics
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>为 Log Analytics 工作区启用诊断日志

1. 登录到 Azure PowerShell：

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. 若要为 Log Analytics 工作区启用诊断日志，请输入以下命令。 将变量替换为值：

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Categories CoreAnalytics
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>为事件中心命名空间启用诊断日志

1. 登录到 Azure PowerShell：

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. 若要为 Log Analytics 工作区启用诊断日志，请输入以下命令。 将变量替换为值：

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $eventhubname = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhubname -Enabled $true -Categories CoreAnalytics
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>从 Azure 存储中使用诊断日志
本部分介绍 Azure 存储帐户中的 CDN 核心分析和组织的架构，并提供用于将日志下载到 CSV 文件的示例代码。

### <a name="using-microsoft-azure-storage-explorer"></a>使用 Microsoft Azure 存储资源管理器
若要下载该工具，请参阅 [Azure 存储资源管理器](https://storageexplorer.com/)。 下载并安装软件后，请将其配置为使用已配置为 CDN 诊断日志目标的同一 Azure 存储帐户。

1.  打开**Microsoft Azure 存储资源管理器**
2.  找到存储帐户
3.  展开该存储帐户下的“Blob 容器”节点。****
4.  选择名为 *insights-logs-coreanalytics* 的容器。
5.  结果显示在右窗格中，从第一级开始，类似于 *resourceId=*。 继续选择每个级别，直至找到 *PT1H.json* 文件。 有关路径的说明，请参阅 [Blob 路径格式](cdn-azure-diagnostic-logs.md#blob-path-format)。
6.  每个 Blob *PT1H.json* 文件表示特定 CDN 终结点或其自定义域一小时内的分析日志。
7.  有关此 JSON 文件的内容架构，请参阅核心分析日志的“架构”部分。


#### <a name="blob-path-format"></a>Blob 路径格式

核心分析日志每隔一小时生成一次，数据以 JSON 有效负载的形式收集并存储在单个 Azure Blob 中。 存储资源管理器工具将 "/" 解释为目录分隔符，并显示层次结构。 将显示 Azure blob 的路径，如同存在分层结构并表示 blob 名称。 Blob 的名称遵循以下命名约定：    

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

字段说明：****

|值|描述|
|-------|---------|
|订阅 ID    |Azure 订阅的 ID，采用 GUID 格式。|
|资源组名称 |CDN 资源所属资源组的名称。|
|配置文件名称 |CDN 配置文件的名称|
|终结点名称 |CDN 终结点的名称|
|年份|  年份的四位数表示形式，例如 2017|
|月| 月份的两位数表示形式。 01 = 一月 .。。12 = 12 月|
|天|   月份中日的两位数表示形式|
|PT1H.json| 实际存储分析数据的 JSON 文件|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>将核心分析数据导出到 CSV 文件

为了访问核心分析，我们提供了工具的示例代码。 此工具可将 JSON 文件下载为逗号分隔的平面文件格式，然后可以使用该格式创建图表或其他聚合。

该工具的使用方式如下：

1.  访问 GitHub 链接：[https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  下载代码。
3.  遵照说明进行编译和配置。
4.  运行该工具。
5.  生成的 CSV 文件以简单的平面层次结构显示分析数据。

## <a name="log-data-delays"></a>日志数据延迟

下表介绍 **Microsoft 推出的 Azure CDN 标准版**、**Akamai 推出的 Azure CDN 标准版**和 **Verizon 推出的 Azure CDN 标准/高级版**的日志数据延迟。

Microsoft 日志数据延迟 | Verizon 日志数据延迟 | Akamai 日志数据延迟
--- | --- | ---
延迟 1 小时。 | 延迟 1 小时，在终结点传播完成后可能需要长达 2 小时的时间才会开始显示。 | 延迟 24 小时；如果数据是在 24 小时以前创建的，则最长需要在 2 小时后才会开始显示。 如果是最近创建的，则可能需要长达 25 个小时才能开始显示日志。

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>CDN 核心分析的诊断日志类型

Microsoft 当前仅提供核心分析日志，其中包含一些显示 HTTP 响应统计信息和出口统计信息的指标（如 CDN POP/边缘中所见）。

### <a name="core-analytics-metrics-details"></a>核心分析指标详细信息
下表显示了的核心分析日志中可用指标的列表：

* **Microsoft Azure CDN Standard**
* **Akamai 中的 Azure CDN 标准**
* **Verizon 中的标准/高级 Azure CDN**

并非所有提供商提供的所有指标都可用，尽管这种差异很小。 此表还显示了某提供商的给定指标是否可用。 这些指标仅适用于在其上拥有流量的 CDN 终结点。


|指标                     | 描述 | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | 在此期间请求命中的总次数。 | 是 | 是 |是 |
| RequestCountHttpStatus2xx | 导致 2xx HTTP 代码（例如 200 和 202）的所有请求的计数。 | 是 | 是 |是 |
| RequestCountHttpStatus3xx | 导致 3xx HTTP 代码（例如 300 和 302）的所有请求的计数。 | 是 | 是 |是 |
| RequestCountHttpStatus4xx | 导致 4xx HTTP 代码（例如 400 和 404）的所有请求的计数。 | 是 | 是 |是 |
| RequestCountHttpStatus5xx | 导致 5xx HTTP 代码（例如 500 和 504）的所有请求的计数。 | 是 | 是 |是 |
| RequestCountHttpStatusOthers | 所有其他 HTTP 代码的计数（2xx-5xx 除外）。 | 是 | 是 |是 |
| RequestCountHttpStatus200 | 导致 200 HTTP 代码响应的所有请求的计数。 | 是 | 否  |是 |
| RequestCountHttpStatus206 | 导致 206 HTTP 代码响应的所有请求的计数。 | 是 | 否  |是 |
| RequestCountHttpStatus302 | 导致 302 HTTP 代码响应的所有请求的计数。 | 是 | 否  |是 |
| RequestCountHttpStatus304 | 导致 304 HTTP 代码响应的所有请求的计数。 | 是 | 否  |是 |
| RequestCountHttpStatus404 | 导致 404 HTTP 代码响应的所有请求的计数。 | 是 | 否  |是 |
| RequestCountCacheHit | 导致缓存命中的所有请求的计数。 资产已直接从 POP 提供给客户端。 | 是 | 是 | 否  |
| RequestCountCacheMiss | 导致缓存未命中的所有请求的计数。 缓存未命中意味着在最靠近客户端的 POP 上找不到该资产，并已从源中检索到该资产。 | 是 | 是 | 否 |
| RequestCountCacheNoCache | 因边缘上的用户配置而无法缓存的资产的所有请求计数。 | 是 | 是 | 否 |
| RequestCountCacheUncacheable | 对资产的缓存控制和到期标头阻止缓存的对资产的所有请求计数。 此计数表明它不应在 POP 上缓存或由 HTTP 客户端缓存。 | 是 | 是 | 否 |
| RequestCountCacheOthers | 上述未涵盖的具有缓存状态的所有请求的计数。 | 否 | 是 | 否  |
| EgressTotal | 出站数据传输量（按 GB 计） | 是 |是 |是 |
| EgressHttpStatus2xx | 针对状态代码为 2xx HTTP 的响应的出站数据传输量*（按 GB 计）。 | 是 | 是 | 否  |
| EgressHttpStatus3xx | 针对状态代码为 3xx HTTP 的响应的出站数据传输量（按 GB 计）。 | 是 | 是 | 否  |
| EgressHttpStatus4xx | 针对状态代码为 4xx HTTP 的响应的出站数据传输量（按 GB 计）。 | 是 | 是 | 否  |
| EgressHttpStatus5xx | 针对状态代码为 5xx HTTP 的响应的出站数据传输量（按 GB 计）。 | 是 | 是 | 否 |
| EgressHttpStatusOthers | 针对带其他 HTTP 状态代码的响应的出站数据传输量（按 GB 计）。 | 是 | 是 | 否  |
| EgressCacheHit | 针对直接从 CDN POP/边缘上 CDN 缓存传递的响应的出站数据传输量。 | 是 | 是 | 否 |
| EgressCacheMiss。 | 对于在最近的 POP 服务器上找不到的响应的出站数据传输，以及从源服务器检索到的响应。 | 是 | 是 | 否 |
| EgressCacheNoCache | 因边缘上的用户配置而无法缓存的资产的出站数据传输。 | 是 | 是 | 否 |
| EgressCacheUncacheable | 无法由资产的缓存控制和或过期标头缓存的资产的出站数据传输。 指示它不应在 POP 上缓存或由 HTTP 客户端缓存。 | 是 | 是 | 否 |
| EgressCacheOthers | 其他缓存方案的出站数据传输量。 | 否 | 是 | 否 |

* 出站数据传输量是指从 CDN POP 服务器传递到客户端的流量。


### <a name="schema-of-the-core-analytics-logs"></a>核心分析日志的架构 

所有日志以 JSON 格式存储，每个项包含遵循以下架构的字符串字段：

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

其中，*time* 表示报告统计信息的小时边界的开始时间。 CDN 提供程序不支持的指标（而不是 double 或 integer 值）会导致 null 值。 该 null 值表示不存在指标且与 0 值不同。 在终结点上配置每个域的一组指标。

示例属性：

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>其他资源

* [Azure 诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [通过 Azure CDN 补充门户进行核心分析](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure Monitor 日志](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Azure Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics)







