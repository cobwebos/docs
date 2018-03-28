---
title: Durable Functions 中的 HTTP API - Azure
description: 了解如何实现 Azure Functions 的 Durable Functions 扩展中的 HTTP API。
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 5fa5d9e66912bdeffdf553ddc0cb7d3feb0a5b77
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
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

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

这个示例函数生成以下 JSON 响应数据。 所有字段的数据类型均为 `string`。

| 字段             |说明                           |
|-------------------|--------------------------------------|
| id                |业务流程实例的 ID。 |
| statusQueryGetUri |业务流程实例的状态 URL。 |
| sendEventPostUri  |业务流程实例的“引发事件”URL。 |
| terminatePostUri  |业务流程实例的“终止”URL。 |

下面是示例响应：

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```
> [!NOTE]
> Webhook URL 的格式可能会有所不同，具体取决于所运行 Azure Functions 主机的版本。 上面的示例适用于 Azure Functions 2.0 主机。

## <a name="async-operation-tracking"></a>异步操作跟踪

前面提到的 HTTP 响应旨在通过 Durable Functions 实现长时间运行的 HTTP 异步 API。 有时，这被称为轮询使用者模式。 客户端/服务器流工作方式如下：

1. 客户端发出 HTTP 请求，启动长时间运行的进程，例如业务流程协调程序函数。
2. 目标 HTTP 触发器返回 HTTP 202 响应，其中包含带有 `statusQueryGetUri` 值的 `Location` 标头。
3. 客户端轮询 `Location` 标头中的 URL。 可继续看到包含 `Location` 标头的 HTTP 202 响应。
4. 实例完成（或失败）后，`Location` 标头中的终结点返回 HTTP 200。

此协议允许通过外部客户端或支持轮询 HTTP 终结点并遵循 `Location` 标头的服务协调长时间运行的进程。 基础部分已经内置于 Durable Functions HTTP API 中。

> [!NOTE]
> 默认情况下，[Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)提供的所有基于 HTTP 的操作都支持标准异步操作模式。 使用此功能，可在逻辑应用工作流中嵌入长时间运行的持久函数。 有关异步 HTTP 模式的逻辑应用支持的更多详细信息，请参阅 [Azure 逻辑应用工作流操作和触发器文档](../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns)。

## <a name="http-api-reference"></a>HTTP API 引用

由扩展实现的所有 HTTP API 均采用以下参数。 所有参数的数据类型均为 `string`。

| 参数  | 参数类型  | 说明 |
|------------|-----------------|-------------|
| instanceId | 代码             | 业务流程实例的 ID。 |
| taskHub    | 查询字符串    | [任务中心](durable-functions-task-hubs.md)的名称。 如果未指定，则使用当前函数应用的任务中心名称。 |
| 连接 | 查询字符串    | 用于存储帐户的连接字符串的名称。 如果未指定，则使用函数应用的默认连接字符串。 |
| systemKey  | 查询字符串    | 需要授权密钥才可调用 API。 |
| showHistory| 查询字符串    | 可选参数。 如果设置为 `true`，业务流程执行历史记录将包含在响应有效负载中。| 
| showHistoryOutput| 查询字符串    | 可选参数。 如果设置为 `true`，活动输出将包含在业务流程执行历史记录中。| 

`systemKey` 是 Azure Functions 主机自动生成的授权密钥。 它可专门向 Durable Task 扩展 API 授予访问权限，且可通过与管理[其他授权密钥](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API)相同的方式进行管理。 发现 `systemKey` 值的最简单的方法是使用上文提及的 `CreateCheckStatusResponse` API。

后面几节介绍扩展支持的特殊 HTTP API，并提供有关其用法的示例。

### <a name="get-instance-status"></a>获取实例状态

获取指定业务流程实例的状态。

#### <a name="request"></a>请求

对于 Functions 1.0，请求格式如下：

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

Functions 2.0 格式包含的所有参数均相同，但 URL 前缀略有不同：

```http
GET /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}&showHistory={showHistory}&showHistoryOutput={showHistoryOutput}
```

#### <a name="response"></a>响应

可返回若干可能的状态代码值。

* HTTP 200 (正常)：指定实例的状态为已完成。
* HTTP 202 (已接受)：指定实例正在进行中。
* HTTP 400 (错误请求)：指定实例失败或已终止。
* HTTP 404 (找不到)：指定实例不存在或未开始运行。

值为 HTTP 200 和 HTTP 202 时的响应负载是包含以下字段的 JSON 对象：

| 字段           | 数据类型 | 说明 |
|-----------------|-----------|-------------|
| runtimeStatus   | 字符串    | 实例的运行时状态。 相关的值为：正在运行、挂起、失败、已取消、已终止和已完成。 |
| input           | JSON      | 用于初始化实例的 JSON 数据。 |
| output          | JSON      | 实例的 JSON 输出。 如果实例不是已完成状态，则该字段为 `null`。 |
| createdTime     | 字符串    | 创建实例的时间。 使用 ISO 8601 扩展表示法。 |
| lastUpdatedTime | 字符串    | 实例持续的时间。 使用 ISO 8601 扩展表示法。 |
| historyEvents   | JSON      | 包含业务流程执行历史记录的 JSON 数组。 除非 `showHistory` 查询字符串参数设置为 `true`，否则此字段为 `null`。  | 

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

### <a name="raise-event"></a>引发事件

向正在运行的业务流程实例发送事件通知消息。

#### <a name="request"></a>请求

对于 Functions 1.0，请求格式如下：

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Functions 2.0 格式包含的所有参数均相同，但 URL 前缀略有不同：

```http
POST /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

此 API 的请求参数包括前面提及的默认集及以下唯一参数：

| 字段       | 参数类型  | 数据类型 | 说明 |
|-------------|-----------------|-----------|-------------|
| eventName   | 代码             | 字符串    | 目标业务流程正在等待的事件的名称。 |
| {content}   | 请求内容 | JSON      | JSON 格式的事件负载。 |

#### <a name="response"></a>响应

可返回若干可能的状态代码值。

* HTTP 202 (已接受)：已接受引发的事件，正在处理。
* HTTP 400 (错误请求)：请求内容不属于 `application/json` 类型或不是有效的 JSON。
* HTTP 404 (找不到)：找不到指定的实例。
* HTTP 410 (消失)：指定的实例已完成或失败，且无法处理任何引发的事件。

下面的请求示例向等待名为 operation 的事件的实例发送 JSON 字符串 `"incr"`：

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

此 API 的响应不包含任何内容。

### <a name="terminate-instance"></a>终止实例

终止正在运行的业务流程实例。

#### <a name="request"></a>请求

对于 Functions 1.0，请求格式如下：

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Functions 2.0 格式包含的所有参数均相同，但 URL 前缀略有不同：

```http
DELETE /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

此 API 的请求参数包括前面提及的默认集及以下唯一参数。

| 字段       | 参数类型  | 数据类型 | 说明 |
|-------------|-----------------|-----------|-------------|
| 原因      | 查询字符串    | 字符串    | 可选。 终止业务流程实例的原因。 |

#### <a name="response"></a>响应

可返回若干可能的状态代码值。

* HTTP 202 (已接受)：已接受终止请求，正在处理。
* HTTP 404 (找不到)：找不到指定的实例。
* HTTP 410 (消失)：指定的实例已完成或失败。

下面的示例请求终止正在运行的实例，并将原因指定为 buggy：

```
DELETE /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

此 API 的响应不包含任何内容。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何处理错误](durable-functions-error-handling.md)
