---
title: IoT 即插即用约定 |Microsoft Docs
description: 约定 IoT 即插即用的说明在发送遥测和属性时需要设备使用，并处理命令和属性更新。
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 8b5492a737b733f486455507a8a813b5d583d453
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91573831"
---
# <a name="iot-plug-and-play-conventions"></a>IoT 即插即用约定

IoT 即插即用设备在与 IoT 中心交换消息时应遵循一组约定。 IoT 即插即用设备使用 MQTT 协议与 IoT 中心通信。

设备可以包含 [模块](../iot-hub/iot-hub-devguide-module-twins.md)，也可以在 IoT Edge 运行时承载的 [IoT Edge 模块](../iot-edge/about-iot-edge.md) 中实现。

使用 [数字孪生定义语言 v2 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl) _模型_来描述 IoT 即插即用设备实现的遥测、属性和命令。 本文中引用的模型分为两种类型：

- **无组件** -没有组件的模型。 该模型在主接口的内容部分中将遥测、属性和命令声明为顶级属性。 在 Azure IoT 资源管理器工具中，此模型显示为单个 _默认组件_。
- **多个组件** -由两个或多个接口组成的模型。 主接口，它显示为 _默认组件_，具有遥测、属性和命令。 将一个或多个接口声明为包含附加遥测、属性和命令的组件。

有关详细信息，请参阅 [IoT 即插即用模型中的组件](concepts-components.md)。

## <a name="identify-the-model"></a>标识模型

若要公布它实现的模型，IoT 即插即用设备或模块通过添加到字段，在 MQTT 连接数据包中包含模型 ID `model-id` `USERNAME` 。

若要标识设备或模块实现的模型，服务可以从以下各内容获取模型 ID：

- 设备克隆 `modelId` 字段。
- 数字双子 `$metadata.$model` 字段。
- 数字克隆更改通知。

## <a name="telemetry"></a>遥测

从无组件设备发送的遥测无需任何额外的元数据。 系统添加 `dt-dataschema` 属性。

从多个组件设备发送的遥测必须 `$.sub` 作为消息属性添加。 系统添加 `dt-subject` 和 `dt-dataschema` 属性。

## <a name="read-only-properties"></a>只读属性

### <a name="sample-no-component-read-only-property"></a>示例无组件只读属性

设备或模块可以发送遵循 DTDL v2 规则的任何有效 JSON。

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

报告的示例属性负载：

```json
"reported" :
{
  "temperature" : 21.3
}
```

### <a name="sample-multiple-components-read-only-property"></a>示例多个组件只读属性

设备或模块必须添加 `{"__t": "c"}` 标记，以指示元素引用组件。

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}

{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

报告的示例属性负载：

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "temperature": 21.3
  }
}
```

## <a name="writable-properties"></a>可写属性

设备或模块应通过发送报告的属性来确认它已接收到属性。 报告的属性应包括：

- `value` -属性的实际值 (通常是接收到的值，但设备可能决定报告不同的值) 。
- `ac` -使用 HTTP 状态代码的确认代码。
- `av` -引用所 `$version` 需属性的的确认版本。 可以在所需的属性 JSON 有效负载中找到此值。
- `ad` -可选确认说明。

设备启动时，应该请求设备克隆，并检查是否有可写的属性更新。 如果在设备处于脱机状态时增加了可写属性的版本，则设备应发送报告的属性响应，以确认收到更新。

当设备首次启动时，如果它没有从中心接收到初始所需属性，则可以为报告的属性发送初始值。 在这种情况下，设备应设置 `av` 为 `1` 。 例如：

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 1,
    "ad": "initialize"
  }
}
```

设备可以使用报告的属性向中心提供其他信息。 例如，设备可能会使用一系列正在进行的消息（例如）进行响应：

```json
"reported": {
  "targetTemperature": {
    "value": 35.0,
    "ac": 202,
    "av": 3,
    "ad": "In-progress - reporting current temperature"
  }
}
```

当设备达到目标温度时，它将发送以下消息：

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 3,
    "ad": "Reached target temperature"
  }
}
```

设备可能会报告错误，如：

```json
"reported": {
  "targetTemperature": {
    "value": 120.0,
    "ac": 500,
    "av": 3,
    "ad": "Target temperature out of range. Valid range is 10 to 99."
  }
}
```

### <a name="sample-no-component-writable-property"></a>无组件可写属性示例

当设备在单个有效负载中接收多个报告属性时，它可以跨多个有效负载发送报告的属性响应。

设备或模块可以发送遵循 DTDL v2 规则的任何有效 JSON：

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

所需的属性负载示例：

```json
"desired" :
{
  "targetTemperature" : 21.3,
  "targetHumidity" : 80
},
"$version" : 3
```

报告的示例属性优先负载：

```json
"reported": {
  "targetTemperature": {
    "value": 21.3,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

报告的示例属性第二个负载：

```json
"reported": {
  "targetHumidity": {
    "value": 80,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

### <a name="sample-multiple-components-writable-property"></a>示例多组件可写属性

设备或模块必须添加 `{"__t": "c"}` 标记，以指示元素引用组件。

仅为在组件中定义的属性更新发送标记。 对默认组件中定义的属性的更新不包括标记，请参阅 [示例无组件可写属性](#sample-no-component-writable-property)

当设备在单个有效负载中接收多个报告属性时，它可以跨多个有效负载发送报告的属性响应。

设备或模块应通过发送报告的属性来确认它已接收到属性：

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}

{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

所需的属性负载示例：

```json
"desired": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": 21.3,
    "targetHumidity": 80
  }
},
"$version" : 3
```

报告的示例属性优先负载：

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": {
      "value": 23,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

报告的示例属性第二个负载：

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetHumidity": {
      "value": 80,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

## <a name="commands"></a>命令

没有组件接口使用不带前缀的命令名。

在设备或模块上，多个组件接口使用命令名称，格式如下： `componentName*commandName` 。

## <a name="next-steps"></a>后续步骤

现在，你已了解 IoT 即插即用约定，下面是一些其他资源：

- [数字孪生定义语言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 设备 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [模型组件](./concepts-components.md)
