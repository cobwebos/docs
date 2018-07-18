---
title: Azure 诊断日志 | Microsoft Docs
description: 客户可为 Azure CDN 启用日志分析。
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: v-deasim
ms.openlocfilehash: 98a7fc5c4607115811e17a7cf6acd4e867663833
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261298"
---
# <a name="azure-diagnostic-logs"></a>Azure 诊断日志

使用 Azure 诊断日志，可以查看核心分析并将其保存到一个或多个目标，包括：

 - Azure 存储帐户
 - Azure 事件中心
 - [Log Analytics 工作区](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
此功能在所有定价层的 CDN 终结点上提供。 

通过 Azure 诊断日志，可将基本使用情况指标从 CDN 终结点导出到不同的源，以便以自定义方式使用它们。 例如，可执行以下类型的数据导出：

- 将数据导出到 Blob 存储、导出为CSV，并在 Excel 中生成图形。
- 将数据导出到事件中心，并关联到其他 Azure 服务的数据。
- 将数据导出到 Log Analytics，并在自己的 Log Analytics 工作区中查看数据

下图显示了一个典型的 CDN 核心分析数据视图。

![门户 - 诊断日志](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*图 1 - CDN 核心分析视图*

有关诊断日志的详细信息，请参阅[诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)。

## <a name="enable-logging-with-the-azure-portal"></a>使用 Azure 门户启用日志记录

遵循以下步骤使用 CDN 核心分析启用日志记录：

登录到 [Azure 门户](http://portal.azure.com)。 如果还没有为工作流启用 CDN，请在继续操作之前[创建 Azure CDN 配置文件和终结点](cdn-create-new-endpoint.md)。

1. 在 Azure 门户中，导航到“CDN 配置文件”。

2. 在 Azure 门户中，搜索一个 CDN 配置文件或者从仪表板中选择一个。 然后，选择要为其启用诊断日志的 CDN 终结点。

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. 在“监视”部分中选择“诊断日志”。

   此时将显示“诊断日志”页。

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>使用 Azure 存储启用日志记录

若要使用存储帐户来存储日志，请执行以下步骤：
    
1. 对于“名称”，输入你的诊断日志设置的名称。
 
2. 选择“存档到存储帐户”，然后选择“CoreAnalytics”。 

2. 对于“保留期(天数)”，请选择保留天数。 如果保留期为 0 天，则会无限期存储日志。 

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. 选择“存储帐户”。

    此时将显示“选择存储帐户”页面。

4. 从下拉列表中选择一个存储帐户，然后选择“确定”。

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. 完成诊断日志设置后，选择“保存”。

### <a name="logging-with-log-analytics"></a>使用 Log Analytics 进行日志记录

若要使用 Log Analytics 来存储日志，请执行以下步骤：

1. 从“诊断日志”页面中，选择“发送到 Log Analytics”。 

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. 选择“配置”以配置 Log Analytics 日志记录。 

   此时将显示“OMS 工作区”页面。

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. 选择“新建工作区”。

    此时将显示“OMS 工作区”页面。

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/07_Create-new.png)

4. 对于“OMS 工作区”，输入 OMS 工作区名称。 OMS 工作区名称必须唯一，只能包含字母、数字和连字符；不允许空格和下划线。 

5. 对于“订阅”，从下拉列表中选择一个现有的订阅。 

6. 对于“资源组”，创建一个新资源组或选择现有的资源组。

7. 对于“位置”，从下拉列表中选择一个位置。

8. 如果希望将日志配置保存到仪表板，请选择“固定到仪表板”。 

9. 选择“确定”以完成配置。

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/08_Workspace-resource.png)

10. 创建工作区后，会返回到“诊断日志”页面。 确认新 Log Analytics 工作区的名称。

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. 选择“CoreAnalytics”，然后选择“保存”。

12. 若要查看新的 Log Analytics 工作区，请从 CDN 终结点页面选择“Core analytics”。

    ![门户 - 诊断日志](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    Log Analytics 工作区现在已经可以用来记录数据日志。 为了使用该数据，必须使用本文下文中提供的 [Log Analytics 解决方案](#consuming-diagnostics-logs-from-a-log-analytics-workspace)。

有关日志数据延迟的详细信息，请参阅[日志数据延迟](#log-data-delays)。

## <a name="enable-logging-with-powershell"></a>使用 PowerShell 启用日志记录

以下示例演示了如何通过 Azure PowerShell Cmdlet 启用诊断日志。

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>在存储帐户中启用诊断日志

1. 登录并选择一个订阅：

    Connect-AzureRmAccount 

    Select-AzureSubscription -SubscriptionId 

2. 若要在存储帐户中启用诊断日志，请输入以下命令：

    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. 若要在 Log Analytics 工作区中启用诊断日志，请输入以下命令：

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
3.  展开该存储帐户下的“Blob 容器”节点。
4.  选择名为 *insights-logs-coreanalytics* 的容器。
5.  结果显示在右窗格中，从第一级开始，类似于 *resourceId=*。 继续选择每个级别，直至找到 *PT1H.json* 文件。 有关路径的说明，请参阅下面的 *Blob 路径格式*注释。
6.  每个 Blob *PT1H.json* 文件表示特定 CDN 终结点或其自定义域一小时内的分析日志。
7.  有关此 JSON 文件的内容架构，请参阅核心分析日志的“架构”部分。


> [!NOTE]
> Blob 路径格式
> 
> 核心分析日志每隔一小时生成一次，数据以 JSON 有效负载的形式收集并存储在单个 Azure Blob 中。 由于“存储资源管理器”工具将“/”解释为目录分隔符并显示层次结构，Azure Blob 的路径看起来像是采用了分层结构并表示 Blob 名称。 Blob 的名称遵循以下命名约定： 
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

字段说明：

|值|说明|
|-------|---------|
|订阅 ID    |Azure 订阅的 ID，采用 GUID 格式。|
|资源组名称 |CDN 资源所属资源组的名称。|
|配置文件名称 |CDN 配置文件的名称|
|终结点名称 |CDN 终结点的名称|
|年龄|  年份的四位数表示形式，例如 2017|
|月份| 月份的两位数表示形式。 01 = 1 月...12 = 12 月|
|日期|   月份中日的两位数表示形式|
|PT1H.json| 实际存储分析数据的 JSON 文件|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>将核心分析数据导出到 CSV 文件

为了方便访问核心分析，我们提供了工具的示例代码。 此工具可将 JSON 文件下载为逗号分隔的平面文件格式，然后可以使用该格式创建图表或其他聚合。

该工具的使用方式如下：

1.  访问 github 链接：[https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  下载代码。
3.  遵照说明进行编译和配置。
4.  运行工具。
5.  生成的 CSV 文件以简单的平面层次结构显示分析数据。

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>从 Log Analytics 工作区使用诊断日志
Log Analytics 是一项 Azure 服务，用于监视云和本地环境，使其保持较高的可用性和性能。 它可以收集云和本地环境中的资源生成的数据以及其他监视工具的数据，针对多个源提供分析。 

若要使用 Log Analytics，必须在 Azure Log Analytics 工作区中[启用日志记录](#enable-logging-with-azure-storage)，本文上文中已经对此进行了讨论。

### <a name="using-the-log-analytics-workspace"></a>使用 Log Analytics 工作区

 下图显示了存储库的输入和输出的体系结构：

![Log Analytics 工作区](./media/cdn-diagnostics-log/12_Repo-overview.png)

图 3 - Log Analytics 存储库

使用管理解决方案可通过各种方式显示数据。 可从 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions)获取管理解决方案。

可以通过选择每个解决方案底部的“立即获取”链接从 Azure 市场安装管理解决方案。

### <a name="add-a-log-analytics-cdn-management-solution"></a>添加 Log Analytics CDN 管理解决方案

请按照下列步骤添加 Log Analytics 管理解决方案：

1.   使用你的 Azure 订阅登录到 Azure 门户并转到你的仪表板。
    ![Azure 仪表板](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. 在“新建”页面中，在“市场”下，选择“监视 + 管理”。

    ![市场](./media/cdn-diagnostics-log/14_Marketplace.png)

3. 在“监视 + 管理”页面中，选择“查看全部”。

    ![查看全部](./media/cdn-diagnostics-log/15_See-all.png)

4. 在搜索框中搜索 CDN。

    ![查看全部](./media/cdn-diagnostics-log/16_Search-for.png)

5. 选择“Azure CDN 核心分析”。 

    ![查看全部](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. 选择“创建”后，系统会要求创建一个新的 Log Analytics 工作区或使用现有的 Log Analytics 工作区。 

    ![查看全部](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. 选择之前创建的工作区。 然后，需要添加自动化帐户。

    ![查看全部](./media/cdn-diagnostics-log/19_Add-automation.png)

8. 以下屏幕显示必须填写的自动化帐户窗体。 

    ![查看全部](./media/cdn-diagnostics-log/20_Automation.png)

9. 创建自动化帐户后，即可添加解决方案。 选择“创建”按钮。

    ![查看全部](./media/cdn-diagnostics-log/21_Ready.png)

10. 你的解决方案现已添加到工作区。 返回到 Azure 门户仪表板。

    ![查看全部](./media/cdn-diagnostics-log/22_Dashboard.png)

    选择你创建的 Log Analytics 工作区以转到你的工作区。 

11. 选择“OMS 门户”磁贴来查看新解决方案。

    ![查看全部](./media/cdn-diagnostics-log/23_workspace.png)

12. 你的门户现在应如以下屏幕所示：

    ![查看全部](./media/cdn-diagnostics-log/24_OMS-solution.png)

    选择其中一个磁贴以查看多个数据视图。

    ![查看全部](./media/cdn-diagnostics-log/25_Interior-view.png)

    可向左或向右滚动查看表示各个数据视图的其他磁贴。 

    选择其中一个磁贴以查看数据的更多详细信息。

     ![查看全部](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>产品和定价层

可在[此处](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers)查看管理解决方案的产品/服务和定价层。

### <a name="customizing-views"></a>自定义视图

可使用“视图设计器”自定义数据视图。 若要开始设计，请转到 Log Analytics 工作区并选择“视图设计器”磁贴。

![视图设计器](./media/cdn-diagnostics-log/27_Designer.png)

拖放图表类型，并填写要分析的数据详细信息。

![视图设计器](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>日志数据延迟

下表介绍 **Microsoft 推出的 Azure CDN 标准版**、**Akamai 推出的 Azure CDN 标准版**和 **Verizon 推出的 Azure CDN 标准/高级版**的日志数据延迟。

Microsoft 日志数据延迟 | Verizon 日志数据延迟 | Akamai 日志数据延迟
--- | --- | ---
延迟 1 小时。 | 延迟 1 小时，在终结点传播完成后可能需要长达 2 小时的时间才会开始显示。 | 延迟 24 小时；如果数据是在 24 小时以前创建的，则最长需要在 2 小时后才会开始显示。 如果是最近创建的，则可能需要长达 25 个小时才能开始显示日志。

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>CDN 核心分析的诊断日志类型

Microsoft 当前仅提供核心分析日志，其中包含一些显示 HTTP 响应统计信息和出口统计信息的指标（如 CDN POP/边缘中所见）。

### <a name="core-analytics-metrics-details"></a>核心分析指标详细信息
下表显示了 **Microsoft 推出的 Azure CDN 标准版**、**Akamai 推出的 Azure CDN 标准版**和 **Verizon 推出的 Azure CDN 标准/高级版**的核心分析日志中提供的一系列指标。 并非所有提供商提供的所有指标都可用，尽管这种差异很小。 此表还显示了某提供商的给定指标是否可用。 这些指标仅适用于在其上拥有流量的 CDN 终结点。


|指标                     | 说明 | Microsoft | Verizon | Akamai |
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
| RequestCountCacheMiss | 导致缓存未命中的所有请求的计数。 缓存未命中的意思是在最靠近客户端的 POP 上找不到资产，因此从源进行检索。 | 是 | 是 | 否 |
| RequestCountCacheNoCache | 因边缘上的用户配置而无法缓存的资产的所有请求计数。 | 是 | 是 | 否 |
| RequestCountCacheUncacheable | 因资产的 Cache-Control 和 Expires 标头而无法缓存的资产的所有请求计数，这些标头指示该资产不应在 POP 上缓存或不应由 HTTP 客户端缓存。 | 是 | 是 | 否 |
| RequestCountCacheOthers | 上述未涵盖的具有缓存状态的所有请求的计数。 | 否 | 是 | 否  |
| EgressTotal | 出站数据传输量（按 GB 计） | 是 |是 |是 |
| EgressHttpStatus2xx | 针对状态代码为 2xx HTTP 的响应的出站数据传输量*（按 GB 计）。 | 是 | 是 | 否  |
| EgressHttpStatus3xx | 针对状态代码为 3xx HTTP 的响应的出站数据传输量（按 GB 计）。 | 是 | 是 | 否  |
| EgressHttpStatus4xx | 针对状态代码为 4xx HTTP 的响应的出站数据传输量（按 GB 计）。 | 是 | 是 | 否  |
| EgressHttpStatus5xx | 针对状态代码为 5xx HTTP 的响应的出站数据传输量（按 GB 计）。 | 是 | 是 | 否 |
| EgressHttpStatusOthers | 针对带其他 HTTP 状态代码的响应的出站数据传输量（按 GB 计）。 | 是 | 是 | 否  |
| EgressCacheHit | 针对直接从 CDN POP/边缘上 CDN 缓存传递的响应的出站数据传输量。 | 是 | 是 | 否 |
| EgressCacheMiss。 | 针对未在最近的 POP 服务器上找到并从源服务器检索的响应的出站数据传输量。 | 是 | 是 | 否 |
| EgressCacheNoCache | 针对因边缘上的用户配置而无法缓存的资源的出站数据传输量。 | 是 | 是 | 否 |
| EgressCacheUncacheable | 针对因资产的 Cache-Control 和/或 Expires 标头而无法缓存的资产的出站数据传输量。 指示该资产不应在 POP 上缓存或不应由 HTTP 客户端缓存。 | 是 | 是 | 否 |
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

其中，*time* 表示报告统计信息的小时边界的开始时间。 如果 CDN 提供程序不支持指标，则返回 null 值，而不是双精度或整型值。 该 null 值表示不存在指标且与 0 值不同。 在终结点上配置的每个域都有其中一组指标。

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







