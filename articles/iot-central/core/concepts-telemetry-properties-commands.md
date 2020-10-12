---
title: Azure IoT Central 中的遥测、属性和命令负载 |Microsoft Docs
description: Azure IoT Central 设备模板使你可以指定设备的遥测、属性和命令必须实现。 了解设备可与 IoT Central 交换的数据的格式。
author: dominicbetts
ms.author: dobett
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 554079ddec3332ced2817d18ea55ce1260d68817
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87291618"
---
# <a name="telemetry-property-and-command-payloads"></a>遥测、属性和命令有效负载

本文适用于设备开发人员。

Azure IoT Central 中的设备模板是用于定义以下内容的蓝图：

* 设备发送到 IoT Central 的遥测。
* 设备与 IoT Central 进行同步的属性。
* IoT Central 在设备上调用的命令。

本文介绍了对于设备开发人员而言，为设备模板中定义的遥测、属性和命令发送和接收的 JSON 有效负载。

本文不介绍每种可能的遥测、属性和命令有效负载类型，但这些示例演示了所有密钥类型。

每个示例显示了来自设备功能模型的代码片段 (DCM) ，用于定义类型和示例 JSON 有效负载，以说明设备应该如何与 IoT Central 应用程序交互。

> [!NOTE]
> IoT Central 接受任何有效的 JSON，但它仅可用于可视化效果（如果它与 DCM 中的定义匹配）。 可以导出与定义不匹配的数据，请参阅 [将 IoT 数据导出到 Azure 中的目标](howto-export-data.md)。

定义 DCM 的 JSON 文件使用 [数字克隆定义语言 (DTDL) V1](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/dtdlv1.md)。 此规范包括属性格式的定义 `@id` 。

有关显示所使用的部分负载的示例设备代码，请参阅 [创建客户端应用程序并将其连接到 azure IoT Central 应用程序 ( # A0) ](tutorial-connect-device-nodejs.md) ，并 [创建客户端应用程序并将其连接到 azure IoT Central 应用程序 (Python) ](tutorial-connect-device-python.md) 教程。

## <a name="view-raw-data"></a>查看原始数据

IoT Central 允许查看设备发送到应用程序的原始数据。 此视图可用于排查从设备发送的有效负载的问题。 查看设备正在发送的原始数据：

1. 从 " **设备** " 页面导航到该设备。

1. 选择 " **原始数据** " 选项卡：

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="“原始数据”视图":::

    在此视图中，你可以选择要显示的列，并设置要查看的时间范围。 “未建模数据”列显示设备中与设备模板中的任何属性或遥测定义不匹配的数据。

## <a name="telemetry"></a>遥测

### <a name="primitive-types"></a>基元类型

本部分说明设备流式传输到 IoT Central 应用程序的基元遥测类型的示例。

DCM 中的以下代码片段显示了 `boolean` 遥测类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "BooleanTelemetry"
  },
  "name": "BooleanTelemetry",
  "schema": "boolean"
}
```

设备客户端应发送类似于以下示例的 JSON 的遥测数据：

```json
{ "BooleanTelemetry": true }
```

DCM 中的以下代码片段显示了 `string` 遥测类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "StringTelemetry"
  },
  "name": "StringTelemetry",
  "schema": "string"
}
```

设备客户端应发送类似于以下示例的 JSON 的遥测数据：

```json
{ "StringTelemetry": "A string value - could be a URL" }
```

DCM 中的以下代码片段显示了 `integer` 遥测类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "IntegerTelemetry"
  },
  "name": "IntegerTelemetry",
  "schema": "integer"
}

```

设备客户端应发送类似于以下示例的 JSON 的遥测数据：

```json
{ "IntegerTelemetry": 23 }
```

DCM 中的以下代码片段显示了 `double` 遥测类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DoubleTelemetry"
  },
  "name": "DoubleTelemetry",
  "schema": "double"
}
```

设备客户端应发送类似于以下示例的 JSON 的遥测数据：

```json
{ "DoubleTelemetry": 56.78 }
```

DCM 中的以下代码片段显示了 `dateTime` 遥测类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

设备客户端应以 JSON 形式发送遥测数据，如以下示例所示- `DateTime` 类型必须符合 ISO 8061：

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

DCM 中的以下代码片段显示了 `duration` 遥测类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

设备客户端应发送类似于以下示例的 JSON 的遥测-持续时间必须符合 ISO 8601 持续时间：

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>复杂类型

本部分说明设备流式传输到 IoT Central 应用程序的复杂遥测类型。

DCM 中的以下代码片段显示了 `geopoint` 遥测类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "GeopointTelemetry"
  },
  "name": "GeopointTelemetry",
  "schema": "geopoint"
}
```

设备客户端应发送类似于以下示例的 JSON 的遥测数据。 IoT Central 将值显示为地图上的 pin：

```json
{
  "GeopointTelemetry": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

DCM 中的以下代码片段显示了 `Enum` 遥测类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

设备客户端应发送类似于以下示例的 JSON 的遥测数据。 可能的值为 `0` 、 `1` 和， `2` 它们在 IoT Central 中显示为、和 `Item1` `Item2` `Item3` ：

```json
{ "EnumTelemetry": 1 }
```

DCM 中的以下代码片段显示了 `Object` 遥测类型的定义。 此对象具有三个类型为 `dateTime` 、 `integer` 和的字段 `Enum` ：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@id": "<element id>",
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item3"
              },
              "enumValue": 2,
              "name": "Item3"
            }
          ]
        }
      }
    ]
  }
}
```

设备客户端应发送类似于以下示例的 JSON 的遥测数据。 `DateTime` 类型必须符合 ISO 8061。 的可能值 `Property3` 为 `0` 、 `1` 和，它们在 IoT Central 中显示为 `Item1` 、和 `Item2` `Item3` ：

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

DCM 中的以下代码片段显示了 `vector` 遥测类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "VectorTelemetry"
  },
  "name": "VectorTelemetry",
  "schema": "vector"
}
```

设备客户端应发送类似于以下示例的 JSON 的遥测数据：

```json
{
  "VectorTelemetry": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="event-and-state-types"></a>事件和状态类型

本部分演示设备发送到 IoT Central 应用程序的遥测事件和状态的示例。

DCM 中的以下代码片段显示事件类型的定义 `integer` ：

```json
{
  "@id": "<element id>",
  "@type": [
    "Telemetry",
    "SemanticType/Event"
  ],
  "displayName": {
    "en": "IntegerEvent"
  },
  "name": "IntegerEvent",
  "schema": "integer"
}
```

设备客户端应将事件数据作为 JSON 发送，如以下示例所示：

```json
{ "IntegerEvent": 74 }
```

DCM 中的以下代码片段显示了 `integer` 状态类型的定义：

```json
{
  "@id": "<element id>",
  "@type": [
    "Telemetry",
    "SemanticType/State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level3"
        },
        "enumValue": 3,
        "name": "Level3"
      }
    ]
  }
}
```

设备客户端应发送类似于以下示例的 JSON 状态。 可能的整数状态值为 `1` 、 `2` 或 `3` ：

```json
{ "IntegerState": 2 }
```

## <a name="properties"></a>属性

> [!NOTE]
> 属性的负载格式适用于在07/14/2020 或之后创建的应用程序。

### <a name="primitive-types"></a>基元类型

本部分演示设备发送到 IoT Central 应用程序的基元属性类型的示例。

DCM 中的以下代码片段显示了 `boolean` 属性类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean"
}
```

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性：

```json
{ "BooleanProperty": false }
```

DCM 中的以下代码片段显示了 `boolean` 属性类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long"
}
```

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性：

```json
{ "LongProperty": 439 }
```

DCM 中的以下代码片段显示了 `date` 属性类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date"
}
```

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性。 `Date` 类型必须符合 ISO 8061：

```json
{ "DateProperty": "2020-05-17" }
```

DCM 中的以下代码片段显示了 `duration` 属性类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration"
}
```

设备客户端应发送 JSON 有效负载，此有效负载类似于以下示例，因为设备克隆期间的报告属性必须符合 ISO 8601 持续时间：

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

DCM 中的以下代码片段显示了 `float` 属性类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float"
}
```

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性：

```json
{ "FloatProperty": 1.9 }
```

DCM 中的以下代码片段显示了 `string` 属性类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string"
}
```

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性：

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>复杂类型

本部分演示设备发送到 IoT Central 应用程序的复杂属性类型的示例。

DCM 中的以下代码片段显示了 `geopoint` 属性类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint"
}
```

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性：

```json
{
  "GeopointProperty": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

DCM 中的以下代码片段显示了 `Enum` 属性类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性。 可能的值为 `0` 、 `1` 和，它们在 IoT Central 中显示为 `Item1` 、和 `Item2` `Item3` ：

```json
{ "EnumProperty": 1 }
```

DCM 中的以下代码片段显示了 `Object` 属性类型的定义。 此对象包含两个具有类型 `string` 和的字段 `integer` ：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性：

```json
{
  "ObjectProperty": {
    "Field1": 37,
    "Field2": "A string value"
  }
}
```

DCM 中的以下代码片段显示了 `vector` 属性类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector"
}
```

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性：

```json
{
  "VectorProperty": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="writeable-property-types"></a>可写的属性类型

本部分介绍设备从 IoT Central 应用程序接收的可写属性类型的示例。

IoT Central 需要从设备到可写属性更新的响应。 响应消息应包含 `ac` 和 `av` 字段。 `ad` 字段为可选。 有关示例，请参阅以下代码片段。

`ac` 使用下表中的值的数值字段：

| 值 | Label | 说明 |
| ----- | ----- | ----------- |
| `'ac': 200` | 已完成 | 属性更改操作已成功完成。 |
| `'ac': 202`  或 `'ac': 201` | 挂起 | 属性更改操作已挂起或正在进行 |
| `'ac': 4xx` | 错误 | 请求的属性更改无效或出现错误 |
| `'ac': 5xx` | 错误 | 设备在处理请求的更改时遇到意外错误。 |

`av` 发送到设备的版本号。

`ad` 是一个选项字符串说明。

DCM 中的以下代码片段显示可写 `string` 属性类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "StringPropertyWritable"
  },
  "name": "StringPropertyWritable",
  "writable": true,
  "schema": "string"
}
```

设备从 IoT Central 接收以下有效负载：

```json
{  
  "StringPropertyWritable": "A string from IoT Central", "$version": 7
}
```

在处理更新后，设备应将以下 JSON 有效负载发送到 IoT Central。 此消息包括从 IoT Central 收到的原始更新的版本号。 IoT Central 收到此消息时，它会将属性标记为在 UI 中 **同步** ：

```json
{
  "StringPropertyWritable": {
    "value": "A string from IoT Central",
    "ac": 200,
    "ad": "completed",
    "av": 7
  }
}
```

DCM 中的以下代码片段显示可写 `Enum` 属性类型的定义：

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

设备从 IoT Central 接收以下有效负载：

```json
{  
  "EnumPropertyWritable":  1 , "$version": 10
}
```

在处理更新后，设备应将以下 JSON 有效负载发送到 IoT Central。 此消息包括从 IoT Central 收到的原始更新的版本号。 IoT Central 收到此消息时，它会将属性标记为在 UI 中 **同步** ：

```json
{
  "EnumPropertyWritable": {
    "value": 1,
    "ac": 200,
    "ad": "completed",
    "av": 10
  }
}
```

## <a name="commands"></a>命令

### <a name="synchronous-command-types"></a>同步命令类型

DCM 中的以下代码片段显示了不具有参数并且不希望设备返回任何内容的同步命令的定义：

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "displayName": {
    "en": "SynchronousCommandBasic"
  },
  "name": "SynchronousCommandBasic"
}
```

设备在请求中接收空负载，并应在响应中返回空负载，并使用 `200` HTTP 响应代码来指示成功。

DCM 中的以下代码片段显示了具有整数参数并且需要设备返回整数值的同步命令的定义：

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "SynchronousCommandSimple"
  },
  "name": "SynchronousCommandSimple"
}
```

设备接收一个整数值作为请求负载。 设备应返回整数值作为响应负载，并使用 `200` HTTP 响应代码来指示成功。

DCM 中的以下代码片段显示了具有 object 参数并且需要设备返回对象的同步命令的定义。 在此示例中，两个对象都具有整数和字符串字段：

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": {
      "@id": "<element id>",
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": {
      "@id": "<element id>",
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "displayName": {
    "en": "SynchronousCommandComplex"
  },
  "name": "SynchronousCommandComplex"
}
```

以下代码片段显示了发送到设备的示例请求负载：

```json
{ "Field1": 56, "Field2": "A string value" }
```

以下代码片段显示了从设备发送的示例响应负载。 使用 `200` HTTP 响应代码来指示成功：

```json
{ "Field1": 87, "Field2": "Another string value" }
```

### <a name="asynchronous-command-types"></a>异步命令类型

DCM 中的以下代码片段显示了异步命令的定义。 命令有一个整数参数，并期望设备返回整数值：

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "asynchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "AsynchronousCommandSimple"
  },
  "name": "AsynchronousCommandSimple"
}
```

设备接收一个整数值作为请求负载。 设备应返回一个空的响应有效负载，其中包含 `202` HTTP 响应代码，以指示设备已接受异步处理请求。

设备处理完请求后，应将属性发送到 IoT Central，如以下示例所示。 属性名称必须与命令名称相同：

```json
{
  "AsynchronousCommandSimple": {
    "value": 87
  }
}
```

## <a name="next-steps"></a>后续步骤

作为一个设备开发人员，现在你已了解设备模板，接下来是了解有关如何将设备注册到 [Azure IoT Central](./concepts-get-connected.md) 的详细信息，以了解有关如何使用 IoT Central 以及 IoT Central 如何保护设备连接的详细信息。
