---
title: 使用 Azure 数字孪生的设备连接与遥测数据入口 | Microsoft Docs
description: 使用 Azure 数字孪生加入设备的概述
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 61d05a7e9936a0edd17c5528ce4f55233b6e7e0e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323690"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>设备连接与遥测数据入口

设备和传感器发送的遥测数据构成了任何 IoT 解决方案的主干。 因此，如何表示这些不同的资源并在某个位置的上下文中管理它们是 IoT 应用开发中的一个主要问题。 Azure 数字孪生将设备和传感器与空间智能图相结合，简化了 IoT 解决方案的开发。

首先，应在空间图的根空间处创建一个 `IoTHub` 资源，使根空间下的所有设备均能发送消息。 创建了 IoT 中心，并且已在数字孪生实例中注册了带有传感器的设备后，这些设备就可以开始通过 [Azure IoT 设备 SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-device-sdks) 向数字孪生服务发送数据。

有关加入设备的分步指南，请参阅[部署和配置数字孪生的教程](tutorial-facilities-setup.md)。 步骤概览：

- 从 [Azure 门户](https://portal.azure.com)部署一个 Azure 数字孪生实例
- 在图中创建空格
- 创建 `IoTHub` 资源并将它分配到图中的某个空间
- 在图中创建设备和传感器，并将它们分配到上述步骤中创建的空间
- 创建匹配程序，根据条件筛选遥测消息
- 创建一个[**用户定义的函数**](concepts-user-defined-functions.md)并将它分配到图中的某个空间，以供遥测消息的自定义处理
- 分配角色，使用户定义的函数能够访问图中的数据
- 从数字孪生管理 API 获取 IoT 中心设备连接字符串
- 使用 Azure IoT 设备 SDK 在设备上配置设备连接字符串

下面介绍如何从数字孪生管理 API 获取 IoT 中心设备连接字符串以及如何使用 IoT 中心遥测消息格式发送基于传感器的遥测数据。 数字孪生要求它收到的每条遥测数据都与空间图中的传感器相关联，以确保在适当的空间上下文中处理和路由数据。

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>从管理 API 获取 IoT 中心设备连接字符串

使用 `includes=ConnectionString` 参数在设备 API 上执行 GET 调用，以获取 IoT 中心设备连接字符串。 可以按设备 GUID 或硬件 ID 进行筛选来查找给定设备：

```plaintext
https://yourManagementApiUrl/api/v1.0/devices/yourDeviceGuid?includes=ConnectionString
```

```plaintext
https://yourManagementApiUrl/api/v1.0/devices?hardwareIds=yourDeviceHardwareId&includes=ConnectionString
```

| 自定义属性名称 | 替换为 |
| --- | --- |
| `yourManagementApiUrl` | 管理 API 的完整 URL 路径 |
| `yourDeviceGuid` | 设备 ID |
| `yourDeviceHardwareId` | 设备硬件 ID |

在响应有效负载中，复制设备的 `connectionString` 属性，将在调用 Azure IoT 设备 SDK 向 Azure 数字孪生发送数据时使用该属性。

## <a name="device-to-cloud-message"></a>设备到云消息

可以自定义设备的消息格式和有效负载，以满足解决方案的需求。 可以使用任何可以序列化为 [Azure IoT Device Client Message 类、Message(byte[] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___) 支持的字节数组或流的数据协定。 只要在相应的用户定义函数中解码数据协定，该消息就可采用你选择的自定义二进制格式。 设备到云消息的唯一要求是维护一组属性（见下文），以确保将消息恰当地路由到处理引擎。

### <a name="telemetry-properties"></a>遥测属性

虽然 `Message` 的有效负载内容可以是最大 256 kb 的任意数据，但对预期 [Message.Properties](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) 的要求很少。 下面列出的步骤可反映系统支持的必需和可选属性：

| 属性名称 | 值 | 必选 | Description |
|---|---|---|---|
| DigitalTwins-Telemetry | 1.0 | 是 | 标识系统消息的常数值 |
| DigitalTwins-SensorHardwareId | `string(72)` | 是 | 发送 `Message` 的传感器的唯一标识符。 该值必须与对象的 `HardwareId` 属性匹配，以便系统可以处理它。 例如： `00FF0643BE88-CO2` |
| CreationTimeUtc | `string` | 否 | [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 格式的日期字符串，用于标识有效负载的采样时间。 例如： `2018-09-20T07:35:00.8587882-07:00` |
| CorrelationId | `string` | 否 | 可用于跟踪系统中的事件的 `uuid`。 例如： `cec16751-ab27-405d-8fe6-c68e1412ce1f`

### <a name="sending-your-message-to-digital-twins"></a>将消息发送到数字孪生

使用 DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) 或 [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) 调用将消息发送到数字孪生服务。

## <a name="next-steps"></a>后续步骤

若要了解 Azure 数字孪生的数据处理和用户定义的函数这两个功能，请阅读 [Azure 数字孪生数据处理和用户定义的函数](concepts-user-defined-functions.md)。

