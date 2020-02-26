---
title: 开发人员指南-IoT 即插即用预览版 |Microsoft Docs
description: 用于 IoT 即插即用开发人员的设备建模说明
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5fda51e6d2f62b9cbef0fcac22d5bb2ea0df905b
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605226"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>IoT 即插即用预览版建模开发人员指南

IoT 即插即用预览版允许构建将其功能公布到 Azure IoT 应用程序的设备。 当客户将其连接到 IoT 即插即用启用的应用程序时，IoT 即插即用设备不需要手动配置。 IoT Central 是启用 IoT 即插即用的应用程序的一个示例。

若要构建 IoT 即插即用设备，需要创建设备描述。 使用名为数字孪生定义语言（DTDL）的简单定义语言来完成说明。

## <a name="device-capability-model"></a>设备功能模型

使用 DTDL，可以创建_设备功能模型_来描述设备的各个部分。 典型的 IoT 设备由以下内容组成：

- 自定义部件，这是使您的设备独一无二的东西。
- 标准部件，是所有设备通用的部分。

这些部件在设备功能模型中称为_接口_。 接口定义设备实现的每个部件的详细信息。

以下示例显示了恒温器设备的设备功能模型：

```json
{
  "@id": "urn:example:Thermostat_T_1000:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "name": "thermostat",
      "schema": "urn:example:Thermostat:1"
    },
    {
      "name": "urn_azureiot_deviceManagement_DeviceInformation",
      "schema": "urn:azureiot:deviceManagement:DeviceInformation:1"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

功能模型具有一些必需字段：

- `@id`：唯一 ID，采用简单统一资源名称的形式。
- `@type`：声明此对象是功能模型。
- `@context`：指定用于功能模型的 DTDL 版本。
- `implements`：列出设备实现的接口。

"实现" 部分的接口列表中的每个条目都有：

- `name`：接口的编程名称。
- `schema`：功能模型实现的接口。

还有其他可选字段可用于向功能模型中添加更多详细信息，例如显示名称和说明。 在功能模型中声明的接口可以被视为该设备的组件。 对于公共预览版，接口列表每个架构只能有一个条目。

## <a name="interface"></a>接口

使用 DTDL，可以使用接口描述设备的功能。 接口描述设备的一部分实现的_属性_、_遥测_和_命令_：

- `Properties` 列中的一个值匹配。 属性是表示设备状态的数据字段。 使用属性来表示设备的持久状态，如冷却剂泵的关闭状态。 属性还可以表示基本设备属性，例如设备的固件版本。 可以将属性声明为只读或可写。
- `Telemetry` 列中的一个值匹配。 遥测字段表示传感器的度量。 如果设备采用传感器度量，应发送包含传感器数据的遥测事件。
- `Commands` 列中的一个值匹配。 命令表示设备的用户可以在设备上执行的方法。 例如，使用 reset 命令或命令来打开或关闭风扇。

以下示例显示了恒温器设备的接口：

```json
{
  "@id": "urn:example:Thermostat:1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Telemetry",
      "name": "temperature",
      "schema": "double"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

接口具有一些必需字段：

- `@id`：唯一 ID，采用简单统一资源名称的形式。
- `@type`：声明此对象是接口。
- `@context`：指定用于接口的 DTDL 版本。
- `contents`：列出组成设备的属性、遥测和命令。

在这个简单的示例中，只有一个遥测字段。 最小字段说明具有：

- `@type`：指定功能类型： `Telemetry`、`Property`或 `Command`。
- `name`：提供遥测值的名称。
- `schema`：指定遥测数据类型。 此值可以是基元类型，如 double、integer、boolean 或 string。 还支持复杂的对象类型、数组和映射。

其他可选字段（如显示名称和说明）允许您将更多详细信息添加到界面和功能。

### <a name="properties"></a>属性

默认情况下，属性是只读的。 只读属性意味着设备将属性值更新报告到 IoT 中心。 IoT 中心无法设置只读属性的值。

还可以在接口上将属性标记为可写。 设备可以从 IoT 中心接收对可写属性的更新，以及向中心报告属性值更新。

设备无需连接即可设置属性值。 当设备下一次连接到中心时，将传输更新的值。 此行为同时适用于只读属性和可写属性。

不要使用属性从设备发送遥测数据。 例如，readonly 属性（如 `temperatureSetting=80`）应意味着设备温度已设置为80，并且设备尝试进入或停留在此温度上。

对于可写属性，设备应用程序会返回所需状态状态代码、版本和说明，以指示是否已收到并应用属性值。

### <a name="telemetry"></a>遥测

默认情况下，IoT 中心会将来自设备的所有遥测消息路由到与[事件中心](https://azure.microsoft.com/documentation/services/event-hubs/)兼容的[内置面向服务的终结点（**消息/事件**）](../iot-hub/iot-hub-devguide-messages-read-builtin.md) 。

可以使用[IoT 中心的自定义终结点和路由规则](../iot-hub/iot-hub-devguide-messages-d2c.md)将遥测数据发送到其他目标，例如 blob 存储或其他事件中心。 路由规则使用消息属性选择消息。

### <a name="commands"></a>命令

命令为同步或异步。 默认情况下，必须在30秒内执行同步命令，并且在命令到达时必须连接设备。 如果设备有时间响应或设备未连接，则该命令将失败。

为长时间运行的操作使用异步命令。 设备使用遥测消息发送进度信息。 这些进度消息具有以下标头属性：

- `iothub-command-name`：命令名称，例如 `UpdateFirmware`。
- `iothub-command-request-id`：在服务器端生成并在初始调用中发送到设备的请求 ID。
- `iothub-interface-id`：在其上定义此命令的接口的 ID，例如 `urn:example:AssetTracker:1`。
 `iothub-interface-name`：此接口的实例名称，例如 `myAssetTracker`。
- `iothub-command-statuscode`：从设备返回的状态代码，例如 `202`。

## <a name="register-a-device"></a>注册设备

IoT 即插即用可以轻松地公布设备的功能。 对于 IoT 即插即用，在设备连接到 IoT 中心后，必须注册设备功能模型。 注册使客户能够使用设备的 IoT 即插即用功能。

本指南演示如何使用适用于 C 语言的 Azure IoT 设备 SDK 注册设备。

对于设备实现的每个接口，都必须创建一个接口，并将其连接到其实现。

对于先前显示的恒温器接口，使用 C SDK 创建恒温器接口并将其连接到其实现：

```c
DIGITALTWIN_INTERFACE_HANDLE thermostatInterfaceHandle;

DIGITALTWIN_CLIENT_RESULT result = DigitalTwin_InterfaceClient_Create(
    "thermostat",
    "urn:example:Thermostat:1",
    null, null,
    &thermostatInterfaceHandle);

result = DigitalTwin_Interface_SetCommandsCallbacks(
    thermostatInterfaceHandle,
    commandsCallbackTable);

result = DigitalTwin_Interface_SetPropertiesUpdatedCallbacks(
    thermostatInterfaceHandle,
    propertiesCallbackTable);

```

对设备实现的每个接口重复此代码。

创建接口后，使用 IoT 中心注册设备功能模型和接口：

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaces, 2,
    null, null);
```

## <a name="use-a-device"></a>使用设备

IoT 即插即用允许你使用已在 IoT 中心注册其功能的设备。 例如，你可以直接访问设备的属性和命令。

若要使用已连接到 IoT 中心的 IoT 即插即用设备，请使用 IoT 中心 REST API 或 IoT 语言 Sdk 之一。 以下示例使用 IoT 中心 REST API。 `2019-07-01-preview`当前版本的 API。 向 REST PI 调用追加 `?api-version=2019-07-01-preview`。

若要获取设备属性的值，例如恒温器的 `DeviceInformation` 接口中的固件版本（`fwVersion`），请使用数字孪生 REST API。

如果你的恒温器设备 `t-123`调用，你将获取设备使用 REST API GET 调用实现的所有接口的所有属性：

```REST
GET /digitalTwins/t-123/interfaces
```

一般来说，所有接口上的所有属性都是通过此 REST API 模板访问的，其中 `{device-id}` 是设备的标识符：

```REST
GET /digitalTwins/{device-id}/interfaces
```

如果知道接口的名称（例如 `deviceInformation`），并且想要获取该特定接口的属性，请按名称将请求按名称限定到特定的接口：

```REST
GET /digitalTwins/t-123/interfaces/deviceInformation
```

更常见的情况是，可以通过此 REST API 模板访问特定接口的属性，其中 `device-id` 是设备的标识符，`{interface-name}` 是接口的名称：

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

可以直接调用 IoT 即插即用设备命令。 如果 `t-123` 设备中的 `Thermostat` 接口具有 `restart` 命令，则可以使用 REST API POST 调用来调用它：

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

通常，可以通过此 REST API 模板调用命令：

- `device-id`：设备的标识符。
- `interface-name`：设备功能模型的 "实现" 部分中的接口的名称。
- `command-name`：命令的名称。

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>后续步骤

现在，你已了解设备建模，以下是一些其他资源：

- [数字克隆定义语言（DTDL）](https://aka.ms/DTDL)
- [C 设备 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
