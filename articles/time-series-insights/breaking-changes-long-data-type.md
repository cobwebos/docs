---
title: Azure 时序见解 Gen2 中的 long 数据类型支持 |Microsoft Docs
description: Azure 时序见解 Gen2 中的 long 数据类型支持。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: dpalled
ms.openlocfilehash: 34cf770a8ac75c2516480ec3136e61da15f4e4ff
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446638"
---
# <a name="adding-support-for-long-data-type-in-azure-time-series-insights-gen2"></a>添加对 Azure 时序见解 Gen2 中 long 数据类型的支持

添加对 long 数据类型的支持将影响我们仅在 Azure 时序见解 Gen2 环境中存储和索引数字数据的方式。 如果你有 Gen1 环境，你可以忽略这些更改。

从2020年6月29日或6月30日开始，根据你所在的区域，你的数据将被索引为**Long**和**Double**。  如果你对此更改有任何疑问或问题，请通过 Azure 门户提交支持票证并提及此信息。

如果受以下任何情况的影响，请进行建议的更改：

- **情况 1**：目前使用时序模型变量，并且仅在遥测数据中发送整数数据类型。
- **案例 2**：目前使用时序模型变量，并在遥测数据中发送整型和非整型数据类型。
- **情况 3**：使用分类变量将整数值映射到类别。
- **情况 4**：使用 JavaScript SDK 构建自定义前端应用程序。
- **情况 5**：接近热存储中的1000属性名称限制，并发送整型和非整型数据。 可以在[Azure 门户](https://portal.azure.com/)中查看属性计数。

如果有任何事例适用于你，请对模型进行更改。 使用建议的更改更新变量定义中的时序表达式（TSX）。 更新两个：

- Azure 时序见解 Gen2 资源管理器
- 使用我们的 Api 的任何自定义客户端

根据 IoT 解决方案和限制，可能无法查看发送到 Azure 时序见解 Gen2 环境的数据。 如果不确定数据是仅整型还是整型和非整型，则有几个选项可供选择：

- 可以等待该功能释放。 然后，在资源管理器 UI 中浏览原始事件，以了解在两个不同的列中保存了哪些属性。
- 您可以提前对所有数字标记进行建议的更改。
- 你可以暂时将事件的子集路由到存储，以便更好地了解和浏览你的架构。

若要存储事件，请打开 Azure 事件中心的[事件捕获](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)，或者从 IoT 中心[路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-storage)到 azure Blob 存储。

还可以通过[事件中心资源管理器](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)或[事件处理器主机](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send#receive-events)来观察数据。

如果使用 IoT 中心，请参阅[从内置终结点读取设备到云的消息](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin)，了解如何访问内置终结点。

> [!NOTE]
> 如果未进行建议的更改，则可能会遇到中断。 例如，通过查询 Api 或时序见解资源管理器访问的受影响的时序见解变量将返回**null** （即，不在资源管理器中显示数据）。

## <a name="recommended-changes"></a>建议的更改

### <a name="case-1-using-time-series-model-variables-and-sending-only-integral-data-types-in-telemetry-data"></a>案例1：使用时序模型变量并仅在遥测数据中发送整数数据类型

方案1的建议更改与案例2的更改相同。 按照方案2部分中的说明进行操作。

### <a name="case-2-using-time-series-model-variables-and-sending-both-integral-and-nonintegral-types-in-telemetry-data"></a>案例2：使用时序模型变量并在遥测数据中同时发送整型和非整型类型

如果你当前发送整数遥测数据，则你的数据将分为两列：

- **propertyValue_double**
- **propertyValue_long**

你的整数数据写入**propertyValue_long**。 在**propertyValue_double**中，以前的引入（以及将来的引入）数字数据不会进行复制。

如果要跨这两个列对数据进行查询，**则需要**在 TSX 中使用**合并（）** 标量函数。 函数接受相同**数据类型**的参数，并返回参数列表中的第一个非 null 值。 有关详细信息，请参阅[Azure 时序见解 Gen2 数据访问概念](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions)。

#### <a name="variable-definition-in-tsx---numeric"></a>TSX 中的变量定义-数字

*以前的变量定义：*

[![以前的变量定义](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*新变量定义：*

[![新的变量定义](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

您还可以将**合并（$Event toDouble （$event））** 用作自定义[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)。

#### <a name="inline-variable-definition-using-tsx-query-apis---numeric"></a>使用 TSX 查询 Api 的内联变量定义-数字

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

*新变量定义：*

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

您还可以将**合并（$Event toDouble （$event））** 用作自定义[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)。

> [!NOTE]
> 建议在可能使用这些变量的所有位置更新它们。 这些位置包括时序模型、保存的查询和 Power BI 连接器查询。

### <a name="case-3-using-categorical-variables-to-map-integer-values-to-categories"></a>事例 3：使用类别变量将整数值映射到类别

如果你当前使用将整数值映射到类别的分类变量，则很可能使用**toLong**函数将数据从**Double**类型转换为**Long**类型。 就像用例1和2一样，您需要合并**Double**和**Long** **数据类型**列。

#### <a name="variable-definition-in-time-series-explorer---categorical"></a>时序资源管理器中的变量定义-分类

*以前的变量定义：*

[![以前的变量定义](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*新变量定义：*

[![新的变量定义](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

您还可以将**合并（$Event toDouble （$event））** 用作自定义[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)。

分类变量仍要求值为整数类型。 **合并（）** 中所有参数的**数据**类型在自定义[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)中必须是**Long**类型。

#### <a name="inline-variable-definition-using-tsx-query-apis---categorical"></a>使用 TSX 查询 Api 的内联变量定义-分类

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

*新变量定义：*

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

分类变量仍要求值为整数类型。 **合并（）** 中所有参数的**数据**类型在自定义[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)中必须是**Long**类型。

> [!NOTE]
> 建议在可能使用这些变量的所有位置更新它们。 这些位置包括时序模型、保存的查询和 Power BI 连接器查询。

### <a name="case-4-using-the-javascript-sdk-to-build-a-custom-front-end-application"></a>事例 4：使用 JavaScript SDK 构建自定义前端应用程序

如果受案例1到步骤3的影响并生成自定义应用程序，则需要将查询更新为使用**合并（）** 函数，如前面的示例中所示。

### <a name="case-5-nearing-warm-store-1000-property-limit"></a>事例 5：即将达到暖存储的 1,000 属性限制

如果你是具有大量属性的热存储用户，并且认为此更改会将你的环境推送到1000温存储属性-名称限制，请通过 Azure 门户提交支持票证并提及此通信。

## <a name="next-steps"></a>后续步骤

- 查看[支持的数据类型](concepts-supported-data-types.md)的完整列表。
