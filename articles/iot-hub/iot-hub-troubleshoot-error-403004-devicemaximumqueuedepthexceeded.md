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
ms.openlocfilehash: 1edf723aa885ff18d2ce2dda4d71b67700a98a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497496"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

本文介绍 **403004 DeviceMaximumQueueDepthExceeded** 错误的原因和解决方案。

## <a name="symptoms"></a>症状

尝试发送云到设备的消息时，请求失败，并出现错误 **403004** 或 **DeviceMaximumQueueDepthExceeded**。

## <a name="cause"></a>原因

根本原因是，设备的已排队消息数超出[队列限制 (50)](./iot-hub-devguide-quotas-throttling.md#other-limits)。

遇到此限制最可能的原因是你在使用 HTTPS 接收消息，这会导致使用 `ReceiveAsync` 进行持续轮询，从而导致 IoT 中心限制请求。

## <a name="solution"></a>解决方案

使用 HTTPS 的云到设备消息，其支持模式是间歇连接到设备，且不常检查消息（时间间隔小于 25 分钟）。 若要降低遇到队列限制的可能性，请切换到 AMQP 或 MQTT（针对云到设备的消息）。

也可增强设备端的逻辑，以便快速完成、拒绝或放弃排队的消息，缩短生存时间，或者考虑发送较少的消息。 请参阅 [C2D 消息的生存时间](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live)。

最后，请考虑使用[清除队列 API](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue)，在达到限制之前定期清除挂起的消息。