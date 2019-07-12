---
title: 使用 Azure 数据资源管理器 （预览版） 的 Azure Monitor 中的查询数据
description: 在本主题中，通过使用 Application Insights 和 Log Analytics 中创建 Azure 数据资源管理器代理的叉积查询查询 Azure 监视器中的数据
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: f363e59e6faa6b115eb40a2a5d35432f02299d52
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811188"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>使用 Azure 数据资源管理器 （预览版） 的 Azure Monitor 中的查询数据

Azure 数据资源管理器代理群集 （ADX 代理） 是一个实体，使您可以执行 Azure 数据资源管理器之间的叉积查询[Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)，并[日志分析 (LA)](/azure/azure-monitor/platform/data-platform-logs)中[Azure Monitor](/azure/azure-monitor/)服务。 可以将 Azure 监视器 Log Analytics 工作区或 Application Insights 应用映射与代理群集。 然后可以查询代理群集使用 Azure 数据资源管理器工具，并跨群集查询中引用它。 本文介绍如何连接到代理群集、 将代理服务器群集添加到 Azure 数据资源管理器 Web UI 中和从 Azure 数据资源管理器针对自己的 AI 应用程序或 LA 工作区中运行查询。

Azure 数据资源管理器代理流： 

![ADX 代理流](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>先决条件

> [!NOTE]
> ADX 代理是在预览模式下。 若要启用此功能，请联系[ADXProxy](mailto:adxproxy@microsoft.com)团队。

## <a name="connect-to-the-proxy"></a>连接到代理

1. 验证你的 Azure 数据资源管理器本机群集 (如*帮助*群集) 之前连接到 Log Analytics 或 Application Insights 群集将显示在左侧菜单上。

    ![ADX 本机群集](media/adx-proxy/web-ui-help-cluster.png)

1. Azure 数据资源管理器 UI 中 (https://dataexplorer.azure.com/clusters) ，选择**添加群集**。

1. 在中**添加群集**窗口：

    * 将 URL 添加到 LA 或 AI 群集。 例如： `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * 选择 **添加** 。

    ![添加群集](media/adx-proxy/add-cluster.png)

    如果将连接添加到多个代理群集中，为每个不同的名称。 否则它们将具有相同名称的左窗格中。

1. 建立连接后，LA 或 AI 群集将显示与本机 ADX 群集的左窗格中。 

    ![Log Analytics 和 Azure 数据资源管理器群集](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>运行查询

可以使用 Kusto 资源管理器，ADX web 浏览器中，Jupyter Kqlmagic 或 REST API 来查询代理群集。 

> [!TIP]
> * 数据库名称应具有与代理群集中指定的资源相同的名称。 名称是区分大小写。
> * 在跨群集查询，请确保[的应用和工作区命名](#application-insights-app-and-log-analytics-workspace-names)正确无误。

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>对本机 Azure 数据资源管理器群集进行查询 

Azure 数据资源管理器群集上运行查询 (如*StormEvents*表中*帮助*群集)。 当运行查询，验证在左窗格中选择本机 Azure 数据资源管理器群集。

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![查询 StormEvents 表](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>查询对 LA 或 AI 群集

LA 或所有群集上运行查询时，验证在左窗格中选择 LA 或 AI 群集。 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![查询 LA 工作区](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>查询从 ADX 代理 LA 或 AI 群集  

从代理 LA 或 AI 群集上运行查询，验证在左窗格中选择 ADX 本机群集。 下面的示例演示如何使用本机 ADX 群集在 LA 工作区的查询

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![查询从 Azure 数据资源管理器代理](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>从 ADX 代理跨 LA 或 AI 群集和 ADX 分类的查询 

从代理运行跨群集查询时，验证在左窗格中选择 ADX 本机群集。 以下示例演示了合并 ADX 群集表 (使用`union`) 与最新工作区。

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![从 Azure 数据资源管理器代理跨查询](media/adx-proxy/cross-query-adx-proxy.png)

使用[`join`运算符](/azure/kusto/query/joinoperator)，而不是联合，可能需要在 Azure 数据资源管理器本机群集上 （和不在代理服务器上） 运行它的提示。 

## <a name="additional-syntax-examples"></a>更多语法示例

调用 Application Insights (AI) 或日志分析 (LA) 群集时，以下语法选项有：

|语法说明  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 包含在此订阅中仅定义的资源的群集中的数据库 (**建议用于跨群集查询**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| 包含所有应用程序/工作区在此订阅中的群集    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|包含的所有应用程序/工作区的订阅中，并且是此资源组的成员的群集    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|包含在此订阅中仅定义的资源的群集      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

### <a name="application-insights-app-and-log-analytics-workspace-names"></a>Application Insights 应用和 Log Analytics 工作区名称

* 如果名称包含特殊字符，它们被替换为代理群集名称中的编码的 URL。 
* 如果名称包含不符合[KQL 标识符名称规则](/azure/kusto/query/schema-entities/entity-names)，它们的替换为短划线 **-** 字符。

## <a name="next-steps"></a>后续步骤

[编写查询](write-queries.md)