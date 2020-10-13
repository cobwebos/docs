---
title: 什么是 Azure 中的设备模板 IoT Central |Microsoft Docs
description: Azure IoT Central 设备模板使你能够指定连接到应用程序的设备的行为。 设备模板指定设备必须实现的遥测、属性和命令。 设备模板还在 IoT Central （例如，操作员使用的窗体和仪表板）中定义设备的 UI。
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 75317b5c6af2d0ce89d2db32f4343d9cc73a1a81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91813162"
---
# <a name="what-are-device-templates"></a>什么是设备模板？

_本文适用于设备开发人员和解决方案构建者。_

Azure IoT Central 中的设备模板是一个蓝图，用于定义连接到应用程序的设备类型的特征和行为。 例如，设备模板定义设备发送的遥测，以便 IoT Central 可以创建使用正确单位和数据类型的可视化效果。

解决方案生成器将设备模板添加到 IoT Central 的应用程序。 设备开发人员写入实现设备模板中定义的行为的设备代码。

设备模板包括以下部分：

- _一种设备功能模型 (DCM) _。 设备模板的此部分定义设备与应用程序交互的方式。 设备开发人员实现 DCM 中定义的行为。
    - _接口_。 DCM 包含一个或多个接口，用于定义设备必须实现的遥测、属性和命令。
- _云属性_。 设备模板的此部分使解决方案开发人员可以指定任何要存储的设备元数据。 云属性永远不会与设备同步，而只存在于应用程序中。 云属性不影响设备开发人员为实现 DCM 而编写的代码。
- _自定义_。 设备模板的此部分使解决方案开发人员可以覆盖 DCM 中的一些定义。 如果解决方案开发人员想要优化应用程序处理值的方式（例如更改属性的显示名称或用于显示遥测值的颜色），则自定义项很有用。 自定义不会影响设备开发人员为实现 DCM 而编写的代码。
- _视图_。 设备模板的此部分使解决方案开发人员可以定义可视化，以查看设备中的数据，以及用于管理和控制设备的窗体。 视图使用 DCM、云属性和自定义项。 视图不影响设备开发人员为实现 DCM 而编写的代码。

## <a name="device-capability-models"></a>设备功能模型

DCM 定义设备与 IoT Central 应用程序交互的方式。 设备开发人员必须确保设备实现 DCM 中定义的行为，以便 IoT Central 能够监视和管理设备。 DCM 由一个或多个 _接口_组成，每个接口都可以定义 _遥测_ 类型、 _设备属性_和 _命令_的集合。 解决方案开发人员可以将定义 DCM 的 JSON 文件导入设备模板，或使用 IoT Central 中的 web UI 来创建或编辑 DCM。 使用 Web UI 进行 DCM 的更改需要对 [设备模板进行版本控制](./howto-version-device-template.md)。

解决方案开发人员还可以导出包含 DCM 的 JSON 文件。 设备开发人员可以使用此 JSON 文档来了解设备应该如何与 IoT Central 的应用程序进行通信。

定义 DCM 的 JSON 文件使用 [数字克隆定义语言 (DTDL) V1](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)。 IoT Central 要求 JSON 文件包含具有内联定义的接口的 DCM，而不是在单独的文件中。

典型的 IoT 设备由以下内容组成：

- 自定义部件，这是使您的设备独一无二的东西。
- 标准部件，是所有设备通用的部分。

这些部分称为 DCM 中的 _接口_ 。 接口定义设备实现的每个部件的详细信息。 接口可在 DCMs 之间重复使用。

下面的示例演示具有两个接口的环境传感器设备的设备功能模型的轮廓：

```json
{
  "@id": "urn:contoso:sensor_device:1",
  "@type": "CapabilityModel",
  "displayName": "Environment Sensor Capability Model",
  "implements": [
    {
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": "Device Information",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    },
    {
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:1",
        "@type": "Interface",
        "displayName": "Environmental Sensor",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
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

接口具有一些必需字段：

- `@id`：唯一 ID，采用简单统一资源名称的形式。
- `@type`：声明此对象是接口。
- `@context`：指定用于接口的 DTDL 版本。
- `contents`：列出组成设备的属性、遥测和命令。

有一些可选字段可用于向功能模型中添加更多详细信息，例如显示名称和说明。

## <a name="interfaces"></a>界面

DTDL 可让你描述设备的功能。 相关功能分组为接口。 接口描述设备的一部分实现的属性、遥测和命令：

- `Properties`. 属性是表示设备状态的数据字段。 使用属性来表示设备的持久状态，如冷却剂泵的关闭状态。 属性还可以表示基本设备属性，例如设备的固件版本。 你可以将属性声明为只读或可写。 只有设备可以更新只读属性的值。 操作员可以设置要发送到设备的可写属性的值。
- `Telemetry`. 遥测字段表示传感器的度量。 如果设备采用传感器度量，应发送包含传感器数据的遥测事件。
- `Commands`. 命令表示设备的用户可以在设备上执行的方法。 例如，使用 reset 命令或命令来打开或关闭风扇。

以下示例显示了环境传感器接口定义：

```json
{
  "@type": "Property",
  "displayName": "Device State",
  "description": "The state of the device. Two states online/offline are available.",
  "name": "state",
  "schema": "boolean"
},
{
  "@type": "Property",
  "displayName": "Customer Name",
  "description": "The name of the customer currently operating the device.",
  "name": "name",
  "schema": "string",
  "writable": true
},
{
  "@type": [
    "Telemetry",
    "SemanticType/Temperature"
  ],
  "description": "Current temperature on the device",
  "displayName": "Temperature",
  "name": "temp",
  "schema": "double",
  "unit": "Units/Temperature/fahrenheit"
},
{
  "@type": "Command",
  "name": "turnon",
  "comment": "This Commands will turn-on the LED light on the device.",
  "commandType": "synchronous"
},
{
  "@type": "Command",
  "name": "turnoff",
  "comment": "This Commands will turn-off the LED light on the device.",
  "commandType": "synchronous"
}
```

此示例显示了两个属性 (一个只读和一个可写) 、一个遥测类型和两个命令。 最小字段说明具有：

- `@type` 指定功能类型： `Telemetry` 、 `Property` 或 `Command` 。  在某些情况下，该类型包括一个语义类型，以使 IoT Central 可以做出有关如何处理该值的一些假设。
- `name` 对于遥测值。
- `schema` 指定遥测的数据类型或属性。 此值可以是基元类型，如 double、integer、boolean 或 string。 还支持复杂的对象类型、数组和映射。
- `commandType` 指定应如何处理命令。

可选字段（如显示名称和说明）使你可以向界面和功能添加更多详细信息。

## <a name="properties"></a>属性

默认情况下，属性是只读的。 只读属性意味着设备将属性值更新报告给你的 IoT Central 应用程序。 IoT Central 应用程序无法设置只读属性的值。

还可以在接口上将属性标记为可写。 设备可以从你的 IoT Central 应用程序中接收可写属性的更新，以及向应用程序报告属性值更新。

设备无需连接即可设置属性值。 当设备下一次连接到应用程序时，将传输更新的值。 此行为同时适用于只读属性和可写属性。

不要使用属性从设备发送遥测数据。 例如，readonly 属性（如） `temperatureSetting=80` 应意味着设备温度已设置为80，并且设备尝试进入或停留在此温度上。

对于可写属性，设备应用程序会返回所需状态状态代码、版本和说明，以指示是否已收到并应用属性值。

## <a name="telemetry"></a>遥测

IoT Central 允许你查看仪表板和图表上的遥测，并使用规则在达到阈值时触发操作。 IoT Central 使用 DCM 中的信息（如数据类型、单位和显示名称）来确定如何显示遥测值。

你可以使用 IoT Central 数据导出功能将遥测流式传输到其他目标（例如存储或事件中心）。

## <a name="commands"></a>命令

命令为同步或异步。 默认情况下，必须在30秒内执行同步命令，并且在命令到达时必须连接设备。 如果设备有时间响应或设备未连接，则该命令将失败。

为长时间运行的操作使用异步命令。 设备使用遥测消息发送进度信息。 这些进度消息具有以下标头属性：

- `iothub-command-name`：例如，命令名称 `UpdateFirmware` 。
- `iothub-command-request-id`：在服务器端生成并在初始调用中发送到设备的请求 ID。
- `iothub-interface-id`：例如，在其上定义此命令的接口的 ID `urn:example:AssetTracker:1` 。
 `iothub-interface-name`：例如，此接口的实例名称 `myAssetTracker` 。
- `iothub-command-statuscode`：例如，从设备返回的状态代码 `202` 。

## <a name="cloud-properties"></a>云属性

云属性是设备模板的一部分，但并不是 DCM 的组成部分。 云属性使解决方案开发人员可以指定要存储在 IoT Central 应用程序中的任何设备元数据。 云属性不影响设备开发人员为实现 DCM 而编写的代码。

解决方案开发人员可以将云属性添加到仪表板，并将视图添加到设备属性，使操作员能够管理连接到应用程序的设备。 解决方案开发人员还可以使用云属性作为规则定义的一部分，使阈值可编辑阈值。

## <a name="customizations"></a>自定义

自定义项是设备模板的一部分，但不是 DCM 的组成部分。 自定义使解决方案开发人员可以增强或覆盖 DCM 中的一些定义。 例如，解决方案开发人员可以更改遥测类型或属性的显示名称。 解决方案开发人员还可以使用自定义项添加验证，如字符串设备属性的最小值或最大长度。

自定义可能会影响设备开发人员为实现 DCM 而编写的代码。 例如，自定义可以设置遥测的最小和最大字符串长度或最小和最大数值。

## <a name="views"></a>视图

解决方案开发人员创建允许操作员监视和管理连接的设备的视图。 视图是设备模板的一部分，因此视图与特定设备类型相关联。 视图可以包括：

- 用于绘制遥测数据的图表。
- 用于显示只读设备属性的磁贴。
- 磁贴，使操作员可以编辑可写的设备属性。
- 用于使运算符编辑云属性的磁贴。
- 允许操作员调用命令的磁贴，包括需要有效负载的命令。
- 用于显示标签、图像或 markdown 文本的磁贴。

可以添加到视图中的遥测、属性和命令由设备模板中的 DCM、云属性和自定义项决定。

## <a name="next-steps"></a>后续步骤

作为设备开发人员，现在你已了解设备模板，接下来要做的后续步骤是阅读 [遥测、属性和命令有效负载](./concepts-telemetry-properties-commands.md) ，了解有关与 IoT Central 设备交换的数据的详细信息。

作为解决方案开发人员，建议的下一步是 [在 Azure IoT Central 应用程序中阅读定义新的 IoT 设备类型](./howto-set-up-template.md) ，以了解有关如何创建设备模板的详细信息。
