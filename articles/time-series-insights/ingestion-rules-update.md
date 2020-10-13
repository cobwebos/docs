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
ms.date: 10/02/2020
ms.custom: lyhughes
ms.openlocfilehash: 320d92ef0ad6d02dbe7c31b883eb7f73472378ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91667803"
---
# <a name="upcoming-changes-to-json-flattening-and-escaping-rules-for-new-environments"></a>新环境的 JSON 平展和转义规则即将发生更改

> [!IMPORTANT]
> 这些更改将仅应用于 *新创建* 的 Microsoft Azure 时序见解 Gen2 环境。 更改不适用于 Gen1 环境。

Azure 时序见解 Gen2 环境将按照一组特定的命名约定动态创建存储列。 如果事件是引入的，则时序见解会将一组规则应用到 JSON 负载和属性名称。 在7月2020中，对 JSON 数据如何平展和存储的更改对新的 Azure 时序见解 Gen2 环境有效。 在以下情况下，此更改会对你造成影响：

* JSON 有效负载包含嵌套的对象。
* JSON 有效负载包含数组。
* 在 JSON 属性名称中使用以下四个特殊字符中的任意一个： `[` `\` `.``'`
* 一个或多个时序 (TS) ID 属性位于嵌套对象内。

如果你创建一个新的环境，并且其中一个或多个事例适用于你的事件负载，你的数据将以不同方式进行平展和存储。 下表总结了这些更改：

| 当前规则 | 新建规则 | 示例 JSON | 以前的列名 | 新列名
|---|---| ---| ---|  ---|
| 使用下划线作为 delineator 来平展嵌套的 JSON。 |使用句点作为 delineator 来平展嵌套的 JSON。  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| 特殊字符不进行转义。 | JSON 属性名称包含特殊字符 `.` `[`   `\` ，并 `'` 使用和进行转义 `['` `']` 。 在 `['` 和中 `']` ，有多个转义的单引号和反斜杠。 单引号将作为 `\'` 和反斜杠写入 `\\` 。  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| 基元的数组存储为字符串。 | 基元类型的数组存储为动态类型。  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
始终平展对象的数组，从而生成多个事件。 | 如果数组中的对象不具有 TS ID 或时间戳属性，则对象的数组将作为动态类型存储。 | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>针对新环境的建议更改

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>如果 TS ID 和/或时间戳属性嵌套在对象中

任何新的部署都需要匹配新的引入规则。 例如，如果 TS ID 为 `telemetry_tagId` ，则需要更新任何 Azure 资源管理器模板或自动部署脚本，将其配置 `telemetry.tagId` 为环境 TS ID。 还需要对嵌套 JSON 中的事件源时间戳进行此更改。

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>如果有效负载包含嵌套的 JSON 或特殊字符，并且你可以自动创作[时序模型](.\time-series-insights-update-tsm.md)变量表达式

更新执行 [TypesBatchPut](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) 的客户端代码以匹配新的引入规则。 例如，你应该将上一个 [时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) 更新 `"value": {"tsx": "$event.series_value.Double"}` 为以下选项之一：

* `"value": {"tsx": "$event.series.value.Double"}`
* `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>后续步骤

* 了解 [Azure 时序见解 Gen2 存储和入口](./time-series-insights-update-storage-ingress.md)。

* 了解如何使用 [时序查询 api](./concepts-query-overview.md)查询数据。

* 详细了解[新的时序表达式语法](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)。
