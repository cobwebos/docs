---
title: 排查 Azure IoT 中心错误 403004 DeviceMaximumQueueDepthExceeded
description: 了解如何修复错误 403004 DeviceMaximumQueueDepthExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d48fd9aa9ba52c850a514d392f25b980d0219470
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960901"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

本文介绍**403004 DeviceMaximumQueueDepthExceeded**错误的原因和解决方案。

## <a name="symptoms"></a>症状

尝试发送云到设备的消息时，请求会失败，并出现错误**403004**或**DeviceMaximumQueueDepthExceeded**。

## <a name="cause"></a>原因

根本原因是为设备排队的消息数超出了[队列限制（50）](./iot-hub-devguide-quotas-throttling.md#other-limits)。

最可能的原因是你正在使用 HTTPS 接收消息，这会导致使用 `ReceiveAsync`进行持续轮询，导致 IoT 中心限制请求。

## <a name="solution"></a>解决方案

使用 HTTPS 的云到设备消息，其支持模式是间歇连接到设备，且不常检查消息（时间间隔小于 25 分钟）。 若要降低在队列限制中运行的可能性，请切换到 AMQP 或 MQTT，以获取云到设备的消息。

或者，增强设备端逻辑来快速完成、拒绝或放弃排队消息，缩短生存时间或考虑发送更少的消息。 请参阅 [C2D 消息的生存时间](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live)。

最后，在达到限制之前，请考虑使用[清除队列 API](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue)定期清除挂起的消息。