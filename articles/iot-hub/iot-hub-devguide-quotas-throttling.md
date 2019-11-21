---
title: 了解 Azure IoT 中心配额和限制 | Microsoft Docs
description: 开发人员指南 - 介绍适用于 IoT 中心的配额和预期限制行为。
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: daeb09acd11d727b11ad8a7b98d97ff90fddc6d8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228260"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>参考 - IoT 中心配额和限制

This article explains the quotas for an IoT Hub, and provides information to help you understand how throttling works.

## <a name="quotas-and-throttling"></a>配额和限制

每个 Azure 订阅最多可以有 50 个 IoT 中心和 1 个免费中心。

每个 IoT 中心都在特定层中预配了特定单位数。 层和单位数决定了可以发送的消息的每日配额上限。 对于免费层中心，用于计算每日配额的消息大小为 0.5 KB，对于所有其他层为 4 KB。 有关详细信息，请参阅 [Azure IoT 中心定价](https://azure.microsoft.com/pricing/details/iot-hub/)。

层还决定了 IoT 中心对所有操作强制实施的限制。

### <a name="iot-plug-and-play"></a>IoT 即插即用

During public preview, IoT Plug and Play devices will send separate messages per interface, which may increase the number of messages counted towards your message quota.

## <a name="operation-throttles"></a>操作限制

Operation throttles are rate limitations that are applied in minute ranges and are intended to prevent abuse. They're also subject to [traffic shaping](#traffic-shaping).

下表显示了强制限制。 值与单个中心相关。

| 限制 | 免费、B1 和 S1 | B2 和 S2 | B3 和 S3 | 
| -------- | ------- | ------- | ------- |
| [Identity registry operations](#identity-registry-operations-throttle) (create, retrieve, list, update, delete) | 1.67/秒/单位（100/分钟/单位） | 1.67/秒/单位（100/分钟/单位） | 83.33/sec/unit (5,000/min/unit) |
| [New device connections](#device-connections-throttle) (this limit applies to the rate of _new connections_, not the total number of connections) | 高于 100/秒或 12/秒/单位 <br/> 例如，两个 S1 单位是 2\*12 = 24 个新连接/秒，但你的单位至少有 100 个新连接/秒。 如果有 9 个 S1 单位，则你的单位就有 108 个新连接/秒 (9\*12)。 | 120 个新连接/秒/单位 | 6,000 new connections/sec/unit |
| 设备到云的发送 | Higher of 100 send operations/sec or 12 send operations/sec/unit <br/> For example, two S1 units are 2\*12 = 24/sec, but you have at least 100 send operations/sec across your units. With nine S1 units, you have 108 send operations/sec (9\*12) across your units. | 120 send operations/sec/unit | 6,000 send operations/sec/unit |
| 云到设备的发送<sup>1</sup> | 1.67 send operations/sec/unit (100 messages/min/unit) | 1.67 send operations/sec/unit (100 send operations/min/unit) | 83.33 send operations/sec/unit (5,000 send operations/min/unit) |
| 云到设备的接收<sup>1</sup> <br/> （仅当设备使用 HTTPS 时）| 16.67 receive operations/sec/unit (1,000 receive operations/min/unit) | 16.67 receive operations/sec/unit (1,000 receive operations/min/unit) | 833.33 receive operations/sec/unit (50,000 receive operations/min/unit) |
| 文件上传 | 1.67 file upload initiations/sec/unit (100/min/unit) | 1.67 file upload initiations/sec/unit (100/min/unit) | 83.33 file upload initiations/sec/unit (5,000/min/unit) |
| 直接方法<sup>1</sup> | 160KB/秒/单位<sup>2</sup> | 480KB/秒/单位<sup>2</sup> | 24MB/秒/单位<sup>2</sup> | 
| 查询 | 20/min/unit | 20/min/unit | 1,000/min/unit |
| 孪生（设备和模块）读取<sup>1</sup> | 100/sec | Higher of 100/sec or 10/sec/unit | 500/sec/unit |
| 孪生更新（设备和模块）<sup>1</sup> | 50/sec | Higher of 50/sec or 5/sec/unit | 250/sec/unit |
| 作业操作<sup>1</sup> <br/> （创建、更新、列表、删除） | 1.67/秒/单位（100/分钟/单位） | 1.67/秒/单位（100/分钟/单位） | 83.33/sec/unit (5,000/min/unit) |
| 作业设备操作<sup>1</sup> <br/> （更新孪生、调用直接方法） | 10/秒 | 高于 10/秒或 1/秒/单位 | 50/秒/单位 |
| 配置和 Edge 部署<sup>1</sup> <br/> （创建、更新、列表、删除） | 0.33/秒/单位（20/分钟/单位） | 0.33/秒/单位（20/分钟/单位） | 0.33/秒/单位（20/分钟/单位） |
| Device stream initiation rate<sup>1</sup> | 5 个新流/秒 | 5 个新流/秒 | 5 个新流/秒 |
| Maximum number of concurrently connected device streams<sup>1</sup> | 50 | 50 | 50 |
| Maximum device stream data transfer<sup>1</sup> (aggregate volume per day) | 300 MB | 300 MB | 300 MB |

<sup>1</sup>此功能在 IoT 中心的基本层内不可用。 有关详细信息，请参阅[如何选择正确的 IoT 中心](iot-hub-scaling.md)。 <br/><sup>2</sup>Throttling meter size is 4 KB.

### <a name="throttling-details"></a>Throttling Details

* The meter size determines at what increments your throttling limit is consumed. If your direct call's payload is between 0 and 4 KB, it is counted as 4 KB. You can make up to 40 calls per second per unit before hitting the limit of 160 KB/sec/unit.

   Similarly, if your payload is between 4 KB and 8 KB, each call accounts for 8 KB and you can make up to 20 calls per second per unit before hitting the max limit.

   Finally, if your payload size is between 156KB and 160 KB, you'll be able to make only 1 call per second per unit in your hub before hitting the limit of 160 KB/sec/unit.

*  For *Jobs device operations (update twin, invoke direct method)* for tier S2, 50/sec/unit only applies to when you invoke methods using jobs. If you invoke direct methods directly, the original throttling limit of 24 MB/sec/unit (for S2) applies.

*  **Quota** is the aggregate number of messages you can send in your hub *per day*. You can find your hub's quota limit under the column **Total number of messages /day** on the [IoT Hub pricing page](https://azure.microsoft.com/pricing/details/iot-hub/).

*  Your cloud-to-device and device-to-cloud throttles determine the maximum *rate* at which you can send messages -- number of messages irrespective of 4 KB chunks. Each message can be up to 256 KB which is the [maximum message size](iot-hub-devguide-quotas-throttling.md#other-limits).

*  It's a good practice to throttle your calls so that you don't hit/exceed the throttling limits. If you do hit the limit, IoT Hub responds with error code 429 and the client should back-off and retry. These limits are per hub (or in some cases per hub/unit). For more information, refer to [Manage connectivity and reliable messaging/Retry patterns](iot-hub-reliability-features-in-sdks.md#retry-patterns).

### <a name="traffic-shaping"></a>Traffic shaping

To accommodate burst traffic, IoT Hub accepts requests above the throttle for a limited time. The first few of these requests are processed immediately. However, if the number of requests continues violate the throttle, IoT Hub starts placing the requests in a queue and processed at the limit rate. This effect is called *traffic shaping*. Furthermore, the size of this queue is limited. If the throttle violation continues, eventually the queue fills up, and IoT Hub starts rejecting requests with `429 ThrottlingException`.

For example, you use a simulated device to send 200 device-to-cloud messages per second to your S1 IoT Hub (which has a limit of 100/sec D2C sends). For the first minute or two, the messages are processed immediately. However, since the device continues to send more messages than the throttle limit, IoT Hub begins to only process 100 messages per second and puts the rest in a queue. You start noticing increased latency. Eventually, you start getting `429 ThrottlingException` as the queue fills up, and the "number of throttle errors" in [IoT Hub's metrics](iot-hub-metrics.md) starts increasing.

### <a name="identity-registry-operations-throttle"></a>Identity registry operations throttle

Device identity registry operations are intended for run-time use in device management and provisioning scenarios. 通过[导入和导出作业](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)可以支持读取或更新大量的设备标识。

### <a name="device-connections-throttle"></a>Device connections throttle

“设备连接”限制控制与 IoT 中心建立新设备连接的速率。 “设备连接”限制不控制同时连接的最大设备数。 “设备连接”速率限制取决于为 IoT 中心预配的单位数。

例如，如果购买的是单一 S1 单位，则限制为每秒 100 个连接。 Therefore, to connect 100,000 devices, it takes at least 1,000 seconds (approximately 16 minutes). 但是，同时连接的设备数可与用户在标识注册表中注册的设备数相同。

## <a name="other-limits"></a>其他限制

IoT 中心强制实施的其他操作限制：

| Operation | Limit |
| --------- | ----- |
| 设备 | The total number of devices plus modules that can be registered to a single IoT hub is capped at 1,000,000. The only way to increase this limit is to contact [Microsoft Support](https://azure.microsoft.com/support/options/).|
| 文件上传 | 10 concurrent file uploads per device. |
| 作业<sup>1</sup> | Maximum concurrent jobs is 1 (for Free and S1), 5 (for S2), and 10 (for S3). However, the max concurrent [device import/export jobs](iot-hub-bulk-identity-mgmt.md) is 1 for all tiers. <br/>Job history is retained up to 30 days. |
| 额外终结点 | 付费 SKU 中心可能有 10 个额外终结点。 免费 SKU 中心可能有 1 个额外终结点。 |
| Message routing queries | Paid SKU hubs may have 100 routing queries. Free SKU hubs may have five routing queries. |
| 消息扩充 | Paid SKU hubs can have up to 10 message enrichments. Free SKU hubs can have up to 2 message enrichments.|
| 设备到云的消息传递 | 最大消息大小为 256 KB |
| 云到设备的消息传递<sup>1</sup> | 最大消息大小为 64 KB。 Maximum pending messages for delivery is 50 per device. |
| 直接方法<sup>1</sup> | 直接方法有效负载的最大大小为 128 KB。 |
| 自动设备配置<sup>1</sup> | 每个付费 SKU 中心 100 个配置。 每个免费 SKU 中心 20 个配置。 |
| IoT Edge automatic deployments<sup>1</sup> | 每个部署 20 个模块。 每个付费 SKU 中心 100 个部署。 10 deployments per free SKU hub. |
| 孪生<sup>1</sup> | Maximum size of desired properties and reported properties sections are 32 KB each. Maximum size of tags section is 8 KB. |

<sup>1</sup>此功能在 IoT 中心的基本层内不可用。 有关详细信息，请参阅[如何选择正确的 IoT 中心](iot-hub-scaling.md)。

## <a name="increasing-the-quota-or-throttle-limit"></a>Increasing the quota or throttle limit

At any given time, you can increase quotas or throttle limits by [increasing the number of provisioned units in an IoT hub](iot-hub-upgrade.md).

## <a name="latency"></a>延迟

IoT 中心致力于降低所有操作的延迟。 However, due to network conditions and other unpredictable factors it cannot guarantee a certain latency. 在设计解决方案时，应该：

* 避免对任何 IoT 中心操作的最大延迟进行假定。
* 在离设备最近的 Azure 区域预配 IoT 中心。
* 请考虑在设备上或在离设备近的网关上使用 Azure IoT Edge 执行易受延迟影响的操作。

如前所述，多个 IoT 中心单位影响限制，但未提供任何附加延迟权益或保证。

如果发现操作延迟意外增加，请与 [Microsoft 支持部门](https://azure.microsoft.com/support/options/)联系。

## <a name="next-steps"></a>后续步骤

有关 IoT 中心限制行为的深入讨论，请参阅博客文章 [IoT 中心限制与你息息相关](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)。

此 IoT 中心开发人员指南中的其他参考主题包括：

* [IoT 中心终结点](iot-hub-devguide-endpoints.md)
