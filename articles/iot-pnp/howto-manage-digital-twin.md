---
title: 如何管理 IoT 即插即用数字孪生
description: 如何使用数字克隆 Api 管理 IoT 即插即用设备
author: prashmo
ms.author: prashmo
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5f1c52b764634f8086763aca67dfc32b507d2edd
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042841"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>管理 IoT 即插即用数字孪生

IoT 即插即用支持 **获取数字** 克隆，并 **更新数字** 克隆操作以管理数字孪生。 可以使用 [REST api](/rest/api/iothub/service/digitaltwin) 或 [服务 sdk](libraries-sdks.md)之一。

撰写本文时，数字克隆 API 版本是 `2020-09-30` 。

## <a name="update-a-digital-twin"></a>更新数字孪生

IoT 即插即用设备实现 [数字孪生定义语言 v2 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl)所述的模型。 解决方案开发人员可以使用 **更新数字克隆 API** 来更新组件的状态和数字克隆的属性。

本文中用作示例的 IoT 即插即用设备使用[恒温器](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)组件实现[温度控制器模型](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)。

以下代码片段显示对 " **获取数字** 克隆" 请求进行格式化以作为 JSON 对象的响应。 若要了解有关数字输出格式的详细信息，请参阅 [了解 IoT 即插即用数字孪生](./concepts-digital-twin.md#digital-twin-json-format)：

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 20.4,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 20.4,
                "desiredVersion": 4,
                "ackVersion": 4,
                "ackCode": 200,
                "ackDescription": "Successfully executed patch",
                "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
            },
            "maxTempSinceLastReboot": {
                "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
            }
        }
    },
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

数字孪生允许使用 [JSON 修补程序](http://jsonpatch.com/)更新整个组件或属性。

例如，可以按如下所示更新 `targetTemperature` 属性：

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

上一个更新在相应的组件级别中设置属性的所需值 `$metadata` ，如以下代码片段所示。 IoT 中心更新属性的所需版本：

```json
"thermostat1": {
    "targetTemperature": 20.4,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.4,
            "desiredVersion": 5,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        }
    }
}
```

### <a name="add-replace-or-remove-a-component"></a>添加、替换或删除组件

组件级别操作在值中需要空对象 `$metadata` 标记。

"添加或替换组件" 操作设置所提供的所有属性的所需值。 它还清除了更新未提供的任何可写属性的所需值。

删除组件将清除所有可写属性的所需值。 设备最终会同步此删除操作，并停止报告各个属性。 然后，将从数字克隆中删除该组件。

下面的 JSON 修补程序示例演示如何添加、替换或删除组件：

```json
[
    {
        "op": "add",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "anotherWritableProperty": 42,
            "$metadata": {}
        }
    },
    {
        "op": "replace",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "$metadata": {}
        }
    },
    {
        "op": "remove",
        "path": "/thermostat2"
    }
]
```

### <a name="add-replace-or-remove-a-property"></a>添加、替换或删除属性

添加或替换操作设置属性的所需值。 设备可同步状态，并报告值的更新以及 `ack` 代码、版本和说明。

如果设置了属性，则删除属性将清除其所需的值。 然后，设备可以停止报告此属性，并将其从组件中删除。 如果此属性是组件中的最后一个属性，则也会删除该组件。

下面的 JSON 修补程序示例演示如何在组件中添加、替换或删除属性：

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    },
    {
        "op": "replace",
        "path": "/thermostat1/anotherWritableProperty",
        "value": 42
    },
    {
        "op": "remove",
        "path": "/thermostat2/targetTemperature",
    }
]
```

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>用于设置数字克隆属性的所需值的规则

**名称**

组件或属性的名称必须是有效的 DTDL v2 name。

允许的字符为 a-z、a-z、0-9 (而不是第一个字符) ，而下划线 (不是作为第一个或最后一个字符) 。

名称长度为1-64 个字符。

**属性值**

该值必须是有效的 [DTDL V2 属性](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)。

支持所有基元类型。 在复杂类型中，支持枚举、映射和对象。 若要了解详细信息，请参阅 [DTDL V2 架构](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas)。

属性不支持数组或具有数组的任何复杂架构。

对于复杂对象，支持最大深度为五个级别。

复杂对象内的所有字段名称都应该是有效的 DTDL v2 名称。

所有映射键都应该是有效的 DTDL v2 名称。

## <a name="troubleshoot-update-digital-twin-api-errors"></a>更新数字克隆 API 错误疑难解答

数字克隆 API 引发以下一般错误消息：

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

如果看到此错误，请确保更新修补程序遵循 [设置数字克隆属性的所需值的规则](#rules-for-setting-the-desired-value-of-a-digital-twin-property)

更新组件时，请确保设置 [空对象 $metadata 标记](#add-replace-or-remove-a-component) 。

如果设备报告的值不符合 [IoT 即插即用约定](./concepts-convention.md#writable-properties)，则更新可能会失败。

## <a name="next-steps"></a>后续步骤

现在，你已了解数字孪生，下面是一些其他资源：

- [与解决方案中的设备交互](quickstart-service-node.md)
- [IoT 数字克隆 REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT 资源管理器](howto-use-iot-explorer.md)