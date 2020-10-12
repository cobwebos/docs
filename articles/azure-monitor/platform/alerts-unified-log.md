---
title: Azure Monitor 中的日志警报
description: 满足指定的日志查询条件时，触发电子邮件、通知、调用网站 Url (webhook) 或自动化
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 5/31/2019
ms.subservice: alerts
ms.openlocfilehash: 8081c60833c3c02d55ae66ca695ba106dba01450
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91294132"
---
# <a name="log-alerts-in-azure-monitor"></a>Azure Monitor 中的日志警报

## <a name="overview"></a>概述

日志警报是 [Azure 警报](./alerts-overview.md)中支持的警报类型之一。 日志警报允许用户使用 [Log Analytics](../log-query/get-started-portal.md) 查询来评估每个设置频率的资源日志，并根据结果触发警报。 规则可使用 [操作组](./action-groups.md)触发一个或多个操作。

> [!NOTE]
> 可以将 [Log Analytics 工作区](../log-query/get-started-portal.md) 中的日志数据发送到 Azure Monitor 指标存储中。 指标警报具有[不同的行为](alerts-metric-overview.md)，该行为可能更可取，具体取决于你要使用的数据。 要了解如何将日志路由到指标，请参阅[日志的指标警报](alerts-metric-logs.md)。

> [!NOTE]
> 对于 API 版本 `2020-05-01-preview` 和以资源为中心的日志警报，当前没有额外的费用。  未来将公布预览版中的功能的定价，以及开始计费之前提供的通知。 如果你选择在通知期后继续使用新的 API 版本和以资源为中心的日志警报，则将按适用的费率向你收费。

## <a name="prerequisites"></a>必备条件

日志警报对 Log Analytics 的数据运行查询。 首先，应开始 [收集日志数据](resource-logs.md) 并查询日志数据以查找问题。 您可以使用 Log Analytics 中的 " [警报查询示例" 主题](../log-query/saved-queries.md) 来了解可以发现或 [开始编写您自己的查询的](../log-query/get-started-portal.md)内容。

[Azure 监视参与者](./roles-permissions-security.md) 是创建、修改和更新日志警报所需的常见角色。 还需要访问资源日志的 & 查询执行权限。 对资源日志进行部分访问可能导致查询失败或返回部分结果。 [详细了解如何在 Azure 中配置日志警报](./alerts-log.md)。

> [!NOTE]
> 使用旧 [Log Analytics 警报 API](api-alerts.md)管理 Log Analytics 的日志警报。 [详细了解如何切换到当前的 SCHEDULEDQUERYRULES API](alerts-log-api-switch.md)。

## <a name="query-evaluation-definition"></a>查询评估定义

日志搜索规则条件定义开始于：

- 要运行哪个查询？
- 如何使用结果？

以下各节介绍了可用于设置上述逻辑的不同参数。

### <a name="log-query"></a>日志查询
用于计算规则的 [Log Analytics](../log-query/get-started-portal.md) 查询。 此查询返回的结果用于确定是否触发警报。 查询的作用域可以是：

- 特定资源，如虚拟机。
- 大规模资源，如订阅或资源组。
- 使用 [跨资源查询](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights)的多个资源。 
 
> [!IMPORTANT]
> 警报查询具有限制，可确保结果的最佳性能和相关性。 [在此处了解详细信息](./alerts-log-query.md)。

> [!IMPORTANT]
> 仅支持使用当前的 scheduledQueryRules API 进行资源中心和 [跨资源查询](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) 。 如果使用旧版 [Log Analytics 警报 API](api-alerts.md)，则需要切换。 [了解有关切换的详细信息](./alerts-log-api-switch.md)

#### <a name="query-time-range"></a>查询时间范围

在规则条件定义中设置时间范围。 在工作区和 Application Insights 中，它称为 **Period**。 在所有其他资源类型中，称为 " **替代查询时间范围**"。

与在 log analytics 中一样，时间范围限制查询数据到指定的范围。 即使查询中使用了 **前** 一个命令，也会应用时间范围。

例如，当时间范围为60分钟时，查询将扫描60分钟，即使文本中 ** (1d) **也是如此。 时间范围和查询时间筛选需要匹配。 在此示例中，将 "**期间**"  /  **重写查询时间范围**更改为一天将按预期方式工作。

### <a name="measure"></a>度量

日志警报将日志转换为可计算的数字值。 可以衡量两个不同的内容：

#### <a name="count-of-the-results-table-rows"></a>结果表行计数

结果计数为默认度量值。 适用于事件，例如 Windows 事件日志、syslog 和应用程序异常。 在计算的时间范围内发生日志记录或不发生日志记录时触发触发器。

当你尝试在日志中检测数据时，日志警报最有效。 当你尝试检测日志中缺少的数据时，它的工作效率会降低。 例如，对虚拟机检测信号发出警报。

对于工作区和 Application Insights，将 **基于** 所选的 **结果数**来调用它。 在所有其他资源类型中，它被称为具有选择**表行**的**度量值**。

> [!NOTE]
> 由于日志是半结构化数据，因此，它们本质上比指标更具延迟，尝试检测日志中缺少数据时可能会遇到 misfires，应考虑使用 [指标警报](alerts-metric-overview.md)。 可以使用 [日志的指标警报](alerts-metric-logs.md)，将数据从日志发送到指标存储。

##### <a name="example-of-results-table-rows-count-use-case"></a>结果表行计数用例示例

如果你想要了解应用程序的响应时间，错误代码 500 (内部服务器错误) 。 可以创建一个警报规则，详情如下：

- **查询：** 

```Kusto
requests
| where resultCode == "500"
```

- **时间段：** 15 分钟
- **警报频率：** 15 分钟
- **阈值：** 大于 0

然后，警报规则监视所有以500错误代码结尾的请求。 查询每15分钟运行一次，过去15分钟。 如果只找到一个记录，则会触发警报并触发所配置的操作。

#### <a name="calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value"></a>基于数值列计算度量值 (例如 CPU 计数器值) 

对于工作区和 Application Insights，它将 **基于** 选择 **指标度量值**进行调用。 在所有其他资源类型中，它被称为 " **度量值** "，并选择任意数量的列名称。

### <a name="aggregation-type"></a>聚合类型

在多个记录上完成的用于将其聚合为一个数值的计算。 例如：
- **Count** 返回查询中的记录数
- **Average** 返回定义的度量值列 [**聚合粒度**](#aggregation-granularity) 的平均值。

在工作区和 Application Insights 中，仅在 **指标度量** 度量值类型中受支持。 查询结果必须包含一个名为 AggregatedValue 的列，该列在用户定义的聚合之后提供数值。 在所有其他资源类型中，" **聚合类型** " 是从该名称的字段中选择的。

### <a name="aggregation-granularity"></a>聚合粒度

确定用于聚合多个记录到一个数值的间隔。 例如，如果指定 **5 分钟**，则将使用指定的 **聚合类型** 按5分钟间隔对记录进行分组。

在工作区和 Application Insights 中，仅在 **指标度量** 度量值类型中受支持。 查询结果必须包含在查询结果中设置时间间隔 [ ( # B1 的 bin ](/azure/kusto/query/binfunction) 。 在所有其他资源类型中，控制此设置的字段称为 **聚合粒度**。

> [!NOTE]
> 如果 [bin ( # B1 ](/azure/kusto/query/binfunction) 可能导致不一致的时间间隔，则在运行时，警报服务将自动将 [Bin ( # B3 ](/azure/kusto/query/binfunction) 函数转换为 [bin_at ( # B5 ](/azure/kusto/query/binatfunction) 函数，以确保具有固定点的结果。

### <a name="split-by-alert-dimensions"></a>按警报维度拆分

按数字或字符串列将警报拆分为单独的警报，方法是将其分组为唯一的组合。 以规模 (订阅或资源组范围) 创建以资源为中心的警报时，可以按 Azure 资源 ID 列进行拆分。 "对 Azure 资源 ID 进行拆分" 列会将警报的目标更改为指定的资源。

在工作区和 Application Insights 中，仅在 **指标度量** 度量值类型中受支持。 字段 **在上**被称为聚合。 它限制为三列。 查询中的列数超过三个可能导致意外的结果。 在所有其他资源类型中，它在条件 (限制为六个分隔) 的情况下，配置为 " **按维度拆分** " 部分。

#### <a name="example-of-splitting-by-alert-dimensions"></a>按警报维度拆分的示例

例如，你想要监视在特定资源组中运行你的网站/应用的多个虚拟机的错误。 可以使用日志警报规则执行此操作，如下所示：

- **查询：** 

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    ```

    将工作区和 Application Insights 与 **指标度量** 警报逻辑一起使用时，需要将此行添加到查询文本：

    ```Kusto
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **资源 Id 列：** 警报规则中按资源 id 列划分的 _ResourceId (仅适用于当前) 的订阅和资源组
- **维度/聚合依据：**
  - 计算机 = VM1，VM2 (警报规则定义中的筛选值当前不适用于工作区和 Application Insights。 筛选查询文本。 ) 
- **时间段：** 15 分钟
- **警报频率：** 15 分钟
- **阈值：** 大于 0

此规则监视在过去15分钟内虚拟机是否有错误事件。 将单独监视每个虚拟机，并分别触发操作。

> [!NOTE]
> 按警报维度分割仅适用于当前 scheduledQueryRules API。 如果使用旧版 [Log Analytics 警报 API](api-alerts.md)，则需要切换。 [了解有关切换的详细信息](./alerts-log-api-switch.md)。 仅在 API 版本及更高版本中支持规模为资源中心的警报 `2020-05-01-preview` 。

## <a name="alert-logic-definition"></a>警报逻辑定义

定义要运行的查询并对结果进行评估后，需要定义警报逻辑以及何时触发操作。 以下各节介绍了可以使用的不同参数：

### <a name="threshold-and-operator"></a>阈值和运算符

查询结果将转换为一个数字，该数字将与阈值和运算符进行比较。

### <a name="frequency"></a>频率

查询运行的时间间隔。 可以设置为5分钟到一天。 必须等于或小于 [查询时间范围](#query-time-range) ，才能不错过日志记录。

例如，如果将时间段设置为30分钟，频率设置为1小时。  如果查询在00:00 运行，则它将返回23:30 和00:00 之间的记录。 下一次运行查询时，将在00:30 到01:00 之间返回记录01:00。 在00:00 和00:30 之间创建的任何记录永远都不会进行评估。

### <a name="number-of-violations-to-trigger-alert"></a>触发警报的冲突数

可以指定触发警报所需的警报评估期和失败次数。 允许您更好地定义触发警报的影响时间。 

例如，如果你的规则 [**聚合粒度**](#aggregation-granularity) 定义为 "5 分钟"，则仅当发生三次故障 (15 分钟) 上一小时后，才能触发警报。 此设置由您的应用程序业务策略定义。

## <a name="state-and-resolving-alerts"></a>状态和解决警报

日志警报是无状态的。 每次满足条件时，都会触发警报，即使之前已激发也是如此。 触发的警报不会解析。 你可以 [将警报标记为已关闭](alerts-managing-alert-states.md)。 你还可以对操作进行静音，以防它们在触发警报规则后触发一段时间。

在工作区和 Application Insights 中，这称为 **禁止显示警报**。 在所有其他资源类型中，这称为 " **静音操作**"。 

请参阅此警报评估示例：

| 时间    | 日志条件评估 | 结果 
| ------- | ----------| ----------| ------- 
| 00:05 | false | 不会触发警报。 没有调用任何操作。
| 00:10 | true  | 警报触发，操作组被调用。 新的警报状态处于活动状态。
| 00:15 | true  | 警报触发，操作组被调用。 新的警报状态处于活动状态。
| 00:20 | false | 不会触发警报。 没有调用任何操作。 早期警报状态保持活动状态。

## <a name="pricing-and-billing-of-log-alerts"></a>日志警报的定价和计费

定价信息位于 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)中。 "资源提供程序" 下列出 `microsoft.insights/scheduledqueryrules` 了日志警报：

- Application Insights 上显示的日志警报，其中包含与资源组和警报属性完全相同的资源名称。
- 与资源组和警报属性一起显示的 Log Analytics 上显示的日志警报当使用 [SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules)创建时。
- 从 [旧 LOG ANALYTICS API](./api-alerts.md) 创建的日志警报不跟踪 [Azure 资源](../../azure-resource-manager/management/overview.md) ，并且不会强制实施唯一资源名称。 这些警报仍在中 `microsoft.insights/scheduledqueryrules` 作为隐藏资源创建，这些资源具有此资源命名结构 `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` 。 旧版 API 上的日志警报与 "资源组" 和 "警报属性" 一起显示在一起。

> [!NOTE]
> 不受支持的资源字符（如） `<, >, %, &, \, ?, /` `_` 在隐藏资源名称中被替换为，这也会在计费信息中反映出来。

> [!NOTE]
> Log Analytics 的日志警报，该警报用于通过旧 [Log Analytics 警报 API](api-alerts.md) 和旧模板 [Log Analytics 保存的搜索和警报](../insights/solutions.md)进行管理。 [详细了解如何切换到当前的 SCHEDULEDQUERYRULES API](alerts-log-api-switch.md)。 任何警报规则管理都应该使用 [旧的 LOG ANALYTICS API](api-alerts.md) 完成，直到您决定切换，而不能使用隐藏的资源。

## <a name="next-steps"></a>后续步骤

* 了解如何[在 Azure 中创建日志警报](./alerts-log.md)。
* 了解 [Azure 日志警报中的 Webhook](alerts-log-webhook.md)。
* 了解 [Azure 警报](./alerts-overview.md)。
* 详细了解 [Log Analytics](../log-query/log-query-overview.md)。

