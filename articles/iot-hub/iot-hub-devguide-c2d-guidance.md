---
title: "Azure IoT 中心从云到设备选项 | Microsoft Docs"
description: "开发人员指南 - 指导用户何时使用直接方法、设备孪生的所需属性或云到设备的消息，以进行从云到设备的通信。"
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: dea438c8b1bd3d6331f84de01def989f66b6f387
ms.openlocfilehash: 88ae905708a421052666316eb81c1dd97d6c9635
ms.lasthandoff: 02/09/2017


---
# <a name="cloud-to-device-communications-guidance"></a>从云到设备通信指南
IoT 中心提供三个选项，允许设备应用向后端应用公开功能：

* [直接方法][lnk-methods]，适用于需要立即确认其结果的通信，通常是对设备的交互式控制，例如，打开风扇；
* [克隆的所需属性][lnk-twins]，适用于旨在将设备置于某个所需状态的长时间运行命令。 例如，将遥测发送间隔设置为 30 分钟；
* [云到设备 (C2D) 消息][lnk-c2d]，向设备应用提供单向通知。

下面详细比较了各种从云到设备的通信选项。

|  | 直接方法 | 克隆的所需属性 | C2D 消息 |
| ---- | ------- | ---------- | ---- |
| 方案 | 需要立即确认的命令，例如打开风扇。 | 旨在将设备置于某个所需状态的长时间运行命令。 例如，将遥测发送间隔设置为 30 分钟。 | 提供给设备应用的单向通知。 |
| 数据流 | 双向。 设备应用可立即响应方法。 解决方案后端根据上下文接收请求结果。 | 单向。 设备应用接收更改了属性的通知。 | 单向。 设备应用接收消息
| 持续性 | 不联系已断开连接的设备。 通知后端：设备未连接。 | 设备克隆会保留属性值。 设备会在下次重新连接时读取属性值。 属性值可通过 [IoT 中心查询语言][lnk-query]检索。 | IoT 中心可保留消息长达 48 小时。 |
| 目标 | 使用 **deviceId** 与单个设备通信，或使用[作业][lnk-jobs]与多个设备通信。 | 使用 **deviceId** 与单个设备通信，或使用[作业][lnk-jobs]与多个设备通信。 | 使用 **deviceId** 与单个设备通信。 |
| 大小 | 最多 8KB 请求和 8KB 响应。 | 所需属性大小最大为 8KB。 | 最多 64KB 的消息。 |
| 频率 | 高。 有关详细信息，请参阅 [IoT 中心限制][lnk-quotas]。 | 中。 有关详细信息，请参阅 [IoT 中心限制][lnk-quotas]。 | 低。 有关详细信息，请参阅 [IoT 中心限制][lnk-quotas]。 |
| 协议 | 目前仅在使用 MQTT 时提供。 | 目前仅在使用 MQTT 时提供。 | 适用于所有协议。 使用 HTTP 时，设备必须轮询。 |

通过以下教程，了解如何使用直接方法、所需属性和云到设备消息：

* [使用直接方法][lnk-methods-tutorial]：针对直接方法；
* [使用所需属性配置设备][lnk-twin-properties]：针对设备孪生的所需属性； 
* [发送云到设备的消息][lnk-c2d-tutorial]：针对云到设备的消息。

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-tutorial]: iot-hub-node-node-c2d.md

