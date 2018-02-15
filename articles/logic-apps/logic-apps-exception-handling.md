---
title: "Azure 中逻辑应用的错误和异常处理 | Microsoft Docs"
description: "逻辑应用中的错误和异常处理模式。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: derek1ee
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: a74c7d18306359c9152f139299de1208b5932fe5
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2017
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>处理逻辑应用中的错误和异常

Azure 中的逻辑应用提供丰富的工具和模式，帮助确保你的集成是可靠的并且可从故障中复原。 任何集成体系结构都存在一个难题，即如何恰当处理依赖系统的停机时间或问题。 逻辑应用可提供一流的错误处理体验。 它提供了处理工作流中存在的异常和错误所需的工具。

## <a name="retry-policies"></a>重试策略

重试策略是最基本的异常和错误处理类型。 如果初始请求超时或失败（导致 429 或 5xx 响应的任何请求），一个重试策略将定义是否应重试操作以及如何重试。 

重试策略有四种类型：默认、无、固定时间间隔和指数式时间间隔。 如果工作流定义中未提供重试策略，则会使用服务定义的默认策略。 

可以在特定操作或触发器的*输入*中配置重试策略（如果可重试）。 同样，可在逻辑应用设计器中配置重试策略（如果适用）。 若要设置重试策略，请在逻辑应用设计器中转到特定操作的“设置”。

有关重试策略限制的详细信息，请参阅[逻辑应用的限制和配置](../logic-apps/logic-apps-limits-and-config.md)。 有关受支持语法的详细信息，请参阅[“工作流操作”和“触发器”中的“重试策略”部分][retryPolicyMSDN]。

### <a name="default"></a>默认

如果未定义重试策略（retryPolicy 是未定义的），则会使用默认策略。 默认策略是指数式时间间隔策略，它以缩放幅度为 7.5 秒的指数级增长间隔，最多发送 4 次重试。 此间隔限制在 5 到 45 秒之间。 此默认策略等效于以下示例 HTTP 工作流定义中的策略：

```json
"HTTP":
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7.5S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT45S"
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

### <a name="none"></a>无

如果“retryPolicy”设置为“none”，则不会重试失败的请求。

| 元素名称 | 必选 | Type | 说明 |
| ------------ | -------- | ---- | ----------- |
| type | 是 | String | **无** |

### <a name="fixed-interval"></a>固定间隔

如果“retryPolicy”设置为“fixed”，策略将在发送下一个请求之前等待所指定的时间间隔以重试失败的请求。

| 元素名称 | 必选 | Type | 说明 |
| ------------ | -------- | ---- | ----------- |
| type | 是 | String | **fixed** |
| 计数 | 是 | Integer | 重试的次数。 必须介于 1 和 90 之间。 |
| interval | 是 | String | 为 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)的重试时间间隔。 必须介于 PT5S 和 PT1D 之间。 |

### <a name="exponential-interval"></a>指数间隔

如果“retryPolicy”设置为“exponential”，此策略将在指数级增长范围中一个随机的时间间隔后重试失败的请求。 保证每次重试尝试以大于 **minimumInterval** 且小于 **maximumInterval** 的随机间隔发送。 下表中指示范围内的一个统一随机变量是为每次重试生成的（最大为 count）：

**随机变量范围**

| 重试次数 | 最小间隔 | 最大间隔 |
| ------------ |  ------------ |  ------------ |
| 1 | Max(0, **minimumInterval**) | Min(interval, **maximumInterval**) |
| #N/A | Max(interval, **minimumInterval**) | Min(2 * interval, **maximumInterval**) |
| 3 | Max(2 * interval, **minimumInterval**) | Min(4 * interval, **maximumInterval**) |
| 4 | Max(4 * interval, **minimumInterval**) | Min(8 * interval, **maximumInterval**) |
| ... |

对于指数类型策略，需要“count”和“interval”。 “minimumInterval”和“maximumInterval”的值都是可选的。 可以添加它们以分别替代默认值 PT5S 和 PT1D。

| 元素名称 | 必选 | Type | 说明 |
| ------------ | -------- | ---- | ----------- |
| type | 是 | String | **exponential** |
| 计数 | 是 | Integer | 重试的次数。 必须介于 1 和 90 之间。  |
| interval | 是 | String | 为 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)的重试时间间隔。 必须介于 PT5S 和 PT1D 之间。 |
| minimumInterval | 否 | String | 为 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)的重试最小时间间隔。 必须介于 PT5S 和“interval”之间。 |
| maximumInterval | 否 | String | 为 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)的重试最小时间间隔。 必须介于“interval”和 PT1D 之间。 |

## <a name="catch-failures-with-the-runafter-property"></a>使用 runAfter 属性捕获失败

每个逻辑应用操作声明在该操作开始之前必须要首先完成的操作。 这类似于在工作流中对步骤进行排序。 在操作定义中，这种顺序称为 runAfter 属性。 

runAfter 属性是一个描述执行的操作和操作状态的对象。 默认情况下，如果前一个步骤的结果为“Succeeded”，使用逻辑应用设计器添加的所有操作会设置为在此步骤后运行。 

但是，可以自定义 runAfter 值以在前面的操作结果为“Failed”、“Skipped”或可能为一组这些值时触发操作。 如果要在特定操作 Insert_Row 失败后向指定的 Azure 服务总线主题添加项，可使用以下 runAfter 配置：

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

请注意，如果 Insert_Row 操作结果为“Failed”，runAfter 则会设置为触发。 若要在操作状态为“Succeeded”、“Failed”或“Skipped”时运行该操作，可使用以下语法：

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> 在前一个操作失败后运行并成功完成的操作将被标记为“Succeeded”。 这意味着，如果成功捕获了一个工作流中的所有失败，则运行本身会被标记为“Succeeded”。

## <a name="scopes-and-results-to-evaluate-actions"></a>用于评估操作的范围和结果

可以将一个[范围](../logic-apps/logic-apps-loops-and-scopes.md)内的操作组合在一起，类似于在单个操作后的运行方式。 一个范围是操作的逻辑分组。 

范围可用于组织逻辑应用操作，以及对某个范围的状态执行聚合计算。 范围中的所有操作完成之后，范围本身将接收某个状态。 确定范围状态的条件与运行相同。 如果执行分支中的最后一个操作是“Failed”或“Aborted”，则状态为“Failed”。

若要针对范围内发生的任何失败触发特定操作，可对标记为“Failed”的范围使用 runAfter。 如果范围中的任何操作失败并且为一个范围使用 runAfter，则可以创建单个操作来捕获失败。

### <a name="get-the-context-of-failures-with-results"></a>获取失败的上下文及结果

尽管从范围中捕获失败非常有用，但可能还需要借助上下文来确切了解失败的操作以及返回的任何错误或状态代码。 @result() 工作流函数提供有关某个范围内所有操作的结果的上下文。

@result() 函数采用单个参数（范围名称），并从该范围内返回所有操作结果的数组。 这些操作对象包含与 @actions() 对象相同的属性，包括操作开始时间、操作结束时间、操作状态、操作输入、操作相关 ID 和操作输出。 

若要发送某个范围内任何失败的操作的上下文，可以轻松地将 @result() 函数与 runAfter 属性搭配使用。

若要对某个范围内结果为“Failed”的 for each 操作执行某个操作，或者将结果数组筛选为失败的操作，则可将 @result() 与 [Filter_array](../connectors/connectors-native-query.md) 操作和 [foreach](../logic-apps/logic-apps-loops-and-scopes.md) 循环搭配使用。 凭借筛选的结果数组，可以使用 foreach 循环对每个失败执行操作。 

以下是发送一个 HTTP POST 请求的示例，其中包含 My_Scope 范围中失败的任何操作的响应正文：

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

下面是描述上述示例中发生的情况的详细演练：

1. 为了获取 My_Scope 中所有操作的结果，Filter_array 操作将对 @result('My_Scope') 进行筛选。

2. Filter_array 的条件是状态等效于“Failed”的任何 @result() 项。 此条件从 My_Scope 将所有操作结果中的数组筛选为仅包含失败操作结果的数组。

3. 对筛选数组输出执行 foreach 操作。 此步骤针对前面筛选的*每个*失败操作结果执行操作。

    如果范围中的单个操作失败，foreach 中的操作则仅运行一次。 如果存在多个失败的操作，则将对每个失败执行一次操作。

4. 对 foreach 项响应正文或 @item()['outputs']['body'] 发送 HTTP POST。 @result() 项形状与 @actions() 形状相同。 可以通过相同的方式对它进行解析。

5. 包括两个自定义标头，其中包含失败操作的名称 @item()['name'] 和失败的运行客户端跟踪 ID @item()['clientTrackingId']。

以下是单个 @result() 项的示例以供参考。 其中显示了在前面的示例中分析的 name、body 和 clientTrackingId 属性。 在 foreach 操作外部，@result() 会返回这些对象的数组。

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

对于不同的异常处理模式，可以使用本文前面描述的表达式。 可以选择在范围外部执行单个异常处理操作，此操作接受经过筛选的整个失败数组，并删除 foreach。 如前面所述，还可以包含 @result() 响应中其他有用的属性。

## <a name="azure-diagnostics-and-telemetry"></a>Azure 诊断和遥测

本文中所述的模式非常适合于处理运行中的错误和异常，但你也可以独立于运行本身来标识和响应错误。 [Azure 诊断](../logic-apps/logic-apps-monitor-your-logic-apps.md)提供了一种简单方式，将所有工作流事件（包括所有运行和操作状态）发送到 Azure 存储帐户或 Azure 事件中心中的事件中心。 

若要评估运行状态，可以监视日志和指标，或将它们发布到首选的任何监视工具。 一种潜在选项是通过事件中心将所有事件流式传输到 [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)。 在流分析中，可以根据诊断日志中的任何异常、平均值或失败编写实时查询。 可以使用流分析将信息发送到其他数据源，例如发送到队列、主题、SQL、Azure Cosmos DB 或 Power BI。

## <a name="next-steps"></a>后续步骤

* 了解客户如何[在 Azure 中使用逻辑应用构建错误处理](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)。
* 查找更多[逻辑应用示例和方案](../logic-apps/logic-apps-examples-and-scenarios.md)。
* 了解如何创建[逻辑应用的自动部署](../logic-apps/logic-apps-create-deploy-template.md)。
* 了解如何[使用 Visual Studio 生成和部署逻辑应用](logic-apps-deploy-from-vs.md)。

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
