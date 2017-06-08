---
title: "Azure CDN 日志分析 | Microsoft Docs"
description: "客户可为 Azure CDN 启用日志分析。"
services: cdn
documentationcenter: 
author: lichard
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: rli
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: e3be8aced50a2ce330523d8633f524803af8c8a7
ms.contentlocale: zh-cn
ms.lasthandoff: 05/11/2017


---

# <a name="diagnostics-logs-for-azure-cdn"></a>Azure CDN 诊断日志

为应用程序启用 CDN 后，可能希望监视 CDN 使用情况、检查交付内容的运行状况以及排除潜在问题。 Azure CDN 通过[核心分析](cdn-analyze-usage-patterns.md)提供了这些功能。

作为具有 Verizon 标准或高级配置文件的当前 Azure CDN 用户，可通过 Azure 门户的“管理”选项查看补充门户中的核心分析。 通过这种新的[诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)功能，现可查看核心分析并将其保存到一个或多个目标，包括 Azure 存储帐户、Azure 事件中心和/或 [Log Analytics (OMS) 工作区](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)。 此功能适用于所有属于 Verizon（标准版和高级版）和 Akamai（标准版）CDN 配置文件的 CDN 终结点。

通过诊断日志，可将基本使用指标从 CDN 终结点导出到不同的源，以便以自定义方式使用它们。 例如，可执行以下操作：

- 将数据导出到 Blob 存储、导出为CSV，并在 Excel 中生成图形。
- 将数据导出到事件中心，并关联到其他 Azure 服务的数据。
- 将数据导出到 Log Analytics，并在自己的 OMS 工作区查看数据


![门户 - 诊断日志](./media/cdn-diagnostics-log/OMS-workspace.png)

以下演练将介绍核心分析数据的架构，并介绍启用该功能并将其传递到不同目标以及从这些目标使用时所涉及的步骤。

## <a name="enable-logging-with-azure-portal"></a>使用 Azure 门户启用日志记录

诊断日志默认已**禁用**。 执行以下步骤进行启用：


登录到 [Azure 门户](http://portal.azure.com)。 如果工作流尚未启用 CDN，请先[启用 Azure CDN](cdn-create-new-endpoint.md) 再继续操作。

1. 在门户中，导航到 CDN 配置文件。
2. 选择 CDN 配置文件，然后选择要启用诊断日志的 CDN 终结点。
    ![门户 - 诊断日志](./media/cdn-diagnostics-log/Browse-to-Diagnostics-logs.png)
3. 转到“监视”部分下的“诊断日志”边栏选项卡，将状态更改为“开”。
    ![门户 - 诊断日志](./media/cdn-diagnostics-log/Diagnostics-logs-options.png)
4. 选择并配置所需的存档目标（存储帐户、事件中心、Log Analytics）。 
    
    在此示例中，Azure 存储用于存储日志，请选择“存档到存储帐户”，选择保留天数，然后单击“日志”下的“核心分析”。 目前只提供“核心分析”，但将来会提供更多的日志类型。 有关核心分析的架构、聚合和延迟信息，请参阅下文。 

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/Diagnostics-logs-storage.png)
5.  保存新的诊断配置。
    
    Verizon 日志数据延迟 1 小时，在终结点传播完成后长达 2 小时才开始显示。
    Akamai 日志数据延迟 24 小时，如果在 24 小时以前创建，则在长达 2 小时后才开始显示。 如果是刚刚创建的，则可能需要长达 25 个小时才能开始显示日志。

## <a name="enable-logging-with-powershell"></a>使用 PowerShell 启用日志记录

下面两个示例介绍了如何通过 Azure PowerShell Cmdlet 启用并获得诊断日志。

###<a name="enabling-diagnostic-logs-in-a-storage-account"></a>在存储帐户中启用诊断日志

若要在存储帐户中启用诊断日志，请使用以下命令：

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```

## <a name="consuming-logs-from-storage"></a>从存储中使用日志
本节介绍 CDN 核心分析的架构及其在 Azure 存储帐户中的组织方式，并提供了用于将日志下载到 CSV 文件的示例代码。

### <a name="using-microsoft-azure-storage-explorer"></a>使用 Microsoft Azure 存储资源管理器
首先需要一款可访问存储帐户内容的工具，然后才可从 Azure 存储帐户访问核心分析数据。 市场上有多种可用的工具，但推荐使用 Microsoft Azure 存储资源管理器。 可从[此处](http://storageexplorer.com/)下载工具。 下载并安装软件后，请配置使用配置为 CDN 诊断日志目标的同一 Azure 存储帐户。

1.    打开 Microsoft Azure 存储资源管理器
2.    找到存储帐户
3.    转到该存储帐户下的“Blob 容器”节点并展开该节点
4.    选择名为“insights-logs-coreanalytics”的容器，然后双击它
5.    结果显示在右窗格中，从第一级开始，类似“resourceId =”。 继续单击，直到看到文件 PT1H.json。 有关路径的说明，请参阅以下备注。
6.    每个 Blob PT1H.json 表示特定 CDN 终结点或其自定义域一小时内的分析日志。
7.    有关此 JSON 文件的内容架构，请参阅核心分析日志的“架构”部分


> [!NOTE]
> Blob 路径格式
> 
> 核心分析日志每小时生成一次。 收集一小时内的所有数据，并作为 JSON 有效负载存储在单个 Azure Blob 中。 此 Azure Blob 的路径看起来像是一个层次结构。 这是因为存储资源管理器工具将“/”解释为目录分隔符，以方便显示层次结构。 其实，整个路径仅表示 Blob 名称。 Blob 的名称遵循以下命名约定    
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

字段说明：

|值|说明|
|-------|---------|
|订阅 ID    |Azure 订阅的 ID。 采用 Guid 格式。|
|资源 |组名称：CDN 资源所属资源组的名称。|
|配置文件名称 |CDN 配置文件的名称|
|终结点名称 |CDN 终结点的名称|
|年份|    年份的 4 位数表示形式（例如 2017 年）|
|月份|    月份的 2 位数表示形式。 01 = 1 月... 12 = 12 月|
|日期|    月份中日期的 2 位数表示形式|
|PT1H.json|    实际存储分析数据的 JSON 文件|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>将核心分析数据导出到 CSV 文件

为便于访问核心分析，我们提供了工具的示例代码，该代码可将 JSON 文件下载到逗号分隔的文件格式中，从中可轻松创建图表或其他聚合。

该工具的使用方式如下：

1.    请访问 GitHub 链接：[https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.    下载代码
3.    按照说明进行编译和配置
4.    运行工具
5.    生成的 CSV 文件以简单的平面层次结构显示分析数据。

## <a name="diagnostic-logs-types"></a>诊断日志类型

目前只提供核心分析日志，其中包含一些显示 HTTP 响应统计信息和出口统计信息的指标（如 CDN POP/边缘中所见）。 今后，我们将添加其他类型的日志。

### <a name="core-analytics-metrics-details"></a>核心分析指标详细信息
下面是核心分析日志中可用指标的列表。 并非所有提供商提供的所有指标都可用，尽管这种差异很小。 下表还展示了某提供商的给定指标是否可用。 请注意，这些指标仅适用于在其上拥有流量的 CDN 终结点。


|指标                     | 说明   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |在此期间请求命中的总次数| 是  |是   |
| RequestCountHttpStatus2xx |导致 2xx HTTP 代码（如 200 和 202）的所有请求的计数              | 是  |是   |
| RequestCountHttpStatus3xx | 导致 3xx HTTP 代码（如 300 和 302）的所有请求的计数              | 是  |是   |
| RequestCountHttpStatus4xx |导致 4xx HTTP 代码（如 400 和 404）的所有请求的计数               | 是   |是   |
| RequestCountHttpStatus5xx | 导致 5xx HTTP 代码（如 500 和 504）的所有请求的计数              | 是  |是   |
| RequestCountHttpStatusOthers |  所有其他 HTTP 代码的计数（2xx-5xx 除外） | 是  |是   |
| RequestCountHttpStatus200 | 导致 200 HTTP 代码响应的所有请求的计数              |否   |是   |
| RequestCountHttpStatus206 | 导致 206 HTTP 代码响应的所有请求的计数              |否   |是   |
| RequestCountHttpStatus302 | 导致 302 HTTP 代码响应的所有请求的计数              |否   |是   |
| RequestCountHttpStatus304 |  导致 304 HTTP 代码响应的所有请求的计数             |否   |是   |
| RequestCountHttpStatus404 | 导致 404 HTTP 代码响应的所有请求的计数              |否   |是   |
| RequestCountCacheHit |导致缓存命中的所有请求的计数。 这意味着资产直接从 POP 提供给客户端。               | 是  |否   |
| RequestCountCacheMiss | 导致缓存未命中的所有请求的计数。 这意味着在最靠近客户端的 POP 上找不到资产，因此从源进行检索。              |是   | 否  |
| RequestCountCacheNoCache | 因边缘上的用户配置而无法缓存的资产的所有请求计数。              |是   | 否  |
| RequestCountCacheUncacheable | 因资产的 Cache-Control 和 Expires 标头而无法缓存的资产的所有请求计数，这些标头指示该资产不应在 POP 上缓存或不应由 HTTP 客户端缓存                |是   |否   |
| RequestCountCacheOthers | 上述未涵盖的具有缓存状态的所有请求的计数。              |是   | 否  |
| EgressTotal | 出站数据传输量（按 GB 计）              |是   |是   |
| EgressHttpStatus2xx | 针对状态代码为 2xx HTTP 的响应的出站数据传输量*（按 GB 计）            |是   |否   |
| EgressHttpStatus3xx | 针对状态代码为 3xx HTTP 的响应的出站数据传输量（按 GB 计）              |是   |否   |
| EgressHttpStatus4xx | 针对状态代码为 4xx HTTP 的响应的出站数据传输量（按 GB 计）               |是   | 否  |
| EgressHttpStatus5xx | 针对状态代码为 5xx HTTP 的响应的出站数据传输量（按 GB 计）               |是   |  否 |
| EgressHttpStatusOthers | 针对带其他 HTTP 状态代码的响应的出站数据传输量（按 GB 计）                |是   |否   |
| EgressCacheHit |  针对直接从 CDN POP/边缘上 CDN 缓存传递的响应的出站数据传输量    |是   |  否 |
| EgressCacheMiss | 针对未在最近的 POP 服务器上找到并从源服务器检索的响应的出站数据传输量              |是   |  否 |
| EgressCacheNoCache | 针对因边缘上的用户配置而无法缓存的资源的出站数据传输量。                |是   |否   |
| EgressCacheUncacheable | 针对因资产的 Cache-Control 和/或 Expires 标头而无法缓存的资产的出站数据传输量，这些标头指示该资产不应在 POP 上缓存或不应由 HTTP 客户端缓存                    |是   | 否  |
| EgressCacheOthers |  其他缓存方案的出站数据传输量。             |是   | 否  |

*出站数据传输量是指从 CDN POP 服务器传递到客户端的流量。


### <a name="schema-of-the-core-analytics-logs"></a>核心分析日志的架构 

所有日志均以 JSON 格式存储，每个项包含以下架构的字符串字段：

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

其中“time”表示报告统计信息的小时边界的开始时间。 请注意，若 CDN 提供商不支持指标，此值将为 null 值，而不是 double 或 integer 值。 该 null 值表示不存在指标且与 0 值不同。 另请注意，在终结点上配置的每个域都有一组这些指标。

示例属性如下：

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


