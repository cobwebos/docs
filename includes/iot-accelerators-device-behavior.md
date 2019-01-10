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
ms.openlocfilehash: 0b2b6814ad0b55d8f56998f6d4c9b6bb88663e04
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2018
ms.locfileid: "53443987"
---
## <a name="state-behavior"></a>状态行为

设备模型架构的 [Simulation](../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) 节定义模拟设备的内部状态：

- `InitialState` 定义设备状态对象的所有属性的初始值。
- `Script` 标识按计划运行的、更新设备状态的 JavaScript 文件。

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
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

`InitialState` 节中定义的模拟设备状态由模拟服务保存在内存中。 状态信息作为输入传递给 **chiller-01-state.js** 中定义的 `main` 函数。 在此示例中，模拟服务每隔 5 秒运行 **chiller-01-state.js** 文件。 该脚本可修改模拟设备的状态。

下面概要显示了典型的 `main` 函数：

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

`context` 参数具有以下属性：

- 字符串形式的 `currentTime`，采用 `yyyy-MM-dd'T'HH:mm:sszzz` 格式
- `deviceId`，例如 `Simulated.Chiller.123`
- `deviceModel`，例如 `Chiller`

`state` 参数包含设备模拟服务维护的设备状态。 此值是先前调用 `main` 后返回的 `state` 对象。

以下示例演示了用于处理模拟服务维护的设备状态的 `main` 方法的典型实现：

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState, previousProperties) {

  restoreState(previousState);

  // Update state

  return state;
}
```

以下示例演示 `main` 方法如何模拟不断变化的遥测值：

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState, previousProperties) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

可以在 GitHub 上查看完整的 [chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js)。

## <a name="method-behavior"></a>方法行为

设备模型架构的 [CloudToDeviceMethods](../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) 节定义模拟设备响应的方法。

以下示例显示了模拟冷却器设备支持的方法列表：

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
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

每个方法都有一个关联的 JavaScript 文件用于实现该方法的行为。

该架构的 `InitialState` 节中定义的模拟设备状态由模拟服务保存在内存中。 调用该方法时，状态信息将作为输入传递给 JavaScript 文件中定义的 `main` 函数。 该脚本可修改模拟设备的状态。

下面概要显示了典型的 `main` 函数：

```javascript
function main(context, previousState, previousProperties) {

}
```

`context` 参数具有以下属性：

- 字符串形式的 `currentTime`，采用 `yyyy-MM-dd'T'HH:mm:sszzz` 格式
- `deviceId`，例如 `Simulated.Chiller.123`
- `deviceModel`，例如 `Chiller`

`state` 参数包含设备模拟服务维护的设备状态。

`properties` 参数包含作为报告属性写入到 IoT 中心设备孪生的设备属性。

可以借助三个全局函数来实现方法的行为：

- `updateState` 用于更新模拟服务保存的状态。
- `updateProperty` 用于更新单个设备属性。
- `sleep` 用于暂停执行，以模拟长时间运行的任务。

以下示例演示模拟冷却器设备使用的 **IncreasePressure-method.js** 脚本的缩减版：

```javascript
function main(context, previousState, previousProperties) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>调试脚本文件

无法将调试程序附加到设备模拟服务使用的 Javascript 解释器来运行状态和方法脚本。 但是，可以在服务日志中记录信息。 使用内置的 `log()` 函数可以保存信息，以跟踪和调试函数执行。

如果存在语法错误，则解释器会失败，并在服务日志中写入一个 `Jint.Runtime.JavaScriptException` 条目。

GitHub 上的[本地运行服务](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks)文章介绍了如何在本地运行设备模拟服务。 在本地运行服务可以更方便地调试模拟设备，然后将其部署到云中。