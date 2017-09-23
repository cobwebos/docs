---
title: "错误和异常处理 - Azure 逻辑应用 | Microsoft 文档"
description: "Azure 逻辑应用中的错误和异常处理模式"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 9af2f71b3d288cc6f4e271d0915545d43a1249bc
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017

---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>在 Azure 逻辑应用中处理错误和异常

Azure 逻辑应用提供一组丰富的工具和模式，帮助确保集成可靠并且可在故障后复原。 任何集成体系结构都存在一个难题，那就是如何确保适当地处理依赖系统的停机时间或问题。 逻辑应用使错误处理成为卓越的体验，提供处理工作流中的异常和错误所需的工具。

## <a name="retry-policies"></a>重试策略

重试策略是最基本的异常和错误处理类型。 如果初始请求超时或失败（导致 429 或 5xx 响应的任何请求），此策略将定义是否应重试操作。 默认情况下，所有操作都会以 20 秒间隔额外重试 4 次。 因此，如果第一个请求收到 `500 Internal Server Error` 响应，则工作流引擎会暂停 20 秒，然后再次尝试请求。 如果进行所有重试之后，响应仍然是异常或失败，则工作流会继续进行，并将操作状态标记为 `Failed`。

可以在特定操作的**输入**中配置重试策略。 例如，可将重试策略配置为每隔 1 小时尝试多达 4 次。 有关输入属性的完整详细信息，请参阅[工作流操作和触发器][retryPolicyMSDN]。

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

如果希望 HTTP 操作重试 4 次，并在每次尝试之间等待 10 分钟，可使用以下定义：

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

有关支持的语法的详细信息，请参阅[“工作流操作和触发器”中的“重试策略”部分][retryPolicyMSDN]。

## <a name="catch-failures-with-the-runafter-property"></a>使用 RunAfter 属性捕获失败

每个逻辑应用操作声明在该操作开始之前需要完成的操作，这类似于将工作流中的步骤排序。 在操作定义中，这种顺序称为 `runAfter` 属性。 此属性是一个描述执行的操作和操作状态的对象。 默认情况下，通过逻辑应用设计器添加的所有操作将设置为对上一步执行 `runAfter`（如果上一步是 `Succeeded`）。 但是，可以自定义此值以便在上一个操作是 `Failed`、`Skipped` 或这些值的可能集合时触发操作。 如果要在特定操作 `Insert_Row` 失败之后向指定服务总线主题添加项目，可使用以下 `runAfter` 配置：

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

请注意，`runAfter` 属性设置为在 `Insert_Row` 操作 `Failed` 时触发。 若要在操作状态是 `Succeeded`、`Failed` 或 `Skipped` 时运行操作，可使用以下语法：

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> 在前面的操作失败之后运行并成功完成的操作将标记为 `Succeeded`。 此行为意味着，如果成功捕获了工作流中的所有失败，则运行本身将标记为 `Succeeded`。

## <a name="scopes-and-results-to-evaluate-actions"></a>用于评估操作的范围和结果

与在各个操作之后运行的方式相似，还可以在某个[范围](../logic-apps/logic-apps-loops-and-scopes.md)（用作操作的逻辑分组）中将操作分组在一起。 范围可用于组织逻辑应用操作，以及对范围的状态执行聚合计算。 范围中的所有操作完成之后，范围本身将接收某个状态。 确定范围状态的条件与运行相同。 如果执行分支中的最后一个操作是 `Failed` 或 `Aborted`，则状态为 `Failed`。

若要对范围中发生的任何失败触发特定的操作，可对标记为 `Failed` 的范围使用 `runAfter`。 如果范围中的*任何*操作失败，在范围失败之后运行可以创建单个操作来捕获失败。

### <a name="getting-the-context-of-failures-with-results"></a>获取失败的上下文及结果

尽管从范围中捕获失败非常有用，但可能还需要借助上下文来确切了解失败的操作以及返回的任何错误或状态代码。 `@result()` 工作流函数提供有关范围中所有操作的结果的上下文。

`@result()` 采用单个参数、范围名称，并从该范围中返回所有操作结果的数组。 这些操作对象包含与 `@actions()` 对象相同的属性，包括操作开始时间、操作结束时间、操作状态、操作输入、操作相关 ID 和操作输出。 若要发送范围中失败的任何操作的上下文，可以轻松地将 `@result()` 函数与 `runAfter` 搭配使用。

若要对范围中状态为 `Failed` 的*每个*操作执行操作，或者将结果数组筛选为失败的操作，可将 `@result()` 与**[筛选数组](../connectors/connectors-native-query.md)**操作和 **[ForEach](../logic-apps/logic-apps-loops-and-scopes.md)** 循环搭配使用。 可以采用筛选的结果数组并使用 **ForEach** 循环对每个失败执行操作。 以下示例（后面随附了详细的说明）发送一个 HTTP POST 请求，其中包含范围 `My_Scope` 中失败的任何操作的响应正文。

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

下面是描述发生的情况的详细演练：

1. 为了获取 `My_Scope` 中所有操作的结果，**筛选数组**操作将筛选 `@result('My_Scope')`。

2. **筛选数组**的条件是状态等于 `Failed` 的任何 `@result()` 项。 此条件将 `My_Scope` 中所有操作结果的数组筛选为仅包含失败操作结果的数组。

3. 对**筛选数组**输出执行 **For Each** 操作。 此步骤针对前面筛选的*每个*失败操作结果执行操作。

    如果范围中只有一个操作失败，`foreach` 中的操作只运行一次。 
    如果失败的操作很多，将对每个失败执行一次操作。

4. 对 `foreach` 项目响应正文（或 `@item()['outputs']['body']`）发送 HTTP POST。 `@result()` 项目形状与 `@actions()` 形状相同，可以按相同方式进行分析。

5. 包括两个自定义标头，其中包含失败操作的名称 `@item()['name']` 和失败的运行客户端跟踪 ID `@item()['clientTrackingId']`。

下面提供了单个 `@result()` 项的示例供参考，其中显示 `name`、`body` 和 `clientTrackingId` 属性已在前面的示例进行分析。 在 `foreach` 外部，`@result()` 返回这些对象的数组。

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/folder-name/resource-name does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

若要执行不同的异常处理模式，可以使用前面所示的表达式。 可以选择在范围外部执行单个异常处理操作，它接受经过筛选的整个失败数组，并删除 `foreach`。 还可以包含前面所示 `@result()` 响应中的其他有用属性。

## <a name="azure-diagnostics-and-telemetry"></a>Azure 诊断和遥测

上述模式非常适合于处理运行中的错误和异常，不过也可以独立于运行本身来标识和响应错误。 
[Azure 诊断](../logic-apps/logic-apps-monitor-your-logic-apps.md)提供了一种简单方式，可将所有工作流事件（包括所有运行和操作状态）发送到 Azure 存储帐户或 Azure 事件中心。 若要评估运行状态，可以监视日志和指标，或将它们发布到偏好的任何监视工具中。 一种潜在选项是通过 Azure 事件中心将所有事件流式传输到[流分析](https://azure.microsoft.com/services/stream-analytics/)。 在流分析中，可以编写从诊断日志进行的任何异常、平均值或失败的实时查询。 流分析可以轻松输出到其他数据源，如队列、主题、SQL、Azure Cosmos DB 和 Power BI。

## <a name="next-steps"></a>后续步骤

* [了解如何使用 Azure 逻辑应用构建错误处理逻辑](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [查找更多逻辑应用示例和方案](../logic-apps/logic-apps-examples-and-scenarios.md)
* [了解如何创建逻辑应用的自动部署](../logic-apps/logic-apps-create-deploy-template.md)
* [使用 Visual Studio 构建和部署逻辑应用](logic-apps-deploy-from-vs.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9

