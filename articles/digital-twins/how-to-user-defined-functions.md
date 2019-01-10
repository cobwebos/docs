---
title: 如何在 Azure 数字孪生中创建用户定义函数 | Microsoft Docs
description: 有关如何使用 Azure 数字孪生创建用户定义的函数、匹配程序和角色分配的指南。
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: alinast
ms.custom: seodec18
ms.openlocfilehash: 91c0b5700fbc648f1fcd1355a438694cecc07a04
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993394"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>如何在 Azure 数字孪生中创建用户定义函数

[用户定义函数](./concepts-user-defined-functions.md)使用户能够针对传入的遥测消息和空间图形元数据配置执行的自定义逻辑。 用户还可将事件发送到预定义的[终结点](./how-to-egress-endpoints.md)。

本指南通过一个示例演示如何检测和警告任何超过特定温度的读数，这些读数来自接收到的温度事件。

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>客户端库参考

如需了解用户定义函数运行时中可用作帮助程序方法的函数，请参阅[客户端库参考](./reference-user-defined-functions-client-library.md)一文。

## <a name="create-a-matcher"></a>创建匹配程序

匹配程序是图形对象，可决定要对给定遥测消息运行哪些用户定义的函数。

- 有效匹配程序条件的比较：

  - `Equals`
  - `NotEquals`
  - `Contains`

- 有效匹配程序条件的目标：

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

对于数据类型值为 `"Temperature"` 的所有传感器遥测事件，以下示例匹配程序的计算结果都为 true。 可通过向以下对象发出经过身份验证的 HTTP POST 请求，在用户定义函数中创建多个匹配程序：

```plaintext
YOUR_MANAGEMENT_API_URL/matchers
```

使用 JSON 体：

```JSON
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "YOUR_SPACE_IDENTIFIER"
}
```

| 值 | 替换为 |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | 托管实例的服务器区域 |

## <a name="create-a-user-defined-function"></a>创建用户定义函数

创建匹配程序后，使用以下经过身份验证的 HTTP POST 请求来上传函数代码片段：

```plaintext
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

> [!IMPORTANT]
> - 验证标头是否包括：`Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`。
> - 提供的正文包含多个部分：
>   - 第一部分包含所需的 UDF 元数据。
>   - 第二部分包含 JavaScript 计算逻辑。
> - 在“USER_DEFINED_BOUNDARY”部分中，替换“spaceId”(`YOUR_SPACE_IDENTIFIER`) 和“matchers”(`YOUR_MATCHER_IDENTIFIER`) 值。
> - 请注意，JavaScript UDF 作为 `Content-Type: text/javascript` 提供。

使用以下 JSON 体：

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "name": "User Defined Function",
  "description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| 值 | 替换为 |
| --- | --- |
| USER_DEFINED_BOUNDARY | 多部分内容边界名称 |
| YOUR_SPACE_IDENTIFIER | 空间标识符  |
| YOUR_MATCHER_IDENTIFIER | 要使用的匹配程序的 ID |

### <a name="example-functions"></a>示例函数

直接针对数据类型为 **Temperature**（也就是 `sensor.DataType`）的传感器设置传感器遥测读数：

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

telemetry 参数公开了 SensorId 和 Message 属性（对应于传感器发送的消息）。 **ExecutionContext** 参数公开了以下属性：

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

在下一示例中，如果传感器遥测读数超过预定义的阈值，则记录消息。 如果 Azure 数字孪生实例上启用了诊断设置，则还会转发用户定义函数中的日志：

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

如果温度高于预定义常量，以下代码会触发通知：

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

有关更复杂的用户定义函数代码示例，请参阅[占用情况快速入门](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)。

## <a name="create-a-role-assignment"></a>创建角色分配

创建角色分配，以便用户定义函数依据其运行。 如果用户定义函数不存在任何角色分配，则它将没有与管理 API 交互的适当权限，或者无权访问图形对象。用户定义函数可以执行的操作通过以下方式指定和定义：Azure 数字孪生管理 API 中基于角色的访问控制。 例如，用户定义函数可通过指定特定角色或特定访问控制路径来限制这些操作的范围。 有关详细信息，请参阅[基于角色的访问控制](./security-role-based-access-control.md)文档。

1. 针对所有角色[查询系统 API](./security-create-manage-role-assignments.md#all) 以获取要分配给 UDF 的角色 ID。 可通过发出经过身份验证的 HTTP GET 请求执行此操作，以便：

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   保留所需的角色 ID。 它将作为下面的 JSON 体属性“roleId”(`YOUR_DESIRED_ROLE_IDENTIFIER`) 传递。

1. objectId (`YOUR_USER_DEFINED_FUNCTION_ID`) 就是先前创建的 UDF ID。
1. 通过使用 `fullpath` 查询你的空间来查找“path”(`YOUR_ACCESS_CONTROL_PATH`) 的值。
1. 复制返回的 `spacePaths` 值。 稍后你将使用该值。 向以下对象发出经过身份验证的 HTTP GET 请求：

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | 值 | 替换为 |
    | --- | --- |
    | YOUR_SPACE_NAME | 要使用的空间名称 |

1. 将返回的 `spacePaths` 值粘贴到“path”，以通过向以下对象发出经过身份验证的 HTTP POST 请求来创建 UDF 角色分配：

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    使用 JSON 体：

    ```JSON
    {
      "roleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "objectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "objectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | 值 | 替换为 |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | 所需角色的标识符 |
    | YOUR_USER_DEFINED_FUNCTION_ID | 要使用的 UDF 的 ID |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | 指定 UDF 类型的 ID |
    | YOUR_ACCESS_CONTROL_PATH | 访问控制路径 |

>[!TIP]
> 请阅读文章[如何创建和管理角色分配](./security-create-manage-role-assignments.md)，获取有关 UDF 相关管理 API 操作和终结点的详细信息。

## <a name="send-telemetry-to-be-processed"></a>发送要处理的遥测数据

空间智能图中定义的传感器发送遥测。 反过来，遥测会触发已上传的用户定义函数的执行。 数据处理器将选取遥测数据。 然后为调用用户定义函数创建执行计划。

1. 为生成读数的传感器检索匹配程序。
1. 根据成功进行计算的匹配程序，检索相关联的用户定义函数。
1. 执行每个用户定义函数。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Azure 数字孪生终结点](./how-to-egress-endpoints.md)以向其发送事件。

- 若要了解 Azure 数字孪生中的路由的详细信息，请阅读[路由事件和消息](./concepts-events-routing.md)。

- 请查看[客户端库参考文档](./reference-user-defined-functions-client-library.md)。
