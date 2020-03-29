---
title: 开发人员指南 - IoT 即插即用预览 |微软文档
description: 物联网即插即用开发人员的设备建模说明
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5fda51e6d2f62b9cbef0fcac22d5bb2ea0df905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605226"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>IoT 即插即用预览建模开发人员指南

IoT 即插即用预览允许您构建向 Azure IoT 应用程序通告其功能的设备。 当客户将其连接到启用 IoT 即插即用的应用程序时，IoT 即插即用设备不需要手动配置。 IoT 中心是支持 IoT 即插即用的应用程序的示例。

要构建 IoT 即插即用设备，您需要创建设备说明。 描述使用一种称为数字孪生定义语言 （DTDL） 的简单定义语言完成。

## <a name="device-capability-model"></a>设备功能模型

使用 DTDL，您可以创建_设备功能模型_来描述设备的部件。 典型的 IoT 设备由：

- 自定义部件，这些部件使您的设备独一无二。
- 标准部件，这是所有设备共有的部件。

这些部件称为设备功能模型中的_接口_。 接口定义设备实现的每个部件的详细信息。

下面的示例显示了恒温器设备的设备功能模型：

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

- `@id`：以简单统一资源名称的形式提供的唯一 ID。
- `@type`声明此对象是功能模型。
- `@context`：指定用于功能模型的 DTDL 版本。
- `implements`：列出设备实现的接口。

实现部分中接口列表中的每个条目都有：

- `name`：接口的编程名称。
- `schema`：功能模型实现的接口。

可以使用其他可选字段向功能模型添加更多详细信息，如显示名称和说明。 在功能模型中声明的接口可以被视为设备的组件。 对于公共预览，每个架构的接口列表可能只有一个条目。

## <a name="interface"></a>接口

使用 DTDL，您可以使用接口描述设备的功能。 接口描述设备实现_的属性、__遥测_和_命令_：

- `Properties`. 属性是表示设备状态的数据字段。 使用属性表示设备的持久状态，例如冷却液泵的开断状态。 属性还可以表示基本设备属性，如设备的固件版本。 您可以将属性声明为只读或可写。
- `Telemetry`. 遥测字段表示来自传感器的测量值。 每当您的设备进行传感器测量时，它都应该发送包含传感器数据的遥测事件。
- `Commands`. 命令表示设备用户可以在设备上执行的方法。 例如，重置命令或命令以打开或关闭风扇。

下面的示例显示了恒温器设备的接口：

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

接口具有一些必填字段：

- `@id`：以简单统一资源名称的形式提供的唯一 ID。
- `@type`声明此对象是接口。
- `@context`：指定用于接口的 DTDL 版本。
- `contents`：列出构成设备的属性、遥测和命令。

在这个简单示例中，只有一个遥测字段。 最小字段描述具有：

- `@type`： 指定功能的类型： `Telemetry`、`Property`或`Command`。
- `name`： 提供遥测值的名称。
- `schema`：指定遥测的数据类型。 此值可以是基元类型，如双精度、整数、布尔或字符串。 还支持复杂的对象类型、数组和映射。

其他可选字段（如显示名称和说明）允许您向界面和功能添加更多详细信息。

### <a name="properties"></a>属性

默认情况下，属性是只读的。 只读属性表示设备向 IoT 中心报告属性值更新。 IoT 中心无法设置只读属性的值。

您还可以在接口上将属性标记为可写。 设备可以从 IoT 中心接收到可写入属性的更新，以及向中心报告属性值更新。

不必连接设备来设置属性值。 当设备下次连接到集线器时，将传输更新的值。 此行为同时适用于只读属性和可写入属性。

不要使用属性从设备发送遥测数据。 例如，只读属性（如`temperatureSetting=80`应表示设备温度已设置为 80，设备正在尝试达到或保持此温度）。

对于可写属性，设备应用程序返回所需的状态代码、版本和说明，以指示它是否接收和应用属性值。

### <a name="telemetry"></a>遥测

默认情况下，IoT 中心将所有遥测消息从设备路由到与[事件中心](https://azure.microsoft.com/documentation/services/event-hubs/)兼容[的内置面向服务的终结点（**消息/事件**）。](../iot-hub/iot-hub-devguide-messages-read-builtin.md)

您可以使用[IoT 中心的自定义终结点和路由规则](../iot-hub/iot-hub-devguide-messages-d2c.md)将遥测发送到其他目标，如 Blob 存储或其他事件中心。 路由规则使用消息属性来选择消息。

### <a name="commands"></a>命令

命令是同步的或异步的。 默认情况下，同步命令必须在 30 秒内执行，并且必须在命令到达时连接设备。 如果设备及时响应，或者设备未连接，则命令将失败。

对长时间运行的操作使用异步命令。 设备使用遥测消息发送进度信息。 这些进度消息具有以下标头属性：

- `iothub-command-name`：命令名称，例如`UpdateFirmware`。
- `iothub-command-request-id`：在服务器端生成并在初始调用中发送到设备的请求 ID。
- `iothub-interface-id`：此命令定义的接口 ID，例如`urn:example:AssetTracker:1`。
 `iothub-interface-name`：此接口的实例名称，例如`myAssetTracker`。
- `iothub-command-statuscode`：从设备返回的状态代码，例如`202`。

## <a name="register-a-device"></a>注册设备

IoT 即插即用可让您轻松宣传设备的功能。 使用 IoT 即插即用，在设备连接到 IoT 中心后，必须注册设备功能模型。 注册使客户能够使用设备的 IoT 即插即用功能。

本指南演示如何使用 Azure IoT 设备 SDK 为 C 注册设备。

对于设备实现的每个接口，必须创建一个接口并将其连接到其实现。

对于前面显示的恒温器接口，使用 C SDK 创建恒温器接口并将其连接到其实现：

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

创建接口后，请注册设备功能模型和与 IoT 中心接口：

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

IoT 即插即用允许您使用已在 IoT 中心注册其功能的设备。 例如，您可以直接访问设备的属性和命令。

要使用连接到 IoT 中心功能中心的 IoT 即插即用设备，请使用 IoT 中心 REST API 或 IoT 语言 SDK 之一。 以下示例使用 IoT 中心 REST API。 API 的当前版本为`2019-07-01-preview`。 追加`?api-version=2019-07-01-preview`到 REST PI 呼叫。

要获取恒温器中`fwVersion``DeviceInformation`接口中的设备属性（如固件版本 （ ） 的值，请使用数字孪生 REST API。

如果调用`t-123`恒温器设备，则通过 REST API GET 调用获取设备实现的所有接口上的所有属性：

```REST
GET /digitalTwins/t-123/interfaces
```

更一般地说，所有接口上的所有属性都使用此 REST API`{device-id}`模板进行访问，其中是设备的标识符：

```REST
GET /digitalTwins/{device-id}/interfaces
```

如果知道接口的名称（如`deviceInformation`），并且想要获取该特定接口的属性，请按名称将请求范围限定为特定接口：

```REST
GET /digitalTwins/t-123/interfaces/deviceInformation
```

更一般地说，可以通过此 REST API 模板访问特定接口的属性，`device-id`该模板是设备的标识符，是`{interface-name}`接口的名称：

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

您可以直接调用 IoT 即插即用设备命令。 如果`Thermostat``t-123`设备中的接口具有`restart`命令，则可以使用 REST API POST 调用调用它：

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

更一般地说，可以通过以下 REST API 模板调用命令：

- `device-id`：设备的标识符。
- `interface-name`：设备功能模型中实现部分的接口的名称。
- `command-name`：命令的名称。

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>后续步骤

现在，您已经了解了设备建模，下面是一些其他资源：

- [数字双定义语言 （DTDL）](https://aka.ms/DTDL)
- [C 设备 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [物联网 REST API](https://docs.microsoft.com/rest/api/iothub/device)
