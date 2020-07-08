---
title: 解释事件数据
titleSuffix: Azure Digital Twins
description: 请参阅如何解释不同的事件类型及其不同的通知消息。
author: baanders
ms.author: baanders
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f6794559c2296b02ef61d0e280d29456904ae607
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85609293"
---
# <a name="understand-event-data"></a>了解事件数据

Azure 数字孪生中的不同事件会生成**通知**，这使解决方案后端可以在发生不同操作时了解。 然后，这些位置会[路由](concepts-route-events.md)到 Azure 数字孪生内部和外部的不同位置，这些位置可使用此信息来采取措施。

有几种类型的通知可以生成，通知消息可能看上去不同，具体取决于生成警报的事件类型。 本文提供了有关不同消息类型的详细信息，以及它们的外观。

此图显示了不同的通知类型：

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

通常，通知由两部分组成：标头和正文。 

### <a name="event-notification-headers"></a>事件通知标头

通知消息标头用键值对表示。 根据所使用的协议（MQTT、AMQP 或 HTTP），将以不同的方式序列化消息标头。 本部分讨论通知消息的常规标头信息，而不考虑所选的特定协议和序列化。

某些通知符合 CloudEvents 标准。 CloudEvents 一致性如下所示。
* 从设备发出的通知继续遵循现有的通知规范
* IoT 中心处理和发出的通知继续遵循现有的通知规范，但 IoT 中心选择支持 CloudEvents （如通过事件网格）
* 从[数字孪生](concepts-twins-graph.md)发出的包含[模型](concepts-models.md)的通知符合 CloudEvents
* Azure 数字孪生处理和发出的通知符合 CloudEvents

服务必须在所有通知上添加序列号以指示它们的顺序，或以其他方式维护其自己的排序。 

Azure 数字孪生向事件网格发出的通知将自动格式化为 CloudEvents 架构或 EventGridEvent 架构，具体取决于事件网格主题中定义的架构类型。 

标头上的扩展属性将作为负载内的事件网格架构中的属性添加。 

### <a name="event-notification-bodies"></a>事件通知正文

此处的 JSON 中描述了通知消息的正文。 根据消息正文（如 JSON、CBOR、Protobuf 等）所需的序列化，消息正文可能会以不同方式进行序列化。

正文包含的一组字段随不同的通知类型而变化。 下面是两个示例消息正文，用于大致了解和可能包括的内容。

遥测消息：

```json
{
  "specversion": "1.0",
  "id": "df5a5992-817b-4e8a-b12c-e0b18d4bf8fb",
  "type": "microsoft.iot.telemetry",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net/digitaltwins/room1",
  "data": {
    "Temperature": 10
  },
  "dataschema": "dtmi:example:com:floor4;2",
  "datacontenttype": "application/json",
  "traceparent": "00-7e3081c6d3edfb4eaf7d3244b2036baa-23d762f4d9f81741-01"
}
```

生命周期通知消息：

```json
{
  "specversion": "1.0",
  "id": "d047e992-dddc-4a5a-b0af-fa79832235f8",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net",
  "data": {
    "$dtId": "floor1",
    "$etag": "W/\"e398dbf4-8214-4483-9d52-880b61e491ec\"",
    "$metadata": {
      "$model": "dtmi:example:Floor;1"
    }
  },
  "subject": "floor1",
  "time": "2020-06-23T19:03:48.9700792Z",
  "datacontenttype": "application/json",
  "traceparent": "00-18f4e34b3e4a784aadf5913917537e7d-691a71e0a220d642-01"
}
```

## <a name="message-format-detail-for-different-event-types"></a>不同事件类型的消息格式详细信息

本部分详细介绍 IoT 中心和 Azure 数字孪生（或其他 Azure IoT 服务）发出的不同类型的通知。 你将了解触发每个通知类型的相关信息，以及每种类型的通知正文中包含的字段集。

### <a name="digital-twin-life-cycle-notifications"></a>数字克隆生命周期通知

所有[数字孪生](concepts-twins-graph.md)发出通知，无论它们是否代表[Azure 数字孪生中的 IoT 中心设备](how-to-ingest-iot-hub-data.md)。 这是因为**生命周期通知**，它们有关数字一本身。

生命周期通知将在以下情况触发：
* 创建数字克隆
* 删除数字克隆

#### <a name="properties"></a>属性

下面是生命周期通知正文中的字段。

| “属性” | “值” |
| --- | --- |
| `id` | 通知的标识符，如由服务维护的 UUID 或计数器。 `source` + `id`对于每个 distinct 事件都是唯一的。 |
| `source` | IoT 中心或 Azure 数字孪生实例的名称，例如*myhub.azure-devices.net*或*mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | 数字输出的 ID |
| `time` | 在克隆上发生操作的时间戳 |
| `traceparent` | 事件的 W3C 跟踪上下文 |

#### <a name="body-details"></a>正文详细信息

正文是受影响的数字克隆，以 JSON 格式表示。 这种情况的架构是*数字孪生资源 7.1*。

对于创建事件，负载会反映创建资源后的克隆状态，因此它应包括所有系统生成的元素，就像 `GET` 调用一样。

下面是[IoT 即插即用（PnP）](../iot-pnp/overview-iot-plug-and-play.md)设备（包含组件，无顶级属性）的正文的示例。 应该省略对设备没有意义的属性（如报告的属性）。

```json
{
  "$dtId": "device-digitaltwin-01",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

下面是数字克隆的另一个示例。 此项基于[模型](concepts-models.md)，不支持组件：

```json
{
  "$dtId": "logical-digitaltwin-01",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="digital-twin-relationship-change-notifications"></a>数字克隆关系更改通知

当创建、更新或删除数字克隆的任何关系时，将触发**关系更改通知**。 

#### <a name="properties"></a>属性

下面是边缘更改通知正文中的字段。

| “属性”    | “值” |
| --- | --- |
| `id` | 通知的标识符，如由服务维护的 UUID 或计数器。 `source` + `id`对于每个 distinct 事件都是唯一的 |
| `source` | Azure 数字孪生实例的名称，如*mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete`
|`datacontenttype`| `application/json` |
| `subject` | 关系的 ID，例如`<twinID>/relationships/<relationshipID>` |
| `time` | 对关系发生操作时的时间戳 |
| `traceparent` | 事件的 W3C 跟踪上下文 |

#### <a name="body-details"></a>正文详细信息

主体是关系的负载，也是 JSON 格式。 它与 `GET` 通过[DigitalTwins API](how-to-use-apis-sdks.md)对关系的请求使用相同的格式。 

"更新关系" 意味着关系的属性已更改。 

下面是更新关系通知以更新属性的示例：

```json
{
    "modelId": "dtmi:example:Floor;1",
    "patch": [
      {
        "value": "user3",
        "path": "/ownershipUser",
        "op": "replace"
      }
    ]
  }
```

对于 `Relationship.Delete` ，正文与 `GET` 请求相同，并在删除之前获得最新状态。

下面是创建或删除关系通知的示例：

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

### <a name="digital-twin-change-notifications"></a>数字克隆更改通知

更新数字克隆时，将触发**数字克隆更改通知**，如：
* 属性值或元数据的更改时间。
* 数字克隆或组件元数据发生更改时。 这种情况的一个示例是，更改数字克隆的模型。

#### <a name="properties"></a>属性

下面是数字克隆更改通知正文中的字段。

| “属性”    | “值” |
| --- | --- |
| `id` | 通知的标识符，如由服务维护的 UUID 或计数器。 `source` + `id`对于每个 distinct 事件都是唯一的 |
| `source` | IoT 中心或 Azure 数字孪生实例的名称，例如*myhub.azure-devices.net*或*mydigitaltwins.westus2.azuredigitaltwins.net*
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | 数字输出的 ID |
| `time` | 在数字克隆上发生操作的时间戳 |
| `traceparent` | 事件的 W3C 跟踪上下文 |

#### <a name="body-details"></a>正文详细信息

通知的正文 `Twin.Update` 是一个 JSON 修补文档，其中包含数字克隆的更新。

例如，假设使用以下修补程序更新了数字克隆。

```json
[
    {
        "op": "replace",
        "value": 40,
        "path": "/Temperature"
    },
    {
        "op": "add",
        "value": 30,
        "path": "/comp1/prop1"
    }
]
```

对应的通知（如果是由服务同步执行的（如 Azure 数字孪生更新数字克隆），其正文如下所示：

```json
{
    "modelId": "dtmi:example:com:floor4;2",
    "patch": [
      {
        "value": 40,
        "path": "/Temperature",
        "op": "replace"
      },
      {
        "value": 30,
        "path": "/comp1/prop1",
        "op": "add"
      }
    ]
  }
```

## <a name="next-steps"></a>后续步骤

请参阅如何创建用于传递事件的终结点和路由：
* [操作说明：管理终结点和路由](how-to-manage-routes.md)

或者，详细了解 Azure 数字孪生 Api 和 SDK 选项：
* [操作说明：使用 Azure 数字孪生 Api 和 Sdk](how-to-use-apis-sdks.md)