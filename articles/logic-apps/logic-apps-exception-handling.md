---
title: 处理工作流中的错误和异常
description: 了解如何处理使用 Azure 逻辑应用创建的自动化任务和工作流中发生的错误和异常
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.date: 01/11/2020
ms.topic: article
ms.openlocfilehash: 73b116117530e5a2103b604efbf757d691006508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284026"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>在 Azure 逻辑应用中处理错误和异常

恰当地处理依赖系统造成的停机或问题对任何集成体系结构而言可能都是一个难题。 为了帮助创建可适当处理问题和故障的可靠、可复原集成，逻辑应用提供了用于处理错误和异常的一流体验。

<a name="retry-policies"></a>

## <a name="retry-policies"></a>重试策略

对于最基本的异常和错误处理，可以在支持的任何操作或触发器中使用*重试策略*，例如，请参阅[HTTP 操作](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)。 重试策略指定当原始请求（导致 408、429 或 5xx 响应的任何请求）超时或失败时，操作或触发器是否且如何重试请求。 如果未使用任何其他重试策略，则使用默认策略。

重试策略类型如下所示：

| 类型 | 说明 |
|------|-------------|
| **默认** | 此策略可*按指数级增长*间隔发送最多 4 次重试，增幅为 7.5 秒，但范围限定在 5 到 45 秒之间。 |
| **指数间隔**  | 此策略会等待从指数增长的范围中随机选定的时间间隔，然后再发送下一个请求。 |
| **固定间隔**  | 此策略会等待指定的时间间隔，然后再发送下一个请求。 |
| **无**  | 不重新发送请求。 |
|||

要了解重试策略限制，请参阅[逻辑应用限制和配置](../logic-apps/logic-apps-limits-and-config.md#request-limits)。

### <a name="change-retry-policy"></a>更改重试策略

要选择其他重试策略，请执行以下步骤：

1. 在逻辑应用设计器中打开逻辑应用。

1. 打开操作或触发器的“设置”****。

1. 如果操作或触发器支持重试策略，请在“重试策略”下选择所需类型****。

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
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*必需*

| “值” | 类型 | 说明 |
|-------|------|-------------|
| <*重试策略类型*> | String | 要使用的重试策略类型：`default`、`none`、`fixed` 或 `exponential` |
| <*重试间隔*> | String | 其中值必须使用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)的重试时间间隔。 默认的最小时间间隔是 `PT5S`，而最大时间间隔是 `PT1D`。 如果使用指数式时间间隔策略，可更改最小值和最大值。 |
| <*重试尝试*> | Integer | 重试尝试次数，它必须介于 1 和 90 之间 |
||||

*可选*

| “值” | 类型 | 说明 |
|-------|------|-------------|
| <*最小间隔*> | String | 对于指数式时间间隔策略，是指随机选定的时间间隔的最小时间间隔（采用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)） |
| <*最大间隔*> | String | 对于指数式时间间隔策略，是指随机选定的时间间隔的最大时间间隔（采用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)） |
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

要指定操作或触发器不重试失败的请求，请将 <retry-policy-type> 设置为 `none`**。

### <a name="fixed-interval"></a>固定间隔

要指定操作或触发器在等待指定的时间间隔后再发送下一个请求，请将 <retry-policy-type> 设置为 `fixed`**。

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

### <a name="exponential-interval"></a>指数间隔

要指定操作或触发器在等待随机的时间间隔后再发送下一个请求，请将 <retry-policy-type> 设置为 `exponential`**。 随机时间间隔选自呈指数增长的范围。 此外，可通过自行指定最小时间间隔和最大时间间隔替代默认的最小和最大时间间隔。

**随机变量范围**

下表展示了逻辑应用如何为每次重试生成指定范围内的一个统一随机变量（不超过重试次数）：

| 重试次数 | 最小间隔 | 最大间隔 |
|--------------|------------------|------------------|
| 1 | 最大（0，<*最小间隔*>） | 最小值（间隔，<*最大间隔*>） |
| 2 | 最大（间隔，<*最小间隔*>） | 最小值（2 = 间隔，<*最大间隔*>） |
| 3 | 最大（2 = 间隔，<*最小间隔*>） | 最小值（4 = 间隔，<*最大间隔*>） |
| 4 | 最大（4 = 间隔，<*最小间隔*>） | 最小值（8 = 间隔，<*最大间隔*>） |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>通过更改"后运行"行为来捕获和处理故障

在逻辑应用设计器中添加操作时，将隐式声明用于运行这些操作的顺序。 操作完成`Succeeded`运行后，该操作将标有状态，如 、`Failed``Skipped`或`TimedOut`。 在每个操作定义中`runAfter`，属性指定必须先完成的前置任务操作以及该前置任务执行者可以运行之前允许的状态。 默认情况下，在设计器中添加的操作仅在前置任务完成状态`Succeeded`后运行。

当操作引发未处理的错误或异常时，将标记`Failed`该操作，并标记`Skipped`任何后续操作 。 如果对于具有并行分支的操作发生此行为，逻辑应用引擎将遵循其他分支来确定其完成状态。 例如，如果分支以操作`Skipped`结尾，则该分支的完成状态基于跳过的操作的前置状态。 逻辑应用运行完成后，引擎通过评估所有分支状态来确定整个运行的状态。 如果任何分支以失败告终，则对整个逻辑应用运行进行标记`Failed`。

![显示如何计算运行状态的示例](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

为了确保操作在其前身的状态如何仍可以运行，[请自定义操作的"后运行"行为](#customize-run-after)以处理前置任务的失败状态。

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>自定义"后运行"行为

您可以自定义操作的"后运行"行为，以便在前置任务的状态为`Succeeded`、、、、、`Failed``Skipped`或`TimedOut`任何这些状态时运行该操作。 例如，在为 Excel Online`Add_a_row_into_a_table`前置任务前置操作`Failed`进行标记后`Succeeded`发送电子邮件，而不是通过以下任一步骤更改"运行后"行为：

* 在设计视图中，选择椭圆 **（...）** 按钮，然后选择 **"配置运行后**运行"。

  ![为操作配置"运行后"行为](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  操作形状显示前置操作所需的默认状态，在此示例中，该状态是**将一行添加到表中**：

  ![操作的默认"运行后"行为](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  将"运行后运行"行为更改为所需的状态，**在此示例中失败：**

  ![将"运行后"行为更改为"已失败"](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  要指定操作是否将前置操作标记为`Failed`"`Skipped`或`TimedOut`"，请选择其他状态：

  ![将"运行后"行为更改为具有任何其他状态](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* 在代码视图中，在操作的 JSON 定义中，编辑`runAfter`遵循以下语法的属性：

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  在此示例中，将`runAfter`属性从`Succeeded`更改为`Failed`：

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  要指定操作是否将前置操作标记为`Failed`"`Skipped`或`TimedOut`"添加其他状态：：

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>评估具有作用域的操作及其结果

与使用`runAfter`属性执行单个操作后运行步骤类似，您可以在[作用域](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)内将操作组合在一起。 如果希望以逻辑方式将各个操作组合在一起，可以使用作用域，评估作用域的聚合状态，并基于该状态执行操作。 当某个作用域中的所有操作都完成运行后，该作用域本身也确定了其自己的状态。

要检查作用域的状态，可以使用用于检查逻辑应用的运行状态的相同条件，例如`Succeeded`、`Failed`等。

默认情况下，当作用域的所有操作成功时，将标记`Succeeded`作用域的状态。 如果作用域中的最后操作为`Failed`或`Aborted`，则将标记`Failed`作用域的状态。

要捕获`Failed`作用域中的异常并运行处理这些错误的操作，可以使用 该`runAfter``Failed`作用域的属性。 这样 *，如果作用域*中的任何操作失败，并且对该作用域使用`runAfter`该属性，则可以创建单个操作来捕获失败。

有关作用域的限制，请参阅[限制和配置](../logic-apps/logic-apps-limits-and-config.md)。

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>获取失败的上下文和结果

尽管从作用域中捕获失败非常有用，但可能还需要借助上下文来确切了解失败的操作以及返回的任何错误或状态代码。

函数[`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result)提供有关作用域中所有操作的结果的上下文。 `result()` 函数接受单个参数（即作用域的名称），并返回一个数组，其中包含该作用域中的所有操作结果。 这些操作对象包括与 `actions()` 对象相同的属性，例如操作的开始时间、结束时间、状态、输入、相关 ID 和输出。 若要发送作用域内失败的任何操作的上下文，可以轻松将 `@result()` 表达式与 `runAfter` 属性配对。

要对`Failed`具有结果的作用域中的每个操作运行操作，并将结果数组筛选为失败操作，`@result()`可以将表达式与[**筛选器数组**](logic-apps-perform-data-operations.md#filter-array-action)操作和 for[**每个**](../logic-apps/logic-apps-control-flow-loops.md)循环配对。 您可以采用筛选的结果数组，并使用`For_each`循环对每个失败执行操作。

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

1. 要获取“My_Scope”中所有操作的结果，**筛选数组**操作将使用筛选表达式：`@result('My_Scope')`

1. **筛选器数组**的条件是状态等于`@result()``Failed`的任何项。 此条件将具有“My_Scope”中所有操作结果的数组筛选为仅包含失败操作结果的数组。

1. 对`For_each`*筛选的阵列*输出执行循环操作。 此步骤针对前面筛选的每个失败操作结果执行操作。

   如果作用域中的单个操作失败，`For_each`则循环中的操作仅运行一次。 如果存在多个失败的操作，则将对每个失败执行一次操作。

1. 在`For_each`项目响应正文（表达式`@item()['outputs']['body']`）上发送 HTTP POST。

   `@result()` 项的形状与 `@actions()` 形状相同，可按相同的方式进行分析。

1. 包括两个自定义标头，其中包含失败操作的名称 (`@item()['name']`) 和失败的运行客户端跟踪 ID (`@item()['clientTrackingId']`)。

下面提供了单个 `@result()` 项的示例供参考，其中显示 `name`、`body` 和 `clientTrackingId` 属性已在前面的示例进行分析。 在`For_each`操作之外，`@result()`返回这些对象的数组。

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

若要执行不同的异常处理模式，可以使用本文中前面所述的表达式。 您可以选择在接受整个筛选的故障数组的范围之外执行单个异常处理操作，并删除该`For_each`操作。 您还可以包括响应中的其他有用属性，`\@result()`如前面所述。

## <a name="set-up-azure-monitor-logs"></a>设置 Azure Monitor 日志

上述模式非常适合于处理运行中的错误和异常，不过也可以独立于运行本身来标识和响应错误。 [Azure 监视器](../azure-monitor/overview.md)提供了一种将所有工作流事件（包括所有运行和操作状态）发送到[日志分析工作区](../azure-monitor/platform/data-platform-logs.md)[、Azure 存储帐户](../storage/blobs/storage-blobs-overview.md)或[Azure 事件中心](../event-hubs/event-hubs-about.md)的简单方法。

要评估运行状态，可以监视日志和指标，或将它们发布到你习惯使用的任何监视工具中。 一种潜在选项是通过事件中心将所有事件流式传输到 [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)。 在流分析中，可以根据诊断日志中的任何异常、平均值或失败编写实时查询。 可以使用流分析将信息发送到其他数据源，例如队列、主题、SQL、Azure Cosmos DB 或 Power BI。

## <a name="next-steps"></a>后续步骤

* [了解如何使用 Azure 逻辑应用构建错误处理逻辑](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [查找更多逻辑应用示例和方案](../logic-apps/logic-apps-examples-and-scenarios.md)
