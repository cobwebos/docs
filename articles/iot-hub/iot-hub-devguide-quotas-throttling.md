---
title: "开发人员指南 - 配额和限制 | Microsoft Docs"
description: "Azure IoT 中心开发人员指南 - 适用于 IoT 中心的配额和预期的限制行为的说明"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 425e1b08-8789-4377-85f7-c13131fae4ce
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: c0f8c779d7f9552dc05ac3791b74c3d57cb1fe64


---
# <a name="reference---quotas-and-throttling"></a>参考 - 配额和限制
## <a name="quotas-and-throttling"></a>配额和限制
每个 Azure 订阅最多可以拥有 10 个 IoT 中心和 1 个免费中心。

每个 IoT 中心预配了特定 SKU 的特定单位数（有关详细信息，请参阅 [Azure IoT 中心定价][lnk-pricing]）。 SKU 和单位数目确定可以发送的消息的每日配额上限。

SKU 还确定了 IoT 中心对所有操作强制实施的限制。

## <a name="operation-throttles"></a>操作限制
操作限制是在分钟范围内应用的速率限制，主要是为了避免不当使用。 IoT 中心会尽可能避免返回错误，但如果违反限制太久，就会开始返回异常。

以下是强制实施的限制列表。 值与单个中心相关。

| 限制 | 免费和 S1 中心 | S2 中心 | S3 中心 | 
| -------- | ------- | ------- | ------- |
| 标识注册表操作（创建、检索、列出、更新、删除） | 100/分钟/单位 | 100/分钟/单位 | 5000/分钟/单位 |
| 设备连接 | 最大值为 100/秒或 12/秒/单位 <br/> 例如，两个 S1 单位是 2\*12 = 24/秒，但是在所有单位中至少有 100/秒。 如果有 9 个 S1 单位，则所有单位就有 108/秒 (9\*12)。 | 120/秒/单位 | 6000/秒/单位 |
| 设备到云的发送 | 最大值为 100/秒或 12/秒/单位 <br/> 例如，两个 S1 单位是 2\*12 = 24/秒，但是在所有单位中至少有 100/秒。 如果有 9 个 S1 单位，则所有单位就有 108/秒 (9\*12)。 | 120/秒/单位 | 6000/秒/单位 |
| 云到设备的发送 | 100/分钟/单位 | 100/分钟/单位 | 5000/分钟/单位 |
| 云到设备的接收 <br/> （仅当设备使用 HTTP 时）| 1000/分钟/单位 | 1000/分钟/单位| 50000/分钟/单位 |
| 文件上载 | 100 个文件上载通知/分钟/单位 | 100 个文件上载通知/分钟/单位 | 5000 个文件上载通知/分钟/单位 |
| 直接方法 | 10/秒/单位 | 30/秒/单位 | 1500/秒/单位 | 
| 克隆读取 | 10/秒 | 最大值为 10/秒或 1/秒/单位 | 50/秒/单位 |
| 克隆更新 | 10/秒 | 最大值为 10/秒或 1/秒/单位 | 50/秒/单位 |
| 作业操作 <br/> （创建、更新、列表、删除） | 100/分钟/单位 | 100/分钟/单位 | 5000/分钟/单位 |
| 作业每设备操作吞吐量 | 10/秒 | 最大值为 10/秒或 1/秒/单位 | 50/秒/单位 |

必须清楚地知道，*设备连接*限制控制可与 IoT 中心建立新设备连接的速率，而不是控制同时连接的设备数上限。 该限制取决于为 IoT 中心预配的单位数。

例如，如果你购买的是单一 S1 单位，则限制为每秒 100 个连接。 这意味着，若要连接 100,000 台设备，至少需要花费 1000 秒（大约 16 分钟）。 但是，同时连接的设备数可与用户在标识注册表中注册的设备数相同。

有关 IoT 中心限制行为的深入讨论，请参阅博客文章 [IoT Hub throttling and you][lnk-throttle-blog]（IoT 中心限制与你息息相关）。

> [!NOTE]
> 无论何时，都可以通过增加 IoT 中心预配的单位来提高配额或限制。
> 
> [!IMPORTANT]
> 标识注册表操作用于设备管理与预配方案中的运行时使用。 通过[导入和导出作业][lnk-importexport]可以支持读取或更新大量的设备标识。
> 
> 

## <a name="other-limits"></a>其他限制

IoT 中心对其不同的功能实施其他限制。

| 操作 | 限制 |
| --------- | ----- |
| 文件上载 URI | 存储帐户一次可传出 10000 个 SAS URI。 <br/> 一次可传出 10 个 SAS URI/设备。 |
| 作业 | 作业历史记录最多保留 30 天 <br/> 最大并发作业数为 1（适用于免费版和 S1）、5（适用于 S2）、10（适用于 S3）。 |

## <a name="next-steps"></a>后续步骤
此 IoT 中心开发人员指南中的其他参考主题包括：

* [IoT 中心终结点][lnk-devguide-endpoints]
* [设备克隆和作业的 IoT 中心查询语言][lnk-devguide-query]
* [IoT 中心 MQTT 支持][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md



<!--HONumber=Nov16_HO5-->


