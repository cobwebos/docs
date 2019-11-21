---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 34de38e91d47457d215c7ebf65d04ed2dbae5324
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224405"
---
The following table lists the limits associated with the different service tiers S1, S2, S3, and F1. For information about the cost of each *unit* in each tier, see [Azure IoT Hub pricing](https://azure.microsoft.com/pricing/details/iot-hub/).

| 资源 | S1 标准 | S2 标准 | S3 标准 | F1 免费 |
| --- | --- | --- | --- | --- |
| 消息/天 |400,000 |6,000,000 |300,000,000 |8,000 |
| 最大单位数 |200 |200 |10 |第 |

> [!NOTE]
> If you anticipate using more than 200 units with an S1 or S2 tier hub or 10 units with an S3 tier hub, contact Microsoft Support.
> 
> 

The following table lists the limits that apply to IoT Hub resources.

| 资源 | Limit |
| --- | --- |
| 每个 Azure 订阅的付费 IoT 中心数上限 |100 |
| 每个 Azure 订阅的免费 IoT 中心数上限 |第 |
| Maximum number of characters in a device ID | 128 |
| 设备标识的最大数目<br/> 在单个调用中返回 |1,000 |
| IoT 中心消息的设备到云消息的最长保留期 |7 天 |
| 设备到云消息的最大大小 |256 KB |
| 设备到云消息批的最大大小 |AMQP 和 HTTP：整个批为 256 KB <br/>MQTT：每条消息为 256 KB |
| 设备到云消息批中的消息数上限 |500 |
| 云到设备的消息数上限 |64 KB |
| 云到设备消息的最大 TTL |2 天 |
| 云到设备消息的最大传送计数 <br/> messages |100 |
| Maximum cloud-to-device queue depth per device |50 |
| 反馈消息的最大传送计数 <br/> （响应云到设备消息时） |100 |
| 反馈消息的最大 TTL <br/> （响应云到设备消息时） |2 天 |
| [Maximum size of device twin](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB for tags section, and 32 KB for desired and reported properties sections each |
| Maximum length of device twin string key | 1 KB |
| Maximum length of device twin string value | 4 KB |
| [Maximum depth of object in device twin](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| 直接方法有效负载的最大大小 | 128 KB |
| 作业历史记录最长保留期 | 30 天 |
| 最大并发作业数 | 10（适用于 S3）、5（适用于 S2）、1（适用于 S1） |
| 额外终结点最大数目 | 10 (for S1, S2, and S3) |
| 最大消息路由规则数 | 100 (for S1, S2, and S3) |
| 最大并发连接设备流数 | 50（仅适用于 S1、S2、S3 和 F1） |
| 最大设备流数据传输 | 300 MB/天（仅适用于 S1、S2、S3 和 F1） |

> [!NOTE]
> If you need more than 100 paid IoT hubs in an Azure subscription, contact Microsoft Support.

> [!NOTE]
> Currently, the total number of devices plus modules that can be registered to a single IoT hub is capped at 1,000,000. 如果想要增加此限制，请联系 [Microsoft 支持](https://azure.microsoft.com/support/options/)。

IoT Hub throttles requests when the following quotas are exceeded.

| 限制 | 每个中心的值 |
| --- | --- |
| 标识注册表操作 <br/> (create, retrieve, list, update, and delete), <br/> 单个或批量导入/导出 |83.33/sec/unit (5,000/min/unit) (for S3). <br/> 1.67/秒/单位（100/分钟/单位）（适用于 S1 和 S2）。 |
| 设备连接 |6,000/sec/unit (for S3), 120/sec/unit (for S2), 12/sec/unit (for S1). <br/>最小值为 100/秒。 |
| 设备到云的发送 |6,000/sec/unit (for S3), 120/sec/unit (for S2), 12/sec/unit (for S1). <br/>最小值为 100/秒。 |
| 云到设备的发送 | 83.33/sec/unit (5,000/min/unit) (for S3), 1.67/sec/unit (100/min/unit) (for S1 and S2). |
| 云到设备的接收 |833.33/sec/unit (50,000/min/unit) (for S3), 16.67/sec/unit (1,000/min/unit) (for S1 and S2). |
| 文件上传操作 |83.33 file upload initiations/sec/unit (5,000/min/unit) (for S3), 1.67 file upload initiations/sec/unit (100/min/unit) (for S1 and S2). <br/> 10,000 SAS URIs can be out for an Azure Storage account at one time.<br/> 一次可传出 10 个 SAS URI/设备。 |
| 直接方法 | 24 MB/sec/unit (for S3), 480 KB/sec/unit (for S2), 160 KB/sec/unit (for S1).<br/> Based on 8-KB throttling meter size. |
| 设备孪生读取 | 500/sec/unit (for S3), Maximum of 100/sec or 10/sec/unit (for S2), 100/sec (for S1) |
| 设备孪生更新 | 250/sec/unit (for S3), Maximum of 50/sec or 5/sec/unit (for S2), 50/sec (for S1) |
| 作业操作 <br/> (create, update, list, and delete) | 83.33/sec/unit (5,000/min/unit) (for S3), 1.67/sec/unit (100/min/unit) (for S2), 1.67/sec/unit (100/min/unit) (for S1). |
| 作业每设备操作吞吐量 | 50/sec/unit (for S3), maximum of 10/sec or 1/sec/unit (for S2), 10/sec (for S1). |
| 设备流启动率 | 5 new streams/sec (for S1, S2, S3, and F1 only). |
