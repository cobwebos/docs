---
title: Azure 诊断日志 | Microsoft Docs
description: 客户可为 Azure CDN 启用日志分析。
services: cdn
documentationcenter: ''
author: ''
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: v-deasim
ms.openlocfilehash: 9c61fe7c62f0718d390509d3b0ff3327bd193f43
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="azure-diagnostic-logs"></a>Azure 诊断日志

使用 Azure 诊断日志，可以查看核心分析并将其保存到一个或多个目标，包括：

 - Azure 存储帐户
 - Azure 事件中心
 - [Log Analytics 工作区](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
此功能适用于所有属于 Verizon（标准版和高级版）和 Akamai（标准版）CDN 配置文件的 CDN 终结点。 

通过 Azure 诊断日志，可将基本使用情况指标从 CDN 终结点导出到不同的源，以便以自定义方式使用它们。 例如，可执行以下类型的数据导出：

- 将数据导出到 Blob 存储、导出为CSV，并在 Excel 中生成图形。
- 将数据导出到事件中心，并关联到其他 Azure 服务的数据。
- 将数据导出到 Log Analytics，并在自己的 Log Analytics 工作区中查看数据

下图显示了一个典型的 CDN 核心分析数据视图。

![门户 - 诊断日志](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*图 1 - CDN 核心分析视图*

有关诊断日志的详细信息，请参阅[诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)。

## <a name="enable-logging-with-azure-portal"></a>使用 Azure 门户启用日志记录

遵循以下步骤使用 CDN 核心分析启用日志记录：

登录到 [Azure 门户](http://portal.azure.com)。 如果工作流尚未启用 CDN，请先[启用 Azure CDN](cdn-create-new-endpoint.md) 再继续操作。

1. 在门户中，导航到 CDN 配置文件。
2. 选择 CDN 配置文件，并选择要启用“诊断日志”的 CDN 终结点。

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. 在“监视”部分中选择“诊断日志”。

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>使用 Azure 存储启用日志记录
    
1. 若要使用 Azure 存储来存储日志，请依次选择“存档到存储帐户”、“CoreAnalytics”，并在“保留期(天)”下选择保留天数。 如果保留期为 0 天，则会无限期存储日志。 
2. 输入设置名称，单击“存储帐户”。 选择存储帐户后，单击“保存”。

![门户 - 诊断日志](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png)

图 2 - 使用 Azure 存储的日志记录

### <a name="logging-with-log-analytics"></a>使用 Log Analytics 进行日志记录

若要使用 Log Analytics 来存储日志，请执行以下步骤：

1. 在“诊断日志”边栏选项卡中，选择“发送到 Log Analytics”。 

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. 单击“配置”配置 Log Analytics 日志记录。 在“OMS 工作区”对话框中，可以选择上一个工作区或创建新的工作区。

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. 单击“创建新工作区”。

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/07_Create-new.png)

4. 输入一个新的 Log Analytics 工作区名称。 Log Analytics 工作区名称必须唯一，只能包含字母、数字和连字符；不允许空格和下划线。 
5. 接下来，选择现有订阅、新的或现有的资源组、位置和定价层。 还可以选择将此配置固定到仪表板。 单击“**确定**”以完成配置。

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/08_Workspace-resource.png)

5.  创建工作区后，会返回到“诊断日志”窗口。 确认新 Log Analytics 工作区的名称。

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/09_Return-to-logging.png)

    设置 Log Analytics 配置后，请确认是否已选择“CoreAnalytics”。

6. 单击“ **保存**”。

7. 若要查看新的 Log Analytics 工作区，请转到 Azure 门户仪表板，并单击 Log Analytics 工作区的名称。 单击“OMS 门户”磁贴来查看 Log Analytics 工作区。 

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    Log Analytics 工作区现在已经可以用来记录数据日志。 为了使用该数据，必须使用本文下文中提供的 [Log Analytics 解决方案](#consuming-diagnostics-logs-from-a-log-analytics-workspace)。

有关日志数据延迟的详细信息，请参阅[日志数据延迟](#log-data-delays)。

## <a name="enable-logging-with-powershell"></a>使用 PowerShell 启用日志记录

以下示例演示如何通过 Azure PowerShell Cmdlet 启用诊断日志。

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>在存储帐户中启用诊断日志

首先登录并选择订阅：

    Login-AzureRmAccount 

    Select-AzureSubscription -SubscriptionId 


若要在存储帐户中启用诊断日志，请使用以下命令：

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```
若要在 Log Analytics 工作区中启用诊断日志，请使用以下命令：

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
```



## <a name="consuming-diagnostics-logs-from-azure-storage"></a>从 Azure 存储中使用诊断日志
本部分介绍 CDN 核心分析的架构及其在 Azure 存储帐户中的组织方式，并提供用于将日志下载到 CSV 文件的示例代码。

### <a name="using-microsoft-azure-storage-explorer"></a>使用 Microsoft Azure 存储资源管理器
首先需要一款可访问存储帐户内容的工具，才可从 Azure 存储帐户访问核心分析数据。 市场上有多种可用的工具，但推荐使用 Microsoft Azure 存储资源管理器。 若要下载该工具，请参阅 [Azure 存储资源管理器](http://storageexplorer.com/)。 下载并安装软件后，请将其配置为使用配置为 CDN 诊断日志目标的同一 Azure 存储帐户。

1.  打开 Microsoft Azure 存储资源管理器
2.  找到存储帐户
3.  转到该存储帐户下的“Blob 容器”节点并展开该节点
4.  选择名为“insights-logs-coreanalytics”的容器，并双击它
5.  结果显示在右窗格中，从第一级开始，类似“resourceId =”。 继续单击，直到看到文件 PT1H.json。 有关路径的说明，请参阅以下注释。
6.  每个 Blob PT1H.json 表示特定 CDN 终结点或其自定义域一小时内的分析日志。
7.  有关此 JSON 文件的内容架构，请参阅核心分析日志的“架构”部分


> [!NOTE]
> Blob 路径格式
> 
> 核心分析日志每隔一小时生成一次，数据以 JSON 有效负载的形式收集并存储在单个 Azure Blob 中。 由于“存储资源管理器”工具将“/”解释为目录分隔符并显示层次结构，Azure Blob 的路径看起来像是采用了分层结构并表示 Blob 名称。 Blob 的名称遵循以下命名约定： 
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

字段说明：

|值|description|
|-------|---------|
|订阅 ID    |Azure 订阅的 ID，采用 GUID 格式。|
|资源 |组名称：CDN 资源所属资源组的名称。|
|配置文件名称 |CDN 配置文件的名称|
|终结点名称 |CDN 终结点的名称|
|年龄|  年份的 4 位数表示形式，例如 2017|
|月份| 月份的 2 位数表示形式。 01 = 1 月...12 = 12 月|
|日期|   月份中日期的 2 位数表示形式|
|PT1H.json| 实际存储分析数据的 JSON 文件|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>将核心分析数据导出到 CSV 文件

为了方便访问核心分析，我们提供了工具的示例代码。 此工具可将 JSON 文件下载到逗号分隔的文件格式中，从中可轻松创建图表或其他聚合。

该工具的使用方式如下：

1.  访问 github 链接：[https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.  下载代码。
3.  遵照说明进行编译和配置。
4.  运行工具。
5.  生成的 CSV 文件以简单的平面层次结构显示分析数据。

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>从 Log Analytics 工作区使用诊断日志
Log Analytics 是 Azure 中的一个服务，用于监视云和本地环境，使其保持较高的可用性和性能。 它可以收集云和本地环境中的资源生成的数据以及其他监视工具的数据，针对多个源提供分析。 

若要使用 Log Analytics，必须在 Azure Log Analytics 工作区中[启用日志记录](#enable-logging-with-azure-storage)，本文上文中已经对此进行了讨论。

### <a name="using-the-log-analytics-workspace"></a>使用 Log Analytics 工作区

 下图显示了存储库的输入和输出的体系结构：

![Log Analytics 工作区](./media/cdn-diagnostics-log/12_Repo-overview.png)

图 3 - Log Analytics 存储库

使用管理解决方案可通过各种方式显示数据。 可从 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions) 获取管理解决方案。

可以通过单击每个解决方案底部的“立即获取”链接从 Azure Marketplace 安装管理解决方案。

### <a name="adding-a-log-analytics-cdn-management-solution"></a>添加 Log Analytics CDN 管理解决方案

请按照下列步骤添加管理解决方案：

1.   如果尚未登录 Azure 门户，请使用 Azure 订阅登录并转到仪表板。
    ![Azure 仪表板](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. 在“新建”边栏选项卡中，在“Marketplace”下，选择“监视 + 管理”。

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. 在“监视 + 管理”边栏选项卡中，单击“查看所有”。

    ![查看全部](./media/cdn-diagnostics-log/15_See-all.png)

4.  在搜索框中搜索 CDN。

    ![查看全部](./media/cdn-diagnostics-log/16_Search-for.png)

5.  选择“Azure CDN 核心分析”。 

    ![查看全部](./media/cdn-diagnostics-log/17_Core-analytics.png)

6.  单击“创建”后，系统将要求创建一个新的 Log Analytics 工作区或使用现有的 Log Analytics 工作区。 

    ![查看全部](./media/cdn-diagnostics-log/18_Adding-solution.png)

7.  选择之前创建的工作区。 然后，需要添加自动化帐户。

    ![查看全部](./media/cdn-diagnostics-log/19_Add-automation.png)

8. 以下屏幕显示必须填写的自动化帐户窗体。 

    ![查看全部](./media/cdn-diagnostics-log/20_Automation.png)

9. 创建自动化帐户后，即可添加解决方案。 单击“创建”  按钮。

    ![查看全部](./media/cdn-diagnostics-log/21_Ready.png)

10. 你的解决方案现已添加到工作区。 返回到 Azure 门户仪表板。

    ![查看全部](./media/cdn-diagnostics-log/22_Dashboard.png)

    单击创建的 Log Analytics 工作区以转到工作区。 

11. 单击“OMS 门户”磁贴来查看新解决方案。

    ![查看全部](./media/cdn-diagnostics-log/23_workspace.png)

12. 你的门户现在应如以下屏幕所示：

    ![查看全部](./media/cdn-diagnostics-log/24_OMS-solution.png)

    单击其中一个磁贴以查看多个数据视图。

    ![查看全部](./media/cdn-diagnostics-log/25_Interior-view.png)

    可向左或向右滚动查看表示各个数据视图的其他磁贴。 

    单击其中一个磁贴可以提供有关数据的更多详细信息。

     ![查看全部](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>产品和定价层

可在[此处](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers)查看管理解决方案的产品/服务和定价层。

### <a name="customizing-views"></a>自定义视图

可使用“视图设计器”自定义数据视图。 若要开始设计，请转到 Log Analytics 工作区并单击“视图设计器”磁贴。

![视图设计器](./media/cdn-diagnostics-log/27_Designer.png)

可以拖放图表类型，并填写要分析的数据详细信息。

![视图设计器](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>日志数据延迟

Verizon 日志数据延迟 | Akamai 日志数据延迟
--- | ---
Verizon 日志数据延迟 1 小时，在终结点传播完成后长达 2 小时才开始显示。 | Akamai 日志数据延迟 24 小时；如果数据是在 24 小时以前创建的，则最长需要在 2 小时后才会开始显示。 如果是最近创建的，则可能需要长达 25 个小时才能开始显示日志。

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>CDN 核心分析的诊断日志类型

目前只提供核心分析日志，其中包含一些显示 HTTP 响应统计信息和出口统计信息的指标（如 CDN POP/边缘中所见）。

### <a name="core-analytics-metrics-details"></a>核心分析指标详细信息
下表显示了核心分析日志中可用指标的列表。 并非所有提供商提供的所有指标都可用，尽管这种差异很小。 还介绍了某提供商的给定指标是否可用。 请注意，这些指标仅适用于在其上拥有流量的 CDN 终结点。


|指标                     | 说明   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |在此期间请求命中的总次数| 是  |是   |
| RequestCountHttpStatus2xx |导致 2xx HTTP 代码（例如 200 和 202）的所有请求的计数              | 是  |是   |
| RequestCountHttpStatus3xx | 导致 3xx HTTP 代码（例如 300 和 302）的所有请求的计数              | 是  |是   |
| RequestCountHttpStatus4xx |导致 4xx HTTP 代码（例如 400 和 404）的所有请求的计数               | 是   |是   |
| RequestCountHttpStatus5xx | 导致 5xx HTTP 代码（例如 500 和 504）的所有请求的计数              | 是  |是   |
| RequestCountHttpStatusOthers |  所有其他 HTTP 代码的计数（2xx-5xx 除外） | 是  |是   |
| RequestCountHttpStatus200 | 导致 200 HTTP 代码响应的所有请求的计数              |否   |是   |
| RequestCountHttpStatus206 | 导致 206 HTTP 代码响应的所有请求的计数              |否   |是   |
| RequestCountHttpStatus302 | 导致 302 HTTP 代码响应的所有请求的计数              |否   |是   |
| RequestCountHttpStatus304 |  导致 304 HTTP 代码响应的所有请求的计数             |否   |是   |
| RequestCountHttpStatus404 | 导致 404 HTTP 代码响应的所有请求的计数              |否   |是   |
| RequestCountCacheHit |导致缓存命中的所有请求的计数。 资产已直接从 POP 提供给客户端。               | 是  |否   |
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
| EgressCacheHit |  针对直接从 CDN POP/边缘上 CDN 缓存传递的响应的出站数据传输量  |是   |  否 |
| EgressCacheMiss | 针对未在最近的 POP 服务器上找到并从源服务器检索的响应的出站数据传输量              |是   |  否 |
| EgressCacheNoCache | 针对因边缘上的用户配置而无法缓存的资源的出站数据传输量。                |是   |否   |
| EgressCacheUncacheable | 针对因资产的 Cache-Control 和/或 Expires 标头而无法缓存的资产的出站数据传输量。 指示该资产不应在 POP 上缓存或不应由 HTTP 客户端缓存。                   |是   | 否  |
| EgressCacheOthers |  其他缓存方案的出站数据传输量。             |是   | 否  |

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

其中“time”表示报告统计信息的小时边界的开始时间。 如果 CDN 提供程序不支持指标，则返回 null 值，而不是双精度或整型值。 该 null 值表示不存在指标且与 0 值不同。 在终结点上配置的每个域都有其中一组指标。

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
* [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Azure Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics)







