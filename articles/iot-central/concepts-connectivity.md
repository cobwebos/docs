---
title: Azure IoT Central 中的设备连接 | Microsoft Docs
description: 本文介绍与 Azure IoT Central 中的设备连接相关的重要概念
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: dc9fe144c2258f33ce59c61ce63c15835cc3fa53
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628328"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Azure IoT Central 中的设备连接

本文介绍与 Microsoft Azure IoT Central 中的设备连接相关的重要概念。

每个连接到 Azure IoT Central 应用程序的设备都连接到 Azure IoT Central 所使用的 IoT 中心公开的[终结点](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints)。 可以使用 IoT 中心从连接的设备进行既能缩放又安全可靠的连接。

## <a name="sdk-support"></a>SDK 支持

可以使用 Azure 设备 SDK 以最简单的方式在连接到 Azure IoT Central 应用程序的设备上实现代码。 以下设备 SDK 已发布：

- [适用于 C 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-c)
- [适用于 Python 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-python)
- [适用于 Node.js 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-node)
- [适用于 Java 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-java)
- [适用于 .NET 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-csharp)

每个设备都使用可以标识设备的唯一连接字符串进行连接。 设备只能连接到已在其中注册的 IoT 中心。 在 Azure IoT Central 应用程序中创建真实设备时，该应用程序会为你生成一个可以使用的连接字符串。

## <a name="sdk-features-and-iot-hub-connectivity"></a>SDK 功能和 IoT 中心连接

与 IoT 中心进行的所有设备通信都使用以下 IoT 中心连接选项：

- [设备到云的消息传递](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [设备孪生](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

下表对 Azure IoT Central 设备功能映射到 IoT 中心功能的具体情况进行了汇总：

| Azure IoT Central | Azure IoT 中心 |
| ----------- | ------- |
| 度量：遥测 | 设备到云的消息传递 |
| 设备属性 | 设备孪生报告属性 |
| 设置 | 设备孪生所需的和报告的属性 |

若要详细了解如何使用设备 SDK，请查看下面的某篇文章以获取示例代码：

- [将泛型 Node.js 客户端连接到 Azure IoT Central 应用程序](howto-connect-nodejs.md)
- [将 Raspberry Pi 设备连接到 Azure IoT Central 应用程序](howto-connect-raspberry-pi-python.md)
- [将 DevDiv 工具包设备连接到 Azure IoT Central 应用程序](howto-connect-devkit.md)。

下面的视频简单介绍了如何将真实设备连接到 Azure IoT Central：

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Connect-real-devices-to-Microsoft-IoT-Central/Player]

## <a name="protocols"></a>协议

设备 SDK 支持下述用于连接到 IoT 中心的网络协议：

- MQTT
- AMQP
- HTTPS

若要了解这些不同的协议以及选择协议的指南，请参阅[选择通信协议](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols)。

如果设备无法使用这些受支持协议中的任何一种，可以使用 Azure IoT Edge 进行协议转换。 IoT Edge 支持其他边缘智能方案，可以将处理从 Azure IoT Central 应用程序卸载到边缘。

## <a name="security"></a>“安全”

在设备与 Azure IoT Central 之间交换的所有数据都经过加密。 如果设备已连接到任何面向设备的 IoT 中心终结点，则 IoT 中心会对从该设备发出的所有请求进行身份验证。 为了避免通过网络交换凭据，设备使用签名的令牌进行身份验证。 有关详细信息，请参阅[控制对 IoT 中心的访问](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security)。

> [!NOTE]
> 目前，连接到 Azure IoT Central 的设备必须使用 SAS 令牌。 连接到 Azure IoT Central 的设备不支持 X.509 证书。

## <a name="next-steps"></a>后续步骤

了解 Azure IoT Central 中的设备连接后，建议接下来执行以下步骤：

- [准备和连接 DevKit 设备](howto-connect-devkit.md)
- [准备和连接 Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [将泛型 Node.js 客户端连接到 Azure IoT Central 应用程序](howto-connect-nodejs.md)
