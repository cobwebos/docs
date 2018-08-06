---
title: include 文件
description: include 文件
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 07/26/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: dc87079083b8f07ad18f5f871bff64de8d492ebd
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285640"
---
## <a name="the-parts-of-the-device-model-schema"></a>设备模型架构的组成部分

每个设备模型（例如冷却器或卡车）定义模拟服务可以模拟的设备类型。 每个设备模型存储在采用以下顶级架构的 JSON 文件中：

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "elevator-01",
  "Version": "0.0.1",
  "Name": "Elevator",
  "Description": "Elevator with floor, vibration and temperature sensors.",
  "Protocol": "AMQP",
  "Simulation": {
    // Specify the simulation behavior
  },
  "Properties": {
    // Define properties
  },
  "Telemetry": [
    // Specify telemetry
  ],
  "CloudToDeviceMethods": {
    // Specify methods
  }
}
```

可以在 GitHub 上的 [devicemodels 文件夹](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels)中查看默认模拟设备的架构文件。

下表描述了顶级架构条目：

| 架构条目 | Description |
| -- | --- |
| `SchemaVersion` | 架构版本始终是 `1.0.0`，特定于此文件的格式。 |
| `Id` | 此设备模型的唯一 ID。 |
| `Version` | 标识设备模型的版本。 |
| `Name` | 设备模型的友好名称。 |
| `Description` | 设备模型的说明。 |
| `Protocol` | 设备使用的连接协议。 可以是 `AMQP`、`MQTT` 和 `HTTP`。 |

以下部分介绍 JSON 架构中的其他节：

## <a name="simulation"></a>模拟

在 `Simulation` 节中定义模拟设备的内部状态。 设备发送的任何遥测值必须是此设备状态的一部分。

设备状态的定义包含两个元素：

* `InitialState` 定义设备状态对象的所有属性的初始值。
* `Script` 标识按计划运行的、更新设备状态的 JavaScript 文件。 可以使用此脚本来随机化设备发送的遥测值。

若要详细了解用于更新设备状态对象的 JavaScript 文件，请参阅[了解设备模型行为](../articles/iot-accelerators/iot-accelerators-device-simulation-device-behavior.md)。

以下示例演示模拟冷却器设备的设备状态对象定义：

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

模拟服务每隔 5 秒运行 **chiller-01-state.js** 来更新设备状态。 可以在 GitHub 上的 [scripts 文件夹](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts)中查看默认模拟设备的 JavaScript 文件。 根据约定，这些 JavaScript 文件使用后缀 **-state** 将自身与用于实现方法行为的文件相区分。

## <a name="properties"></a>属性

架构的 `Properties` 节定义设备向解决方案报告的属性值。 例如：

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

解决方案启动时，会查询所有模拟设备，以生成要在 UI 中使用的 `Type` 值列表。 解决方案使用 `Latitiude` 和 `Longitude` 属性将设备位置添加到仪表板上的地图中。

## <a name="telemetry"></a>遥测

`Telemetry` 数组列出模拟设备向解决方案发送的所有遥测类型。

以下示例每隔 10 秒发送 JSON 遥测消息，其中包含来自电梯传感器的 `floor`、`vibration` 和 `temperature` 数据：

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate": "{\"floor\":${floor},\"vibration\":${vibration},\"vibration_unit\":\"${vibration_unit}\",\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
    "MessageSchema": {
      "Name": "elevator-sensors;v1",
      "Format": "JSON",
      "Fields": {
        "floor": "integer",
        "vibration": "double",
        "vibration_unit": "text",
        "temperature": "double",
        "temperature_unit": "text"
      }
    }
  }
]
```

`MessageTemplate` 定义模拟设备发送的 JSON 消息的结构。 `MessageTemplate` 中的占位符使用语法 `${NAME}`，其中，`NAME` 是[设备状态对象](#simulation)中的某个键。 字符串应括在引号中，而数字则不能。

`MessageSchema` 定义模拟设备发送的消息的架构。 消息架构也会发布到 IoT 中心，使后端应用程序能够重复使用相关信息来解释传入的遥测数据。

目前，只能使用 JSON 消息架构。 架构中列出的字段可使用以下类型：

* 对象 - 使用 JSON 序列化
* 二进制 - 使用 base64 序列化
* 文本
* 布尔
* Integer
* Double
* DateTime

若要按不同的间隔发送遥测消息，请将多种遥测类型添加到 `Telemetry` 数组。 以下示例每隔 10 秒发送温度和湿度数据，每隔 1 分钟发送照明状态：

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate":
      "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
    "MessageSchema": {
      "Name": "RoomComfort;v1",
      "Format": "JSON",
      "Fields": {
        "temperature": "double",
        "temperature_unit": "text",
        "humidity": "integer"
      }
    }
  },
  {
    "Interval": "00:01:00",
    "MessageTemplate": "{\"lights\":${lights_on}}",
    "MessageSchema": {
      "Name": "RoomLights;v1",
      "Format": "JSON",
      "Fields": {
        "lights": "boolean"
      }
    }
  }
],
```

## <a name="cloudtodevicemethods"></a>CloudToDeviceMethods

模拟设备可以响应从 IoT 中心调用的云到设备方法。 设备模型架构文件中的 `CloudToDeviceMethods` 节：

* 定义模拟设备可以响应的方法。
* 标识包含要执行的逻辑的 JavaScript 文件。

模拟设备将它支持的方法列表发送到它连接到的 IoT 中心。

若要详细了解用于实现设备行为的 JavaScript 文件，请参阅[了解设备模型行为](../articles/iot-accelerators/iot-accelerators-device-simulation-device-behavior.md)。

以下示例指定三个支持的方法，以及用于实现这些方法的 JavaScript 文件：

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

可以在 GitHub 上的 [scripts 文件夹](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts)中查看默认模拟设备的 JavaScript 文件。 根据约定，这些 JavaScript 文件使用后缀 **-method** 将自身与用于实现状态行为的文件相区分。