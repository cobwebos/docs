---
title: 了解 IoT 即插即用模型中的组件 |Microsoft Docs
description: 了解使用不使用组件的组件和模型的 IoT 即插即用 DTDL 模型之间的差异。
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4c41edc477460e6d239688aafe6d7219bed36cd4
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352205"
---
# <a name="iot-plug-and-play-components-in-models"></a>模型中的 IoT 即插即用组件

在 IoT 即插即用约定中，如果设备连接到 IoT 中心时，它会显示其数字孪生定义语言（DTDL）型号，则该设备为 IoT 即插即用设备。

以下代码片段演示了一些示例模型 Id：

```json
 "@id": "dtmi:com:example:TemperatureController;1"
 "@id": "dtmi:com:example:Thermostat;1",
```

## <a name="no-components"></a>无组件

简单的模型不使用嵌入或级联的组件。 其中包括用于定义遥测、属性和命令的标头信息和内容部分。

下面的示例演示了不使用组件的简单模型的一部分：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

尽管模型没有显式定义组件，但其行为就像具有所有遥测、属性和命令定义的单个组件。

以下屏幕截图显示了如何在 Azure IoT 资源管理器工具中显示模型：

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Azure IoT 浏览器中的默认组件":::

模型 ID 存储在设备的克隆属性中，如以下屏幕截图所示：

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="数字克隆属性中的模型 ID":::

对于包含一组遥测、属性和命令的设备，没有组件的 DTDL 模型是一种有用的简化。 不使用组件的模型使你可以轻松地将现有设备迁移为 IoT 即插即用设备-你可以创建一个 DTDL 模型来描述你的实际设备，而无需定义任何组件。

## <a name="multiple-components"></a>多个组件

组件允许您将模型接口生成为其他接口的程序集。

例如，[恒温器](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)接口定义为模型。 定义[温度控制器模型](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)时，可以将此接口作为一个或多个组件合并。 在下面的示例中，这些组件称为 `thermostat1` 和 `thermostat2` 。

对于包含多个组件的 DTDL 模型，有两个或两个以上的组件部分。 每节均 `@type` 设置为 `Component` ，并显式引用架构，如以下代码片段所示：

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
... 
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
...
```

此模型包含三个组件（在 "内容" 部分中定义）-两个 `Thermostat` 组件和一个 `DeviceInformation` 组件。 还有一个默认的根组件。

## <a name="next-steps"></a>后续步骤

现在，你已了解模型组件，下面是一些其他资源：

- [数字孪生定义语言 v2 （DTDL）](https://github.com/Azure/opendigitaltwins-dtdl)
- [模型存储库](./concepts-model-repository.md)