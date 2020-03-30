---
title: 使用 Azure 数据资源管理器在 Azure 监视器中查询数据（预览）
description: 本主题介绍如何使用 Application Insights 和 Log Analytics 通过为跨产品查询创建 Azure 数据资源管理器代理来查询 Azure Monitor 中的数据
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: c7e98c31c0db1db3051ad66df6526dcbddb265c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560416"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>使用 Azure 数据资源管理器（预览版）查询 Azure Monitor 中的数据

Azure 数据资源管理器代理群集（ADX 代理）是一个实体，可用于在 [Azure Monitor](/azure/azure-monitor/) 服务中的 Azure 数据资源管理器、[Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview) 和 [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) 之间执行跨产品查询。 可将 Azure Monitor Log Analytics 工作区或 Application Insights 应用映射为代理群集。 然后，可以使用 Azure 数据资源管理器工具查询代理群集，并在跨群集查询中引用该群集。 本文介绍如何连接到代理群集、将代理群集添加到 Azure 数据资源管理器 Web UI，然后从 Azure 数据资源管理器针对 AI 应用或 LA 工作区运行查询。

Azure 数据资源管理器代理流： 

![ADX 代理流](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>先决条件

> [!NOTE]
> ADX 代理处于预览模式。 [连接到代理](#connect-to-the-proxy)，为群集启用 ADX 代理功能。 如果有任何疑问，请联系 [ADXProxy](mailto:adxproxy@microsoft.com) 团队。

## <a name="connect-to-the-proxy"></a>连接到代理

1. 在连接到 Log Analytics 或 Application Insights 群集之前，请先确认 Azure 数据资源管理器本机群集（例如 *help* 群集）是否显示在左侧菜单中。

    ![ADX 本机群集](media/adx-proxy/web-ui-help-cluster.png)

1. 在 Azure 数据资源管理器 UI (https://dataexplorer.azure.com/clusters) 中，选择“添加群集”。****

1. 在“添加群集”窗口中，添加 LA 或 AI 群集的 URL。**** 
    
    * 对于 LA：`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * 对于 AI：`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * 选择“添加”****。

    ![添加群集](media/adx-proxy/add-cluster.png)

    如果将某个连接添加到多个代理群集，请为每个代理群集指定不同的名称。 否则它们全部以相同的名称显示在左窗格。

1. 建立连接后，LA 或 AI 群集将与本机 ADX 群集一起显示在左窗格中。 

    ![Log Analytics 和 Azure 数据资源管理器群集](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>运行查询

您可以使用支持 Kusto 查询的客户端工具运行查询，例如：库斯特资源管理器、ADX Web UI、Jupyter Kqlmagic、Flow、PowerQuery、PowerShell、Jarvis、镜头、REST API。

> [!TIP]
> * 数据库名称应与代理群集中指定的资源名称相同。 名称区分大小写。
> * 在跨群集查询中，请确保 Application Insights 应用和 Log Analytics 工作区的命名正确。
>     * 如果名称包含特殊字符，将按代理群集名称中的 URL 编码替换这些字符。 
>     * 如果名称包含的字符不符合 [KQL 标识符命名规则](/azure/kusto/query/schema-entities/entity-names)，这些字符将由短划线 **-** 字符替换。

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>从 LA 或 AI ADX 代理群集直接查询

在 LA 或 AI 群集上运行查询。 验证是否在左窗格中选择了群集。 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![查询 LA 工作区](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>对 LA 或 AI ADX 代理群集和 ADX 本机群集进行交叉查询 

从代理运行跨群集查询时，请确认是否在左窗格中选择了 ADX 本机群集。 以下示例演示如何将 ADX 群集表与 LA 工作区合并到一起（使用 `union`）。

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [ ![从 Azure 数据资源管理器代理运行交叉查询](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

使用[`join`运算符](/azure/kusto/query/joinoperator)（而不是联合） 可能需要 在[`hint`](/azure/kusto/query/joinoperator#join-hints)Azure 数据资源管理器本机群集（而不是在代理上）上运行它。 

## <a name="additional-syntax-examples"></a>其他语法示例

调用 Application Insights (AI) 或 Log Analytics (LA) 群集时，可以使用以下语法选项：

|语法说明  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 仅包含此订阅中所定义资源的群集中的数据库（**建议用于跨群集查询**） |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| 包含此订阅中所有应用/工作区的群集    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|包含订阅中所有应用/工作区且属于此资源组的群集    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|仅包含此订阅中定义的资源的群集      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>后续步骤

[编写查询](write-queries.md)
