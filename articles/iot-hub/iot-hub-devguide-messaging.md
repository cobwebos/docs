---
title: 了解 Azure IoT 中心消息传送 | Microsoft Docs
description: 开发人员指南 - 使用 IoT 中心进行设备到云和云到设备的消息传送。 包括消息格式和支持的通信协议的相关信息。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: f56332fa7f53c729ffaa28ea375f043d1b4a3678
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60626241"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>使用 IoT 中心发送设备到云和云到设备的消息

IoT 中心支持与设备进行双向通信。 使用 IoT 中心消息传递，通过将消息从设备发送到解决方案后端，并将命令从 IoT 解决方案后端发送到设备，可实现与设备的通信。 了解有关 [IoT 中心消息格式](iot-hub-devguide-messages-construct.md)的详细信息。

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>将设备到云的消息发送到 IoT 中心

IoT 中心具有内置服务终结点，后端服务可以使用该终结点从设备中读取遥测消息。 此终结点与[事件中心](https://docs.microsoft.com/azure/event-hubs/)兼容，你可以使用标准 IoT 中心 SDK [从该内置终结点读取](iot-hub-devguide-messages-read-builtin.md)。

IoT 中心还支持用户可定义的[自定义终结点](iot-hub-devguide-endpoints.md#custom-endpoints)使用[消息路由](iot-hub-devguide-messages-d2c.md)将设备遥测数据和事件发送到 Azure 服务。

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>从 IoT 中心发送云到设备的消息

可以将[云到设备](iot-hub-devguide-messages-c2d.md)的消息从解决方案后端发送到设备。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT 中心消息传送功能的核心属性是消息的可靠性和持久性。 这些属性可在设备端上恢复间歇性连接，以及在云恢复事件处理的负载高峰。 IoT 中心对从设备到云和从云到设备的消息传送实施*至少一次*传送保证。

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>选择适当类型的 IoT 中心消息传递

使用设备到云的消息从设备应用发送时序遥测数据和警报，使用云到设备的消息向设备应用发送单向通知。

* 有关在使用设备到云的消息、报告属性或文件上传方面进行选择的信息，请参阅[设备到云通信指南](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-d2c-guidance)。

* 有关在使用云到设备的消息、所需属性或直接方法方面进行选择的消息，请参阅[云到设备通信指南](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-c2d-guidance)。

## <a name="next-steps"></a>后续步骤

* 了解 IoT 中心[消息路由](iot-hub-devguide-messages-d2c.md)。

* 了解 IoT 中心[云到设备的消息传递](iot-hub-devguide-messages-c2d.md)。