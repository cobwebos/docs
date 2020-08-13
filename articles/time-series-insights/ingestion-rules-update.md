---
title: 即将更改 Azure 时序见解 Gen2 中的引入和平展规则 |Microsoft Docs
description: 引入规则更改
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/12/2020
ms.custom: lyhughes
ms.openlocfilehash: cdf90614e1f766fc37e04a1416081bd35e19b74e
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168195"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>即将针对新环境进行的 JSON 平展和转义规则更改

这些更改将仅适用于新创建的 Azure 时序见解 Gen2 环境。这些更改不适用于 Gen1 环境。

Azure 时序见解 Gen2 环境将按照一组特定的命名约定动态创建存储列。 引入事件时，会将一组规则应用于 JSON 有效负载和属性名称。 2020 年 7 月，已开始在新的 Azure 时序见解 Gen2 环境中应用对 JSON 数据的平展和存储方式的更改。 在以下情况下，此更改会对你造成影响：

* 如果 JSON 有效负载包含嵌套对象
* 如果 JSON 有效负载包含数组
* 如果在 JSON 属性名称中使用以下四个特殊字符中的任意一个：[ \ . '
* 如果嵌套对象内有一个或多个 TS ID 属性。

如果你创建一个新环境，并且上述一个或多个事例适用于你的事件负载，你将看到数据以不同方式平展和存储。 更改的摘要如下：

| 当前规则 | 新建规则 | 示例 JSON | 旧列名 | 新列名
|---|---| ---| ---|  ---|
| 使用下划线作为分割线来平展嵌套的 JSON |使用句点作为分割线来平展嵌套的 JSON  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| 不转义特殊字符 | 如果 JSON 属性名称包含特殊字符 .、 [ 、\、和 '，将使用 [' 和 '] 对属性名称进行转义。 在 [' 和 '] 中，额外转义单引号和反斜杠。 单引号将写为 \'，反斜杠将写为 \\\  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| 基元数组存储为字符串 | 基元类型的数组存储为动态类型  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
始终平展对象数组，产生多个事件 | 如果数组中的对象不具有 TS ID 或时间戳属性，则对象数组将整体存储为动态类型 | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>针对新环境的建议更改

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>如果 TS ID 和/或 timestamp 属性嵌套在对象内

* 任何新部署都需要匹配新的引入规则。 例如，如果 TS ID 为， `telemetry_tagId` 则需要更新任何 Azure 资源管理器 (ARM) 模板或自动部署脚本，以将配置 `telemetry.tagId` 为环境 TS ID。 嵌套的 JSON 中的事件源时间戳也需要进行此更改。

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>如果有效负载包含嵌套的 JSON 或特殊字符，并且你可以自动创作[时序模型](.\time-series-insights-update-tsm.md)变量表达式

* 更新执行 [TypesBatchPut](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) 的客户端代码，以匹配新的引入规则。 例如，`"value": {"tsx": "$event.series_value.Double"}` 之前的[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)应更新为以下选项之一：
  * `"value": {"tsx": "$event.series.value.Double"}`
  * `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>后续步骤

* 阅读 [Azure 时序见解 Gen2 存储和引入](./time-series-insights-update-storage-ingress.md)。

* 详细了解如何使用[时序查询 API](./concepts-query-overview.md) 查询数据。

* 详细了解[新的时序表达式语法](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)。
