---
title: Durable Functions 中的 HTTP Api-Azure Functions
description: 了解如何实现 Azure Functions 的 Durable Functions 扩展中的 HTTP API。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/07/2019
ms.author: azfuncdf
ms.openlocfilehash: c81eccaa2b3a4335f034b9667f6e7be317635f43
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933391"
---
# <a name="http-api-reference"></a>HTTP API 引用

Durable Functions 扩展公开了一组内置 HTTP Api，可用于在[业务流程](durable-functions-types-features-overview.md#orchestrator-functions)、[实体](durable-functions-types-features-overview.md#entity-functions)和[任务中心](durable-functions-task-hubs.md)执行管理任务。 这些 HTTP Api 是由 Azure Functions 主机授权但由 Durable Functions 扩展直接处理的扩展性 webhook。

扩展实现的所有 HTTP Api 都需要以下参数。 所有参数的数据类型均为 `string`。

| 参数        | 参数类型  | 描述 |
|------------------|-----------------|-------------|
| **`taskHub`**    | 查询字符串    | [任务中心](durable-functions-task-hubs.md)的名称。 如果未指定，则使用当前函数应用的任务中心名称。 |
| **`connection`** | 查询字符串    | 用于存储帐户的连接字符串的名称。 如果未指定，则使用函数应用的默认连接字符串。 |
| **`systemKey`**  | 查询字符串    | 需要授权密钥才可调用 API。 |

`systemKey`是 Azure Functions 主机自动生成的授权密钥。 它可专门向 Durable Task 扩展 API 授予访问权限，且可通过与管理[其他授权密钥](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API)相同的方式进行管理。 发现 `systemKey` 值的最简单的方法是使用上文提及的 `CreateCheckStatusResponse` API。

后面几节介绍扩展支持的特殊 HTTP API，并提供有关其用法的示例。

## <a name="start-orchestration"></a>启动业务流程

开始执行指定的业务流程协调程序函数的新实例。

### <a name="request"></a>请求

对于 1.x 版 Functions 运行时，请求格式如下（为简洁起见，已分多行显示）：

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

在 2.x 版 Functions 运行时中，URL 格式包含的所有参数相同，但前缀略有不同：

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

此 API 的请求参数包括前面提及的默认集及以下唯一参数：

| 字段              | 参数类型  | 描述 |
|--------------------|-----------------|-------------|
| **`functionName`** | URL             | 要启动的业务流程协调程序函数的名称。 |
| **`instanceId`**   | URL             | 可选参数。 业务流程实例的 ID。 如果未指定，则业务流程协调程序函数将以随机实例 ID 开始。 |
| **`{content}`**    | 请求内容 | 可选。 JSON 格式的业务流程协调程序函数输入。 |

### <a name="response"></a>响应

可返回若干可能的状态代码值。

* **HTTP 202 (已接受)** ：指定的业务流程协调程序函数已计划开始运行。 `Location` Response 标头包含用于轮询业务流程状态的 URL。
* **HTTP 400 (错误请求)** ：指定的业务流程协调程序函数不存在、指定的实例 ID 无效或请求内容不是有效的 JSON。

下面是一个启动`RestartVMs`业务流程协调程序函数并包括 JSON 对象负载的示例请求：

```http
POST /runtime/webhooks/durabletask/orchestrators/RestartVMs?code=XXX
Content-Type: application/json
Content-Length: 83

{
    "resourceGroup": "myRG",
    "subscriptionId": "111deb5d-09df-4604-992e-a968345530a9"
}
```

**HTTP 202**事例的响应负载是包含以下字段的 JSON 对象：

| 字段                       | 描述                          |
|-----------------------------|--------------------------------------|
| **`id`**                    |业务流程实例的 ID。 |
| **`statusQueryGetUri`**     |业务流程实例的状态 URL。 |
| **`sendEventPostUri`**      |业务流程实例的“引发事件”URL。 |
| **`terminatePostUri`**      |业务流程实例的“终止”URL。 |
| **`purgeHistoryDeleteUri`** |业务流程实例的“清除历史记录”URL。 |
| **`rewindPostUri`**         |（预览）业务流程实例的“后退”URL。 |

所有字段的数据类型均为 `string`。

下面是业务流程实例`abc123`的响应有效负载的示例，其 ID 为（可读性）：

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

Http 响应旨在与*轮询使用者模式*兼容。 它还包括以下值得注意的响应标头：

* **位置**：状态终结点的 URL。 此 URL 包含与`statusQueryGetUri`字段相同的值。
* **重试次数**：在轮询操作之间等待的秒数。 默认值为 `10`。

有关异步 HTTP 轮询模式的详细信息，请参阅[HTTP async operation 跟踪](durable-functions-http-features.md#async-operation-tracking)文档。

## <a name="get-instance-status"></a>获取实例状态

获取指定业务流程实例的状态。

### <a name="request"></a>请求

对于 1.x 版 Functions 运行时，请求格式如下（为简洁起见，已分多行显示）：

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

在 2.x 版 Functions 运行时中，URL 格式包含的所有参数相同，但前缀略有不同：

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

此 API 的请求参数包括前面提及的默认集及以下唯一参数：

| 字段                   | 参数类型  | 描述 |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | 业务流程实例的 ID。 |
| **`showInput`**         | 查询字符串    | 可选参数。 如果设置为 `false`，则函数输入不会包含在响应有效负载中。|
| **`showHistory`**       | 查询字符串    | 可选参数。 如果设置为 `true`，业务流程执行历史记录将包含在响应有效负载中。|
| **`showHistoryOutput`** | 查询字符串    | 可选参数。 如果设置为 `true`，函数输出将包含在业务流程执行历史记录中。|
| **`createdTimeFrom`**   | 查询字符串    | 可选参数。 指定后，将筛选在给定 ISO8601 时间戳当时或之后创建的返回实例列表。|
| **`createdTimeTo`**     | 查询字符串    | 可选参数。 指定后，将筛选在给定 ISO8601 时间戳当时或之前创建的返回实例列表。|
| **`runtimeStatus`**     | 查询字符串    | 可选参数。 指定后，根据其运行时状态筛选返回实例列表。 若要查看可能的运行时状态值列表，请参阅[查询实例](durable-functions-instance-management.md)一文。 |

### <a name="response"></a>响应

可返回若干可能的状态代码值。

* **HTTP 200 (正常)** ：指定的实例处于已完成状态。
* **HTTP 202 (已接受)** ：指定实例正在进行中。
* **HTTP 400 (错误请求)** ：指定的实例已失败或被终止。
* **HTTP 404 (找不到)** ：指定的实例不存在或未开始运行。
* **HTTP 500 (内部服务器错误)** ：指定的实例因未处理的异常而失败。

值为 HTTP 200 和 HTTP 202 时的响应负载是包含以下字段的 JSON 对象：

| 字段                 | 数据类型 | 描述 |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | string    | 实例的运行时状态。 相关的值为：正在运行、挂起、失败、已取消、已终止和已完成。 |
| **`input`**           | JSON      | 用于初始化实例的 JSON 数据。 如果 `showInput` 查询字符串参数设置为 `false`，则此字段为 `null`。|
| **`customStatus`**    | JSON      | 用于自定义业务流程状态的 JSON 数据。 如果未设置，此字段为 `null`。 |
| **`output`**          | JSON      | 实例的 JSON 输出。 如果实例不是已完成状态，则该字段为 `null`。 |
| **`createdTime`**     | string    | 创建实例的时间。 使用 ISO 8601 扩展表示法。 |
| **`lastUpdatedTime`** | string    | 实例持续的时间。 使用 ISO 8601 扩展表示法。 |
| **`historyEvents`**   | JSON      | 包含业务流程执行历史记录的 JSON 数组。 除非 `showHistory` 查询字符串参数设置为 `true`，否则此字段为 `null`。 |

下面是包括业务流程执行历史记录和活动输出的示例响应有效负载（为提高可读性已设置格式）：

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

HTTP 202 响应还包括 Location 响应标头，该标头引用了与上文提及的 `statusQueryGetUri` 字段相同的 URL。

## <a name="get-all-instances-status"></a>获取所有实例状态

此外，可以通过从“获取实例状态”请求中删除 `instanceId`，来查询所有实例的状态。 在这种情况下，基本参数与“获取实例状态”相同。 也支持使用查询字符串参数进行筛选。

请务必牢记 `connection` 和 `code` 可选。 如果对该函数进行了匿名身份验证， `code`则不是必需的。
如果你不想要使用 AzureWebJobsStorage 应用设置中未定义的其他存储连接字符串，则可以安全地忽略连接查询字符串参数。

### <a name="request"></a>请求

对于 1.x 版 Functions 运行时，请求格式如下（为简洁起见，已分多行显示）：

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

在 2.x 版 Functions 运行时中，URL 格式包含的所有参数相同，但前缀略有不同：

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

此 API 的请求参数包括前面提及的默认集及以下唯一参数：

| 字段                   | 参数类型  | 描述 |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | 业务流程实例的 ID。 |
| **`showInput`**         | 查询字符串    | 可选参数。 如果设置为 `false`，则函数输入不会包含在响应有效负载中。|
| **`showHistory`**       | 查询字符串    | 可选参数。 如果设置为 `true`，业务流程执行历史记录将包含在响应有效负载中。|
| **`showHistoryOutput`** | 查询字符串    | 可选参数。 如果设置为 `true`，函数输出将包含在业务流程执行历史记录中。|
| **`createdTimeFrom`**   | 查询字符串    | 可选参数。 指定后，将筛选在给定 ISO8601 时间戳当时或之后创建的返回实例列表。|
| **`createdTimeTo`**     | 查询字符串    | 可选参数。 指定后，将筛选在给定 ISO8601 时间戳当时或之前创建的返回实例列表。|
| **`runtimeStatus`**     | 查询字符串    | 可选参数。 指定后，根据其运行时状态筛选返回实例列表。 若要查看可能的运行时状态值列表，请参阅[查询实例](durable-functions-instance-management.md)一文。 |
| **`top`**               | 查询字符串    | 可选参数。 如果指定，则会限制查询返回的实例数。 |

### <a name="response"></a>响应

以下是包含业务流程状态的响应有效负载的示例（为可读性而设置了格式）：

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> 如果实例表中有很多行，则此操作在 Azure存储 I/O 方面可能代价非常高昂。 有关实例表的更多详细信息，请参阅 [Durable Functions (Azure Functions) 中的性能和缩放](durable-functions-perf-and-scale.md#instances-table)文档。
>

如果存在更多结果，则会在响应标头中返回继续标记。  标头的名称为 `x-ms-continuation-token`。

如果在下一个请求标头中设置了继续标记值，则可以获取下一页结果。 请求标头的此名称也是 `x-ms-continuation-token`。

## <a name="purge-single-instance-history"></a>清除单个实例的历史记录

删除指定业务流程实例的历史记录和相关项目。

### <a name="request"></a>请求

对于 1.x 版 Functions 运行时，请求格式如下（为简洁起见，已分多行显示）：

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

在 2.x 版 Functions 运行时中，URL 格式包含的所有参数相同，但前缀略有不同：

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

此 API 的请求参数包括前面提及的默认集及以下唯一参数：

| 字段             | 参数类型  | 描述 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | 业务流程实例的 ID。 |

### <a name="response"></a>响应

可以返回以下 HTTP 状态代码值。

* **HTTP 200 (正常)** ：已成功清除实例历史记录。
* **HTTP 404 (找不到)** ：指定的实例不存在。

值为 **HTTP 200** 时的响应有效负载是包含以下字段的 JSON 对象：

| 字段                  | 数据类型 | 描述 |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | integer   | 删除的实例数。 对于单个实例，此值应始终为 `1`。 |

以下是响应负载的示例（为提高可读性设置了格式）：

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>清除多个实例历史记录

也可以通过删除“清除单个实例的历史记录”请求中的 `{instanceId}`，来删除任务中心内多个实例的历史记录和相关项目。 若要有选择地清除实例历史记录，请使用“获取所有实例状态”请求中所述的相同筛选器。

### <a name="request"></a>请求

对于 1.x 版 Functions 运行时，请求格式如下（为简洁起见，已分多行显示）：

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

在 2.x 版 Functions 运行时中，URL 格式包含的所有参数相同，但前缀略有不同：

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

此 API 的请求参数包括前面提及的默认集及以下唯一参数：

| 字段                 | 参数类型  | 描述 |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | 查询字符串    | 筛选在给定 ISO8601 时间戳当时或之后创建的已清除实例列表。|
| **`createdTimeTo`**   | 查询字符串    | 可选参数。 指定后，将筛选在给定 ISO8601 时间戳当时或之前创建的已清除实例列表。|
| **`runtimeStatus`**   | 查询字符串    | 可选参数。 指定后，将根据运行时状态筛选已清除实例的列表。 若要查看可能的运行时状态值列表，请参阅[查询实例](durable-functions-instance-management.md)一文。 |

> [!NOTE]
> 如果“实例”和/或“历史记录”表中包含许多的行，则此操作可能会导致很高的 Azure 存储 I/O 开销。 有关这些表的更多详细信息，请参阅 [Durable Functions (Azure Functions) 中的性能和缩放](durable-functions-perf-and-scale.md#instances-table)文档。

### <a name="response"></a>响应

可以返回以下 HTTP 状态代码值。

* **HTTP 200 (正常)** ：已成功清除实例历史记录。
* **HTTP 404 (找不到)** ：找不到与筛选表达式匹配的实例。

值为 **HTTP 200** 时的响应有效负载是包含以下字段的 JSON 对象：

| 字段                   | 数据类型 | 描述 |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | integer   | 删除的实例数。 |

以下是响应负载的示例（为提高可读性设置了格式）：

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>引发事件

向正在运行的业务流程实例发送事件通知消息。

### <a name="request"></a>请求

对于 1.x 版 Functions 运行时，请求格式如下（为简洁起见，已分多行显示）：

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

在 2.x 版 Functions 运行时中，URL 格式包含的所有参数相同，但前缀略有不同：

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

此 API 的请求参数包括前面提及的默认集及以下唯一参数：

| 字段             | 参数类型  | 描述 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | 业务流程实例的 ID。 |
| **`eventName`**   | URL             | 目标业务流程正在等待的事件的名称。 |
| **`{content}`**   | 请求内容 | JSON 格式的事件负载。 |

### <a name="response"></a>响应

可返回若干可能的状态代码值。

* **HTTP 202 (已接受)** ：已接受引发的事件，正在处理。
* **HTTP 400 (错误请求)** ：请求内容不属于 `application/json` 类型或不是有效的 JSON。
* **HTTP 404 (找不到)** ：找不到指定的实例。
* **HTTP 410 (消失)** ：指定的实例已完成或失败，且无法处理任何引发的事件。

下面的请求示例向等待名为 operation 的事件的实例发送 JSON 字符串 `"incr"`：

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

此 API 的响应不包含任何内容。

## <a name="terminate-instance"></a>终止实例

终止正在运行的业务流程实例。

### <a name="request"></a>请求

对于 1.x 版 Functions 运行时，请求格式如下（为简洁起见，已分多行显示）：

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

在 2.x 版 Functions 运行时中，URL 格式包含的所有参数相同，但前缀略有不同：

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

此 API 的请求参数包括前面提及的默认集及以下唯一参数。

| 字段             | 参数类型  | 描述 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | 业务流程实例的 ID。 |
| **`reason`**      | 查询字符串    | 可选。 终止业务流程实例的原因。 |

### <a name="response"></a>响应

可返回若干可能的状态代码值。

* **HTTP 202 (已接受)** ：已接受终止请求，正在处理。
* **HTTP 404 (找不到)** ：找不到指定的实例。
* **HTTP 410 (消失)** ：指定的实例已完成或失败。

下面的示例请求终止正在运行的实例，并将原因指定为 buggy：

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

此 API 的响应不包含任何内容。

## <a name="rewind-instance-preview"></a>回退实例（预览版）

通过重播最近的失败操作，将失败的业务流程实例还原为运行状态。

### <a name="request"></a>请求

对于 1.x 版 Functions 运行时，请求格式如下（为简洁起见，已分多行显示）：

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

在 2.x 版 Functions 运行时中，URL 格式包含的所有参数相同，但前缀略有不同：

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

此 API 的请求参数包括前面提及的默认集及以下唯一参数。

| 字段             | 参数类型  | 描述 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | 业务流程实例的 ID。 |
| **`reason`**      | 查询字符串    | 可选。 回退业务流程实例的原因。 |

### <a name="response"></a>响应

可返回若干可能的状态代码值。

* **HTTP 202 (已接受)** ：已接受回退请求，正在处理。
* **HTTP 404 (找不到)** ：找不到指定的实例。
* **HTTP 410 (消失)** ：指定的实例已完成或被终止。

下面的示例请求回退失败的实例，并将原因指定为**已修复**：

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

此 API 的响应不包含任何内容。

## <a name="signal-entity"></a>信号实体

向[持久实体](durable-functions-types-features-overview.md#entity-functions)发送单向操作消息。 如果该实体不存在，系统会自动创建它。

> [!NOTE]
> 从 Durable Functions 2.0 开始提供持久实体。

### <a name="request"></a>请求

HTTP 请求的格式如下（为方便阅读，已分多行显示）：

```http
POST /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

此 API 的请求参数包括前面提及的默认集及以下唯一参数：

| 字段             | 参数类型  | 描述 |
|-------------------|-----------------|-------------|
| **`entityType`**  | URL             | 实体的类型。 |
| **`entityKey`**   | URL             | 实体的唯一名称。 |
| **`op`**          | 查询字符串    | 可选。 要调用的用户定义操作的名称。 |
| **`{content}`**   | 请求内容 | JSON 格式的事件负载。 |

以下示例请求将用户定义的“Add”消息发送到名为 `steps` 的 `Counter` 实体。 该消息的内容为 `5` 值。 如果该实体不存在，此请求会创建该实体：

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

### <a name="response"></a>响应

此操作有多种可能的响应：

* **HTTP 202 (已接受)** ：已接受对信号操作进行异步处理。
* **HTTP 400 (错误请求)** ：请求内容不是 `application/json` 类型、不是有效的 JSON，或者使用了无效的 `entityKey` 值。
* **HTTP 404 (找不到)** ：找不到指定的 `entityType`。

成功的 HTTP 请求在响应中不包含任何内容。 失败的 HTTP 请求可能会在响应内容中包含 JSON 格式的错误信息。

## <a name="query-entity"></a>查询实体

获取指定实体的状态。

### <a name="request"></a>请求

HTTP 请求的格式如下（为方便阅读，已分多行显示）：

```http
GET /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

### <a name="response"></a>响应

此操作有两种可能的响应：

* **HTTP 200 (正常)** ：指定的实体存在。
* **HTTP 404 (找不到)** ：找不到指定的实体。

成功的响应包含实体的 JSON 序列化状态作为其内容。

### <a name="example"></a>示例
以下示例 HTTP 请求获取名为`Counter` `steps`的现有实体的状态：

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

如果 `Counter` 实体只是包含 `currentValue` 字段中保存的一些步骤，则响应内容可能如下所示（为方便阅读，已设置响应的格式）：

```json
{
    "currentValue": 5
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何使用 Application Insights 来监视持久函数](durable-functions-diagnostics.md)