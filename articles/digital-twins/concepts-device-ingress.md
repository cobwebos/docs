---
title: 使用 Azure 数字孪生的设备连接与遥测数据入口 | Microsoft Docs
description: 概述如何将设备载入 Azure 数字孪生
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: alinast
ms.openlocfilehash: a3a5555bf163aedd9b41a9c9aa363a883deb4cb8
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638519"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>设备连接与遥测数据入口

设备和传感器发送的遥测数据构成了任何 IoT 解决方案的主干。 如何表示这些不同的资源并在某个位置的上下文中管理它们是 IoT 应用开发中的一个首要问题。 Azure 数字孪生将设备和传感器与空间智能图相结合，简化了 IoT 解决方案的开发过程。

若要开始，请在空间图的根位置创建 Azure IoT 中心资源。 IoT 中心资源允许根空间下的所有设备发送消息。 创建 IoT 中心后，将设备注册到数字孪生实例中的传感器。 这些设备可以通过 [Azure IoT 设备 SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) 向数字孪生服务发送数据。

有关载入设备的分步指南，请参阅[部署和配置数字孪生的教程](tutorial-facilities-setup.md)。 步骤概览：

- 从 [Azure 门户](https://portal.azure.com)部署数字孪生实例。
- 在图中创建空间。
- 创建 IoT 中心资源并将其分配到图中的某个空间。
- 在图中创建设备和传感器，并将它们分配到上述步骤中创建的空间。
- 创建匹配程序，根据条件筛选遥测消息。
- 创建一个[用户定义的函数](concepts-user-defined-functions.md)并将它分配到图中的某个空间，以对遥测消息进行自定义处理。
- 分配角色，使用户定义的函数能够访问图中的数据。
- 从数字孪生管理 API 获取 IoT 中心设备连接字符串。
- 使用 Azure IoT 设备 SDK 在设备上配置设备连接字符串。

以下各部分介绍如何从数字孪生管理 API 获取 IoT 中心设备连接字符串， 以及如何使用 IoT 中心遥测消息格式发送基于传感器的遥测数据。 数字孪生要求它收到的每条遥测数据都与空间图中的某个传感器相关联。 此项要求可确保在适当的空间上下文中处理和路由数据。

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>从管理 API 获取 IoT 中心设备连接字符串

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

使用 `includes=ConnectionString` 参数在设备 API 上执行 GET 调用，以获取 IoT 中心设备连接字符串。 按设备 GUID 或硬件 ID 进行筛选以查找给定的设备。

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| 参数 | 替换为 |
| --- | --- |
| *YOUR_DEVICE_GUID* | 设备 ID |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?HardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| 参数值 | 替换为 |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | 设备硬件 ID |

在响应有效负载中，复制设备的 **connectionString** 属性。 调用 Azure IoT 设备 SDK 以向数字孪生发送数据时，将要使用该属性。

## <a name="device-to-cloud-message"></a>设备到云的消息

可以自定义设备的消息格式和有效负载，以满足解决方案的需求。 使用任何可以序列化为 [Azure IoT Device Client Message 类 Message(byte[] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___) 支持的字节数组或流的数据合同。 只要在相应的用户定义函数中解码数据合同，该消息就能采用所选的自定义二进制格式。 设备到云的消息只需满足一项要求。 维护一组属性以确保消息正确路由到处理引擎。

### <a name="telemetry-properties"></a>遥测属性

 **消息**的有效负载内容可以是最大为 256 KB 的任意数据。 [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) 类型的属性预期需要满足几项要求。 下表显示了系统支持的必需和可选属性。

| 属性名 | 值 | 必填 | 描述 |
|---|---|---|---|
| DigitalTwins-Telemetry | 1.0 | 是 | 在系统中标识消息的常量值。 |
| DigitalTwins-SensorHardwareId | `string(72)` | 是 | 发送**消息**的传感器的唯一标识符。 该值必须与对象的 HardwareId 属性匹配，以便系统可以处理它。 例如， `00FF0643BE88-CO2` 。 |
| CreationTimeUtc | `string` | 否 | [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 格式的日期字符串，用于标识有效负载的采样时间。 例如， `2018-09-20T07:35:00.8587882-07:00` 。 |
| **CorrelationId** | `string` | 否 | 用于跟踪整个系统中的事件的 UUID。 例如， `cec16751-ab27-405d-8fe6-c68e1412ce1f` 。

### <a name="send-your-message-to-digital-twins"></a>将消息发送到数字孪生

使用 DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) 或 [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) 调用将消息发送到数字孪生。

## <a name="next-steps"></a>后续步骤

- 若要了解 Azure 数字孪生的数据处理和用户定义的函数功能，请阅读 [Azure 数字孪生数据处理和用户定义的函数](concepts-user-defined-functions.md)。
