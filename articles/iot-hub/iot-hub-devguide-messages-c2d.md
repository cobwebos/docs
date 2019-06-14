---
title: 了解 Azure IoT 中心云到设备的消息传送 | Microsoft Docs
description: 本开发人员指南介绍如何使用设备到云消息传送到 IoT 中心。 它包括有关消息生命周期和配置选项的信息。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 0fc1b65a4ba1c8a3d76b48206d6a4703035e05bc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055323"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>从 IoT 中心发送云到设备消息

若要从解决方案后端向设备应用发送单向通知，请向你的设备从 IoT 中心发送云到设备的消息。 Azure IoT 中心支持的其他云到设备选项的讨论，请参阅[云到设备通信指南](iot-hub-devguide-c2d-guidance.md)。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

发送云到设备消息通过面向服务的终结点 */devices/{deviceid}/messages/devicebound*。 设备接收通过特定于设备的终结点，这些消息 */devices/{deviceid}/files /devices/{deviceid}/messages/devicebound*。

若要针对单个设备每条云到设备消息，IoT 中心设置**到**属性设置为 */devices/{deviceid}/files /devices/{deviceid}/messages/devicebound*。

最多，每个设备队列保留 50 条云到设备消息。 若要尝试将更多的消息发送到相同的设备将导致错误。

## <a name="the-cloud-to-device-message-life-cycle"></a>云到设备消息生命周期

为了保证至少一次消息传递，IoT 中心将保留每个设备队列中的云到设备消息。 若要从队列中删除消息的 IoT 中心，设备必须显式确认*完成*。 此方法保证了连接失败和设备故障时能够恢复。

下图中显示生命周期状态关系图：

![云到设备消息生命周期](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

当 IoT 中心服务向设备发送一条消息时，该服务会将消息状态设置为*排入队列的*。 当设备想*接收*一条消息，IoT 中心*锁*将状态设置为消息*不可见*。 此状态允许其他线程开始接收其他消息在设备上。 当设备线程完成对消息的处理时，它将通知 IoT 中心通过*完成*消息。 IoT 中心然后将状态设置为*已完成*。

设备还可以：

* *拒绝*消息，这会使 IoT 中心，以将其设置为*设为死信*状态。 通过消息队列遥测传输 (MQTT) 协议进行连接的设备不能拒绝云到设备消息。

* *放弃*消息，这会使 IoT 中心将消息放回队列中，将状态设置为与*排入队列的*。 通过 MQTT 协议连接的设备无法放弃云到设备的消息。

一个线程可能会失败，而不通知 IoT 中心处理一条消息。 在这种情况下，消息会自动从过渡*不可见*回*排入队列*状态后*可见性*超时值 (或*锁*超时)。 此超时的默认值为一分钟。

**最大传送计数**IoT 中心上的属性确定之间进行转换一条消息，可以最大次数*排入队列*并*不可见*状态。 在该转换次数之后, 的 IoT 中心设置到消息的状态*设为死信*。 同样，IoT 中心设置到的消息的状态*设为死信*后其过期时间。 有关详细信息，请参阅[生存时间](#message-expiration-time-to-live)。

[如何将使用 IoT 中心设备到云消息发送](iot-hub-csharp-csharp-c2d.md)本文介绍如何将云到设备消息从云发送和接收它们的设备上。

设备通常完成云到设备的消息时丢失消息不会影响应用程序逻辑。 出现这种可能的设备已保存本地消息内容或已成功执行操作时。 该消息还可能携带暂时性信息，该信息的丢失不会影响应用程序的功能。 有时，对于长时间运行的任务，可以：

* 设备已保留在本地存储中的任务说明后，请完成云到设备的消息。

* 在作业进度的不同阶段，可以使用一条或多条设备到云的消息通知解决方案后端。

## <a name="message-expiration-time-to-live"></a>消息到期时间（生存时间）

每条云到设备的消息都有过期时间。 通过以下任一情况下，这次是将设置：

* **ExpiryTimeUtc**服务中的属性
* IoT 中心，使用默认*生存时间*，指定为 IoT 中心属性

请参阅[云到设备配置选项](#cloud-to-device-configuration-options)。

若要利用消息到期时间并避免将消息发送到断开连接的设备的常用方法是设置短*生存时间*值。 这种方法实现相同的结果与维护设备连接状态，但更高效。 在请求消息确认时，IoT 中心会通知你哪些设备：

* 可以接收消息。
* 不处于联机状态，或出现故障。

## <a name="message-feedback"></a>消息反馈

当发送云到设备的消息时，服务可以请求有关该消息的最终状态的每个消息反馈的传送。 执行此操作通过设置**iothub ack**发送到以下四个值之一的设备到云消息中的应用程序属性：

| Ack 属性值 | 行为 |
| ------------ | -------- |
| 无     | IoT 中心不会生成反馈消息 （默认行为）。 |
| 积极 | 如果设备到云的消息达到*已完成*状态，IoT 中心生成反馈消息。 |
| 负数 | 如果设备到云的消息达到*设为死信*状态，IoT 中心生成反馈消息。 |
| 完整     | 在任一情况下，IoT 中心生成反馈消息。 |

如果**Ack**值是*完整*，和未收到反馈消息，则意味着反馈消息已过期。 该服务无法了解原始消息的经历。 实际上，服务应该确保它可以在反馈过期之前对其进行处理。 最大过期时间两天，因此，留出时间让服务再次运行在失败时。

中所述[终结点](iot-hub-devguide-endpoints.md)，IoT 中心提供了通过面向服务的终结点，反馈 */messages/servicebound/feedback*，作为消息。 接收反馈的语义与云到设备消息的语义相同。 可能的话，消息反馈将放入单个消息中，其格式如下：

| 属性     | 描述 |
| ------------ | ----------- |
| EnqueuedTime | 时间戳，指示中心收到反馈消息 |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

正文是记录的 JSON 序列化数组，每条记录具有以下属性：

| 属性           | 描述 |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | 时间戳，指示消息的结果发生的情况 （例如，中心收到反馈消息或原始消息已过期） |
| OriginalMessageId  | *MessageId*此反馈信息相关的设备到云消息 |
| StatusCode         | 必需的字符串，由 IoT 中心生成反馈消息中使用： <br/> *Success* <br/> *Expired* <br/> *DeliveryCountExceeded* <br/> *已拒绝* <br/> *清除* |
| 描述        | 字符串值*StatusCode* |
| DeviceId           | *DeviceId*反馈相关的设备到云消息的目标设备 |
| DeviceGenerationId | *DeviceGenerationId*反馈相关的设备到云消息的目标设备 |

对于要将其反馈与原始消息相关联的云到设备消息，该服务必须指定*MessageId*。

反馈消息的正文中的以下代码所示：

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

## <a name="cloud-to-device-configuration-options"></a>云到设备的配置选项

每个 IoT 中心都针对云到设备的消息传送公开以下配置选项：

| 属性                  | 描述 | 范围和默认值 |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | 默认 TTL 的云到设备消息 | ISO_8601 间隔 2 天 （最小 1 分钟）;默认值：1 小时 |
| maxDeliveryCount          | 云到设备每个设备队列的最大传送计数 | 1 到 100;默认值：10 |
| feedback.ttlAsIso8601     | 对于服务绑定反馈消息保留期 | ISO_8601 间隔 2 天 （最小 1 分钟）;默认值：1 小时 |
| feedback.maxDeliveryCount | 反馈队列的最大传送数 | 1 到 100;默认值：100 |

有关如何设置这些配置选项的详细信息，请参阅[创建 IoT 中心](iot-hub-create-through-portal.md)。

## <a name="next-steps"></a>后续步骤

有关可用于接收云到设备消息的 Sdk 的信息，请参阅[Azure IoT Sdk](iot-hub-devguide-sdks.md)。

若要尝试接收云到设备消息，请参阅[发送云到设备](iot-hub-csharp-csharp-c2d.md)教程。
