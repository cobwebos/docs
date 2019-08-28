---
title: Durable Functions 中的 HTTP API - Azure
description: 了解如何实现 Azure Functions 的 Durable Functions 扩展中的 HTTP API。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 11ae418ddbe007c6fd5aa44ef22ed7fddec9c702
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087267"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>Durable Functions 中的 HTTP API (Azure Functions)

Durable Task 扩展公开了一组 HTTP API，可用于执行以下任务：

* 提取业务流程实例的状态。
* 向处于等待状态的业务流程实例发送事件。
* 终止正在运行的业务流程实例。

上述每个 HTTP API 都是 Webhook 操作，可由 Durable Task 扩展直接处理。 它们不特定于函数应用中的任何函数。

> [!NOTE]
> 此外，也可使用 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 类的实例管理 API 直接调用这些操作。 有关详细信息，请参阅[实例管理](durable-functions-instance-management.md)。

## <a name="http-api-url-discovery"></a>HTTP API URL 发现

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 类公开了一个 [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) API，可用于生成 HTTP 响应负载，该负载中包含指向受支持的所有操作的链接。 下面的 HTTP 触发型函数示例演示了如何使用此 API：

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

这些示例函数生成以下 JSON 响应数据。 所有字段的数据类型均为 `string`。

| 字段                   |描述                           |
|-----------------------------|--------------------------------------|
| **`id`**                    |业务流程实例的 ID。 |
| **`statusQueryGetUri`**     |业务流程实例的状态 URL。 |
| **`sendEventPostUri`**      |业务流程实例的“引发事件”URL。 |
| **`terminatePostUri`**      |业务流程实例的“终止”URL。 |
| **`purgeHistoryDeleteUri`** |业务流程实例的“清除历史记录”URL。 |
| **`rewindPostUri`**         |（预览）业务流程实例的“后退”URL。 |

下面是示例响应：

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "purgeHistoryDeleteUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
    "rewindPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/rewind?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```

> [!NOTE]
> Webhook URL 的格式可能会有所不同，具体取决于所运行 Azure Functions 主机的版本。 上面的示例适用于 Azure Functions 2.x 主机。

## <a name="async-operation-tracking"></a>异步操作跟踪

前面提到的 HTTP 响应旨在通过 Durable Functions 实现长时间运行的 HTTP 异步 API。 有时，这被称为轮询使用者模式。 客户端/服务器流工作方式如下：

1. 客户端发出 HTTP 请求，启动长时间运行的进程，例如业务流程协调程序函数。
2. 目标 HTTP 触发器返回 HTTP 202 响应，其中包含带有 `statusQueryGetUri` 值的 `Location` 标头。
3. 客户端轮询 `Location` 标头中的 URL。 可继续看到包含 `Location` 标头的 HTTP 202 响应。
4. 实例完成（或失败）后，`Location` 标头中的终结点返回 HTTP 200。

此协议允许通过外部客户端或支持轮询 HTTP 终结点并遵循 `Location` 标头的服务协调长时间运行的进程。 基础部分已经内置于 Durable Functions HTTP API 中。

> [!NOTE]
> 默认情况下，[Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)提供的所有基于 HTTP 的操作都支持标准异步操作模式。 使用此功能，可在逻辑应用工作流中嵌入长时间运行的持久函数。 有关异步 HTTP 模式的逻辑应用支持的更多详细信息，请参阅 [Azure 逻辑应用工作流操作和触发器文档](../../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns)。

## <a name="http-api-reference"></a>HTTP API 引用

由扩展实现的所有 HTTP API 均采用以下参数。 所有参数的数据类型均为 `string`。

| 参数        | 参数类型  | 描述 |
|------------------|-----------------|-------------|
| **`taskHub`**    | 查询字符串    | [任务中心](durable-functions-task-hubs.md)的名称。 如果未指定，则使用当前函数应用的任务中心名称。 |
| **`connection`** | 查询字符串    | 用于存储帐户的连接字符串的名称。 如果未指定，则使用函数应用的默认连接字符串。 |
| **`systemKey`**  | 查询字符串    | 需要授权密钥才可调用 API。 |

`systemKey` 是 Azure Functions 主机自动生成的授权密钥。 它可专门向 Durable Task 扩展 API 授予访问权限，且可通过与管理[其他授权密钥](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API)相同的方式进行管理。 发现 `systemKey` 值的最简单的方法是使用上文提及的 `CreateCheckStatusResponse` API。

后面几节介绍扩展支持的特殊 HTTP API，并提供有关其用法的示例。

### <a name="get-instance-status"></a>获取实例状态

获取指定业务流程实例的状态。

#### <a name="request"></a>请求

对于 1.x 版 Functions 运行时，请求格式如下（为简洁起见，已分多行显示）：

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub
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
| **`runtimeStatus`**     | 查询字符串    | 可选参数。 指定后，根据其运行时状态筛选返回实例列表。 若要查看可能的运行时状态值列表，请参阅[查询实例](durable-functions-instance-management.md)主题。 |

#### <a name="response"></a>响应

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

### <a name="get-all-instances-status"></a>获取所有实例状态

此外，可以通过从“获取实例状态”请求中删除 `instanceId`，来查询所有实例的状态。 在这种情况下，基本参数与“获取实例状态”相同。 也支持使用查询字符串参数进行筛选。

请务必牢记 `connection` 和 `code` 可选。 如果具有对函数的匿名身份验证，则无需代码。
如果你不想要使用 AzureWebJobsStorage 应用设置中未定义的其他存储连接字符串，则可以安全地忽略连接查询字符串参数。

#### <a name="request"></a>请求

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
| **`runtimeStatus`**     | 查询字符串    | 可选参数。 指定后，根据其运行时状态筛选返回实例列表。 若要查看可能的运行时状态值列表，请参阅[查询实例](durable-functions-instance-management.md)主题。 |
| **`top`**               | 查询字符串    | 可选参数。 如果指定，则会限制查询返回的实例数。 |

#### <a name="response"></a>响应

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

### <a name="purge-single-instance-history"></a>清除单个实例的历史记录

删除指定业务流程实例的历史记录和相关项目。

#### <a name="request"></a>请求

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

#### <a name="response"></a>响应

可以返回以下 HTTP 状态代码值。

* **HTTP 200 (正常)** ：已成功清除实例历史记录。
* **HTTP 404 (找不到)** ：指定的实例不存在。

值为 **HTTP 200** 时的响应有效负载是包含以下字段的 JSON 对象：

| 字段                  | 数据类型 | 描述 |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | 整数   | 删除的实例数。 对于单个实例，此值应始终为 `1`。 |

以下是响应负载的示例（为提高可读性设置了格式）：

```json
{
    "instancesDeleted": 1
}
```

### <a name="purge-multiple-instance-history"></a>清除多个实例的历史记录

也可以通过删除“清除单个实例的历史记录”请求中的 `{instanceId}`，来删除任务中心内多个实例的历史记录和相关项目。 若要有选择地清除实例历史记录，请使用“获取所有实例状态”请求中所述的相同筛选器。

#### <a name="request"></a>请求

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
| **`runtimeStatus`**   | 查询字符串    | 可选参数。 指定后，将根据运行时状态筛选已清除实例的列表。 若要查看可能的运行时状态值列表，请参阅[查询实例](durable-functions-instance-management.md)主题。 |

> [!NOTE]
> 如果“实例”和/或“历史记录”表中包含许多的行，则此操作可能会导致很高的 Azure 存储 I/O 开销。 有关这些表的更多详细信息，请参阅 [Durable Functions (Azure Functions) 中的性能和缩放](durable-functions-perf-and-scale.md#instances-table)文档。

#### <a name="response"></a>响应

可以返回以下 HTTP 状态代码值。

* **HTTP 200 (正常)** ：已成功清除实例历史记录。
* **HTTP 404 (找不到)** ：找不到与筛选表达式匹配的实例。

值为 **HTTP 200** 时的响应有效负载是包含以下字段的 JSON 对象：

| 字段                   | 数据类型 | 描述 |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | 整数   | 删除的实例数。 |

以下是响应负载的示例（为提高可读性设置了格式）：

```json
{
    "instancesDeleted": 250
}
```

### <a name="raise-event"></a>引发事件

向正在运行的业务流程实例发送事件通知消息。

#### <a name="request"></a>请求

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

#### <a name="response"></a>响应

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

### <a name="terminate-instance"></a>终止实例

终止正在运行的业务流程实例。

#### <a name="request"></a>请求

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

#### <a name="response"></a>响应

可返回若干可能的状态代码值。

* **HTTP 202 (已接受)** ：已接受终止请求，正在处理。
* **HTTP 404 (找不到)** ：找不到指定的实例。
* **HTTP 410 (消失)** ：指定的实例已完成或失败。

下面的示例请求终止正在运行的实例，并将原因指定为 buggy：

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

此 API 的响应不包含任何内容。

### <a name="rewind-instance-preview"></a>回退实例（预览版）

通过重播最近的失败操作，将失败的业务流程实例还原为运行状态。

#### <a name="request"></a>请求

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

#### <a name="response"></a>响应

可返回若干可能的状态代码值。

* **HTTP 202 (已接受)** ：已接受回退请求，正在处理。
* **HTTP 404 (找不到)** ：找不到指定的实例。
* **HTTP 410 (消失)** ：指定的实例已完成或被终止。

下面的示例请求回退失败的实例，并将原因指定为**已修复**：

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

此 API 的响应不包含任何内容。

### <a name="signal-entity-preview"></a>信号实体（预览）

向[持久实体](durable-functions-types-features-overview.md#entity-functions)发送单向操作消息。 如果该实体不存在，系统会自动创建它。

#### <a name="request"></a>请求

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

#### <a name="response"></a>响应

此操作有多种可能的响应：

* **HTTP 202 (已接受)** ：已接受对信号操作进行异步处理。
* **HTTP 400 (错误请求)** ：请求内容不是 `application/json` 类型、不是有效的 JSON，或者使用了无效的 `entityKey` 值。
* **HTTP 404 (找不到)** ：找不到指定的 `entityType`。

成功的 HTTP 请求在响应中不包含任何内容。 失败的 HTTP 请求可能会在响应内容中包含 JSON 格式的错误信息。

### <a name="query-entity-preview"></a>查询实体（预览）

获取指定实体的状态。

#### <a name="request"></a>请求

HTTP 请求的格式如下（为方便阅读，已分多行显示）：

```http
GET /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

#### <a name="response"></a>响应

此操作有两种可能的响应：

* **HTTP 200 (正常)** ：指定的实体存在。
* **HTTP 404 (找不到)** ：找不到指定的实体。

成功的响应包含实体的 JSON 序列化状态作为其内容。

#### <a name="example"></a>示例
以下 HTTP 请求示例获取名为 `steps` 的现有 `Counter` 实体的状态：

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
> [了解如何处理错误](durable-functions-error-handling.md)
