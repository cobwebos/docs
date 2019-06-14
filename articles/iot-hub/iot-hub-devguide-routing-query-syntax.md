---
title: Azure IoT 中心消息路由查询 | Microsoft 文档
description: 开发人员指南 - Azure IoT 中心消息路由查询语法。
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 94d3599fe919cf648be7115be68002d2aa458ee3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60400637"
---
# <a name="iot-hub-message-routing-query-syntax"></a>IoT 中心消息路由查询语法

消息路由使用户能够将不同的数据类型（即设备遥测消息、设备生命周期事件和设备孪生更改事件）路由到各个终结点。 此外，还可以在路由此数据之前对其应用丰富查询，以接收对你而言重要的数据。 本文介绍 IoT 中心消息路由查询语言，并提供一些常见的查询模式。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

消息路由允许你查询消息属性和消息正文以及设备孪生标记和设备孪生属性。 如果消息正文不是 JSON，则消息路由仍可路由消息，但查询不能应用于消息正文。  查询被描述为布尔表达式，其中布尔值 true 使查询成功，会路由所有传入数据，而布尔值 false 使查询失败，并且不会路由数据。 如果表达式计算结果为 null 或未定义，则将其视为 false，并且发生故障时将在诊断日志中生成错误。 查询语法必须正确，才能保存和计算路由。  

## <a name="message-routing-query-based-on-message-properties"></a>基于消息属性的消息路由查询 

IoT 中心为所有设备到云的消息传送定义了[格式](iot-hub-devguide-messages-construct.md)，以便实现跨协议互操作性。 IoT 中心消息假设以下 JSON 表示形式的消息。 为所有用户添加系统属性并标识消息的内容。 用户可以有选择地向消息添加应用程序属性。 我们建议使用唯一的属性名称，因为 IoT 中心设备到云消息传递不区分大小写。 例如，如果有多个具有相同名称的属性，IoT 中心将仅发送其中一个属性。  

```json
{ 
  "message": { 
    "systemProperties": { 
      "contentType": "application/json", 
      "contentEncoding": "UTF-8", 
      "iothub-message-source": "deviceMessages", 
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z" 
    }, 
    "appProperties": { 
      "processingPath": "{cold | warm | hot}", 
      "verbose": "{true, false}", 
      "severity": 1-5, 
      "testDevice": "{true | false}" 
    }, 
    "body": "{\"Weather\":{\"Temperature\":50}}" 
  } 
} 
```

### <a name="system-properties"></a>系统属性

系统属性有助于标识消息的内容和源。 

| 属性 | Type | 描述 |
| -------- | ---- | ----------- |
| contentType | 字符串 | 用户指定消息的内容类型。 若要允许查询消息正文，此值应设置应用程序/JSON。 |
| contentEncoding | 字符串 | 用户指定消息的编码类型。 如果 contentType 设置为应用程序/JSON，则允许的值为 UTF-8、UTF-16 和 UTF-32。 |
| iothub-connection-device-id | 字符串 | 此值由 IoT 中心设置，标识设备的 ID。 若要查询，请使用 `$connectionDeviceId`。 |
| iothub-enqueuedtime | 字符串 | 此值由 IoT 中心设置，表示 UTC 中消息排入队列的实际时间。 若要查询，请使用 `enqueuedTime`。 |

如 [IoT 中心消息](iot-hub-devguide-messages-construct.md)中所述，一条消息中还有其他系统属性。 除了 contentType  ，还可以查询 contentEncoding  和 enqueuedTime  、connectionDeviceId  和 connectionModuleId  。

### <a name="application-properties"></a>应用程序属性

应用程序属性是用户定义的字符串，可以添加到消息。 这些字段是可选的。  

### <a name="query-expressions"></a>查询表达式

对消息系统属性的查询需要以 `$` 符号为前缀。 对应用程序属性的查询使用其名称进行访问，而且不应以 `$` 符号为前缀。 如果应用程序属性名称以 `$` 开头，则 IoT 中心将在系统属性中搜索它，如果找不到，再在应用程序属性中查找。 例如： 

查询系统属性 contentEncoding 

```sql
$contentEncoding = 'UTF-8'
```

查询应用程序属性 processingPath：

```sql
processingPath = 'hot'
```

若要组合这些查询，可以使用布尔表达式和函数：

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

受支持的运算符和函数的完整列表在[表达式和条件](iot-hub-devguide-query-language.md#expressions-and-conditions)中列出

## <a name="message-routing-query-based-on-message-body"></a>基于消息正文的消息路由查询 

若要启用对消息正文的查询，消息应采用以 UTF-8、UTF-16 或 UTF-32 编码的 JSON 格式。 在系统属性中，`contentType` 必须设置为 `application/JSON`，而 `contentEncoding` 则设置为一个受支持的 UTF 编码。 如果未指定这些属性，IoT 中心将不会计算消息正文的查询表达式。 

下面的示例演示如何创建具有正确格式和编码 JSON 正文的消息： 

```javascript
var messageBody = JSON.stringify(Object.assign({}, {
    "Weather": {
        "Temperature": 50,
        "Time": "2017-03-09T00:00:00.000Z",
        "PrevTemperatures": [
            20,
            30,
            40
        ],
        "IsEnabled": true,
        "Location": {
            "Street": "One Microsoft Way",
            "City": "Redmond",
            "State": "WA"
        },
        "HistoricalData": [
            {
                "Month": "Feb",
                "Temperature": 40
            },
            {
                "Month": "Jan",
                "Temperature": 30
            }
        ]
    }
}));

// Encode message body using UTF-8  
var messageBytes = Buffer.from(messageBody, "utf8");

var message = new Message(messageBytes);

// Set message body type and content encoding 
message.contentEncoding = "utf-8";
message.contentType = "application/json";

// Add other custom application properties   
message.properties.add("Status", "Active");

deviceClient.sendEvent(message, (err, res) => {
    if (err) console.log('error: ' + err.toString());
    if (res) console.log('status: ' + res.constructor.name);
});
```

### <a name="query-expressions"></a>查询表达式

对消息正文的查询需要以 `$body` 为前缀。 你可以在查询表达式中使用正文引用、正文数组引用或多个正文引用。 查询表达式还可以将正文引用与消息系统属性和消息应用程序属性引用组合在一起。 例如，以下所有查询表达式都有效： 

```sql
$body.Weather.HistoricalData[0].Month = 'Feb' 
```

```sql
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled 
```

```sql
length($body.Weather.Location.State) = 2 
```

```sql
$body.Weather.Temperature = 50 AND processingPath = 'hot'
```

## <a name="message-routing-query-based-on-device-twin"></a>基于设备孪生的消息路由查询 

通过消息路由，可以查询[设备孪生](iot-hub-devguide-device-twins.md)标记和属性，这些是 JSON 对象。 请注意，不支持对模块孪生的查询。 设备孪生标记和属性的示例如下所示。

```JSON
{
    "tags": { 
        "deploymentLocation": { 
            "building": "43", 
            "floor": "1" 
        } 
    }, 
    "properties": { 
        "desired": { 
            "telemetryConfig": { 
                "sendFrequency": "5m" 
            }, 
            "$metadata" : {...}, 
            "$version": 1 
        }, 
        "reported": { 
            "telemetryConfig": { 
                "sendFrequency": "5m", 
                "status": "success" 
            },
            "batteryLevel": 55, 
            "$metadata" : {...}, 
            "$version": 4 
        } 
    } 
} 
```

### <a name="query-expressions"></a>查询表达式

对消息正文的查询需要以 `$twin` 为前缀。 此外，查询表达式还可以将孪生标记或属性引用与正文引用、消息系统属性和消息应用程序属性引用组合在一起。 我们建议在标记和属性中使用唯一名称，因为查询不区分大小写。 同时，请避免使用 `twin`、`$twin`、`body` 或 `$body` 作为属性名称。 例如，以下所有查询表达式都有效： 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

## <a name="next-steps"></a>后续步骤

* 了解[消息路由](iot-hub-devguide-messages-d2c.md)。
* 尝试[消息路由教程](tutorial-routing.md)。