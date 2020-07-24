---
title: 流式处理引入事件源-Azure 时序见解 Gen2 |Microsoft Docs
description: 了解如何将数据流式传输到 Azure 时序见解 Gen2。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: c2a25632942c0c39a20fa0c7f51a1e8937bdd873
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87059395"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure 时序见解 Gen2 事件源

 Azure 时序见解 Gen2 环境最多可以有两个流式处理事件源。 支持两种类型的 Azure 资源作为输入：

- [Azure IoT 中心](../iot-hub/about-iot-hub.md)
- [Azure 事件中心](../event-hubs/event-hubs-about.md)

必须以 UTF-8 编码的 JSON 的形式发送事件。

## <a name="create-or-edit-event-sources"></a>创建或编辑事件源

事件源资源可以与 Azure 时序见解 Gen2 环境或不同的订阅在同一 Azure 订阅中。你可以使用[Azure 门户](time-series-insights-update-create-environment.md#create-a-preview-payg-environment)、 [Azure CLI](https://github.com/Azure/azure-cli-extensions/tree/master/src/timeseriesinsights)、 [ARM 模板](time-series-insights-manage-resources-using-azure-resource-manager-template.md)和[REST API](https://docs.microsoft.com/rest/api/time-series-insights/management/eventsources)来创建、编辑或删除环境的事件源。

当你连接事件源时，Azure 时序见解 Gen2 环境将从最早的事件开始读取当前存储在 Iot 或事件中心中的所有事件。

> [!IMPORTANT]
>
> * 将事件源附加到 Azure 时序见解 Gen2 环境时，可能会遇到较高的初始延迟。
> 事件源的延迟取决于 IoT 中心或事件中心内当前的事件数。
> * 在事件源数据第一次引入后，高延迟会降低。 如果持续遇到较高的延迟，请通过 Azure 门户提交支持票证。

## <a name="streaming-ingestion-best-practices"></a>流式引入最佳实践

* 始终为 Azure 时序见解 Gen2 环境创建唯一的使用者组，以使用来自事件源的数据。 重新使用使用者组可能会导致随机断开连接，并可能会导致数据丢失。

* 在同一 Azure 区域配置 Azure 时序见解 Gen2 环境和 IoT 中心和/或事件中心。 尽管可以在单独的区域中配置事件源，但不支持此方案，并且我们不能保证高可用性。

* 请勿超出环境的[吞吐量速率限制](./concepts-streaming-ingress-throughput-limits.md)或每个分区的限制。

* 配置延迟[警报](https://review.docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency?branch=pr-en-us-117938#monitor-latency-and-throttling-with-alerts)，以便在环境遇到处理数据问题时收到通知。

* 流式传输引入仅限用于近实时数据和最新数据，不支持流式传输历史数据。

* 了解如何对属性进行转义[和存储。](./concepts-json-flattening-escaping-rules.md)

* 提供事件源连接字符串时，请遵循最低权限原则。 对于事件中心，只使用*发送*声明来配置共享访问策略，对于 IoT 中心，只使用 "*服务连接*" 权限。

### <a name="historical-data-ingestion"></a>历史数据引入

Azure 时序见解 Gen2 目前不支持使用流式处理管道导入历史数据。 如果需要将过去的数据导入到环境中，请遵循以下准则：

* 不要并行传输实时和历史数据。 引入无序数据将导致查询性能下降。
* 按时间顺序引入历史数据，以获得最佳性能。
* 不超过以下引入吞吐率上限。
* 如果数据早于你的 Warm 存储保留期，请禁用 Warm 存储。

## <a name="event-source-timestamp"></a>事件源时间戳

配置事件源时，系统将要求你提供时间戳 ID 属性。 Timestamp 属性用于跟踪一段时间内 $event 的事件，这是在 Azure 时序见解 Gen2 资源管理器的[查询 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute)和绘图序列中用作 $ts 的时间。 如果在创建时未提供任何属性，或者事件中缺少 timestamp 属性，则会将事件的 IoT 中心或事件中心排队时间用作默认值。 时间戳属性值以 UTC 格式存储。

通常情况下，用户将选择自定义时间戳属性，并使用传感器或标记生成读取的时间，而不是使用默认的中心排队时间。 当设备出现间歇连接中断，并将一批延迟的消息转发到 Azure 时序见解 Gen2 时，尤其需要这样做。

如果自定义时间戳在嵌套 JSON 对象或数组中，则需要在[平展和转义命名约定](concepts-json-flattening-escaping-rules.md)之后提供正确的属性名称。 例如，[此处](concepts-json-flattening-escaping-rules.md#example-a)所示的 JSON 有效负载的事件源时间戳应作为输入 `"values.time"` 。

### <a name="time-zone-offsets"></a>时区偏移量

时间戳必须以 ISO 8601 格式发送并将以 UTC 格式存储。 如果提供了时区偏移量，则将应用该偏移量，然后将按 UTC 格式存储和返回时间。 如果偏移量的格式不正确，则将被忽略。 在您的解决方案可能没有原始偏移的上下文的情况下，您可以将偏移量数据发送到其他单独的事件属性，以确保它已保留，并且您的应用程序可以在查询响应中引用。

时区偏移量应设置为以下格式之一：

± HHMMZ</br>
± HH： MM</br>
± HH： MMZ</br>

## <a name="next-steps"></a>后续步骤

* 阅读[JSON 平展和转义规则](./concepts-json-flattening-escaping-rules.md)以了解如何存储事件。 

* 了解环境的[吞吐量限制](./concepts-streaming-ingress-throughput-limits.md)




