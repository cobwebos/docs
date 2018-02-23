---
title: "工作流触发器和操作 - Azure 逻辑应用 | Microsoft Docs"
description: "深入了解如何在 Azure 逻辑应用中使用各种触发器和操作创建工作流和进程并使它们自动化"
services: logic-apps
author: MandiOhlinger
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/17/2016
ms.author: LADocs; mandia
ms.openlocfilehash: 981bf5555d1941509e787adf656fe6310dd43cb9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="triggers-and-actions-for-logic-app-workflows"></a>逻辑应用工作流触发器和操作

所有逻辑应用都是使用触发器启动的，然后再操作。 本文介绍如何使用各种触发器和操作创建系统集成并通过构建逻辑应用来使业务工作流或进程自动化。 
  
## <a name="triggers-overview"></a>触发器概述 

所有逻辑应用都是使用触发器启动的，触发器指定可启动逻辑应用运行的调用。 下面是启动工作流运行的两种方法：  

* 轮询触发器  
* 推送触发器 - 调用[工作流服务 REST API](https://docs.microsoft.com/rest/api/logic/workflows)  
  
所有触发器包含以下顶级元素：  
  
```json
"trigger-name": {
    "type": "trigger-type",
    "inputs": { call-settings },
    "recurrence": {  
        "frequency": "Second|Minute|Hour|Day|Week|Month",
        "interval": recurrence-interval-based-on-frequency
    },
    "conditions": [ array-of-required-conditions ],
    "splitOn": "property-used-for-creating-separate-workflows",
    "operationOptions": "operation-options-for-trigger"
}
```

### <a name="trigger-types-and-inputs"></a>触发器类型和输入  

每个触发器类型用于定义其行为的接口和输入都不同。 

| 触发器类型 | 说明 | 
| ------------ | ----------- | 
| **定期** | 根据定义的计划执行。 可以设置在将来某个日期和时间执行此触发器。 根据频率，还可指定运行工作流的次数和天数。 | 
| **请求**  | 使逻辑应用变成可调用的终结点，也称为“手动”触发器。 | 
| **HTTP** | 检查或轮询 HTTP Web 终结点。 HTTP 终结点必须符合特定的触发约定 - 使用“202”异步模式，或返回数组。 | 
| **ApiConnection** | 轮询类似于 HTTP 触发器，但使用 [Microsoft 托管 API](../connectors/apis-list.md)。 | 
| **HTTPWebhook** | 使逻辑应用变成可调用的终结点（类似于请求触发器），但调用指定的 URL 来执行注册和注销。 |
| **ApiConnectionWebhook** | 工作方式类似于 HTTPWebhook 触发器，但使用 Microsoft 托管 API。 | 
||| 

有关其他详细信息，请参阅[工作流定义语言](../logic-apps/logic-apps-workflow-definition-language.md)。 
  
## <a name="recurrence-trigger"></a>重复触发器  

此触发器的运行基于指定的重复周期和计划，借助它可轻松实现定期运行工作流。 下面是一个每天运行的基本重复触发器示例：

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```
还可以计划执行此触发器的启动日期和时间。 例如，要在每个星期一启动每周报告，可将逻辑应用计划为在特定星期一启动，示例如下： 

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime" : "2017-09-18T00:00:00Z"
    }
}
```

下面是此触发器的定义： 

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "second|minute|hour|day|week|month",
        "interval": recurrence-interval-based-on-frequency,
        "schedule": {
            // Applies only when frequency is Day or Week. Separate values with commas.
            "hours": [ one-or-more-hour-marks ], 
            // Applies only when frequency is Day or Week. Separate values with commas.
            "minutes": [ one-or-more-minute-marks ], 
            // Applies only when frequency is Week. Separate values with commas.
            "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
        },
        "startTime": "start-date-time-with-format-YYYY-MM-DDThh:mm:ss",
        "timeZone": "specify-time-zone"
    }
}
```

| 元素名称 | 必选 | Type | 说明 | 
| ------------ | -------- | ---- | ----------- | 
| 频率 | 是 | String | 触发器执行频率的时间单位。 只使用以下值之一：“second”、“minute”、“hour”、“day”、“week”或“month” | 
| interval | 是 | Integer | 一个正整数，描述工作流基于频率运行的频繁度。 <p>下面是最小和最大间隔： <p>- 月：1-16 个月 </br>- 天：1-500 天 </br>- 小时：1-12,000 小时 </br>- 分钟：1-72,000 分钟 </br>- Second：1-9,999,999 秒<p>例如，如果间隔为 6，频率为“month”，那么重复周期为每 6 个月。 | 
| timezone | 否 | String | 仅当指定启动时间时才适用，因为此触发器不接受 [UTC 时差](https://en.wikipedia.org/wiki/UTC_offset)。 指定要应用的时区。 | 
| startTime | 否 | String | 采用以下格式指定启动日期和时间： <p>如果指定时区，则为 YYYY-MM-DDThh:mm:ss <p>-或- <p>如果不指定时区，则为 YYYY-MM-DDThh:mm:ssZ <p>例如，如果需要 2017 年 9 月 18 日下午 2 点，则指定“2017-09-18T14:00:00”并指定时区（如“太平洋标准时间”）。 或者指定“2017-09-18T14:00:00Z”，无需指定时区。 <p>**注意：**此启动时间必须遵循 [UTC 日期时间格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)中的 [ISO 8601 日期时间规范](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，但没有 [UTC 时差](https://en.wikipedia.org/wiki/UTC_offset)。 如果未指定时区，则必须在末尾添加字母“Z”（无空格）。 这个“Z”指等效的[航海时间](https://en.wikipedia.org/wiki/Nautical_time)。 <p>对于简单计划，启动时间即第一次循环；而对于复杂计划，触发器不会在启动时间之前执行。 有关启动日期和时间的详细信息，请参阅[创建和计划定期运行任务](../connectors/connectors-native-recurrence.md)。 | 
| 工作日 | 否 | 字符串或字符串数组 | 如果为 `frequency` 指定“Week”，则可以指定一天或多天（用逗号分隔）作为运行工作流的时间：“Monday”、“Tuesday”、“Wednesday”、“Thursday”、“Friday”、“Saturday”和“Sunday” | 
| 小时 | 否 | 整数或整数数组 | 如果为 `frequency` 指定“Day”或“Week”，可以从 0 到 23 范围内指定一个或多个整数（用逗号分隔），作为一天中要运行工作流的时间点。 <p>例如，如果指定“10”、“12”和“14”，则会将上午 10 点、中午 12 点和下午 2 点作为小时标记。 | 
| 分钟数 | 否 | 整数或整数数组 | 如果为 `frequency` 指定“Day”或“Week”，可以从 0 到 59 范围内指定一个或多个整数（用逗号分隔），作为要运行工作流的分钟。 <p>例如，可指定“30”为分钟标记，使用前面示例中的时间点，则可得到上午 10:30、中午 12:30 和下午 2:30。 | 
|||||| 

例如，此重复触发器指定逻辑应用每周运行，运行时间为太平洋标准时间每个星期一上午 10:30、中午 12:30 和下午 2:30，启动时间不早于 2017 年 9 月 9 日下午 2 点：

``` json
{
    "triggers": {
        "myRecurrenceTrigger": {
            "type": "Recurrence",
            "recurrence": {
                "frequency": "Week",
                "interval": 1,
                "schedule": {
                    "hours": [
                        10,
                        12,
                        14
                    ],
                    "minutes": [
                        30
                    ],
                    "weekDays": [
                        "Monday"
                    ]
                },
               "startTime": "2017-09-07T14:00:00",
               "timeZone": "Pacific Standard Time"
            }
        }
    }
}
```

要深入了解有关此触发器的重复周期和启动时间示例，请参阅[创建和计划定期运行任务](../connectors/connectors-native-recurrence.md)。

## <a name="request-trigger"></a>请求触发器

此触发器充当终结点，可用于通过 HTTP 请求调用逻辑应用。 请求触发器如以下示例所示：  
  
```json
"myRequestTrigger": {
    "type": "Request",
    "kind": "Http",
    "inputs": {
        "schema": {
            "type": "Object",
            "properties": {
                "myInputProperty1": { "type" : "string" },
                "myInputProperty2": { "type" : "number" }
            },
            "required": [ "myInputProperty1" ]
        }
    }
} 
```

此触发器还有一个名为 schema 的可选属性：
  
| 元素名称 | 必选 | Type | 说明 |
| ------------ | -------- | ---- | ----------- |
| schema | 否 | 对象 | 用于验证传入请求的 JSON 架构。 可用于帮助后续工作流步骤了解要引用哪些属性。 | 
||||| 

若要调用此终结点，需要调用 *listCallbackUrl* API。 请参阅[工作流服务 REST API](https://docs.microsoft.com/rest/api/logic/workflows)。

## <a name="http-trigger"></a>HTTP 触发器  

HTTP 触发器将轮询指定的终结点并检查响应，确定是否应运行工作流。 `inputs` 对象采用构造 HTTP 调用所需的参数：  

| 元素名称 | 必选 | Type | 说明 | 
| ------------ | -------- | ---- | ----------- | 
| 方法 | 是 | String | 使用以下 HTTP 方法之一：“GET”、“POST”、“PUT”、“DELETE”、“PATCH”或“HEAD” | 
| uri | 是| String | 触发器检查的 HTTP 或 HTTPS 终结点。 最大字符串大小：2 KB | 
| 查询 | 否 | 对象 | 表示要包括在 URL 中的任何查询参数。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 将 `?api-version=2015-02-01` 添加到 URL。 | 
| headers | 否 | 对象 | 表示请求中发送的每个标头。 <p>例如，在请求中设置语言和类型： <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | 对象 | 表示发送到终结点的有效负载。 | 
| retryPolicy | 否 | 对象 | 此对象用于针对 4xx 或 5xx 错误自定义重试行为。 有关详细信息，请参阅[重试策略](../logic-apps/logic-apps-exception-handling.md)。 | 
| authentication | 否 | 对象 | 表示请求应使用的身份验证方法。 有关详细信息，请参阅[计划程序出站身份验证](../scheduler/scheduler-outbound-authentication.md)。 <p>除了计划程序以外，还有另一个受支持的属性：`authority`。 如果未指定此值，则它默认为 `https://login.windows.net`，但也可使用其他值，例如 `https://login.windows\-ppe.net`。 | 
||||| 
 
为了与逻辑应用正常配合工作，HTTP 触发器要求 HTTP API 符合特定的模式。 触发器可识别以下属性：  
  
| 响应 | 必选 | 说明 | 
| -------- | -------- | ----------- |  
| 状态代码 | 是 | 状态代码 200（“OK”）则运行。 其他任何状态代码均不能引发运行。 | 
| 重试间隔标头 | 否 | 逻辑应用再次轮询终结点之前所要经过的秒数。 | 
| Location 标头 | 否 | 在下一个轮询间隔要调用的 URL。 如果未指定，将使用原始 URL。 | 
|||| 

下面是不同类型请求的一些示例行为：
  
| 响应代码 | 重试间隔 | 行为 | 
| ------------- | ----------- | -------- | 
| 200 | {无} | 运行工作流，然后在定义的重复周期后再次检查是否有更多数据。 | 
| 200 | 10 秒 | 运行工作流，然后在 10 秒后再次检查是否有更多数据。 |  
| 202 | 60 秒 | 不会触发工作流。 下一次尝试将在一分钟后发生，也需要遵循定义的重复周期。 如果定义的重复周期不到一分钟，重试间隔标头优先。 否则，将使用定义的重复周期。 | 
| 400 | {无} | 错误的请求，不会运行工作流。 如果未定义 `retryPolicy`，将使用默认策略。 在达到重试次数后，触发器会在定义的重复周期后再次检查是否有数据。 | 
| 500 | {无}| 服务器错误，不会运行工作流。 如果未定义 `retryPolicy`，将使用默认策略。 在达到重试次数后，触发器会在定义的重复周期后再次检查是否有数据。 | 
|||| 

下面是 HTTP 触发器的输出： 
  
| 元素名称 | Type | 说明 |
| ------------ | ---- | ----------- |
| headers | 对象 | HTTP 响应的标头 | 
| body | 对象 | HTTP 响应的正文 | 
|||| 

## <a name="api-connection-trigger"></a>API 连接触发器  

API 连接触发器在基本功能上类似于 HTTP 触发器。 但是，用于标识操作的参数有所不同。 下面是一个示例：  
  
```json
"myDailyReportTrigger": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            }
        },
        "connection": {
            "name": "@parameters('$connections')['myconnection'].name"
        }
    },  
    "method": "POST",
    "body": {
        "category": "awesomest"
    }
}
```

| 元素名称 | 必选 | Type | 说明 | 
| ------------ | -------- | ---- | ----------- | 
| host | 是 | 对象 | API 应用的托管网关和 ID | 
| 方法 | 是 | String | 使用以下 HTTP 方法之一：“GET”、“POST”、“PUT”、“DELETE”、“PATCH”或“HEAD” | 
| 查询 | 否 | 对象 | 表示要包括在 URL 中的任何查询参数。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 将 `?api-version=2015-02-01` 添加到 URL。 | 
| headers | 否 | 对象 | 表示请求中发送的每个标头。 <p>例如，在请求中设置语言和类型： <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | 对象 | 表示发送到终结点的有效负载。 | 
| retryPolicy | 否 | 对象 | 此对象用于针对 4xx 或 5xx 错误自定义重试行为。 有关详细信息，请参阅[重试策略](../logic-apps/logic-apps-exception-handling.md)。 | 
| authentication | 否 | 对象 | 表示请求应使用的身份验证方法。 有关详细信息，请参阅[计划程序出站身份验证](../scheduler/scheduler-outbound-authentication.md)。 | 
||||| 

有关 `host` 对象，下面是一些属性：  
  
| 元素名称 | 必选 | 说明 | 
| ------------ | -------- | ----------- | 
| api runtimeUrl | 是 | 托管 API 的终结点 | 
| connection name |  | 工作流使用的托管 API 连接的名称。 必须引用名为 `$connection` 的参数。 |
|||| 

下面是 API 连接触发器的输出：
  
| 元素名称 | Type | 说明 |
| ------------ | ---- | ----------- |
| headers | 对象 | HTTP 响应的标头 | 
| body | 对象 | HTTP 响应的正文 | 
|||| 

详细了解[定价如何影响 API 连接触发器](../logic-apps/logic-apps-pricing.md#triggers)。

## <a name="httpwebhook-trigger"></a>HTTPWebhook 触发器  

HTTPWebhook 触发器与请求触发器一样会提供一个终结点，但是，HTTPWebhook 触发器还调用指定的 URL 来执行注册和注销。 下面是 HTTPWebhook 触发器形式的示例：  

```json
"myAppsSpotTrigger": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "https://pubsubhubbub.appspot.com/subscribe",
            "headers": { },
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": { },
            "retryPolicy": { }
        },
        "unsubscribe": {
            "url": "https://pubsubhubbub.appspot.com/subscribe",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "method": "POST",
            "authentication": { }
        }
    },
    "conditions": [ ]
}
```

其中的许多部分是可选的，HTTPWebhook 触发器行为取决于提供或省略了哪些部分。 下面是 HTTPWebhook 触发器的属性：
  
| 元素名称 | 必选 | 说明 | 
| ------------ | -------- | ----------- |  
| subscribe | 否 | 指定创建触发器以及触发器执行初始注册时要调用的传出请求。 | 
| unsubscribe | 否 | 指定删除触发器时要调用的传出请求。 | 
|||| 

可以像指定 [HTTP 异步限制](#asynchronous-limits)一样，针对 webhook 操作指定限制。 下面详细介绍了 `subscribe` 和 `unsubscribe` 操作：

* 因为调用了 `subscribe`，所以触发器可以开始侦听事件。 此传出调用是通过标准 HTTP 操作所用的相同参数启动的。 当工作流发生任何形式的更改时，都会发生此调用，例如，当滚动更新凭据，或者触发器的输入参数发生更改时。 
  
  为了支持此调用，`@listCallbackUrl()` 函数返回工作流中此特定触发器的唯一 URL。 此 URL 表示使用服务 REST API 的终结点的唯一标识符。
  
* 当操作使此触发器失效时，自动调用 `unsubscribe`，包括以下操作：

  * 删除或禁用触发器。 
  * 删除或禁用工作流。 
  * 删除或禁用订阅。 
  
  此函数的参数与 HTTP 触发器相同。

下面是 HTTPWebhook 触发器的输出和传入请求的内容：
  
| 元素名称 | Type | 说明 |
| ------------ | ---- | ----------- |
| headers | 对象 | HTTP 响应的标头 | 
| body | 对象 | HTTP 响应的正文 | 
|||| 

## <a name="conditions"></a>条件  

对于任何触发器，可以使用一个或多个条件来确定是否要运行工作流。 例如：  

```json
"myDailyReportTrigger": {
    "type": "Recurrence",
    "conditions": [ 
        {
            "expression": "@parameters('sendReports')"
        } 
    ],
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```

在本例中，仅当工作流的 `sendReports` 参数设置为 true 时，才会触发报告。 最后，条件可以引用触发器的状态代码。 例如，仅当网站返回状态代码 500 时启动工作流，例如：
  
``` json
"conditions": [ 
    {  
      "expression": "@equals(triggers().code, 'InternalServerError')"  
    }  
]  
```  
  
> [!NOTE]  
> 当任何表达式以任何方式引用触发器的状态代码时，将替换默认行为（仅当状态代码为 200“OK”时才触发）。 例如，如果希望在状态代码为 200 和 201 时均触发，必须包含 `@or(equals(triggers().code, 200),equals(triggers().code,201))` 作为条件。
  
## <a name="start-multiple-runs-for-a-request"></a>为一个请求启动多个运行

若要启动单个请求的多个运行，可以使用 `splitOn`。例如，要轮询可能在不同的轮询间隔期之间包含多个新项的终结点时。
  
使用 `splitOn` 可以在包含项数组的响应有效负载中指定属性，并使用其中的每个项来启动触发器的运行。 例如，假设某个 API 返回此响应：  
  
```json
{
    "status": "Succeeded",
    "rows": [
        {  
            "id" : 938109380,
            "name" : "myFirstRow"
        },
        {
            "id" : 938109381,
            "name" : "mySecondRow"
        }
    ]
}
```
  
逻辑应用只需要 `rows` 内容，因此，可以按以下示例所示构造触发器：  

```json
"mySplitterTrigger": {
    "type": "Http",
    "recurrence": {
        "frequency": "minute",
        "interval": 1
    },
    "intputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
    "splitOn": "@triggerBody()?.rows"
}
```
> [!NOTE]  
> 如果使用 `SplitOn` 命令，则无法获取数组外部的属性，因此对于本示例，无法获取 API 返回的响应中的 `status` 属性。
> 此外，本示例使用了 `?` 运算符，以免在不存在 `rows` 属性的情况下发生失败。 

因此在工作流定义中，`@triggerBody().name` 返回第一个运行的 `myFirstRow`，以及第二个运行的 `mySecondRow`。 触发器的输出如以下示例所示：  

```json
{
    "body": {
        "id": 938109380,
        "name": "mySecondRow"
    }
}
```

```json
{
    "body": {
        "id": 938109381,
        "name": "mySecondRow"
    }
}
```
  
## <a name="single-run-instance"></a>单个运行实例

可以配置重复触发器，使触发器仅在完成所有活动运行后才执行。 如果计划的重复发生在某个工作流实例正在运行时，触发器将跳过执行，并等到下一个计划的重复间隔才再次进行检查。
要配置此设置，请将 `operationOptions` 属性设置为 `singleInstance`：

```json
"triggers": {
    "myHTTPTrigger": {
        "type": "Http",
        "inputs": { ... },
        "recurrence": { ... },
        "operationOptions": "singleInstance"
    }
}
```

## <a name="actions-overview"></a>操作概述

有多种类型的操作，每种类型都有独特的行为。 每个操作类型用于定义操作行为的输入都不同。 集合操作的本身可能包含许多其他操作。 

### <a name="standard-actions"></a>标准操作  

| 操作类型 | 说明 | 
| ----------- | ----------- | 
| **HTTP** | 调用 HTTP Web 终结点。 | 
| **ApiConnection**  | 工作方式类似于 HTTP 操作，但使用 [Microsoft 托管 API](https://docs.microsoft.com/azure/connectors/apis-list)。 | 
| **ApiConnectionWebhook** | 工作方式类似于 HTTPWebhook，但使用 Microsoft 托管 API。 | 
| **响应** | 定义传入调用的响应。 | 
| **Function** | 表示 Azure 函数。 | 
| **Wait** | 等待固定的时间长度，或等到特定的时间。 | 
| **Workflow** | 表示嵌套的工作流。 | 
| **编制** | 从操作输入构造任意对象。 | 
| **查询** | 根据某个条件筛选数组。 | 
| **Select** | 将数组的每个元素投影到一个新值。 例如，可将数字数组转换为对象数组。 | 
| **表** | 将一个项数组转换为 CSV 或 HTML 表。 | 
| **Terminate** | 停止运行工作流。 | 
||| 

### <a name="collection-actions"></a>集合操作

| 操作类型 | 说明 | 
| ----------- | ----------- | 
| **条件** | 计算表达式并根据结果运行相应的分支。 | 
| **范围** | 用于对其他操作进行逻辑分组。 | 
| **ForEach** | 此循环操作循环访问数组并针对每个数组项执行内部操作。 | 
| **Until** | 此循环操作执行内部操作，直到条件结果为 true。 | 
||| 

## <a name="http-action"></a>HTTP 操作  

HTTP 操作调用指定的终结点并检查响应，确定是否应运行工作流。 例如：
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest"
    }
}
```

`inputs` 对象采用构造 HTTP 调用所需的参数： 

| 元素名称 | 必选 | Type | 说明 | 
| ------------ | -------- | ---- | ----------- | 
| 方法 | 是 | String | 使用以下 HTTP 方法之一：“GET”、“POST”、“PUT”、“DELETE”、“PATCH”或“HEAD” | 
| uri | 是| String | 触发器检查的 HTTP 或 HTTPS 终结点。 最大字符串大小：2 KB | 
| 查询 | 否 | 对象 | 表示要包括在 URL 中的任何查询参数。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 将 `?api-version=2015-02-01` 添加到 URL。 | 
| headers | 否 | 对象 | 表示请求中发送的每个标头。 <p>例如，在请求中设置语言和类型： <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | 对象 | 表示发送到终结点的有效负载。 | 
| retryPolicy | 否 | 对象 | 此对象用于针对 4xx 或 5xx 错误自定义重试行为。 有关详细信息，请参阅[重试策略](../logic-apps/logic-apps-exception-handling.md)。 | 
| operationsOptions | 否 | String | 定义要重写的特殊行为集。 | 
| authentication | 否 | 对象 | 表示请求应使用的身份验证方法。 有关详细信息，请参阅[计划程序出站身份验证](../scheduler/scheduler-outbound-authentication.md)。 <p>除了计划程序以外，还有另一个受支持的属性：`authority`。 如果未指定此值，则它默认为 `https://login.windows.net`，但也可使用其他值，例如 `https://login.windows\-ppe.net`。 | 
||||| 

此示例 HTTP 操作重试提取最新的新闻 2 次，如果发生间歇性失败，则总共执行 3 次，每次尝试之间延迟 30 秒：
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```

重试间隔以 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601)指定。 此间隔的最小值（也是默认值）为 20 秒，最大值为 1 小时。 最大重试计数（默认值）为 4 小时。 如果未指定重试策略定义，将为默认重试计数和间隔值使用 `fixed` 策略。 要禁用重试策略，请将其类型设置为 `None`。

### <a name="asynchronous-patterns"></a>异步模式

默认情况下，所有基于 HTTP 的操作都支持标准异步操作模式。 因此，如果远程服务器指示已接受待处理的请求并返回“202 ACCEPTED”响应，则逻辑应用引擎将不断轮询响应位置标头中指定的 URL，直到终端状态不是 202 响应为止。
  
要禁用上述异步行为，请在操作输入中将 `operationOptions` 设置为 `DisableAsyncPattern`。 在本例中，操作的输出取决于来自服务器的初始 202 响应。 例如：
  
```json
"invokeLongRunningOperationAction": {
    "type": "Http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```
<a name="asynchronous-limits"></a>

#### <a name="asynchronous-limits"></a>异步限制

可将异步模式的持续时间限制为特定的时间间隔。 如果该时间间隔结束但未达到终端状态，则操作状态将被标记为 `Cancelled` 并返回代码 `ActionTimedOut`。 限制超时以 ISO 8601 格式指定。 可以使用下面的方法指定限制：

``` json
"action-name": {
    "type": "Workflow|Webhook|Http|ApiConnectionWebhook|ApiConnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="apiconnection-action"></a>APIConnection 操作

APIConnection 操作引用 Microsoft 托管连接器。 此操作需要引用有效的连接以及 API 和参数的相关信息。
下面是示例 APIConnection 操作：

```json
"Send_Email": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "POST",
        "body": {
            "Subject": "New tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
}
```

| 元素名称 | 必选 | Type | 说明 | 
| ------------ | -------- | ---- | ----------- | 
| host | 是 | 对象 | 表示连接器信息，例如 `runtimeUrl` 以及对连接对象的引用。 | 
| 方法 | 是 | String | 使用以下 HTTP 方法之一：“GET”、“POST”、“PUT”、“DELETE”、“PATCH”或“HEAD” | 
| 路径 | 是 | String | API 操作的路径 | 
| 查询 | 否 | 对象 | 表示要包括在 URL 中的任何查询参数。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 将 `?api-version=2015-02-01` 添加到 URL。 | 
| headers | 否 | 对象 | 表示请求中发送的每个标头。 <p>例如，在请求中设置语言和类型： <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | 对象 | 表示发送到终结点的有效负载。 | 
| retryPolicy | 否 | 对象 | 此对象用于针对 4xx 或 5xx 错误自定义重试行为。 有关详细信息，请参阅[重试策略](../logic-apps/logic-apps-exception-handling.md)。 | 
| operationsOptions | 否 | String | 定义要重写的特殊行为集。 | 
| authentication | 否 | 对象 | 表示请求应使用的身份验证方法。 有关详细信息，请参阅[计划程序出站身份验证](../scheduler/scheduler-outbound-authentication.md)。 |
||||| 

## <a name="apiconnection-webhook-action"></a>APIConnection webhook 操作

APIConnectionWebhook 操作引用 Microsoft 托管连接器。 此操作需要引用有效的连接以及 API 和参数的相关信息。 可以像指定 [HTTP 异步限制](#asynchronous-limits)一样，针对 webhook 操作指定限制。

```json
"Send_approval_email": {
    "type": "ApiConnectionWebhook",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "body": {
            "Message": {
                "Subject": "Approval Request",
                "Options": "Approve, Reject",
                "Importance": "Normal",
                "To": "me@email.com"
            }
        },
        "path": "/approvalmail",
        "authentication": "@parameters('$authentication')"
    },
    "runAfter": {}
}
```

| 元素名称 | 必选 | Type | 说明 | 
| ------------ | -------- | ---- | ----------- | 
| host | 是 | 对象 | 表示连接器信息，例如 `runtimeUrl` 以及对连接对象的引用。 | 
| 路径 | 是 | String | API 操作的路径 | 
| 查询 | 否 | 对象 | 表示要包括在 URL 中的任何查询参数。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 将 `?api-version=2015-02-01` 添加到 URL。 | 
| headers | 否 | 对象 | 表示请求中发送的每个标头。 <p>例如，在请求中设置语言和类型： <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | 对象 | 表示发送到终结点的有效负载。 | 
| retryPolicy | 否 | 对象 | 此对象用于针对 4xx 或 5xx 错误自定义重试行为。 有关详细信息，请参阅[重试策略](../logic-apps/logic-apps-exception-handling.md)。 | 
| operationsOptions | 否 | String | 定义要重写的特殊行为集。 | 
| authentication | 否 | 对象 | 表示请求应使用的身份验证方法。 有关详细信息，请参阅[计划程序出站身份验证](../scheduler/scheduler-outbound-authentication.md)。 |
||||| 

## <a name="response-action"></a>Response 操作  

此操作包含 HTTP 请求中的整个响应有效负载，包括 `statusCode`、`body` 和 `headers`：
  
```json
"myResponseAction": {
    "type": "response",
    "inputs": {
        "statusCode": 200,
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        },
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        }
    },
    "runAfter": {}
}
```

response 操作包含一些不适用于其他操作的特殊限制，尤其是：  
  
* 不能在逻辑应用定义内的并行分支中执行 response 操作，因为传入请求需要确定性的响应。
  
* 如果工作流在传入请求已收到响应后到达 response 响应，该 response 操作会被视为失败或冲突。 因此，逻辑应用运行被标记为 `Failed`。
  
* 利用 response 操作的工作流不能在触发器定义中使用 `splitOn` 命令，因为该调用会创建多个运行。 因此，当工作流操作为 PUT 并返回“错误请求”响应时，应检查此情况。

## <a name="function-action"></a>函数操作   

借助此操作可表示和调用 [Azure 函数](../azure-functions/functions-overview.md)，例如：

```json
"my-Azure-Function-name": {
   "type": "Function",
    "inputs": {
        "function": {
            "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/sites/{your-Azure-function-app-name}/functions/{your-Azure-function-name}"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()"
        },
        "method": "POST",
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```
| 元素名称 | 必选 | Type | 说明 | 
| ------------ | -------- | ---- | ----------- |  
| 函数 ID | 是 | String | 要调用的 Azure 函数的资源 ID。 | 
| 方法 | 否 | String | 用于调用函数的 HTTP 方法。 如果未指定，则默认方法为“POST”。 | 
| 查询 | 否 | 对象 | 表示要包括在 URL 中的任何查询参数。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 将 `?api-version=2015-02-01` 添加到 URL。 | 
| headers | 否 | 对象 | 表示请求中发送的每个标头。 <p>例如，在请求中设置语言和类型： <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | 对象 | 表示发送到终结点的有效负载。 | 
|||||

保存逻辑应用时，Azure 逻辑应用会对所引用的函数执行检查：

* 必须具有该函数的访问权限。
* 仅允许使用标准 HTTP 触发器或泛型 JSON webhook 触发器。
* 函数不应定义任何路由。
* 仅允许“函数”和“匿名”授权级别。

在运行时检索、缓存和使用触发器 URL。 因此，如果任何操作使缓存的 URL 失效，则此操作会在运行时失败。 要解决此问题，请重新保存逻辑应用，这会导致逻辑应用重新检索并缓存触发器 URL。

## <a name="wait-action"></a>Wait 操作  

此操作将工作流的执行暂停指定的时间间隔。 下面的示例会导致工作流等待 15 分钟：
  
```json
"waitForFifteenMinutesAction": {
    "type": "Wait",
    "inputs": {
        "interval": {
            "unit": "minute",
            "count": 15
        }
    }
}
```
  
或者，若要等到特定的时间点，可以使用以下示例：
  
```json
"waitUntilOctoberAction": {
    "type": "Wait",
    "inputs": {
        "until": {
            "timestamp": "2017-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> 可以使用 `until` 对象或 `interval` 对象指定等待持续时间，但不能同时使用这两个对象来指定。
  
| 元素名称 | 必选 | Type | 说明 | 
| ------------ | -------- | ---- | ----------- | 
| until | 否 | 对象 | 基于时间点的等待持续时间 | 
| until timestamp | 是 | String | 等待时间失效的时间点（[UTC 日期时间格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)） | 
| interval | 否 | 对象 | 基于时间间隔单位和长度的等待持续时间 | 
| interval unit | 是 | String | 时间单位。 只使用以下值之一：“second”、“minute”、“hour”、“day”、“week”或“month” | 
| interval count | 是 | Integer | 一个正整数，表示等待持续时间所用的时间间隔单位数量 | 
||||| 

## <a name="workflow-action"></a>Workflow 操作   

此操作表示另一个工作流。 逻辑应用针对工作流（更具体地说，是触发器）执行访问检查，这意味着必须具有工作流的访问权限。

此操作的输出基于在子工作流的 `response` 操作中定义的内容。 如果未定义 `response` 操作，则输出为空。

```json
"myNestedWorkflowAction": {
    "type": "Workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName": "mytrigger001"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        },
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| 元素名称 | 必选 | Type | 说明 | 
| ------------ | -------- | ---- | ----------- |  
| host id | 是 | String| 要调用的工作流的资源 ID | 
| host triggerName | 是 | String | 要调用的触发器的名称 | 
| 查询 | 否 | 对象 | 表示要包括在 URL 中的任何查询参数。 <p>例如，`"queries": { "api-version": "2015-02-01" }` 将 `?api-version=2015-02-01` 添加到 URL。 | 
| headers | 否 | 对象 | 表示请求中发送的每个标头。 <p>例如，在请求中设置语言和类型： <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | 否 | 对象 | 表示发送到终结点的有效负载。 | 
|||||   

## <a name="compose-action"></a>Compose 操作

使用此操作可以构造任意对象，操作输入的计算结果即是输出。 

> [!NOTE]
> `Compose` 操作可用于构造任何输出，包括对象、数组，以及逻辑应用原生支持的其他任何类型，例如 XML 和二进制数据。

例如，可使用 compose 操作合并多个操作的输出：

```json
"composeUserRecordAction": {
    "type": "Compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
    }
}
```

## <a name="select-action"></a>选择操作

通过此操作，可将数组的每个元素投影到一个新值。
例如，要将数字数组转换为对象数组，可以使用：

```json
"selectNumbersAction": {
    "type": "Select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

| 名称 | 必选 | Type | 说明 | 
| ---- | -------- | ---- | ----------- | 
| from | 是 | Array | 源数组 |
| 选择 | 是 | 任意 | 应用到源数组中每个元素的投影 |
||||| 

`select` 操作的输出是一个数组，该数组具有与输入数组相同的基数。 每个元素按 `select` 属性定义的方式进行转换。 如果输入数组为空，则输出数组也为空。

## <a name="query-action"></a>Query 操作

使用此操作可以根据条件筛选数组。 下面的示例选择大于 2 的数字：

```json
"filterNumbersAction": {
    "type": "Query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

`query` 操作的输出是一个数组，其中包含输入数组中满足条件的元素。

> [!NOTE]
> 如果没有任何值满足 `where` 条件，则结果为空数组。

| 名称 | 必选 | Type | 说明 | 
| ---- | -------- | ---- | ----------- | 
| from | 是 | Array | 源数组 |
| 其中 | 是 | String | 应用到源数组中每个元素的条件 |
||||| 

## <a name="table-action"></a>表操作

使用此操作可将项数组转换为 CSV 或 HTML 表。 例如，假设使用下面的数组运行 `@triggerBody()`：

```json
[ 
    {
      "id": 0,
      "name": "apples"
    },
    {
      "id": 1, 
      "name": "oranges"
    }
]
```

按照下面的示例定义 table 操作：

```json
"convertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html"
    }
}
```

本示例的结果类似于下面的 HTML 表： 

<table><thead><tr><th>id</th><th>名称</th></tr></thead><tbody><tr><td>0</td><td>apples</td></tr><tr><td>1</td><td>oranges</td></tr></tbody></table>

要自定义此表，可显式指定列，例如：

```json
"ConvertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html",
        "columns": [ 
            {
                "header": "Produce ID",
                "value": "@item().id"
            },
            {
              "header": "Description",
              "value": "@concat('fresh ', item().name)"
            }
        ]
    }
}
```

本示例的结果类似于下面的 HTML 表： 

<table><thead><tr><th>Produce ID</th><th>说明</th></tr></thead><tbody><tr><td>0</td><td>fresh apples</td></tr><tr><td>1</td><td>fresh oranges</td></tr></tbody></table>

| 名称 | 必选 | Type | 说明 | 
| ---- | -------- | ---- | ----------- | 
| from | 是 | Array | 源数组。 如果 `from` 属性值为空数组，输出将为空表。 | 
| 格式 | 是 | String | 所需的表格式 - CSV 或 HTML | 
| 列 | 否 | Array | 所需的表列。 用于替代默认表形状。 | 
| column header | 否 | String | 列标头 | 
| column value | 是 | String | 列值 | 
||||| 

## <a name="terminate-action"></a>Terminate 操作

此操作停止工作流运行，取消正在进行的所有操作，并跳过所有剩余的操作。 terminate 操作不会影响任何已完成的操作。

例如，要终止状态为“Failed”的运行，可使用以下示例：

```json
"handleUnexpectedResponseAction": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response"
        }
    }
}
```

| 名称 | 必选 | Type | 说明 | 
| ---- | -------- | ---- | ----------- | 
| runStatus | 是 | String | 目标运行状态 - `Failed` 或 `Cancelled` |
| runError | 否 | 对象 | 错误详细信息。 仅当 `runStatus` 设置为 `Failed` 时才支持。 |
| runError 代码 | 否 | String | 运行错误代码 |
| runError 消息 | 否 | String | 运行错误消息 |
||||| 

## <a name="collection-actions-overview"></a>集合操作概述

某些操作的本身可能包含操作。 集合中的引用操作可以直接在集合外部引用。 例如，如果在 `scope` 中定义了 `Http`，那么 `@body('http')` 仍在工作流中的任意位置有效。 集合中的操作只能在同一集合中的其他操作之后运行 (`runAfter`)。

## <a name="condition-if-action"></a>条件：If 操作

使用此操作可以评估条件，并根据表达式计算结果是否为 `true` 来执行分支。 
  
```json
"myCondition": {
    "type": "If",
    "actions": {
        "if_true": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "else": {
        "actions": {
            "if_false": {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://myurl"
                },
                "runAfter": {}
            }
        }
    },
    "expression": "@equals(triggerBody(), json(true))",
    "runAfter": {}
}
``` 

| 名称 | 必选 | Type | 说明 | 
| ---- | -------- | ---- | ----------- | 
| actions | 是 | 对象 | `expression` 计算结果为 `true` 时要执行的内部操作 | 
| 表达式 | 是 | String | 要计算的表达式 |
| else | 否 | 对象 | `expression` 计算结果为 `false` 时要执行的内部操作 |
||||| 

如果条件评估成功，该条件将标记为 `Succeeded`。 `actions` 或 `else` 对象中的操作的评估如下： 

* 运行且成功则评估为 `Succeeded`
* 运行但失败则评估为 `Failed`
* 未运行各自的分支则评估为 `Skipped`

下面的示例显示条件如何在操作中使用表达式：
  
| JSON 值 | 结果 | 
| ---------- | -------| 
| `"expression": "@parameters('hasSpecialAction')"` | 计算为 true 的任何值都可使此条件得到满足。 仅支持布尔表达式。 要将其他类型转换为布尔值，请使用以下函数：`empty` 和 `equals` | 
| `"expression": "@greater(actions('act1').output.value, parameters('threshold'))"` | 支持比较函数。 在本示例中，仅当 `act1` 的输出大于阈值时才运行该操作。 | 
| `"expression": "@or(greater(actions('act1').output.value, parameters('threshold')), less(actions('act1').output.value, 100))"` | 支持使用逻辑函数创建嵌套的布尔表达式。 在本示例中，仅当 `act1` 的输出高于阈值或低于 100 时才运行该操作。 | 
| `"expression": "@equals(length(actions('act1').outputs.errors), 0))"` | 要检查某个数组是否包含任何项，可使用数组函数。 在本示例中，仅当 `errors` 数组为空时才运行该操作。 | 
| `"expression": "parameters('hasSpecialAction')"` | 错误，条件无效，因为条件需要 @。 |  
|||

## <a name="scope-action"></a>Scope 操作

使用此操作可对工作流中的操作进行逻辑分组。

```json
"myScope": {
    "type": "Scope",
    "actions": {
        "call_bing": {
            "type": "Http",
             "inputs": {
                "url": "http://www.bing.com"
            }
        }
    }
}
```

| 名称 | 必选 | Type | 说明 | 
| ---- | -------- | ---- | ----------- |  
| actions | 是 | 对象 | 要在范围中运行的内部操作 |
||||| 

## <a name="foreach-action"></a>ForEach 操作

此循环操作循环访问数组并针对每个数组项执行内部操作。 默认情况下，`foreach` 循环并行运行，并且一次可同时运行 20 个执行。 要设置执行规则，可使用 `operationOptions` 参数。

```json
"forEach_EmailAction": {
    "type": "Foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "Send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "runAfter": {
        "email_filter": [ "Succeeded" ]
    }
}
```

| 名称 | 必选 | Type | 说明 | 
| ---- | -------- | ---- | ----------- | 
| actions | 是 | 对象 | 要在循环中运行的内部操作 | 
| foreach | 是 | String | 要循环访问的数组 | 
| operationOptions | 否 | String | 指定用于自定义行为的任何操作选项。 目前仅支持使用 `Sequential` 按顺序运行迭代（默认行为是并行）。 |
||||| 

## <a name="until-action"></a>Until 操作

此循环操作运行内部操作，直到条件结果为 true。

```json
 "runUntilSucceededAction": {
    "type": "Until",
    "actions": {
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

| 名称 | 必选 | Type | 说明 | 
| ---- | -------- | ---- | ----------- | 
| actions | 是 | 对象 | 要在循环中运行的内部操作 | 
| 表达式 | 是 | String | 每次迭代后要计算的表达式 | 
| limit | 是 | 对象 | 循环的限制。 必须至少定义一个限制。 | 
| 计数 | 否 | Integer | 可执行的迭代数限制 | 
| timeout | 否 | String | 超时限制（[ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601)），指定循环应运行多长时间 |
||||| 

## <a name="next-steps"></a>后续步骤

* [工作流定义语言](../logic-apps/logic-apps-workflow-definition-language.md)
* [工作流 REST API](https://docs.microsoft.com/rest/api/logic/workflows)
