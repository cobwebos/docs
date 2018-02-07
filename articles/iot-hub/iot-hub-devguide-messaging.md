---
title: "了解 Azure IoT 中心消息传送 | Microsoft Docs"
description: "开发人员指南 - 使用 IoT 中心进行设备到云和云到设备的消息传送。 包括消息格式和支持的通信协议的相关信息。"
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
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 7fd89bebf9d7497ad5b13c438b362256d3408219
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="device-to-cloud-and-cloud-to-device-messaging-with-iot-hub"></a>使用 IoT 中心进行设备到云和云到设备的消息传递

通过以下两种方式，使用 IoT 中心消息传递与设备通信：

* 通过[设备到云][lnk-d2c]将消息从设备发送到解决方案后端。
* 通过[云到设备][lnk-c2d]将消息从解决方案后端发送到设备。

IoT 中心消息传送功能的核心属性是消息的可靠性和持久性。 这些属性可在设备端上恢复间歇性连接，以及在云恢复事件处理的负载高峰。 IoT 中心对从设备到云和从云到设备的消息传送实施*至少一次*传送保证。

有关 IoT 中心功能的介绍，请参阅 [Azure 和物联网][lnk-azure-iot]及 [Azure IoT 中心服务概述][lnk-iot-hub-overview]。

## <a name="when-to-use-iot-hub-messaging"></a>何时使用 IoT 中心消息传递

使用设备到云的消息从设备应用发送时序遥测数据和警报，使用云到设备的消息向设备应用发送单向通知。

* 如果在使用设备到云的消息、报告属性或文件上传方面有任何疑问，请参阅[从设备到云通信指南][lnk-d2c-guidance]。
* 如果在使用云到设备的消息、所需属性或直接方法方面有任何疑问，请参阅[从云到设备通信指南][lnk-c2d-guidance]。

## <a name="next-steps"></a>后续步骤

* 了解 IoT 中心[设备到云的消息传递][lnk-d2c]。
* 了解 IoT 中心[云到设备的消息传递][lnk-c2d]。

[lnk-azure-iot]: iot-hub-what-is-azure-iot.md
[lnk-iot-hub-overview]: iot-hub-what-is-iot-hub.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md