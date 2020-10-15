---
title: 无需 Azure IoT SDK 进行开发 |Microsoft Docs
description: 开发人员指南-有关的信息和链接，可用于在不使用 Azure IoT SDK 的情况下构建设备应用和后端应用程序的主题。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2020
ms.custom:
- mqtt
- amqp
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: 3968f19329536169c3fb3eb1fbbaff99e99c293d
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079636"
---
# <a name="develop-without-using-an-azure-iot-hub-sdk"></a>在不使用 Azure IoT 中心 SDK 的情况下进行开发

本主题为想要开发设备或后端应用的开发人员提供有用的信息和链接，而无需使用 Azure IoT Sdk。

Microsoft 强烈建议使用 Azure IoT SDK。 Azure IoT 设备和服务 Sdk 发布在多个常用平台上。 Sdk 提供了一个便利层，用于处理底层通信协议（包括设备连接和重新连接，以及重试策略）的许多复杂性。 Sdk 会定期更新，以提供 IoT 中心公开的最新功能以及安全更新。 使用 Sdk 有助于减少开发时间和专用于代码维护的时间。 若要了解有关 Azure IoT Sdk 的详细信息，请参阅 [Azure Iot 设备和服务 sdk](iot-hub-devguide-sdks.md)。 有关使用 Azure IoT SDK 的好处的更多详细信息，请参阅 [使用 Azure Iot sdk 和缺陷的好处，以避免不](https://azure.microsoft.com/en-us/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) 使用博客文章。

尽管 IoT 中心支持 AMQP、AMQP over Websocket、HTTPS、MQTT 和 MQTT over Websocket 以便与设备通信，但如果设备支持，我们建议使用 MQTT。

## <a name="development-prerequisites"></a>开发先决条件

在开始开发之前，你应该全面了解 IoT 中心，以及你希望设备或后端应用程序实现的功能。 下面是一个非常简短的主题列表，你应该熟悉这些主题：

* 请确保了解 IoT 中心公开的终结点和每个终结点支持的协议。 若要了解详细信息，请参阅 [IoT 中心终结点](iot-hub-devguide-endpoints.md)。

* 对于设备应用，我们强烈建议你使用 MQTT。 但在选择协议之前，请务必了解每个协议施加的限制。 若要了解详细信息，请参阅 [选择通信协议](iot-hub-devguide-protocols.md)。

* 若要了解如何使用 IoT 中心进行身份验证，请参阅 [控制对 Iot 中心的访问](iot-hub-devguide-security.md)。

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="help-on-different-protocols"></a>有关不同协议的帮助

有关在没有 Azure IoT SDK 的情况下使用以下协议的帮助：

* **AMQP**上的设备或后端应用，请参阅[AMQP 支持](iot-hub-amqp-support.md)。

* **MQTT**上的设备应用，请参阅[MQTT 支持](iot-hub-mqtt-support.md)。 本主题中的大部分工作直接使用 MQTT 协议。 其中还包含有关使用 [IOT MQTT 示例存储库](https://github.com/Azure-Samples/IoTMQTTSample)的信息。 此存储库包含使用 Eclipse Mosquitto 库向 IoT 中心发送消息的 C 示例。

* 设备或 **HTTPS**上的后端应用，请参阅 [AZURE IoT 中心 REST api](https://docs.microsoft.com/rest/api/iothub/)。 请注意，如 [开发先决条件](#development-prerequisites)中所述，不能使用 x.509 证书颁发机构 (CA) 使用 HTTPS 进行身份验证。

对于设备，强烈建议使用 MQTT （如果设备支持）。

## <a name="next-steps"></a>后续步骤

* [MQTT 支持](iot-hub-mqtt-support.md)
