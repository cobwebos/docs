---
title: 了解 Azure IoT 中心云到设备的消息传送 | Microsoft Docs
description: 本开发人员指南讨论了如何将云到设备的消息传送到 IoT 中心。 它包括有关消息生命周期和配置选项的信息。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 3a7254cc9de89a297811792b4dd64b4b669ba8e4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271234"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>从 IoT 中心发送云到设备的消息

若要从解决方案后端向设备应用发送单向通知，请将云到设备的消息从 IoT 中心发送到设备。 有关 Azure IoT 中心支持的其他云到设备选项的讨论，请参阅[云到设备的通信指南](iot-hub-devguide-c2d-guidance.md)。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

可以通过面向服务的终结点（ */messages/devicebound*）发送云到设备的消息。 然后，设备通过特定于设备的终结点（ */devices/{deviceId}/messages/devicebound*）接收消息。

若要在单个设备上面向每个云到设备的消息，IoT 中心会将 "**到**" 属性设置为 " */devices/{deviceId}/messages/devicebound*"。

每个设备队列最多包含50个云到设备消息。 若要尝试向同一设备发送更多消息，则会导致错误。

## <a name="the-cloud-to-device-message-life-cycle"></a>云到设备的消息生命周期

为了保证至少一次消息传递，IoT 中心将云到设备的消息保留在每个设备队列中。 要使 IoT 中心从队列中删除消息，设备必须显式确认*完成*。 此方法保证了连接失败和设备故障时能够恢复。

生命周期状态图显示在下图中：

![云到设备的消息生命周期](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

当 IoT 中心服务向设备发送消息时，该服务会将消息状态设置为 "*排队*"。 当设备想要接收消息时，IoT 中心*会*通过将状态设置为 "*不可见*" 来*锁定*该消息。 此状态允许设备上的其他线程开始接收其他消息。 当设备线程完成消息的处理时，它会通过*完成*消息通知 IoT 中心。 然后，IoT 中心会将状态设置为 "*已完成*"。

设备还可以：

* *拒绝*消息，这会导致 IoT 中心将其设置为 "*死信*" 状态。 通过消息队列遥测传输（MQTT）协议进行连接的设备无法拒绝云到设备的消息。

* *放弃*消息，这会使 IoT 中心将消息放回队列，并将状态设置为 "*排队*"。 通过 MQTT 协议连接的设备无法放弃云到设备的消息。

线程可能无法处理消息，且不通知 IoT 中心。 在这种情况下，在*可见性*超时（或*锁定*超时）后，消息将自动从*不可见*状态转换回已*排队*状态。 此超时值为一分钟，无法更改。

IoT 中心的 "**最大传递计数**" 属性确定消息可以在*排队*和*不可见*状态之间转换的最大次数。 在该转换次数之后，IoT 中心会将消息的状态设置为 "*死信*"。 同样，IoT 中心会将消息的状态设置为在过期时间之后的*死信*。 有关详细信息，请参阅[生存时间](#message-expiration-time-to-live)。

[如何使用 IoT 中心发送云到设备的消息](iot-hub-csharp-csharp-c2d.md)一文介绍了如何从云中发送云到设备的消息，以及如何在设备上接收这些消息。

当丢失消息不影响应用程序逻辑时，设备通常会完成云到设备的消息。 例如，当设备在本地保留消息内容或已成功执行操作时，可能会出现这种情况。 该消息还可能携带暂时性信息，其丢失不会影响应用程序的功能。 有时，对于长时间运行的任务，可以：

* 在设备已将任务说明保存到本地存储中后，完成云到设备的消息。

* 在作业进度的不同阶段，可以使用一条或多条设备到云的消息通知解决方案后端。

## <a name="message-expiration-time-to-live"></a>消息到期时间（生存时间）

每条云到设备的消息都有过期时间。 此时间由以下任意一项设置：

* 服务中的**ExpiryTimeUtc**属性
* IoT 中心，使用指定为 IoT 中心属性的默认*生存时间*

请参阅[云到设备配置选项](#cloud-to-device-configuration-options)。

使用消息过期和避免将消息发送到断开连接的设备的常见方法是设置较短的*生存时间*值。 此方法可实现与维护设备连接状态相同的结果，但效率更高。 请求消息确认时，IoT 中心会通知你哪些设备是：

* 可以接收消息。
* 不处于联机状态，或出现故障。

## <a name="message-feedback"></a>消息反馈

在发送云到设备的消息时，该服务可以请求传递有关该消息的最终状态的每消息反馈。 为此，可在发送到以下四个值之一的云到设备消息中设置**iothub**应用程序属性：

| Ack 属性值 | 行为 |
| ------------ | -------- |
| none     | IoT 中心不会生成反馈消息（默认行为）。 |
| 积极 | 如果云到设备的消息达到 "*已完成*" 状态，则 IoT 中心将生成反馈消息。 |
| 消极 | 如果云到设备的消息达到了 "*死信*" 状态，则 IoT 中心将生成反馈消息。 |
| 完全     | IoT 中心在任一情况下都会生成反馈消息。 |

如果**Ack**值为*full*，且未收到反馈消息，则意味着反馈消息已过期。 该服务无法了解原始消息的经历。 实际上，服务应该确保它可以在反馈过期之前对其进行处理。 最长过期时间是两天，如果发生故障，该时间会使服务再次运行。

如[终结点](iot-hub-devguide-endpoints.md)中所述，IoT 中心通过面向服务的终结点（ */messages/servicebound/feedback*）以消息的形式提供反馈。 接收反馈的语义与云到设备消息的语义相同。 可能的话，消息反馈将放入单个消息中，其格式如下：

| properties     | 说明 |
| ------------ | ----------- |
| EnqueuedTime | 指示中心收到反馈消息的时间的时间戳 |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

正文是记录的 JSON 序列化数组，每条记录具有以下属性：

| properties           | 说明 |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | 指示消息结果何时发生（例如，集线器收到反馈消息或原始消息过期）的时间戳 |
| OriginalMessageId  | 此反馈信息相关的从云到设备的消息的*MessageId* |
| StatusCode         | 在 IoT 中心生成的反馈消息中使用的所需字符串： <br/> *Success* <br/> *期限* <br/> *DeliveryCountExceeded* <br/> *否决* <br/> *清除* |
| 说明        | *StatusCode*的字符串值 |
| DeviceId           | 此反馈相关的从云到设备的消息的目标设备的*DeviceId* |
| DeviceGenerationId | 此反馈相关的从云到设备的消息的目标设备的*DeviceGenerationId* |

对于云到设备的消息，将其反馈与原始消息相关联，服务必须指定*MessageId*。

反馈消息的正文如以下代码所示：

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

**已删除设备的挂起反馈**

删除设备时，也会删除任何挂起的反馈。 设备反馈按批次发送。 如果设备在确认收到消息后和下一次反馈批次准备时，在 "窄" 窗口（通常不到1秒）内删除设备，则不会进行反馈。

你可以通过等待一段时间等待等待反馈在删除设备之前到达，来解决此问题。 删除设备后，应假定会丢失相关的消息反馈。

## <a name="cloud-to-device-configuration-options"></a>云到设备的配置选项

每个 IoT 中心都针对云到设备的消息传送公开以下配置选项：

| properties                  | 说明 | 范围和默认值 |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | 云到设备消息的默认 TTL | ISO_8601 间隔最多为2天（最小1分钟）;默认值：1小时 |
| maxDeliveryCount          | 云到设备每设备队列的最大传送计数 | 1到 100;默认值：10 |
| feedback.ttlAsIso8601     | 服务绑定反馈消息的保留期 | ISO_8601 间隔最多为2天（最小1分钟）;默认值：1小时 |
| feedback.maxDeliveryCount | 反馈队列的最大传递计数 | 1到 100;默认值：10 |
| lockDurationAsIso8601 | 反馈队列的最大传递计数 | ISO_8601 间隔介于5到300秒（最低5秒）;默认值：60秒。 |

可以通过以下方式之一来设置配置选项：

* **Azure 门户**：在 IoT 中心的 "**设置**" 下，选择 "**内置终结点**"，然后展开 "**云到设备消息**"。 （Azure 门户当前不支持设置**maxDeliveryCount**和**lockDurationAsIso8601**属性。）

    ![在门户中为云到设备的消息设置配置选项](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI**：使用[az iot 中心 update](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-update)命令：

    ```azurecli
    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.defaultTtlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.ttlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.lockDurationAsIso8601=PT0H1M0S
    ```

## <a name="next-steps"></a>后续步骤

有关可用于接收云到设备消息的 Sdk 的信息，请参阅[Azure IoT sdk](iot-hub-devguide-sdks.md)。

若要尝试接收云到设备消息，请参阅[发送云到设备](iot-hub-csharp-csharp-c2d.md)教程。
