---
title: 添加对 Long 数据类型的支持 | Microsoft Docs
description: 对 long 数据类型的支持
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: dpalled
ms.openlocfilehash: c31ca7fd3eca89159d583b8a51b59a7bd6b8ed67
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531026"
---
# <a name="adding-support-for-long-data-type"></a>添加对 long 数据类型的支持

这些更改将仅应用到 Gen2 环境。 如果你有 Gen1 环境，则可能会忽略这些更改。

我们正在更改 Azure 时序见解 Gen2 中存储和索引数字数据的方式，这可能会影响你。 如果你受以下任何情况的影响，请尽快进行必要的更改。 系统将在 2020 年 6 月 29 日到 6月 30 日之间开始以 Long 和 Double 数据类型为你的数据编制索引，具体取决于你所在的区域。 如果你对此更改有任何疑问或问题，请通过 Azure 门户提交支持票证并提及此信息。

在以下情况下，此更改会对你造成影响：

1. 如果你当前使用时序模型变量并且在遥测数据中仅发送整型数据类型。
1. 如果你当前使用时序模型变量并且在遥测数据中同时发送整型和非整型数据类型。
1. 如果你使用分类变量将整数值映射到类别。
1. 如果你使用 JavaScript SDK 构建自定义前端应用程序。
1. 如果你在暖存储 (WS) 中即将达到 1,000 属性名称限制，并且同时发送整型和非整型数据，则可在 [Azure 门户](https://portal.azure.com/)中将属性计数视为指标。

如果你符合以上任何情况，则需要对模型进行更改，使之适应此更改。 使用带有建议更改的 Api，在 Azure 时序见解 Gen2 资源管理器和任何自定义客户端中的变量定义中更新时序表达式。 有关详细信息，请参阅下文。

根据 IoT 解决方案和约束，可能无法查看发送到 Azure 时序见解 Gen2 环境的数据。 如果你不确定你的数据都是整型的还是既有整型的又有非整型的，则你有几个选择。 你可以等待此功能发布，然后在资源管理器 UI 中浏览原始事件，以了解哪些属性已保存在两个单独的列中。 对于所有数字标记，你可以提前进行以下更改，也可以暂时将一部分事件路由到存储，以便更好地了解和探究你的架构。 若要存储事件，请打开事件中心的[事件捕获](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)，或者从 IoT 中心[路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-storage)到 Azure Blob 存储。 还可以通过[事件中心资源管理器](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)或[事件处理器主机](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send#receive-events)来观察数据。 如果使用 IoT 中心，请参阅[此处](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin)的文档，了解如何访问内置终结点。

请注意，如果你受这些更改影响，并且无法在上述日期之前进行更改，则你可能会遇到中断，在这种情况下，通过查询 API 或时序见解资源管理器进行访问的受影响时序变量将返回 null（也就是说，不在资源管理器中显示数据）。

## <a name="recommended-changes"></a>建议的更改

事例 1 和 2：**使用时序模型变量并在遥测数据中仅发送整型数据类型或同时发送整型和非整型数据类型。**

如果你当前正在发送整数遥测数据，则你的数据将拆分到“propertyValue_double”和“propertyValue_long”这两个列中。

当更改生效时，整数数据将写入到“propertyValue_long”中，并且不会复制“propertyValue_double”中以前引入的（和将来引入的）数字数据。

如果你希望跨这两个列查询“propertyValue”属性的数据，则必须在 TSX 中使用 *coalesce()* 标量函数。 此函数接受相同 DataType 的参数，并返回参数列表中的第一个非 null 值（有关用法的详细信息，请参阅[此文](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions)）。

### <a name="variable-definition-in-time-series-explorer---numeric"></a>时序资源管理器中的变量定义 - 数字

以前的变量定义：

[![以前的变量定义](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

新的变量定义：

[![新的变量定义](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

还可以使用 “coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long))”作为自定义[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)。

### <a name="inline-variable-definition-using-time-series-query-apis---numeric"></a>使用时序查询 API 的内联变量定义 - 数字

以前的变量定义：

```JSON
"PropertyValueVariable": {

    "kind": "numeric",

    "value": {

        "tsx": "$event.propertyValue.Double"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

新的变量定义：

```JSON
"PropertyValueVariable ": {

    "kind": "numeric",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

    },

    "filter": null,

    "aggregation": {

        "tsx": "avg($value)"
    }
}
```

还可以使用 “coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long))”作为自定义[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)。

> [!NOTE]
> 我们建议你在可能使用这些变量的所有位置（时序模型、已保存的查询、Power BI 连接器查询）更新这些变量。

事例 3：**使用类别变量将整数值映射到类别**

如果你当前正在使用将整数值映射到类别的类别变量，则可以使用 toLong 函数将数据从 Double 类型转换为 Long 类型。 与上述事例中一样，你需要合并 Double 和 Long DataType 列。

### <a name="variable-definition-in-time-series-explorer---categorical"></a>时序资源管理器中的变量定义 - 类别

以前的变量定义：

[![以前的变量定义](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

新的变量定义：

[![新的变量定义](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

还可以使用 “coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long))”作为自定义[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)。

分类变量仍要求值为整数类型。 在自定义[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)中，coalesce() 中的所有参数的 DataType 都必须是 Long 类型。

### <a name="inline-variable-definition-using-time-series-query-apis---categorical"></a>使用时序查询 API 的内联变量定义 - 类别

以前的变量定义：

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "tolong($event.propertyValue.Double)"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

新的变量定义：

```JSON
"PropertyValueVariable_Long": {

    "kind": "categorical",

    "value": {

        "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

    },

    "categories": [

    {
        "label": "Good",

        "values": [0, 1, 2 ]

    },

    {

        "label": "Bad",

        "values": [ 3, 4 ]

    } ],

    "defaultCategory": {

        "label": "Unknown"

    }
}
```

分类变量仍要求值为整数类型。 在自定义[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)中，coalesce() 中的所有参数的 DataType 都必须是 Long 类型。

> [!NOTE]
> 我们建议你在可能使用这些变量的所有位置（时序模型、已保存的查询、Power BI 连接器查询）更新这些变量。

事例 4：**使用 JavaScript SDK 构建自定义前端应用程序**

如果你受上述事例 1-3 影响并构建自定义应用程序，则需将查询更新为使用 *coalesce()* 函数，如以上示例所示。

事例 5：**即将达到暖存储的 1,000 属性限制**

如果你是具有大量属性的暖存储用户，并且认为此更改会导致你的环境超出 1,000 WS 属性名称限制，请通过 Azure 门户提交支持票证并提及此信息。

## <a name="next-steps"></a>后续步骤

* 若要查看支持的数据类型的完整列表，请参阅[支持的数据类型](concepts-supported-data-types.md)。
