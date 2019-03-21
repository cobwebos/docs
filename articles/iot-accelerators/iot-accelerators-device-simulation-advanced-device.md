---
title: 创建高级模拟设备模型 - Azure | Microsoft Docs
description: 本操作指南介绍如何创建与设备模拟解决方案加速器配合使用的高级设备模型。
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 03/18/2019
ms.author: troyhop
ms.openlocfilehash: 4401d4b93a27e76554368ce72d256b38de61df4c
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286119"
---
# <a name="create-an-advanced-device-model"></a>创建高级设备模型

本操作指南介绍用于定义自定义设备模型的 JSON 和 JavaScript 文件。 本文包含一些示例设备模型定义文件，并介绍如何将其上传到设备模拟实例。 若要进行测试，可以创建高级设备模型来模拟更真实的设备行为。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

若要遵循本操作指南中的步骤，需在 Azure 订阅中部署设备模拟的实例。

如果尚未部署设备模拟，则应完成[在 Azure 中部署并运行 IoT 设备模拟](quickstart-device-simulation-deploy.md)快速入门。

### <a name="open-device-simulation"></a>打开设备模拟

若要在浏览器中运行设备模拟，请先导航到 [Microsoft Azure IoT 解决方案加速器](https://www.azureiotsolutions.com)。

可能会要求使用 Azure 订阅凭据登录。

然后在[在 Azure 中部署并运行 IoT 设备模拟](quickstart-device-simulation-deploy.md)快速入门中部署的设备模拟的磁贴上单击“启动”。

## <a name="device-models"></a>设备模型

每个模拟设备属于定义模拟行为的特定设备模型。 此行为包括发送遥测数据的频率、要发送的消息类型，以及支持的方法。

可以使用 JSON 设备定义文件和一组 JavaScript 文件定义设备模型。 这些 JavaScript 文件定义模拟行为，例如，随机遥测和方法逻辑。

典型的设备模型包含：

* 每个设备模型的一个 JSON 文件（例如 elevator.json）。
* 每个设备模型的一个 JavaScript 行为脚本文件（例如 elevator-state.js）
* 每个设备方法的一个 JavaScript 方法脚本文件（例如 elevator-go-down.js）

> [!NOTE]
> 并非所有设备模型都会定义方法。 因此，设备模型不一定有方法脚本。 但是，所有设备模型必须有一个行为脚本。

## <a name="device-definition-file"></a>设备定义文件

每个设备定义文件包含模拟设备模型的详细信息，其中包括以下信息：

* 设备模型名称：字符串。
* 协议：AMQP |MQTT |HTTP。
* 初始设备状态。
* 刷新设备状态的频率。
* 用于刷新设备状态的 JavaScript 文件。
* 要发送的遥测消息列表，每条消息都有特定的发送频率。
* 由后端应用程序用来分析收到的遥测数据的遥测消息架构。
* 支持的方法列表，以及用于模拟每个方法的 JavaScript 文件。

### <a name="file-schema"></a>文件架构

架构版本始终为“1.0.0”，特定于此文件的格式：

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>设备模型说明

以下属性描述设备模型。 每个类型具有唯一的标识符、语义版本、名称和说明：

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>IoT 协议

IoT 设备可以使用不同的协议进行连接。 模拟允许使用 **AMQP**、**MQTT** 或 **HTTP**：

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>模拟设备状态

每个模拟设备具有一种内部状态，必须定义该状态。 该状态还定义了可在遥测中报告的属性。 例如，冷却器的初始状态可能是：

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

配备多个传感器的运动设备可能具有其他属性，例如：

```json
"InitialState": {
    "latitude": 47.445301,
    "longitude": -122.296307,
    "speed": 30.0,
    "speed_unit": "mph",
    "cargotemperature": 38.0,
    "cargotemperature_unit": "F"
}
```

设备状态由模拟服务保存在内存中，并作为输入提供给 JavaScript 函数。 JavaScript 函数可以决定：

* 忽略状态并生成一些随机数据。
* 根据给定的方案，以某种实际方式更新设备状态。

生成状态的函数也会接收作为输入提供的以下值：

* 设备 ID。
* 设备模型。
* 当前时间。 使用此值可按设备和时间生成不同的数据。

### <a name="generating-telemetry-messages"></a>生成遥测消息

模拟服务可为每个设备发送多种遥测类型。 通常，遥测数据包括设备状态中的数据。 例如，模拟的房间可以每隔 10 秒发送有关温度和湿度的信息。 请注意，以下代码片段中的占位符会自动替换为设备状态中的值：

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
    }
],
```

占位符使用特殊语法 **${NAME}**，其中，**NAME** 是 JavaScript **main** 函数返回的设备状态对象中的键。 字符串应括在引号中，而数字则不应括住。

#### <a name="message-schema"></a>消息架构

每个消息类型必须有完善定义的架构。 消息架构也会发布到 IoT 中心，使后端应用程序能够重复使用相关信息来解释传入的遥测数据。

架构支持 JSON 格式，使用该格式可跨多个系统和服务轻松进行解析、转换与分析。

架构中列出的字段可使用以下类型：

* 对象 - 使用 JSON 序列化
* 二进制 - 使用 base64 序列化
* 文本
* Boolean
* Integer
* Double
* DateTime

### <a name="supported-methods"></a>支持的方法

模拟设备还能对方法调用做出反应，在这种情况下，它们会执行某个逻辑并提供一些响应。 类似于模拟，方法逻辑存储在 JavaScript 文件中，并可与设备状态交互。 例如：

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>创建设备定义文件

本操作指南将介绍如何为某架无人机创建设备模型。 该无人机围绕一组初始坐标随机飞行，其位置和海拔会发生变化。

将以下 JSON 复制到文本编辑器，并将其保存为 **drone.json**。

### <a name="device-definition-json-example"></a>设备定义 JSON 示例

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "drone",
  "Version": "0.0.1",
  "Name": "Drone",
  "Description": "Simple drone.",
  "Protocol": "AMQP",
  "Simulation": {
    "InitialState": {
      "velocity": 0.0,
      "velocity_unit": "mm/sec",
      "acceleration": 0.0,
      "acceleration_unit": "mm/sec^2",
      "latitude": 47.476075,
      "longitude": -122.192026,
      "altitude": 0.0
    },
    "Interval": "00:00:05",
    "Scripts": [{
      "Type": "JavaScript",
      "Path": "drone-state.js"
    }]
  },
  "Properties": {
    "Type": "Drone",
    "Firmware": "1.0",
    "Model": "P-96"
  },
  "Tags": {
    "Owner": "Contoso"
  },
  "Telemetry": [{
      "Interval": "00:00:05",
      "MessageTemplate": "{\"velocity\":\"${velocity}\",\"acceleration\":\"${acceleration}\",\"position\":\"${latitude}|${longitude}|${altitude}\"}",
      "MessageSchema": {
        "Name": "drone-event-sensor;v1",
        "Format": "JSON",
        "Fields": {
          "velocity": "double",
          "velocity_unit": "text",
          "acceleration": "double",
          "acceleration_unit": "text",
          "latitude": "double",
          "longitude": "double",
          "altitude": "double"
        }
      }
    }
  ],
    "CloudToDeviceMethods": {
        "RecallDrone": {
            "Type": "JavaScript",
            "Path": "droneRecall-method.js"
        }
    }
}
```

## <a name="behavior-script-files"></a>行为脚本文件

行为脚本文件中的代码将移动无人机。 该脚本通过操控设备的内存中状态，来改变无人机的高度和位置。

JavaScript 文件必须包含可接受以下两个参数的 **main** 函数：

* 一个 **context** 对象，其中包含三个属性：
    * 字符串形式的 **currentTime**，格式为 **yyyy-MM-dd'T'HH:mm:sszzz**。
    * **deviceId**。 例如 **Simulated.Elevator.123**。
    * **deviceModel**。 例如 **Elevator**。
* 一个 **state** 对象，它是函数在上次调用中返回的值。 此设备状态由模拟服务维护，用于生成遥测消息。

**main** 函数返回新的设备状态。 例如：

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>创建行为脚本文件

将以下 JavaScript 复制到文本编辑器，并将其保存为 **drone-state.js**。

### <a name="device-model-javascript-simulation-example"></a>设备模型 JavaScript 模拟示例

```JavaScript
"use strict";

// Position control
const DefaultLatitude = 47.476075;
const DefaultLongitude = -122.192026;
const DistanceVariation = 10;

// Altitude control
const DefaultAltitude = 0.0;
const AverageAltitude = 499.99;
const AltitudeVariation = 5;

// Velocity control
const AverageVelocity = 60.00;
const MinVelocity = 20.00;
const MaxVelocity = 120.00;
const VelocityVariation = 5;

// Acceleration control
const AverageAcceleration = 2.50;
const MinAcceleration = 0.01;
const MaxAcceleration = 9.99;
const AccelerationVariation = 1;

// Display control for position and other attributes
const GeoSpatialPrecision = 6;
const DecimalPrecision = 2;

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: DefaultLatitude,
    longitude: DefaultLongitude,
    altitude: DefaultAltitude
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Simple formula generating a random value around the average
 * in between min and max
 */
function vary(avg, percentage, min, max) {
    var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
    value = Math.max(value, min);
    value = Math.min(value, max);
    return value;
}

/**
 * Entry point function called by the simulation engine.
 * Returns updated simulation state.
 * Device property updates must call updateProperties() to persist.
 *
 * @param context             The context contains current time, device model and id
 * @param previousState       The device state since the last iteration
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global state before generating the new telemetry, so that
    // the telemetry can apply changes using the previous function state.
    restoreSimulation(previousState, previousProperties);

    state.acceleration = vary(AverageAcceleration, AccelerationVariation, MinAcceleration, MaxAcceleration).toFixed(DecimalPrecision);
    state.velocity = vary(AverageVelocity, VelocityVariation, MinVelocity, MaxVelocity).toFixed(DecimalPrecision);

    // Use the last coordinates to calculate the next set with a given variation
    var coords = varylocation(Number(state.latitude), Number(state.longitude), DistanceVariation);
    state.latitude = Number(coords.latitude).toFixed(GeoSpatialPrecision);
    state.longitude = Number(coords.longitude).toFixed(GeoSpatialPrecision);

    // Fluctuate altitude between given variation constant by more or less
    state.altitude = vary(AverageAltitude, AltitudeVariation, AverageAltitude - AltitudeVariation, AverageAltitude + AltitudeVariation).toFixed(DecimalPrecision);

    return state;
}

/**
 * Generate a random geolocation at some distance (in miles)
 * from a given location
 */
function varylocation(latitude, longitude, distance) {
    // Convert to meters, use Earth radius, convert to radians
    var radians = (distance * 1609.344 / 6378137) * (180 / Math.PI);
    return {
        latitude: latitude + radians,
        longitude: longitude + radians / Math.cos(latitude * Math.PI / 180)
    };
}
```

## <a name="create-a-method-script-file"></a>创建方法脚本文件

方法脚本类似于行为脚本。 它们定义调用特定的云到设备方法时的设备行为。

无人机召回脚本将无人机的坐标设置为固定的点，以模拟无人机返航。

将以下 JavaScript 复制到文本编辑器，并将其保存为 **droneRecall-method.js**。

### <a name="device-model-javascript-simulation-example"></a>设备模型 JavaScript 模拟示例

```JavaScript
"use strict";

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: 0.0,
    longitude: 0.0,
    altitude: 0.0
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Entry point function called by the simulation engine.
 *
 * @param context        The context contains current time, device model and id, not used
 * @param previousState  The device state since the last iteration, not used
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global device properties and the global state before
    // generating the new telemetry, so that the telemetry can apply changes
    // using the previous function state.
    restoreSimulation(previousState, previousProperties);

    //simulate the behavior of a drone when recalled
  state.latitude = 47.476075;
  state.longitude = -122.192026;
  return state;
}
```

## <a name="debugging-script-files"></a>调试脚本文件

尽管无法将调试程序附加到正在运行的行为文件，但可以使用 **log** 函数将信息写入服务日志。 出现语法错误时，解释器将会失败，并将有关异常的信息写入日志。

日志记录示例：

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return updateState;
}
```

## <a name="deploy-an-advanced-device-model"></a>部署高级设备模型

若要部署高级设备模型，请上传设备模拟实例的文件：

在菜单栏中选择“设备模型”。 “设备模型”页列出了此设备模拟实例中可用的设备模型：

![设备模型](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

在页面右上角单击“+ 添加设备模型”：

![添加设备模型](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

单击“高级”打开高级设备模型选项卡：

![“高级”选项卡](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

单击“浏览”，并选择创建的 JSON 和 JavaScript 文件。 请务必选择所有三个文件。 如果缺少任一文件，验证将会失败：

![浏览文件](media/iot-accelerators-device-simulation-advanced-device/browse.png)

如果文件通过了验证，请单击“保存”，然后，该设备模型可在模拟中使用。 否则，请修复所有错误并重新上传文件：

![保存](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>后续步骤

在本操作指南中，我们已了解设备模拟中使用的设备模型文件，以及如何创建高级设备模型。 接下来，请探索如何[使用时序见解可视化从设备模拟解决方案加速器发送的遥测数据](https://docs.microsoft.com/azure/iot-accelerators/iot-accelerators-device-simulation-time-series-insights)。
