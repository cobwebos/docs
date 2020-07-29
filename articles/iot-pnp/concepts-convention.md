---
title: IoT 即插即用约定 |Microsoft Docs
description: 约定 IoT 即插即用的说明在发送遥测和属性时需要设备使用，并处理命令和属性更新。
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: b06edb83e85ef6d56cf8bf8f91551fe99535eba1
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352135"
---
# <a name="iot-plug-and-play-conventions"></a>IoT 即插即用约定

IoT 即插即用预览版设备在与 IoT 中心交换消息时应遵循一组约定。 IoT 即插即用预览版设备使用 MQTT 协议与 IoT 中心通信。

使用[数字孪生定义语言 v2 （DTDL）](https://github.com/Azure/opendigitaltwins-dtdl) _模型_描述 IoT 即插即用设备实现的遥测、属性和命令。 本文中引用的模型分为两种类型：

- **无组件**-没有组件的模型。 该模型在主接口的内容部分中将遥测、属性和命令声明为顶级属性。
- **多个组件**-由两个或多个接口组成的模型。 具有遥测、属性和命令的主接口。 将一个或多个接口声明为包含附加遥测、属性和命令的组件。

有关详细信息，请参阅[IoT 即插即用模型中的组件](concepts-components.md)。

## <a name="model-discovery"></a>模型发现

若要公布它实现的模型，IoT 即插即用设备通过添加到字段，在 MQTT 连接数据包中包含模型 ID `model-id` `USERNAME` 。

若要发现设备实现的模型，服务可以从以下各内容获取模型 ID：

- 设备克隆 `modelId` 字段。
- 数字双子 `$metadata.$model` 字段。
- 数字克隆更改通知。

## <a name="telemetry"></a>遥测

从无组件设备发送的遥测无需任何额外的元数据。 系统添加 `dt-dataschema` 属性。

从多个组件设备发送的遥测必须 `$.sub` 作为消息属性添加。 系统添加 `dt-subject` 和 `dt-dataschema` 属性。

## <a name="read-only-properties"></a>只读属性

### <a name="sample-no-component-read-only-property"></a>示例无组件只读属性

设备可以发送遵循 DTDL v2 规则的任何有效 JSON。

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **示例有效负载**

      ```json
      "reported" :
      {
        "temperature" : 21.3
      }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-read-only-property"></a>示例多个组件只读属性

设备必须添加 `{"__t": "c"}` 标记以指示元素引用组件。

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **报告的属性**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "temperature": 21.3
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="writable-properties"></a>可写属性

设备应通过发送报告的属性来确认它已接收到属性。 报告的属性应包括：

- `value`-设备接收的值。
- `ac`-使用 HTTP 状态代码的确认代码。
- `av`-引用所 `$version` 需属性的的确认版本。
- `ad`-可选确认说明。

### <a name="sample-no-component-writable-property"></a>无组件可写属性示例

设备可以发送遵循 DTDL v2 规则的任何有效 JSON：

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **所需属性**

      ```json
      "desired" :
      {
        "targetTemperature" : 21.3
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **报告的属性**

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
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-writable-property"></a>示例多组件可写属性

设备必须添加 `{"__t": "c"}` 标记以指示元素引用组件。

标记只发送到组件级别的更新，因此设备不得检查此标志。

设备应通过发送报告的属性来确认它已接收到属性：

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **所需属性**

      ```json
      "desired": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": 21.3
        }
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **报告的属性**

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
   :::column-end:::
:::row-end:::

## <a name="commands"></a>命令

没有组件接口使用不带前缀的命令名。

在设备上，多个组件接口使用命令名称，格式如下： `componentName*commandName` 。

## <a name="next-steps"></a>后续步骤

现在，你已了解 IoT 即插即用约定，下面是一些其他资源：

- [数字孪生定义语言（DTDL）](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 设备 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [模型组件](./concepts-components.md)
