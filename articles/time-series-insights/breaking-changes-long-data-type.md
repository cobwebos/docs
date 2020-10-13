---
title: Azure 时序见解第 2 代中对 long 数据类型的支持 | Microsoft Docs
description: Azure 时序见解第 2 代中对 long 数据类型的支持。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: dpalled
ms.openlocfilehash: 2cf86ed4fd4305a37d27bf7a88e8493821ef085c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91629091"
---
# <a name="adding-support-for-long-data-type-in-azure-time-series-insights-gen2"></a>Azure 时序见解第 2 代中添加了对 long 数据类型的支持

添加对 long 数据类型的支持将仅影响我们在 Azure 时序见解第 2 代环境中对数字数据进行存储和为其编制索引的方式。 如果你的是第 1 代环境，则可以忽略这些更改。

从 2020 年 6 月 29 日或 6 月 30 日开始（具体取决于你所在的区域），你的数据将采用 **Long** 和 **Double** 数据类型编制索引。  如果你对此更改有任何疑问或问题，请通过 Azure 门户提交支持票证并提及此信息。

如果你受以下任何情况影响，请进行建议的更改：

- **情况 1**：你当前使用时序模型变量并且在遥测数据中仅发送整型数据类型。
- **情况 2**：你当前使用时序模型变量并且在遥测数据中同时发送整型和非整型数据类型。
- 情况 3：你使用分类变量将整数值映射到类别。
- **情况 4**：你使用 JavaScript SDK 构建自定义前端应用程序。
- **情况 5**：你在暖存储中已快要达到 1,000 属性名称限制，并且你同时发送整型和非整型数据。 可以在 [Azure 门户](https://portal.azure.com/)中将属性计数作为指标查看。

如果你符合上述任何情况，请对模型进行更改。 请使用建议的更改更新你的变量定义中的时序表达式 (TSX)。 同时更新：

- Azure 时序见解资源管理器
- 任何使用我们的 API 的自定义客户端

你可能看不到发送到你的 Azure 时序见解第 2 代环境的数据，具体取决于你的 IoT 解决方案和约束。 如果你不确定你的数据都是整型的还是既有整型的又有非整型的，则你有几个选择。

- 你可以等待此功能发布。 然后，在资源管理器 UI 中浏览原始事件，以了解哪些属性保存在两个单独的列中。
- 你可以提前对所有数字标记进行建议的更改。
- 你可以暂时将部分事件路由到存储，以便更好地了解和探究你的架构。

若要存储事件，请为 Azure 事件中心开启[事件捕获](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)，或者从 IoT 中心[路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-storage)到 Azure Blob 存储。

还可以通过[事件中心资源管理器](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)或[事件处理器主机](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send#receive-events)来观察数据。

如果你使用 IoT 中心，请参阅[从内置终结点读取设备到云消息](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin)以了解如何访问内置终结点。

> [!NOTE]
> 如果未进行建议的更改，则可能会发生中断。 例如，通过查询 API 或时序见解资源管理器访问的受影响的时序见解变量将返回 **null**（即，不在资源管理器中显示数据）。

## <a name="recommended-changes"></a>建议的更改

### <a name="case-1-using-time-series-model-variables-and-sending-only-integral-data-types-in-telemetry-data"></a>情况 1：使用时序模型变量并且在遥测数据中仅发送整型数据类型

针对情况 1 的建议更改与情况 2 相同。 请根据适用于情况 2 的部分中的说明进行操作。

### <a name="case-2-using-time-series-model-variables-and-sending-both-integral-and-nonintegral-types-in-telemetry-data"></a>情况 2：使用时序模型变量并在遥测数据中同时发送整型和非整型类型

如果你当前发送整数遥测数据，则你的数据将拆分为两列：

- **propertyValue_double**
- **propertyValue_long**

整数数据写入到 **propertyValue_long**。 **propertyValue_double** 中以前引入的（和将来引入的）数字数据不会进行复制。

如果你要跨这两个列查询 **propertyValue** 属性的数据，则需要在 TSX 中使用 **coalesce()** 标量函数。 此函数接受相同 **DataType** 的参数，并返回参数列表中的第一个非 null 值。 有关详细信息，请参阅 [Azure 时序见解第 2 代数据访问概念](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions)。

#### <a name="variable-definition-in-tsx---numeric"></a>TSX 中的变量定义 - 数字

*以前的变量定义：*

[![屏幕截图显示 PropertyValue 变量的 "添加新变量" 对话框。](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*新的变量定义：*

[![屏幕截图显示具有自定义值 numeric 的 PropertyValue 变量的 "添加新变量" 对话框。](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

还可以使用 **coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long))** 作为自定义[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)。

#### <a name="inline-variable-definition-using-tsx-query-apis---numeric"></a>使用 TSX 查询 API 的内联变量定义 - 数字

*以前的变量定义：*

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

*新的变量定义：*

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

还可以使用 **coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long))** 作为自定义[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)。

> [!NOTE]
> 建议在可能使用这些变量的所有位置更新它们。 这些位置包括时序模型、已保存的查询和 Power BI 连接器查询。

### <a name="case-3-using-categorical-variables-to-map-integer-values-to-categories"></a>事例 3：使用类别变量将整数值映射到类别

如果你当前在使用将整数值映射到类别的类别变量，则可以使用 **toLong** 函数将数据从 **Double** 类型转换为 **Long** 类型。 与情况 1 和情况 2 一样，你需要联合 **Double** 和 **Long** **DataType** 列。

#### <a name="variable-definition-in-time-series-explorer---categorical"></a>时序资源管理器中的变量定义 - 类别

*以前的变量定义：*

[![屏幕截图显示 PropertyValue 变量分类的 "添加新变量" 对话框。](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*新的变量定义：*

[![屏幕截图显示具有自定义值分类的 PropertyValue 变量的 "添加新变量" 对话框。](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

还可以使用 **coalesce($event.propertyValue.Double, toDouble($event.propertyValue.Long))** 作为自定义[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)。

分类变量仍要求值为整数类型。 在自定义[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)中，**coalesce()** 中的所有参数的 **DataType** 都必须是 **Long** 类型。

#### <a name="inline-variable-definition-using-tsx-query-apis---categorical"></a>使用 TSX 查询 API 的内联变量定义 - 类别

*以前的变量定义：*

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

*新的变量定义：*

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

分类变量仍要求值为整数类型。 在自定义[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)中，**coalesce()** 中的所有参数的 **DataType** 都必须是 **Long** 类型。

> [!NOTE]
> 建议在可能使用这些变量的所有位置更新它们。 这些位置包括时序模型、已保存的查询和 Power BI 连接器查询。

### <a name="case-4-using-the-javascript-sdk-to-build-a-custom-front-end-application"></a>事例 4：使用 JavaScript SDK 构建自定义前端应用程序

如果你受情况 1-3 影响并且构建的是自定义应用程序，则需要更新查询以使用 **coalesce()** 函数，如以上示例所示。

### <a name="case-5-nearing-warm-store-1000-property-limit"></a>事例 5：即将达到暖存储的 1,000 属性限制

如果你是具有大量属性的暖存储用户，并且认为此更改会导致你的环境超出 1,000 暖存储属性名称限制，请通过 Azure 门户提交支持票证并提及此信息。

## <a name="next-steps"></a>后续步骤

- 查看[支持的数据类型](concepts-supported-data-types.md)的完整列表。
