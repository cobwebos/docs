---
title: 添加对 Long 数据类型的支持 |Microsoft Docs
description: 支持 long 数据类型
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: dpalled
ms.openlocfilehash: ebb62b67b56134902f2752b43dd25fb0a7c6ccd4
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045767"
---
# <a name="adding-support-for-long-data-type"></a>添加对 long 数据类型的支持

这些更改将仅应用于预览版（PAYG）环境。 如果你有标准的 SKU TSI 环境，则可能会忽略这些更改。

我们正在更改 Azure 时序见解预览中存储和索引数值数据的方式，这可能会影响你。 如果受以下任何情况的影响，请尽快进行必要的更改。 你的数据将在2020年6月30日到6月30日之间根据你所在的区域，开始编制索引。 如果你对此更改有任何疑问或问题，请通过 Azure 门户提交支持票证并提及此通信。

在以下情况下，此更改会影响你：

1. 如果你当前使用时序模型变量并仅在遥测数据中发送整数数据类型，则为。
1. 如果你当前使用时序模型变量，并在遥测数据中同时发送整型和非整型数据类型。
1. 如果使用分类变量将整数值映射到类别，则为。
1. 如果使用 JavaScript SDK 来构建自定义前端应用程序。
1. 如果接近热存储（WS）中的1000属性名称限制并发送整型和非整型数据，则可以在[Azure 门户](https://portal.azure.com/)中查看属性计数。

如果上述任一情况适用于你，则需要更改模型以适应此更改。 在 TSI 资源管理器和任何自定义客户端中，使用我们的 Api 和建议的更改来更新变量定义中的时序表达式。 请参见以下详细内容。

根据 IoT 解决方案和约束，可能无法查看发送到 TSI PAYG 环境的数据。 如果不确定数据是仅整型还是整型和非整型，则可以使用几个选项。 你可以等待该功能被释放，然后在资源管理器 UI 中浏览原始事件，以了解哪些属性已保存在两个不同的列中。 对于所有数值标记，可以提前进行以下更改，也可以暂时将事件的子集路由到存储，以便更好地了解和浏览架构。 若要存储事件，请打开事件中心的[事件捕获](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)，或者从 IoT 中心[路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-storage)到 Azure Blob 存储。 还可以通过[事件中心资源管理器](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)或使用[事件处理程序主机](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send#receive-events)来观察数据。 如果使用 IoT 中心，请参阅[此处](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin)的文档，了解如何访问内置终结点。

请注意，如果您受这些更改的影响，并且无法通过上述日期进行更改，则您可能会遇到中断，其中，通过查询 Api 或时序见解资源管理器访问的受影响的时序变量将返回*null* （即，不在资源管理器中显示数据）。

## <a name="recommended-changes"></a>建议的更改

案例 1 & 2：**使用时序模型变量并只发送整数数据类型，或在遥测数据中同时发送整型和非整型类型。**

如果当前正在发送整数遥测数据，则数据将分为两列： "propertyValue_double" 和 "propertyValue_long"。

更改生效后，你的整数数据将写入 "propertyValue_long"，并且将不会复制 "propertyValue_double" 中的以前引入（和未来的引入）数字数据。

如果希望在这两个列中查询 "propertyValue" 属性的数据，则必须在 TSX 中使用*联合（）* 标量函数。 函数接受相同数据类型的参数，并返回自变量列表中的第一个非 null 值（有关详细信息，请参阅[此处](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions)的详细信息）。

### <a name="variable-definition-in-time-series-explorer---numeric"></a>时序资源管理器中的变量定义-数字

*以前的变量定义：*

[![上一个变量定义](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*新变量定义：*

[![新变量定义](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

你还可以使用 *"合并（$Event toDouble，（$event）"* 作为自定义[时序表达式。](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="inline-variable-definition-using-time-series-query-apis---numeric"></a>使用时序查询 Api 的内联变量定义-数字

*以前的变量定义：*

    "PropertyValueVariable": {

        "kind": "numeric",

        "value": {

            "tsx": "$event.propertyValue.Double"

        },

        "filter": null,

        "aggregation": {

            "tsx": "avg($value)"

*新变量定义：*

    "PropertyValueVariable ": {

        "kind": "numeric",

        "value": {

            "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

        },

        "filter": null,

        "aggregation": {

            "tsx": "avg($value)"

你还可以使用 *"合并（$Event toDouble，（$event）"* 作为自定义[时序表达式。](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

> [!NOTE]
> 我们建议您在可能使用这些变量的所有位置（时序模型、已保存的查询、Power BI 连接器查询）更新这些变量。

情况3：**使用分类变量将整数值映射到类别**

如果当前正在使用将整数值映射到类别的分类变量，则很可能使用 toLong 函数将数据从 Double 类型转换为 Long 类型。 例如，在上述情况下，需要合并双数据类型列和长数据类型列。

### <a name="variable-definition-in-time-series-explorer---categorical"></a>时序资源管理器中的变量定义-分类

*以前的变量定义：*

[![上一个变量定义](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*新变量定义：*

[![新变量定义](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

你还可以使用 *"合并（$Event toDouble，（$event）"* 作为自定义[时序表达式。](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

分类变量仍要求值为整数类型。 合并（）中所有参数的数据类型在自定义[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)中必须是 Long 类型。

### <a name="inline-variable-definition-using-time-series-query-apis---categorical"></a>使用时序查询 Api 的内联变量定义-分类

*以前的变量定义：*

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

*新变量定义：*

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

分类变量仍要求值为整数类型。 合并（）中所有参数的数据类型在自定义[时序表达式](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)中必须是 Long 类型。

> [!NOTE]
> 我们建议您在可能使用这些变量的所有位置（时序模型、已保存的查询、Power BI 连接器查询）更新这些变量。

情况4：**使用 JAVASCRIPT SDK 构建自定义前端应用程序**

如果受到上述案例1-3 的影响并构建自定义应用程序，则需要将查询更新为使用*合并（）* 函数，如以上示例中所示。

情况5：**接近热存储1000属性限制**

如果你是具有大量属性的热存储用户，并且认为此更改会将你的环境推送到 1000 WS 属性名称限制，请通过 Azure 门户提交支持票证并提及此通信。

## <a name="next-steps"></a>后续步骤

* 请参阅[此](concepts-supported-data-types.md)文，查看支持的数据类型的完整列表。
