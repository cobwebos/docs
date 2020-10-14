---
title: 使用 Azure 数据资源管理器 () 预览版查询 Azure Monitor 中的数据
description: 使用 Azure 数据资源管理器在 Azure Monitor 中的 Azure 数据资源管理器、Log Analytics 工作区和经典 Application Insights 应用程序之间执行跨产品查询。
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 8a503a5456fc28bd1b3ebb69c784fc59b3c6e7df
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92049733"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>使用 Azure 数据资源管理器 () 预览版查询 Azure Monitor 中的数据
Azure 数据资源管理器 proxy 群集可让你在 Azure Monitor 中的 Azure 数据资源管理器、Log Analytics 工作区和经典 Application Insights 应用程序之间执行跨产品查询。 可以将 Azure Monitor 或经典 Application Insights 应用中的 Log Analytics 工作区映射为代理群集。 然后，可以使用 Azure 数据资源管理器工具查询代理群集，并在跨群集查询中引用该群集。 本文介绍了如何连接到代理群集、将代理群集添加到 Azure 数据资源管理器 Web UI，以及如何从 Azure 数据资源管理器对 Log Analytics 工作区或经典 Application Insights 应用运行查询。

下图显示 Azure 数据资源管理器代理流：

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-monitor-proxy-flow.png" alt-text="Azure 数据资源管理器代理流。":::


> [!NOTE]
> Azure 数据资源管理器代理采用公共预览。 [连接到代理](#connect-to-the-proxy) 以启用群集的代理功能。 

## <a name="connect-to-the-proxy"></a>连接到代理
若要连接 Log Analytics 工作区或经典 Application Insights 应用，请打开[Azure 数据资源管理器 WEB UI](https://dataexplorer.azure.com/clusters)。 在连接到 Log Analytics 或 Application Insights 群集之前，请先确认 Azure 数据资源管理器本机群集（例如 *help* 群集）是否显示在左侧菜单中。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Azure 数据资源管理器代理流。" 以建立连接。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Azure 数据资源管理器代理流。":::
 
> [!NOTE]
> 如果将某个连接添加到多个代理群集，请为每个代理群集指定不同的名称。 否则它们全部以相同的名称显示在左窗格。

建立连接后，Log Analytics 或 Application Insights 群集将显示在左窗格中，其中包含本机 Azure 数据资源管理器群集。 

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Azure 数据资源管理器代理流。":::
 
> [!NOTE]
> 可映射的 Azure Monitor 工作区数限制为 100 个。

## <a name="create-queries-using-azure-monitor-data"></a>使用 Azure Monitor 数据创建查询

你可以使用支持 Kusto 查询的客户端工具（例如： Kusto Explorer、Azure 数据资源管理器 Web UI、Jupyter Kqlmagic、Flow、PowerQuery、PowerShell、Jarvis、Lens 和 REST API）来运行查询。

> [!NOTE]
> Azure 数据资源管理器代理功能仅用于检索数据。 有关详细信息，请参阅[函数可支持性](#function-supportability)。

> [!TIP]
> * 数据库名称应与代理群集中指定的资源名称相同。 名称区分大小写。
> * 在跨群集查询中，请确保 Application Insights 应用和 Log Analytics 工作区的命名正确。
>     * 如果名称包含特殊字符，将按代理群集名称中的 URL 编码替换这些字符。 
>     * 如果名称包含的字符不符合 [KQL 标识符命名规则](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names)，这些字符将由短划线 **-** 字符替换。

### <a name="direct-query-from-your-log-analytics-or-application-insights-proxy-cluster"></a>直接查询 Log Analytics 或 Application Insights 代理群集

在 Log Analytics 或 Application Insights 群集上运行查询。 验证是否在左窗格中选择了群集。 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the Log Analaytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Azure 数据资源管理器代理流。":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-proxy-cluster-and-the-azure-data-explorer-native-cluster"></a>跨查询 Log Analytics 或 Application Insights 代理群集和 Azure 数据资源管理器本机群集

从代理运行跨群集查询时，请验证是否已在左窗格中选择 Azure 数据资源管理器本地群集。 下面的示例演示如何结合使用 [union](/azure/data-explorer/kusto/query/unionoperator) 运算符和 Log Analytics 工作区来合并 Azure 数据资源管理器群集表。

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```
使用[ `join` 运算符](/azure/data-explorer/kusto/query/joinoperator?pivots=azuredataexplorer)而不是 union，可能需要[提示](/azure/data-explorer/kusto/query/joinoperator?pivots=azuredataexplorer#join-hints)才能在 Azure 数据资源管理器本机群集 (上运行它，而不是在代理) 上运行。 

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>从一个租户中的 Azure 数据资源管理器群集加入另一个租户中的 Azure Monitor 资源的数据

Azure 数据资源管理器 proxy 不支持跨租户查询。 你已登录到单个租户，用于运行跨这两个资源的查询。

如果 Azure 数据资源管理器资源位于租户 "A"，并且 Log Analytics 工作区位于租户 "B" 中，请使用以下两种方法之一：

- Azure 数据资源管理器允许在不同租户中为主体添加角色。 将租户 "B" 中的用户 ID 添加为 Azure 数据资源管理器群集上的授权用户。 验证 Azure 数据资源管理器群集上的 *["TrustedExternalTenant"](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* 属性包含租户 "B"。 在租户 "B" 中完全运行交叉查询。

- 使用 [Lighthouse](/azure/lighthouse/) 将 Azure Monitor 资源投影到租户 "A"。

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>从不同租户连接到 Azure 数据资源管理器群集

Kusto 资源管理器会自动登录到用户帐户最初所属的租户。 若要访问具有相同用户帐户的其他租户中的资源，必须 `tenantId` 在连接字符串中显式指定： `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>函数可支持性

Azure 数据资源管理器代理群集支持 Log Analytics 和 Application Insights 的函数。 此功能允许跨群集查询直接引用 Azure Monitor 表格函数。

代理支持以下命令：

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

下图描述了一个从 Azure 数据资源管理器 Web UI 查询表格函数的示例。 若要使用此函数，请在“查询”窗口中运行名称。

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Azure 数据资源管理器代理流。":::
 
> [!NOTE]
> Azure Monitor 仅支持不支持参数的表格函数。

## <a name="additional-syntax-examples"></a>其他语法示例

调用 Log Analytics 或 Application Insights 群集时，可以使用以下语法选项：

|语法说明  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 仅包含此订阅中所定义资源的群集中的数据库（**建议用于跨群集查询**） |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| 包含此订阅中所有应用/工作区的群集    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|包含订阅中所有应用/工作区且属于此资源组的群集    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|仅包含此订阅中定义的资源的群集      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>后续步骤

- 详细了解 [Log Analytics 工作区和 Application Insights 的数据结构](data-platform-logs.md)。
- 了解如何 [在 Azure 数据资源管理器中编写查询](https://docs.microsoft.com/azure/data-explorer/write-queries)。