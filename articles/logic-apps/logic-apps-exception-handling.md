---
title: 错误和异常处理 - Azure 逻辑应用 | Microsoft Docs
description: 了解 Azure 逻辑应用中的错误和异常处理模式
services: logic-apps
ms.service: logic-apps
author: dereklee
ms.author: deli
manager: jeconnoc
ms.date: 01/31/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 7ce5c7007414bfe8e17727c25de9712e7993dc1e
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263746"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>在 Azure 逻辑应用中处理错误和异常

恰当地处理依赖系统造成的停机或问题对任何集成体系结构而言可能都是一个难题。 为了帮助创建可适当处理问题和故障的可靠、可复原集成，逻辑应用提供了用于处理错误和异常的一流体验。 

<a name="retry-policies"></a>

## <a name="retry-policies"></a>重试策略

对于最基本的异常和错误处理，可在支持的任何操作或触发器中使用重试策略。 重试策略指定当原始请求（导致 408、429 或 5xx 响应的任何请求）超时或失败时，操作或触发器是否且如何重试请求。 如果未使用任何其他重试策略，则使用默认策略。 

重试策略类型如下所示： 

| 类型 | 说明 | 
|------|-------------| 
| [默认](#default-retry) | 此策略可[按指数级增长](#exponential-retry)的间隔发送最多 4 次重试，增幅为 7.5 秒，但范围限定在 5 到 45 秒之间。 | 
| [指数式时间间隔](#exponential-retry)  | 此策略会等待从指数增长的范围中随机选定的时间间隔，然后再发送下一个请求。 | 
| [固定时间间隔](#fixed-retry)  | 此策略会等待指定的时间间隔，然后再发送下一个请求。 | 
| [无](#no-retry)  | 不重新发送请求。 | 
||| 

要了解重试策略限制，请参阅[逻辑应用限制和配置](../logic-apps/logic-apps-limits-and-config.md#request-limits)。 

### <a name="change-retry-policy"></a>更改重试策略

要选择其他重试策略，请执行以下步骤： 

1. 在逻辑应用设计器中打开逻辑应用。 

2. 打开操作或触发器的“设置”。

3. 如果操作或触发器支持重试策略，请在“重试策略”下选择所需类型。 

或者，可以在支持重试策略的操作或触发器的 `inputs` 部分指定重试策略。 如果不指定重试策略，该操作将使用默认策略。

```json
"<action-name>": {
   "type": "<action-type>", 
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximun-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

必需

| 值 | 类型 | 说明 |
|-------|------|-------------|
| <*retry-policy-type*> | String | 要使用的重试策略类型：“默认”、“无”、“固定”或“指数式” | 
| <*retry-interval*> | String | 其中值必须使用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)的重试时间间隔。 默认的最小时间间隔是 `PT5S`，而最大时间间隔是 `PT1D`。 如果使用指数式时间间隔策略，可更改最小值和最大值。 | 
| <*retry-attempts*> | Integer | 重试尝试次数，它必须介于 1 和 90 之间 | 
||||

可选

| 值 | 类型 | 说明 |
|-------|------|-------------|
| <*minimum-interval*> | String | 对于指数式时间间隔策略，是指随机选定的时间间隔的最小时间间隔（采用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)） | 
| <*maximum-interval*> | String | 对于指数式时间间隔策略，是指随机选定的时间间隔的最大时间间隔（采用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)） | 
|||| 

下面详细介绍不同的策略类型。

<a name="default-retry"></a>

### <a name="default"></a>默认

如果不指定重试策略，则操作将使用默认策略，它实际上是一个[指数式时间间隔策略](#exponential-interval)，按指数级增长的时间间隔（以 7.5 秒为增幅）最多发送 4 次重试。 时间间隔的范围为 5 到 45 秒。 

尽管未在操作或触发器中显式定义，但默认策略在示例 HTTP 操作中的行为方式如下所示：

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>无

要指定操作或触发器不重试失败的请求，请将 <retry-policy-type> 设置为 `none`。

### <a name="fixed-interval"></a>固定时间间隔

要指定操作或触发器在等待指定的时间间隔后再发送下一个请求，请将 <retry-policy-type> 设置为 `fixed`。

*示例*

该重试策略在首次请求失败后再尝试获取最新资讯两次，每次尝试之间延迟 30 秒：

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>指数式时间间隔

要指定操作或触发器在等待随机的时间间隔后再发送下一个请求，请将 <retry-policy-type> 设置为 `exponential`。 随机时间间隔选自呈指数增长的范围。 此外，可通过自行指定最小时间间隔和最大时间间隔替代默认的最小和最大时间间隔。

**随机变量范围**

下表展示了逻辑应用如何为每次重试生成指定范围内的一个统一随机变量（不超过重试次数）：

| 重试次数 | 最小时间间隔 | 最大时间间隔 |
|--------------|------------------|------------------|
| 1 | max(0, <minimum-interval>) | min(interval, <maximum-interval>) |
| 2 | max(interval, <minimum-interval>) | min(2 * interval, <maximum-interval>) |
| 3 | max(2 * interval, <minimum-interval>) | min(4 * interval, <maximum-interval>) |
| 4 | max(4 * interval, <minimum-interval>) | min(8 * interval, <maximum-interval>) |
| .... | .... | .... | 
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

尽管从作用域中捕获失败非常有用，但可能还需要借助上下文来确切了解失败的操作以及返回的任何错误或状态代码。 “@result()”表达式提供某范围内所有操作的结果的上下文。

“@result()”表达式采用单个参数（范围名称），并返回该范围内所有操作结果的数组。 这些操作对象包括与 **@actions()** 对象相同的属性，例如操作的开始时间、结束时间、状态、输入、相关 ID 和输出。 要发送某范围内任何失败的操作的上下文，可轻松地将 **@result()** 函数与 **runAfter** 属性搭配使用。

若要对某范围内结果为 Failed 的每个操作运行某操作，并且将结果数组的筛选范围缩小至失败的操作，可将 @result() 与[筛选数组](../connectors/connectors-native-query.md)操作和 [For Each](../logic-apps/logic-apps-control-flow-loops.md) 循环搭配使用。 可采用筛选的结果数组并使用 For Each 循环对每个失败执行操作。 

以下示例（后附详细说明）发送一个 HTTP POST 请求，其中包含范围内“My_Scope”中失败的任何操作的响应正文：

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

下面是描述此示例中发生的情况的详细演练：

1. 要获取“My_Scope”中所有操作的结果，筛选数组操作将使用筛选表达式“@result('My_Scope')”

2. 筛选数组的条件是状态等效于 Failed 的任何“@result()”项。 此条件将具有“My_Scope”中所有操作结果的数组筛选为仅包含失败操作结果的数组。

3. 对“筛选后的数组”输出执行 For Each 循环操作。 此步骤针对前面筛选的每个失败操作结果执行操作。

   如果范围中的单个操作失败，For Each 循环中的操作仅运行一次。 
   如果存在多个失败的操作，则将对每个失败执行一次操作。

4. 针对 For Each 项响应正文（即“@item()['outputs']['body']”表达式）发送 HTTP POST。 

   “@result()”项的形状与“@actions()”形状相同，可按相同的方式进行分析。

5. 包括两个自定义标头，其中包含失败操作的名称（“@item()['name']”）和失败的运行客户端跟踪 ID（“@item()['clientTrackingId']”）。

下面提供了一个“@result()”项的示例供参考，其中显示了在上一示例中分析过的 name、body 和 clientTrackingId 属性。 在 For Each 操作外部，“@result()”会返回这些对象的数组。

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

要执行不同的异常处理模式，可使用本文中前面所述的表达式。 可选择在范围外执行单个异常处理操作，使此操作接受筛选后的整个失败数组，然后再删除 For Each 操作。 如前面所述，还可包含 **@result()** 响应中其他有用的属性。

## <a name="azure-diagnostics-and-metrics"></a>Azure 诊断和指标

上述模式非常适合于处理运行中的错误和异常，不过也可以独立于运行本身来标识和响应错误。 
[Azure 诊断](../logic-apps/logic-apps-monitor-your-logic-apps.md)提供了一种简单方式，用来将所有工作流事件（包括所有运行和操作状态）发送到 Azure 存储帐户或通过 Azure 事件中心创建的事件中心。 

要评估运行状态，可以监视日志和指标，或将它们发布到你习惯使用的任何监视工具中。 一种潜在选项是通过事件中心将所有事件流式传输到 [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)。 在流分析中，可以根据诊断日志中的任何异常、平均值或失败编写实时查询。 可以使用流分析将信息发送到其他数据源，例如队列、主题、SQL、Azure Cosmos DB 或 Power BI。

## <a name="next-steps"></a>后续步骤

* [了解如何使用 Azure 逻辑应用构建错误处理逻辑](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [查找更多逻辑应用示例和方案](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9