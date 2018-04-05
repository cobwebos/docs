---
title: Azure 中逻辑应用的错误和异常处理 | Microsoft Docs
description: 逻辑应用中的错误和异常处理模式。
services: logic-apps
documentationcenter: ''
author: dereklee
manager: anneta
editor: ''
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 01/31/2018
ms.author: deli; LADocs
ms.openlocfilehash: 70dd4e98dbffd9dac27752f0b4c2f5ce4ca70bdc
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>处理逻辑应用中的错误和异常

恰当地处理依赖系统的停机时间或问题对任何集成体系结构而言可能都是一个难题。 为了创建在发生问题或失败后可以复原的可靠集成，逻辑应用提供了用于处理错误和异常的一流体验。 

## <a name="retry-policies"></a>重试策略

对于最基本的异常和错误处理，可以使用重试策略。 如果初始请求超时或失败（这是导致 429 或 5xx 响应的任何请求），此策略定义了是否以及如何重试操作。 

重试策略有四种类型：默认、无、固定时间间隔和指数式时间间隔。 如果工作流定义中未提供重试策略，则会改用由服务定义的默认策略。

若要设置重试策略（如果适用），请打开你的逻辑应用的逻辑应用设计器，并转到你的逻辑应用中特定操作的“设置”。 另外，还可以在工作流定义中在 **inputs** 部分中为特定操作或触发器（如果可重试）定义重试策略。 下面是常规语法：

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

有关语法和 **inputs** 部分的详细信息，请参阅[工作流操作和触发器中的 retry-policy 部分][retryPolicyMSDN]。 有关重试策略限制的信息，请参阅[逻辑应用限制和配置](../logic-apps/logic-apps-limits-and-config.md)。 

### <a name="default"></a>默认

如果没有在 **retryPolicy** 部分中定义重试策略，则逻辑应用将使用默认策略，这是一个[指数时间间隔策略](#exponential-interval)，它按指数级增长的时间间隔（以 7.5 秒为增幅）发送最多四次重试。 时间间隔的范围为 5 到 45 秒。 此策略等效于以下示例 HTTP 工作流定义中的策略：

```json
"HTTP": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT1H"
        }
    },
    "runAfter": {}
}
```

### <a name="none"></a>无

如果将 **retryPolicy** 设置为 **none**，则此策略不重试失败的请求。

| 元素名称 | 必选 | Type | 说明 | 
| ------------ | -------- | ---- | ----------- | 
| type | 是 | String | **无** | 
||||| 

### <a name="fixed-interval"></a>固定间隔

如果将 **retryPolicy** 设置为 **fixed**，则此策略将等待指定的时间间隔，然后发送下一个请求来重试失败的请求。

| 元素名称 | 必选 | Type | 说明 |
| ------------ | -------- | ---- | ----------- |
| type | 是 | String | **fixed** |
| 计数 | 是 | Integer | 重试尝试次数，它必须介于 1 和 90 之间 | 
| interval | 是 | String | 采用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)的重试时间间隔，它必须介于 PT5S 和 PT1D 之间 | 
||||| 

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>指数间隔

如果将 **retryPolicy** 设置为 **exponential**，则此策略将在指数级增长范围中一个随机的时间间隔后重试失败的请求。 此策略还保证以大于 **minimumInterval** 且小于 **maximumInterval** 的随机时间间隔发送每次重试尝试。 指数策略还需要 **count** 和 **interval**，而 **minimumInterval** 和 **maximumInterval** 的值是可选的。 如果希望分别替代 PT5S 和 PT1D 默认值，则可以添加这些值。

| 元素名称 | 必选 | Type | 说明 |
| ------------ | -------- | ---- | ----------- |
| type | 是 | String | **exponential** |
| 计数 | 是 | Integer | 重试尝试次数，它必须介于 1 和 90 之间  |
| interval | 是 | String | 采用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)的重试时间间隔，它必须介于 PT5S 和 PT1D 之间。 |
| minimumInterval | 否 | String | 采用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)的重试最小时间间隔，它必须介于 PT5S 和 **interval** 之间 |
| maximumInterval | 否 | String | 采用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)的重试最小时间间隔，它必须介于 **interval** 和 PT1D 之间 | 
||||| 

下表展示了如何为每次重试生成指定范围内的一个统一随机变量（最大为 **count**）：

**随机变量范围**

| 重试次数 | 最小间隔 | 最大间隔 |
| ------------ | ---------------- | ---------------- |
| 1 | Max(0, **minimumInterval**) | Min(interval, **maximumInterval**) |
| 2 | Max(interval, **minimumInterval**) | Min(2 * interval, **maximumInterval**) |
| 3 | Max(2 * interval, **minimumInterval**) | Min(4 * interval, **maximumInterval**) |
| 4 | Max(4 * interval, **minimumInterval**) | Min(8 * interval, **maximumInterval**) |
| .... | | | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>使用 RunAfter 属性捕获并处理失败

每个逻辑应用操作都声明在该操作开始之前必须完成的操作，类似于在工作流中指定步骤顺序的情况。 在操作定义中，**runAfter** 属性定义此顺序，它是一个描述基于哪些操作和操作状态执行该操作的对象。

默认情况下，在逻辑应用设计器中添加的所有操作都设置为在上一步骤的结果为 **Succeeded** 时在上一步骤之后运行。 但是，你可以自定义 **runAfter** 值，使操作在上一个操作的结果为 **Failed**、**Skipped** 或这些值的组合时引发。 例如，若要在特定 **Insert_Row** 操作失败后向特定服务总线主题添加某个项，可使用以下示例 **runAfter** 定义：

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

**runAfter** 属性设置为在 **Insert_Row** 操作状态为 **Failed** 时运行。 若要在操作状态为“Succeeded”、“Failed”或“Skipped”时运行该操作，可使用以下语法：

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> 在上一个操作失败后运行并成功完成的操作将被标记为 **Succeeded**。 此行为意味着，如果成功捕获了工作流中的所有失败，则运行本身将被标记为 **Succeeded**。

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>评估具有作用域的操作及其结果

与通过 **runAfter** 属性在个别操作之后运行步骤类似，你可以将操作组合到[作用域](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)中。 如果希望以逻辑方式将各个操作组合在一起，可以使用作用域，评估作用域的聚合状态，并基于该状态执行操作。 当某个作用域中的所有操作都完成运行后，该作用域本身也确定了其自己的状态。 

若要检查作用域的状态，可以使用与用来检查逻辑应用运行状态（例如 **Succeeded**、**Failed**，等等）的条件相同的条件。 

默认情况下，当作用域的所有操作都成功时，作用域的状态将被标记为 **Succeeded**。 如果作用域中最后一个操作的状态为 **Failed** 或 **Aborted**，则作用域的状态被标记为 **Failed**。 

若要捕获状态为 **Failed** 的作用域中的异常并运行用来处理那些错误的操作，可以为该状态为 **Failed** 的作用域使用 **runAfter** 属性。 这样，如果作用域中的“任何”操作失败并且为该作用域使用了 **runAfter** 属性，则可以创建单个操作来捕获失败。

有关作用域的限制，请参阅[限制和配置](../logic-apps/logic-apps-limits-and-config.md)。

### <a name="get-context-and-results-for-failures"></a>获取失败的上下文和结果

尽管从作用域中捕获失败非常有用，但可能还需要借助上下文来确切了解失败的操作以及返回的任何错误或状态代码。 @result() 工作流函数提供有关某个范围内所有操作的结果的上下文。

**@result()** 函数采用单个参数（作用域名称），并返回该作用域内所有操作结果的数组。 这些操作对象包括与 **@actions()** 对象相同的属性，例如操作的开始时间、结束时间、状态、输入、相关 ID 和输出。 若要发送某个作用域内任何失败的操作的上下文，可以轻松地将 **@result()** 函数与 **runAfter** 属性搭配使用。

若要对某个作用域内结果为 **Failed** 的*每个*操作运行某个操作，或者将结果数组筛选为失败的操作，则可将 **@result()** 与**[筛选数组](../connectors/connectors-native-query.md)**操作以及 **[ForEach](../logic-apps/logic-apps-control-flow-loops.md)** 循环搭配使用。 可以采用筛选的结果数组并使用 **ForEach** 循环对每个失败执行操作。 

以下示例（后面随附了详细的说明）发送一个 HTTP POST 请求，其中包含作用域“My_Scope”中失败的任何操作的响应正文。

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

下面是描述此示例中发生的情况的详细演练：

1. 为了获取“My_Scope”中所有操作的结果，**Filter Array** 操作将对 **@result('My_Scope')** 进行筛选。

2. **筛选数组**的条件是状态等效于 **Failed** 的任何 **@result()** 项。 此条件将“My_Scope”中所有操作结果的数组筛选为仅包含失败操作结果的数组。

3. 对“筛选后的数组”输出执行 **For Each** 循环操作。 此步骤针对前面筛选的*每个*失败操作结果执行操作。

   如果范围中的单个操作失败，foreach 中的操作则仅运行一次。 
   如果存在多个失败的操作，则将对每个失败执行一次操作。

4. 对 **foreach** 项响应正文（这是 **@item()['outputs']['body']**）发送 HTTP POST。 **@result()** 项形状与 **@actions()** 形状相同，可以按相同方式进行分析。

5. 包括两个自定义标头，其中包含失败操作的名称 @item()['name'] 和失败的运行客户端跟踪 ID @item()['clientTrackingId']。

下面提供了单个 **@result()** 项的示例供参考，其中显示了在上一示例中分析过的 **name**、**body** 和 **clientTrackingId** 属性。 在 **foreach** 操作外部，**@result()** 返回这些对象的数组。

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

若要执行不同的异常处理模式，可以使用本文中前面所述的表达式。 可以选择在作用域外部执行单个异常处理操作，使此操作接受经过筛选的整个失败数组，并删除 **foreach** 操作。 如前面所述，还可以包含 **@result()** 响应中其他有用的属性。

## <a name="azure-diagnostics-and-telemetry"></a>Azure 诊断和遥测

上述模式非常适合于处理运行中的错误和异常，不过也可以独立于运行本身来标识和响应错误。 
[Azure 诊断](../logic-apps/logic-apps-monitor-your-logic-apps.md)提供了一种简单方式，用来将所有工作流事件（包括所有运行和操作状态）发送到 Azure 存储帐户或通过 Azure 事件中心创建的事件中心。 

要评估运行状态，可以监视日志和指标，或将它们发布到你习惯使用的任何监视工具中。 一种潜在选项是通过事件中心将所有事件流式传输到 [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)。 在流分析中，可以根据诊断日志中的任何异常、平均值或失败编写实时查询。 可以使用流分析将信息发送到其他数据源，例如队列、主题、SQL、Azure Cosmos DB 或 Power BI。

## <a name="next-steps"></a>后续步骤

* [了解如何使用 Azure 逻辑应用构建错误处理逻辑](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [查找更多逻辑应用示例和方案](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9