---
title: "了解 Azure IoT 中心消息格式 | Microsoft Docs"
description: "开发人员指南 - 描述 IoT 中心消息的格式和预期内容。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2017
ms.author: dobett
ms.openlocfilehash: e6eafb1a0030b022da2b5d0b787e092f3067c99f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="create-and-read-iot-hub-messages"></a>创建和读取 IoT 中心消息

为了支持无缝的跨协议互操作性，IoT 中心为所有面向设备的协议定义了通用消息格式。 此消息格式同时适用于[设备到云][lnk-d2c]和[云到设备][lnk-c2d]消息。 [IoT 中心消息][lnk-messaging]由以下部分组成：

* 一组*系统属性*。 IoT 中心解释或设置的属性。 此集合是预先确定的。
* 一组*应用程序属性*。 应用程序可以定义的字符串属性字典，而不需将消息正文反序列化即可进行访问。 IoT 中心永不修改这些属性。
* 不透明的二进制正文。

在执行以下操作时，属性名称和值只能包含 ASCII 字母数字字符加上 ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``：

* 使用 HTTP 协议发送设备到云的消息。
* 发送云到设备的消息。

有关如何使用不同协议对已发送消息进行编码和解码的详细信息，请参阅 [Azure IoT SDK][lnk-sdks]。

下表列出 IoT 中心消息中的系统属性集。

| 属性 | 说明 |
| --- | --- |
| MessageId |用户可设置的，用于请求-答复模式的消息标识符。 格式：ASCII 7 位字母数字字符 + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}` 的区分大小写字符串（最长为 128 个字符）。 |
| 序列号 |IoT 中心分配给每条云到设备消息的编号（对每个设备队列是唯一的）。 |
| 如果 |[从云到设备][lnk-c2d]的消息中指定的目标。 |
| ExpiryTimeUtc |消息过期的日期和时间。 |
| EnqueuedTime |IoT 中心收到[云到设备][lnk-c2d]消息的日期和时间。 |
| CorrelationId |响应消息中的字符串属性，通常包含采用“请求-答复”模式的请求的 MessageId。 |
| UserId |用于指定消息的源的 ID。 如果消息是由 IoT 中心生成的，则设置为 `{iot hub name}`。 |
| Ack |反馈消息生成器。 此属性在云到设备的消息中用于请求 IoT 中心因为设备使用消息而生成反馈消息。 可能的值：**none**（默认值）：不生成任何反馈消息；**positive**：如果消息已完成，则接收反馈消息；**negative**：如果消息未由设备完成就过期（或已达到最大传送计数），则收到反馈消息；**full**：positive 和 negative。 有关详细信息，请参阅[消息反馈][lnk-feedback]。 |
| ConnectionDeviceId |IoT 中心对设备到云的消息设置的 ID。 它包含发送消息的设备的 **deviceId**。 |
| ConnectionDeviceGenerationId |IoT 中心对设备到云的消息设置的 ID。 它包含发送消息的设备的 **generationId**（根据[设备标识属性][lnk-device-properties]）。 |
| ConnectionAuthMethod |由 IoT 中心对设备到云的消息设置的身份验证方法。 此属性包含用于验证发送消息的设备的身份验证方法的相关信息。 有关详细信息，请参阅[从设备到云的反欺骗技术][lnk-antispoofing]。 |

## <a name="message-size"></a>消息大小

IoT 中心用于衡量消息大小的方法与协议无关，仅考虑实际有效负载。 以字节为单位的大小计算以下各项之和：

* 以字节为单位的正文大小。
* 以字节为单位的消息系统属性的所有值的大小。
* 以字节为单位的所有用户属性名称和值的大小。

属性名称和值限制为 ASCII 字符，因此，字符串的长度等于以字节为单位的大小。

## <a name="next-steps"></a>后续步骤

有关 IoT 中心消息大小限制的详细信息，请参阅 [IoT 中心配额和限制][lnk-quotas]。

若要了解如何用不同编程语言创建并读取 IoT 中心消息，请参阅[入门][lnk-get-started]教程。

[lnk-messaging]: iot-hub-devguide-messaging.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-get-started]: iot-hub-get-started.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-feedback]: iot-hub-devguide-messages-c2d.md#message-feedback
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-antispoofing]: iot-hub-devguide-messages-d2c.md#anti-spoofing-properties
