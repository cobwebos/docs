---
title: "逻辑应用异常处理 | Microsoft Docs"
description: "了解使用 Azure 逻辑应用进行错误和异常处理的模式"
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
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 04d1e26cd88fa6324ba2ea3cc64ae0819b47f3f9


---
# <a name="logic-apps-error-and-exception-handling"></a>逻辑应用错误和异常处理
逻辑应用提供一组丰富的工具和模式，用于帮助确保集成可靠并且可从故障中复原。  与任何集成体系结构有关的挑战之一是确保适当地处理依赖系统的故障时间或问题。  逻辑应用使错误处理成为卓越的体验，为你提供处理工作流中的异常和错误所需的工具。

## <a name="retry-policies"></a>重试策略
最基本的异常和错误处理类型是重试策略。  此策略定义在初始请求超时或失败（导致 429 或 5xx 响应的任何请求）时是否应重试操作。  默认情况下，所有操作都会以 20 秒间隔额外重试 4 次。  因此，如果第一个请求收到 `500 Internal Server Error` 响应，则工作流引擎会暂停 20 秒，然后再次尝试请求。  如果进行所有重试之后，响应仍然是异常或失败，则工作流会继续进行，并将操作状态标记为 `Failed`。

可以在特定操作的**输入**中配置重试策略。  重试策略可以配置为以 1 小时间隔尝试多达 4 次。  有关输入属性的完整详细信息可[在 MSDN 上找到][retryPolicyMSDN]。

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

如果你希望 HTTP 操作重试 4 次，并在每次尝试之间等待 10 分钟，则你会具有以下定义：

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

有关支持的语法的详细信息，请查看 [MSDN 上的重试策略部分][retryPolicyMSDN]。

## <a name="runafter-property-to-catch-failures"></a>用于捕获失败的 RunAfter 属性
每个逻辑应用操作都会声明在该操作开始之前需要完成的操作。  可以将此视为工作流中的步骤顺序。  此顺序称为操作定义中的 `runAfter` 属性。  它是一个对象，介绍会执行操作的操作和操作状态。  默认情况下，通过设计器添加的所有操作都设置为对上一步执行 `runAfter`（如果上一步是 `Succeeded`）。  但是，可以自定义此值以便在上一个操作是 `Failed`、`Skipped` 或这些值的可能集合时触发操作。  如果要在特定操作 `Insert_Row` 失败之后向指定服务中线主题添加项目，则会使用以下 `runAfter` 配置：

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

请注意，`runAfter` 属性设置为在 `Insert_Row` 操作 `Failed` 时触发。  若要在操作状态是 `Succeeded`、`Failed` 或 `Skipped` 时运行操作，语法会是：

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> 在前面的操作失败之后运行并且成功完成的操作会标记为 `Succeeded`。  此行为意味着，如果你成功捕获了工作流中的所有失败，则运行本身会标记为 `Succeeded`。
> 
> 

## <a name="scopes-and-results-to-evaluate-actions"></a>用于评估操作的范围和结果
与在各个操作之后运行的方式相似，你还可以在某个[范围](../logic-apps/logic-apps-loops-and-scopes.md)（用作操作的逻辑分组）中将操作分组在一起。  范围可用于组织逻辑应用操作，以及对范围的状态执行聚合计算。  范围中的所有操作都完成之后，范围本身会收到某个状态。  确定范围状态的条件与运行相同 -- 如果执行分支中的最后一个操作是 `Failed` 或 `Aborted`，则状态是 `Failed`。

可以对标记为 `Failed` 的范围执行 `runAfter`，以针对范围中发生的任何失败触发特定操作。  在范围失败之后运行使你可以创建单个操作以在范围中的任何操作失败时捕获失败。

### <a name="getting-the-context-of-failures-with-results"></a>获取失败的上下文及结果
从范围捕获失败非常有用，不过你可能还需要上下文来确切了解失败的操作以及返回的任何错误或状态代码。  `@result()` 工作流函数提供范围中所有操作的结果的上下文。

`@result()` 采用单个参数、范围名称，并从该范围中返回所有操作结果的数组。  这些操作对象包含与 `@actions()` 对象相同的属性，包括操作开始时间、操作结束时间、操作状态、操作输入、操作相关 ID 和操作输出。  可以轻松地将 `@result()` 函数与 `runAfter` 配对使用，以便发送范围中失败的任何操作的上下文。

如果要对范围中状态为 `Failed` 的每个操作执行操作，则可以将 `@result()` 与**[筛选数组](../connectors/connectors-native-query.md)**操作和 **[ForEach](../logic-apps/logic-apps-loops-and-scopes.md)** 循环配对使用。  这使你可以将结果数组筛选到失败的操作。  可以采用筛选的结果数组并使用 **ForEach** 循环对每个失败执行操作。  以下是一个示例，后跟详细说明。  此示例会发送一个 HTTP POST 请求，其中包含范围 `My_Scope` 中失败的任何操作的响应正文。

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

以下是发生的情况的详细演练：

1. **筛选数组**操作，用于筛选 `@result('My_Scope')` 以获取 `My_Scope` 中所有操作的结果
2. **筛选数组**的条件是状态等于 `Failed` 的任何 `@result()` 项目。  这会将 `My_Scope` 中所有操作结果的数组筛选为只是失败操作结果的数组。
3. 对**筛选数组**输出执行 **For Each** 操作。  这会为我们上面筛选的每个失败操作结果执行操作。
   * 如果范围中只有一个操作失败，则 `foreach` 中的操作只会运行一次。  许多失败操作会导致对每个失败执行一次操作。
4. 对 `foreach` 项目响应正文（或 `@item()['outputs']['body']`）发送 HTTP POST。  `@result()` 项目形状与 `@actions()` 形状相同，可以按相同方式进行分析。
5. 还包括两个自定义标头，具有失败操作名称 `@item()['name']` 和失败运行客户端跟踪 ID `@item()['clientTrackingId']`。

以下是单个 `@result()` 项目的示例以供参考。  可以看到 `name`、`body` 和 `clientTrackingId` 属性在上面的示例中进行了分析。  应注意的是，在 `foreach` 外部，`@result()` 返回这些对象的数组。

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
            "message": "/docs/foo/bar does not exist"
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

可以使用上面的表达式执行不同的异常处理模式。  可以选择在范围外部执行单个异常处理操作，它接受经过筛选的整个失败数组，并删除 `foreach`。  还可以包含来自上面显示的 `@result()` 响应的其他有用属性。

## <a name="azure-diagnostics-and-telemetry"></a>Azure 诊断和遥测
上面的模式非常适合于处理运行中的错误和异常，不过也可以独立于运行本身来标识和响应错误。  [Azure 诊断](../logic-apps/logic-apps-monitor-your-logic-apps.md)提供了一种简单方式，可将所有工作流事件（包括所有运行和操作状态）发送到 Azure 存储帐户或 Azure 事件中心。  可以监视日志和指标，或将它们发布到你喜欢的任何监视工具中，以评估运行状态。  一种潜在选项是通过 Azure 事件中心将所有事件流式传输到[流分析](https://azure.microsoft.com/services/stream-analytics/)。  在流分析中，可以编写从诊断日志进行的任何异常、平均值或失败的实时查询。  流分析可以方便地输出到其他数据源，如队列、主题、SQL、DocumentDB 和 Power BI。

## <a name="next-steps"></a>后续步骤
* [参阅客户如何使用逻辑应用构建可靠的错误处理](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [查找更多逻辑应用示例和方案](../logic-apps/logic-apps-examples-and-scenarios.md)
* [了解如何创建逻辑应用的自动部署](../logic-apps/logic-apps-create-deploy-template.md)
* [从 Visual Studio 设计和部署逻辑应用](logic-apps-deploy-from-vs.md)

<!-- References -->
[retryPolicyMSDN]: https://msdn.microsoft.com/library/azure/mt643939.aspx#Anchor_9



<!--HONumber=Jan17_HO3-->


