---
title: 如何使用 Azure IoT Central 解决方案中的属性
description: 如何在 Azure 中使用只读属性和可写属性 IoT Central 解决方案
author: v-krghan
ms.author: v-krghan
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: fa9b07d80c34ec26ca920fe147ada8f8ef7f2fd7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91341648"
---
# <a name="how-to-use-properties-in-an-azure-iot-central-solution"></a>如何使用 Azure IoT Central 解决方案中的属性

本文介绍如何使用 Azure IoT Central 应用程序的设备模板中定义的设备属性。

属性表示时间点值。 例如，设备可使用属性来报告它尝试达到的目标温度。 属性还允许您在设备和 IoT Central 应用程序之间同步状态。  可以从 IoT Central 设置可写属性。

你还可以在 IoT Central 应用程序中定义云属性。 云属性值永远不会与设备交换，并超出了本文的范围。

## <a name="define-your-properties"></a>定义属性

属性是表示设备状态的数据字段。 使用属性来表示设备的持久状态，例如设备的状态。 属性还可以表示设备的基本设备属性，例如设备的软件版本。 你可以将属性声明为只读或可写。

以下屏幕截图显示 Azure IoT Central 应用程序中的属性定义

![定义属性](./media/howto-use-properties/property-definition.png)

下表显示了属性功能的配置设置：

| 字段           | 说明                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 显示名称    | 仪表板和窗体上使用的属性值的显示名称。                                                                                                                                                              |
| 名称            | 属性的名称。 IoT Central 将根据显示名称生成此字段的值，但你可根据需要选择自己的值。 此字段必须为字母数字。                                                 |
| 功能类型 | 属性。                                                                                                                                                                                                                          |
| 语义类型   | 属性的语义类型，如温度、状态或事件。 选择的语义类型将决定以下哪些字段可用。                                                                       |
| 架构          | 属性数据类型，如 double、string 或 vector。 可用的选项取决于语义类型。 架构不可用于事件和状态语义类型。                                               |
| 可写       | 如果属性不是可写，则设备可向 IoT Central 报告属性值。 如果属性是可写的，则该设备可以向 IoT Central 报告属性值，而 IoT Central 可将属性更新发送到设备。 |
| 严重性        | 仅适用于事件语义类型。 严重性为“错误”、“信息”或“警告”************。                                                                                                                         |
| 状态值    | 仅适用于状态语义类型。 定义可能的状态值，其中每个状态值都具有显示名称、名称、枚举类型和值。                                                                                   |
| 单位            | 属性值的单位，如**mph**、 **%** 或** &deg; C**。                                                                                                                                                              |
| 显示单位    | 仪表板和窗体上使用的显示单位。                                                                                                                                                                                    |
| 注释         | 有关属性功能的任何注释。                                                                                                                                                                                        |
| 说明     | 属性功能的说明。                                                                                                                                                                                          |

属性还可以在设备模板中的接口中定义，如下所示：

``` json
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
 "@type": "Property",
 "displayName": "Date ",
 "description": "The date on which the device is currently operating",
 "name": "date",
 "writable": true,
 "schema": "date"
},
{ 
 "@type": "Property",
 "displayName": "Location",
 "description": "The current location of the device",
 "name": "location",
 "writable": true,
 "schema": "geopoint"
},
{
 "@type": "Property",
 "displayName": "Vector Level",
 "description": "The Vector level of the device",
 "name": "vector",
 "writable": true,
 "schema": "vector"
}
```

此示例显示了五个属性，这些属性可与 UI 中的属性定义相关，如下所示：

* `@type` 指定功能类型： `Property`
* `name` 属性值的。
* `schema` 指定属性的数据类型。 此值可以是基元类型，如 double、integer、boolean 或 string。 还支持复杂的对象类型、数组和映射。
* `writable` 默认情况下，属性是只读的。 可以使用此字段将属性标记为可写

可选字段（如显示名称和说明）使你可以向界面和功能添加更多详细信息。

当您创建属性时，可以指定复杂 **架构** 类型，如对象、枚举等。

![添加功能](./media/howto-use-properties/property.png)

如果选择复杂的架构（如 **对象**），则还需要定义该对象。

![定义对象](./media/howto-use-properties/object.png)

下面的代码演示对象属性类型的定义。 此对象包含两个字段，其类型为 string 和 integer：

``` json
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

## <a name="implement-read-only-properties"></a>实现只读属性

默认情况下，属性是只读的。 只读属性意味着设备将属性值更新报告给你的 IoT Central 应用程序。 IoT Central 应用程序无法设置只读属性的值。

IoT Central 使用设备孪生来同步设备与 IoT Central 应用程序之间的属性值。 设备属性值使用设备孪生报告属性。 有关详细信息，请参阅 [设备孪生](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins)

来自设备功能模型的以下代码片段显示了只读属性类型的定义：

``` json
{
  "@type": "Property",
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

设备将只读属性发送到 IoT Central。 属性以 JSON 负载形式发送，有关详细信息，请参阅 [有效负载](./concepts-telemetry-properties-commands.md)。

可以使用 Azure IoT 设备 SDK 将属性更新发送到 IoT Central 应用程序。

通过使用以下功能，可以将设备克隆属性发送到 Azure IoT Central 应用程序：

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

本文使用 Node.js 为简单起见，有关设备应用程序示例的完整信息，请参阅 [创建客户端应用程序并将其连接到 azure IoT Central 应用程序 ( # A1) ](tutorial-connect-device-nodejs.md) ，并 [创建客户端应用程序并将其连接到 azure IoT Central 应用程序 (Python) ](tutorial-connect-device-python.md) 教程。

Azure IoT Central 应用程序中的以下视图显示属性，可以看到视图自动使设备型号属性成为 _只读设备属性_。

![只读属性的视图](./media/howto-use-properties/read-only.png)

## <a name="implement-writable-properties"></a>实现可写属性

可写属性由运算符在窗体上的 IoT Central 应用程序中设置。 IoT Central 将属性发送到设备。 IoT Central 需要设备的确认。 

来自设备功能模型的以下代码片段显示了可写属性类型的定义：

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

设备客户端应将类似于以下示例的 JSON 有效负载发送到设备克隆中的报告属性：

``` json
{ "Brightness Level": 2 }
```

若要定义和处理设备响应的可写属性，可以使用以下代码。

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

响应消息应包含 `ac` 和 `av` 字段。 `ad` 字段为可选。 有关示例，请参阅以下代码片段。

* `ac` 使用下表中的值的数值字段：

* `av` 发送到设备的版本号。

* `ad` 是一个选项字符串说明。

| 值 | Label | 说明 |
| ----- | ----- | ----------- |
| `'ac': 200` | 已完成 | 属性更改操作已成功完成。 |
| `'ac': 202`  或 `'ac': 201` | 挂起 | 属性更改操作已挂起或正在进行 |
| `'ac': 4xx` | 错误 | 请求的属性更改无效或出现错误 |
| `'ac': 5xx` | 错误 | 设备在处理请求的更改时遇到意外错误。 |


有关详细信息，请参阅 [设备孪生](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins)。

当操作员在 IoT Central 应用程序中设置可写属性时，该应用程序会使用设备孪生所需属性将值发送到设备。 然后，设备使用设备孪生报告属性做出响应。 当 IoT Central 收到报告的属性值时，它会更新状态为 "已 **接受**" 的属性视图。

以下视图显示可写属性。 输入值并 **保存**时，初始状态为 " **挂起**"，当设备接受更改时，状态将更改为 "已 **接受**"。

![挂起状态](./media/howto-use-properties/status-pending.png)

![接受的属性](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在 Azure IoT Central 应用程序中使用属性，但你可以查看 [有效负载](concepts-telemetry-properties-commands.md) ，并 [创建客户端应用程序并将其连接到 azure IoT Central 应用程序 ( # A0) ](tutorial-connect-device-nodejs.md)。
