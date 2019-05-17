---
title: 了解 Azure IoT 中心配额和限制 | Microsoft Docs
description: 开发人员指南 - 介绍适用于 IoT 中心的配额和预期限制行为。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/11/2019
ms.openlocfilehash: ea9bea8b314d00db87ad7addacc49a976e0da08e
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550465"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>参考 - IoT 中心配额和限制

## <a name="quotas-and-throttling"></a>配额和限制

每个 Azure 订阅最多可以有 50 个 IoT 中心和 1 个免费中心。

每个 IoT 中心都在特定层中预配了特定单位数。 层和单位数决定了可以发送的消息的每日配额上限。 对于免费层中心，用于计算每日配额的消息大小为 0.5 KB，对于所有其他层为 4 KB。 有关详细信息，请参阅 [Azure IoT 中心定价](https://azure.microsoft.com/pricing/details/iot-hub/)。

层还决定了 IoT 中心对所有操作强制实施的限制。

## <a name="operation-throttles"></a>操作限制

操作限制时，速率限制，应用在分钟范围内，并且应以防止滥用。 它们还受[流量整形](#traffic-shaping)。

下表显示了强制限制。 值与单个中心相关。

| 限制 | 免费、B1 和 S1 | B2 和 S2 | B3 和 S3 | 
| -------- | ------- | ------- | ------- |
| [标识注册表操作](#identity-registry-operations-throttle)（创建、 检索、 列出、 更新、 删除） | 1.67/秒/单位（100/分钟/单位） | 1.67/秒/单位（100/分钟/单位） | 83.33/sec/unit （5000/分钟/单位） |
| [新设备连接](#device-connections-throttle)(此限制适用于的速率_新的连接_，未连接的总数) | 高于 100/秒或 12/秒/单位 <br/> 例如，两个 S1 单位是 2\*12 = 24 个新连接/秒，但你的单位至少有 100 个新连接/秒。 如果有 9 个 S1 单位，则你的单位就有 108 个新连接/秒 (9\*12)。 | 120 个新连接/秒/单位 | 6,000 新连接/秒/单位 |
| 设备到云的发送 | 高于 100/秒或 12/秒/单位 <br/> 例如，两个 S1 单位是 2\*12 = 24/秒，但是在所有单位中至少有 100/秒。 如果有 9 个 S1 单位，则所有单位就有 108/秒 (9\*12)。 | 120/秒/单位 | 6000/秒/单位 |
| 云到设备的发送<sup>1</sup> | 1.67/秒/单位（100/分钟/单位） | 1.67/秒/单位（100/分钟/单位） | 83.33/sec/unit （5000/分钟/单位） |
| 云到设备的接收<sup>1</sup> <br/> （仅当设备使用 HTTPS 时）| 16.67/sec/unit （1000/分钟/单位） | 16.67/sec/unit （1000/分钟/单位） | 833.33/sec/unit （50000/分钟/单位） |
| 文件上传 | 1.67 文件上传通知/秒/单位（100/分钟/单位） | 1.67 文件上传通知/秒/单位（100/分钟/单位） | 83.33 文件上传通知/秒/单位 （5000/分钟/单位） |
| 直接方法<sup>1</sup> | 160KB/秒/单位<sup>2</sup> | 480KB/秒/单位<sup>2</sup> | 24MB/秒/单位<sup>2</sup> | 
| 查询 | 20/分钟/单位 | 20/分钟/单位 | 1000/分钟/单位 |
| 孪生（设备和模块）读取<sup>1</sup> | 100/秒 | 高于 100/秒或 10/秒/单位 | 500/秒/单位 |
| 孪生更新（设备和模块）<sup>1</sup> | 50/秒 | 高于 50/秒或 5/秒/单位 | 250/秒/单位 |
| 作业操作<sup>1</sup> <br/> （创建、更新、列表、删除） | 1.67/秒/单位（100/分钟/单位） | 1.67/秒/单位（100/分钟/单位） | 83.33/sec/unit （5000/分钟/单位） |
| 作业设备操作<sup>1</sup> <br/> （更新孪生、调用直接方法） | 10/秒 | 高于 10/秒或 1/秒/单位 | 50/秒/单位 |
| 配置和 Edge 部署<sup>1</sup> <br/> （创建、更新、列表、删除） | 0.33/秒/单位（20/分钟/单位） | 0.33/秒/单位（20/分钟/单位） | 0.33/秒/单位（20/分钟/单位） |
| 设备流启动速率<sup>1</sup> | 5 个新流/秒 | 5 个新流/秒 | 5 个新流/秒 |
| 最大数目的并发连接的设备流<sup>1</sup> | 50 | 50 | 50 |
| 最大设备流数据传输<sup>1</sup> （聚合每日的卷） | 300 MB | 300 MB | 300 MB |

<sup>1</sup>此功能在 IoT 中心的基本层内不可用。 有关详细信息，请参阅[如何选择正确的 IoT 中心](iot-hub-scaling.md)。 <br/><sup>2</sup>限制计量大小为 4 KB。

### <a name="traffic-shaping"></a>流量整形

为了满足突发流量，IoT 中心，请接受一段时间的上述限制的请求。 这些请求的第几个会立即处理。 但是，如果请求的数量仍违反了限制，请将请求放在队列中并限制速率处理 IoT 中心启动。 此效果称为*流量整形*。 此外，此队列的大小受到限制。 如果仍限制冲突，最终队列已满，并且拒绝请求与 IoT 中心启动`429 ThrottlingException`。 

例如，使用模拟的设备将每秒 200 条设备到云消息发送到 S1 IoT 中心 （它具有的限制为 100/秒 D2C 发送）。 对于第一分钟或两个，立即处理消息。 但是，由于设备仍会继续发送超过的中止值限制的更多消息，IoT 中心开始仅过程每秒 100 个消息，并将剩余部分放在队列中。 首先注意到延迟时间增加。 最终，你会开始收到`429 ThrottlingException`队列用完，以及"的限制错误数"中作为[IoT 中心度量值](iot-hub-metrics.md)开始增加。

### <a name="identity-registry-operations-throttle"></a>标识注册表操作限制

设备标识注册表操作适用于在设备管理和预配方案中运行时使用。 通过[导入和导出作业](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)可以支持读取或更新大量的设备标识。

### <a name="device-connections-throttle"></a>设备连接限制

“设备连接”限制控制与 IoT 中心建立新设备连接的速率。 “设备连接”限制不控制同时连接的最大设备数。 “设备连接”速率限制取决于为 IoT 中心预配的单位数。

例如，如果购买的是单一 S1 单位，则限制为每秒 100 个连接。 因此，若要连接 100、 000 设备花费至少 1000 秒 （大约 16 分钟）。 但是，同时连接的设备数可与用户在标识注册表中注册的设备数相同。


## <a name="other-limits"></a>其他限制

IoT 中心强制实施的其他操作限制：

| Operation | 限制 |
| --------- | ----- |
| 设备 | 你可以连接到单个 IoT 中心的设备的最大数目为 1000000。 增加此限制的唯一方法是联系[Microsoft 支持部门](https://azure.microsoft.com/support/options/)。| 
| 文件上传 URI | 10,000 SAS Uri 可以是出为存储帐户一次。 <br/> 一次可传出 10 个 SAS URI/设备。 |
| 作业<sup>1</sup> | 最大并发作业数是 1 （适用于免费版和 S1）、 5 （适用于 S2) 和 10 （适用于 S3)。 但是，最大并发[设备导入/导出作业](iot-hub-bulk-identity-mgmt.md)为 1 的所有层。 <br/>作业历史记录最多保留 30 天。 |
| 额外终结点 | 付费 SKU 中心可能有 10 个额外终结点。 免费 SKU 中心可能有 1 个额外终结点。 |
| 消息路由规则 | 付费 SKU 中心可包含 100 条路由规则。 免费 SKU 中心可包含 5 条路由规则。 |
| 设备到云的消息传递 | 最大消息大小为 256 KB |
| 云到设备的消息传递<sup>1</sup> | 最大消息大小为 64 KB。 进行传递的最大挂起消息数为 50。 |
| 直接方法<sup>1</sup> | 直接方法有效负载的最大大小为 128 KB。 |
| 自动设备配置<sup>1</sup> | 每个付费 SKU 中心 100 个配置。 每个免费 SKU 中心 20 个配置。 |
| 自动 Edge 部署<sup>1</sup> | 每个部署 20 个模块。 每个付费 SKU 中心 100 个部署。 每个免费 SKU 中心 20 个部署。 |
| 孪生<sup>1</sup> | 每个孪生部分（标记、所需属性、报告的属性）的最大大小为 8 KB |

<sup>1</sup>此功能在 IoT 中心的基本层内不可用。 有关详细信息，请参阅[如何选择正确的 IoT 中心](iot-hub-scaling.md)。

## <a name="increasing-the-quota-or-throttle-limit"></a>增加的配额或限制的限制

在任何给定时间，提高配额或限制的限制[增加 IoT 中心预配的单位数](iot-hub-upgrade.md)。

## <a name="latency"></a>Latency

IoT 中心致力于降低所有操作的延迟。 但是，由于网络条件和其他不可预知的因素，它无法保证某些延迟。 在设计解决方案时，应该：

* 避免对任何 IoT 中心操作的最大延迟进行假定。
* 在离设备最近的 Azure 区域预配 IoT 中心。
* 请考虑在设备上或在离设备近的网关上使用 Azure IoT Edge 执行易受延迟影响的操作。

如前所述，多个 IoT 中心单位影响限制，但未提供任何附加延迟权益或保证。

如果发现操作延迟意外增加，请与 [Microsoft 支持部门](https://azure.microsoft.com/support/options/)联系。

## <a name="next-steps"></a>后续步骤

有关 IoT 中心限制行为的深入讨论，请参阅博客文章 [IoT 中心限制与你息息相关](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)。

此 IoT 中心开发人员指南中的其他参考主题包括：

* [IoT 中心终结点](iot-hub-devguide-endpoints.md)
