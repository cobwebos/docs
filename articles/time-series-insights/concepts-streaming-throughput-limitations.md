---
title: 流式引入吞吐量限制-Azure 时序见解 |Microsoft Docs
description: 了解 Azure 时序见解中的入口吞吐量限制。
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: baa132589f5242ab480c9caa55ca793e0652ad6e
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135004"
---
# <a name="streaming-ingestion-throughput-limits"></a>流式引入吞吐量限制

Azure 时序见解流式传输数据引入限制如下所述。

> [!TIP]
> 有关所有预览版限制的综合列表，请阅读[规划预览版环境](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits)。

## <a name="per-environment-limitations"></a>基于环境的限制

一般而言，引入速率被视为与组织中的设备数、事件发出频率以及每个事件的大小因素有关：

*  设备数 × 事件发射频率 × 每个事件大小  。

默认情况下，时序见解可以按**每秒的速率（MBps）每个时序见解环境**引入传入数据。 存在针对[单个中心分区](concepts-streaming-throughput-limitations.md#hub-partitions-and-per-partition-limits)的其他限制。

> [!TIP]
>
> * 请求最多可提供引入速度为 16 MBps 的环境支持。
> * 如果需要更高的吞吐量，请通过在 Azure 门户中提交支持票证来联系我们。
 
* **示例 1：**

    Contoso Shipping 有 100,000 台设备，每分钟发出事件三次。 事件的大小为 200 字节。 它们使用包含四个分区的 IoT 中心作为时序见解事件源。

    * 时序见解环境的引入速率为：**100,000 个设备 * 200 字节/事件 * (每秒 3 个事件/60) = 1 MBps**。
    * 每个分区的引入速率为 0.25 MBps。
    * Contoso 发货的引入率将在规模限制范围内。

* **示例 2：**

    Contoso Fleet Analytics 有 60,000 台设备，每秒发出一个事件。 Contoso Fleet Analytics 使用分区计数为 4 的事件中心作为时序见解事件源。 事件的大小为 200 字节。

    * 环境引入速率为：60,000 设备 * 200 字节/事件 * 1 事件/秒 = 12 MBps。
    * 每个分区的速率为 3 MBps。
    * Contoso Fleet Analytics 的引入速率超出了环境和分区限制。 它们可以通过 Azure 门户向时序见解提交一个请求，要求提高其环境的引入速率，并创建一个事件中心，提高预览版限制中的分区数。

## <a name="hub-partitions-and-per-partition-limits"></a>中心分区和每个分区的限制

规划时序见解环境时，必须考虑要连接到时序见解的事件源的配置。 Azure IoT 中心和事件中心都利用分区来实现事件处理的水平缩放。 

分区是中心内保留的有序事件。 分区计数是在中心创建阶段设置的，且不可更改。

有关事件中心分区的最佳做法，请参阅[我需要多少分区？](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> 与 Azure 时序见解配合使用的大多数 IoT 中心只需要 4 个分区。

无论是为时序见解环境创建新的中心还是使用现有的集线器，都需要按分区引入速率来计算，以确定其是否在限制范围内。 

在 Azure 时序见解预览版中，**每个分区的限制目前为 0.5 MBps**。

### <a name="iot-hub-specific-considerations"></a>特定于 IoT 中心的注意事项

在 IoT 中心内创建设备时，会将该设备永久分配到某个分区。 这样，IoT 中心就可以保证事件的排序（因为分配永远不会更改）。

固定的分区分配也会影响引入下游 IoT 中心发送的数据的时序见解实例。 使用相同的网关设备 ID 将来自多个设备的消息转发到中心时，这些消息可能抵达同一分区，同时，可能会超出每个分区的规模限制。

**影响**：

* 如果单个分区的引入速率持续超过预览版的限制，则时序见解在超出 IoT 中心数据保留期之前可能不会同步所有设备遥测数据。 因此，如果持续超出引入限制，发送的数据可能会丢失。

为了缓解这种情况，我们建议采用以下最佳做法：

* 在部署解决方案之前按环境和按分区引入速率进行计算。
* 确保以最大可能的程度对 IoT 中心设备进行负载均衡。

> [!IMPORTANT]
> 对于使用 IoT 中心作为事件源的环境，请使用正在使用的集线器设备数来计算引入速率，以确保速率低于每个分区的 0.5 MBps 限制。
>
> * 即使多个事件同时抵达，也不会超出预览版的限制。

  ![IoT 中心分区关系图](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

若要详细了解如何优化中心吞吐量和分区，请参阅以下资源：

* [IoT 中心规模](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [事件中心规模](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [事件中心分区](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

## <a name="next-steps"></a>后续步骤

* 阅读有关数据[存储](concepts-storage.md)的信息