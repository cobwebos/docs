---
title: Azure 时序见解 Gen2 中的引入和平展规则的即将发生的更改 |Microsoft Docs
description: 引入规则更改
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: lyhughes
ms.openlocfilehash: f667ca5ad82182fcf40d5c1fbb325f2ea99a7e08
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495102"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>新环境的 JSON 平展和转义规则即将发生的更改

**这些更改将仅应用于*新创建*的 Azure 时序见解 Gen2 环境。这些更改不适用于 Gen1 环境。**

Azure 时序见解 Gen2 环境使用一组特定的命名约定动态创建存储列。 引入事件时，会将一组规则应用于 JSON 有效负载和属性名称。 对于2020年7月的新 Azure 时序见解 Gen2 环境，对 JSON 数据的平展和存储方式的更改将生效。 在以下情况下，此更改会对你造成影响：

* 如果 JSON 负载包含嵌套对象
*  如果 JSON 负载包含数组
*  如果在 JSON 属性名称中使用以下任意四个特殊字符： [\。 '
*  如果一个或多个 TS ID 属性在嵌套对象内。

如果你创建一个新的环境，并且上面的一个或多个事例适用于你的事件负载，你将看到以不同方式平展和存储的数据。 下面是更改的摘要：

| 当前规则 | 新建规则 | 示例 JSON | 以前的列名 | 新列名
|---|---| ---| ---|  ---|
| 使用下划线作为 delineator 来平展嵌套的 JSON |使用句点作为 delineator 来平展嵌套的 JSON  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| 不转义特殊字符 | 包含特殊字符的 JSON 属性名称。 [\ 和 "使用 [" 和 "] 进行转义。 在 ["和"] 中，有对单引号和反斜杠的额外转义。 将编写一个单引号 \' ，并将反斜杠写入为\\\  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` | 
| 基元数组作为字符串存储 | 基元类型的数组存储为动态类型  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
始终平展对象的数组，产生多个事件 | 如果数组中的对象不具有 TS ID 或 timestamp 属性，则对象的数组将作为动态类型存储 | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>新环境的建议更改

#### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>如果 TS ID 和/或 timestamp 属性嵌套在对象中：

*  任何新的部署都需要匹配新的引入规则。 例如，如果 TS ID 为， `telemetry_tagId` 则需要更新任何 ARM 模板或自动部署脚本，以将配置 `telemetry.tagId` 为环境 TS ID。 嵌套 JSON 中的事件源时间戳还需要进行这种更改。

 #### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>如果负载包含嵌套的 JSON 或特殊字符，则自动编写[时序模型](.\time-series-insights-update-tsm.md)变量表达式

*  更新执行[TypesBatchPut](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput)的客户端代码以匹配新的引入规则。 例如，的上一个[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) `"value": {"tsx": "$event.series_value.Double"}` 应更新为以下选项之一：
    * `"value": {"tsx": "$event.series.value.Double"}`
    * `"value": {"tsx": "$event['series']['value'].Double"}`


## <a name="next-steps"></a>后续步骤

- 阅读[Azure 时序见解 Gen2 存储和入口](./time-series-insights-update-storage-ingress.md)。

- 阅读有关如何使用[时序查询 api](./concepts-query-overview.md)查询数据的详细信息。

- 阅读有关[新时序表达式语法](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)的详细信息。

