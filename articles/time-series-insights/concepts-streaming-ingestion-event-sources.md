---
title: 流式引入事件源 - Azure 时序见解第 2 代 | Microsoft Docs
description: 了解如何将数据流式传输到 Azure 时序见解第 2 代中。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 9564611ee37c2aa2f104bbb14096a3b2f45fb7cd
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89485363"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure 时序见解第 2 代事件源

 Azure 时序见解第 2 代环境最多可以有两个流式传输事件源。 支持使用两种类型的 Azure 资源作为输入：

- [Azure IoT 中心](../iot-hub/about-iot-hub.md)
- [Azure 事件中心](../event-hubs/event-hubs-about.md)

必须以 UTF-8 编码的 JSON 形式发送事件。

## <a name="create-or-edit-event-sources"></a>创建或编辑事件源

你的事件源资源可以与你的 Azure 时序见解 Gen2 环境位于同一 Azure 订阅中，也可以位于其他订阅。你可以使用 [Azure门户](time-series-insights-update-create-environment.md#create-a-preview-payg-environment)、[Azure CLI](https://github.com/Azure/azure-cli-extensions/tree/master/src/timeseriesinsights)、[ARM 模板](time-series-insights-manage-resources-using-azure-resource-manager-template.md)和 [REST API](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) 来创建、编辑或删除环境的事件源。

连接事件源时，Azure 时序见解第 2 代环境会从最早的事件开始，读取当前存储在 IoT 中心或事件中心的所有事件。

> [!IMPORTANT]
>
> - 将事件源附加到 Azure 时序见解第 2 代环境时，可能会遇到较高的初始延迟。
> - 事件源的延迟取决于 IoT 中心或事件中心内当前的事件数。
> - 在事件源数据第一次引入后，高延迟会降低。 如果持续遇到较高的延迟，请通过 Azure 门户提交支持票证。

## <a name="streaming-ingestion-best-practices"></a>流式引入最佳做法

- 始终为 Azure 时序见解第 2 代环境创建唯一的使用者组以使用来自事件源的数据。 重新使用使用者组可能会导致随机断开连接，并且可能会导致数据丢失。

- 在同一 Azure 区域中配置 Azure 时序见解第 2 代环境和 IoT 中心和/或事件中心。 尽管可以在单独的区域中配置事件源，但此方案不受支持，并且我们不能保证高可用性。

- 请勿超出环境的[吞吐量速率限制](./concepts-streaming-ingress-throughput-limits.md)或每个分区的限制。

- 配置一个当你的环境在处理数据的过程中遇到问题时要发送的延迟[警报](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency#monitor-latency-and-throttling-with-alerts)。

- 流式传输引入仅限用于近实时数据和最新数据，不支持流式传输历史数据。

- 了解如何对属性进行转义以及 JSON [数据如何平展和存储。](./concepts-json-flattening-escaping-rules.md)

- 提供事件源连接字符串时，请遵循最低权限原则。 对于事件中心，请配置仅包含“发送”声明的共享访问策略；对于 IoT 中心，请仅使用“服务连接”权限。

### <a name="historical-data-ingestion"></a>历史数据引入

Azure 时序见解第 2 代目前不支持使用流式传输管道导入历史数据。 如果需要将过去的数据导入到环境中，请遵循以下准则：

- 不要并行传输实时和历史数据。 引入无序数据将导致查询性能下降。
- 按时间顺序引入历史数据，以获得最佳性能。
- 不超过以下引入吞吐率上限。
- 如果数据早于你的 Warm 存储保留期，请禁用 Warm 存储。

## <a name="event-source-timestamp"></a>事件源时间戳

配置事件源时，系统会要求你提供时间戳 ID 属性。 Timestamp 属性用于跟踪一段时间内的事件，这是将用作在 Azure 时序见解资源管理器的 [查询 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute) 和绘图序列中使用的 $event $ts 时间。 如果在创建时未提供此属性，或者事件中缺少时间戳属性，则会将事件的 IoT 中心或事件中心排队时间用作默认值。 时间戳属性值以 UTC 格式存储。

通常情况下，用户会选择自定义时间戳属性，并使用传感器或标记生成读数时的时间，而不是使用默认的中心排队时间。 当设备出现间歇性连接中断，并将一批延迟的消息转发到 Azure 时序见解第 2 代时，尤其需要这样做。

如果你的自定义时间戳在嵌套 JSON 对象或数组中，则需要根据[平展和转义命名约定](concepts-json-flattening-escaping-rules.md)提供正确的属性名称。 例如，[此处](concepts-json-flattening-escaping-rules.md#example-a)显示的 JSON 有效负载的事件源时间戳 应当输入为 `"values.time"`。

### <a name="time-zone-offsets"></a>时区偏移

时间戳必须以 ISO 8601 格式发送并将以 UTC 格式存储。 如果提供了时区偏移，则会应用该偏移，然后会以 UTC 格式存储和返回时间。 如果偏移格式不正确，则会忽略它。 在你的解决方案可能没有原始偏移的上下文的情况下，你可以在另外的单独事件属性中发送偏移数据，以确保它会保留，并且你的应用程序可以在查询响应中进行引用。

时区偏移应设置为以下格式之一：

±HHMMZ</br>
±HH:MM</br>
±HH:MMZ</br>

## <a name="next-steps"></a>后续步骤

- 阅读 [JSON 平展和转义规则](./concepts-json-flattening-escaping-rules.md)以了解如何存储事件。

- 了解你的环境的[吞吐量限制](./concepts-streaming-ingress-throughput-limits.md)
