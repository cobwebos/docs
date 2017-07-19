---
title: "工作流操作和触发器 - Azure 逻辑应用 | Microsoft Docs"
description: 
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: dd4e4282d774c2c336889b1df887806bfe512c10
ms.contentlocale: zh-cn
ms.lasthandoff: 06/08/2017

---

# <a name="workflow-actions-and-triggers-for-azure-logic-apps"></a>Azure 逻辑应用的工作流操作和触发器

逻辑应用由触发器和操作构成。 有六种类型的触发器。 每种类型有不同的接口和不同的行为。 也可以查看[工作流定义语言](logic-apps-workflow-definition-language.md)来了解其他详细信息。  
  
请继续阅读，详细了解触发器和操作，以及如何使用它们来构建逻辑应用以改进业务流程和工作流。  
  
### <a name="triggers"></a>触发器  

触发器指定可以启动逻辑应用工作流运行的调用。 下面是启动工作流运行的两种不同方法：  
  
-   轮询触发器  

-   推送触发器 - 通过调用 [工作流服务 REST API](https://docs.microsoft.com/rest/api/logic/workflows)  
  
所有触发器包含以下顶级元素：  
  
```json
"<name-of-the-trigger>" : {
    "type": "<type-of-trigger>",
    "inputs": { <settings-for-the-call> },
    "recurrence": {  
        "frequency": "Second|Minute|Hour|Week|Month|Year",
        "interval": "<recurrence interval in units of frequency>"
    },
    "conditions": [ <array-of-required-conditions > ],
    "splitOn" : "<property to create runs for>",
    "operationOptions": "<operation options on the trigger>"
}
```

### <a name="trigger-types-and-their-inputs"></a>触发器的类型及其输入  

可以使用以下触发器类型：
  
-   **请求** \- 使逻辑应用变成可调用的终结点  
  
-   **重复** \- 根据定义的计划触发  
  
-   **HTTP** \- 轮询 HTTP Web 终结点。 HTTP 终结点必须符合特定的触发约定 \- 使用 202 异步模式，或返回数组  
  
-   **ApiConnection** \- 类似于 HTTP 触发器的轮询，但是，它利用 [Microsoft 托管 API](https://docs.microsoft.com/azure/connectors/apis-list)  
  
-   **HTTPWebhook** \- 与手动触发器一样打开一个终结点，但是，它还调用指定的 URL 来执行注册和注销  
  
-   **ApiConnectionWebhook** \- 工作方式类似于 HTTPWebhook 触发器，利用 Microsoft 托管 API       
    每个触发器类型具有一组用于定义触发器行为的不同**输入**。  
  
## <a name="request-trigger"></a>请求触发器  

此触发器充当终结点，可以通过 HTTP 请求来调用它，从而调用逻辑应用。 请求触发器如以下示例所示：  
  
```json
"<name-of-the-trigger>" : {
    "type" : "request",
    "kind": "http",
    "inputs" : {
        "schema" : {
            "properties" : {
                "myInputProperty1" : { "type" : "string" },
                "myInputProperty2" : { "type" : "number" }
            },
        "required" : [ "myInputProperty1" ],
        "type" : "object"
        }
    }
} 
```

此外，还有一个称为**架构**的可选属性：  
  
|元素名称|必选|说明|  
|----------------|------------|---------------|  
|schema|否|用于验证传入请求的 JSON 架构。 可用于帮助后续工作流步骤了解要引用哪些属性。|

若要调用此终结点，需要调用 *listCallbackUrl* API。 请参阅[工作流服务 REST API](https://docs.microsoft.com/rest/api/logic/workflows)。  
  
## <a name="recurrence-trigger"></a>重复触发器  

重复执行触发器是根据定义的计划运行的触发器。 此类触发器如以下示例所示：  

```json
"dailyReport" : {
    "type": "recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": "1"
    }
}
```

可以看到，它是运行工作流的一种简单方式。  
  
|元素名称|必选|说明|  
|----------------|------------|---------------|  
|频率|是|触发器执行的频率。 只使用以下可能值之一：second、minute、hour、day、week、month 或 year|  
|interval|是|重复周期的给定频率的间隔|  
|startTime|否|如果在没有 UTC 时差的情况下提供 startTime，将使用此 timeZone。|  
|timezone|否|如果在没有 UTC 时差的情况下提供 startTime，将使用此 timeZone。|  
  
还可以将触发器计划在将来的某个时间点开始执行。 例如，如果想要在每个星期一启动每周报告，可创建以下触发器，将逻辑应用计划为在每个星期一启动：  

```json
"dailyReport" : {
    "type": "recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime" : "2015-06-22T00:00:00Z"
    }
}
```

## <a name="http-trigger"></a>HTTP 触发器  

HTTP 触发器将轮询指定的终结点并检查响应，确定是否应执行工作流。 inputs 对象采用构造 HTTP 调用所需的参数集：  
  
|元素名称|必选|说明|类型|  
|----------------|------------|---------------|--------|  
|方法|是|可为以下 HTTP 方法之一：GET、POST、PUT、DELETE、PATCH 或 HEAD|String|  
|uri|是|调用的 http 或 https 终结点。 最大为 2 KB。|String|  
|查询|否|表示要添加到 URL 的查询参数的对象。 例如，`"queries" : { "api-version": "2015-02-01" }` 将 `?api-version=2015-02-01` 添加到 URL。|对象|  
|headers|否|表示要发送到请求的每个标头的对象。 例如，若要在请求中设置语言和类型：`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|对象|  
|body|否|表示要发送到终结点的有效负载的对象。|对象|  
|retryPolicy|否|用于针对 4xx 或 5xx 错误自定义重试行为的对象。|对象|  
|authentication|否|表示应该对请求执行的身份验证方法。 有关此对象的详细信息，请参阅[计划程序出站身份验证](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication)。 除了计划程序以外，还有另一个受支持的属性：`authority`。如果未指定此值，则它默认为 `https://login.windows.net`，但你可以使用不同的受众，例如 `https://login.windows\-ppe.net`|对象|  
  
HTTP 触发器要求 HTTP API 符合特定的模式，这样它才能与逻辑应用正常配合工作。 必须填写以下字段：  
  
|响应|说明|  
|------------|---------------|  
|状态代码|状态代码为 200 \(OK\) 则运行。 其他任何状态代码均不能引发运行。|  
|Retry\-after 标头|逻辑应用再次轮询终结点之前所要经过的秒数。|  
|Location 标头|在下一个轮询间隔要调用的 URL。 如果未指定，将使用原始 URL。|  
  
下面是不同类型的请求的不同行为的一些示例：  
  
|响应代码|Retry\-After|行为|  
|-----------------|----------------|------------|  
|200|\(无\)|不是有效的触发器。必须指定 Retry\-After，否则引擎永远不会轮询下一个请求。|  
|202|60|不会触发工作流。 下一次尝试将在一分钟后发生。|  
|200|10|运行工作流，并在 10 秒后再次检查是否有其他内容。|  
|400|\(无\)|错误的请求，不会运行工作流。 如果未定义**重试策略**，将使用默认策略。 达到重试次数后，触发器将不再有效。|  
|500|\(无\)|服务器错误，不会运行工作流。  如果未定义**重试策略**，将使用默认策略。 达到重试次数后，触发器将不再有效。|  
  
HTTP 触发器的输出如以下示例所示：  
  
|元素名称|说明|类型|  
|----------------|---------------|--------|  
|headers|http 响应的标头。|对象|  
|body|http 响应的正文。|对象|  
  
## <a name="api-connection-trigger"></a>API 连接触发器  

API 连接触发器在基本功能上类似于 HTTP 触发器。 但是，用于标识操作的参数有所不同。 下面是一个示例：  
  
```json
"dailyReport" : {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            },
        }
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

|元素名称|必选|类型|说明|  
|----------------|------------|--------|---------------|  
|主机|是||ApiApp 托管的网关和 ID。|  
|方法|是|String|可为以下 HTTP 方法之一：**GET**、**POST**、**PUT**、**DELETE**、**PATCH** 或 **HEAD**|  
|查询|否|对象|表示要添加到 URL 的查询参数。 例如，`"queries" : { "api-version": "2015-02-01" }` 将 `?api-version=2015-02-01` 添加到 URL。|  
|headers|否|对象|表示要发送到请求的每个标头。 例如，若要在请求中设置语言和类型：`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|否|对象|表示要发送到终结点的有效负载。|  
|retryPolicy|否|对象|用于针对 4xx 或 5xx 错误自定义重试行为。|  
|authentication|否|对象|表示应该对请求执行的身份验证方法。 有关此对象的详细信息，请参阅[计划程序出站身份验证](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication)|  
  
主机的属性包括：  
  
|元素名称|必选|说明|  
|----------------|------------|---------------|  
|api runtimeUrl|是|托管 API 的终结点。|  
|connection name||必须是对名为 `$connection` 的参数的引用，并且是工作流使用的托管 API 连接的名称。|
  
API 连接触发器的输出为：
  
|元素名称|类型|说明|  
|----------------|--------|---------------|  
|headers|对象|http 响应的标头。|  
|body|对象|http 响应的正文。|  
  
## <a name="httpwebhook-trigger"></a>HTTPWebhook 触发器  

HTTPWebhook 触发器与手动触发器一样会打开一个终结点，但是，HTTPWebhook 触发器还调用指定的 URL 来执行注册和注销。 下面是 HTTPWebhook 触发器形式的示例：  

```json
"myappspottrigger": {
    "type": "httpWebhook",
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

其中的许多节是可选的，Webhook 的行为取决于提供或省略了哪些节。  
Webhook 的属性如下所示：  
  
|元素名称|必选|说明|  
|----------------|------------|---------------|  
|subscribe|否|创建触发器以及触发器执行初始注册时调用的传出请求。|  
|unsubscribe|否|删除触发器时调用的传出请求。|  
  
-   **Subscribe** 是开始侦听事件时发出的传出调用。 此调用是使用普通 HTTP 操作所用的同一组参数启动的。 每当工作流发生任何形式的更改时，都会发出此传出调用，例如，每当滚动更新凭据，或者触发器的输入参数发生更改时。
  
    若要支持此调用，可使用以下新函数：`@listCallbackUrl()`。 此函数返回此工作流中此特定触发器的唯一 URL。 此 URL 表示使用服务 REST 的终结点的唯一标识符。  
  
-   **Unsubscribe** 在操作使此触发器失效时调用，包括：  
  
    -   删除或禁用触发器时  
  
    -   删除或禁用工作流时  
  
    -   删除或禁用订阅时  
  
    逻辑应用将自动调用取消订阅操作。 此函数的参数与 HTTP 触发器相同。  
  
    HTTPWebhook 触发器的输出为传入请求的内容：  
  
|元素名称|类型|说明|  
|-----------------|--------|---------------|  
|headers|对象|http 请求的标头。|  
|body|对象|http 请求的正文。|  

可以像指定 [HTTP 异步限制](#asynchronous-limits)一样，针对 Webhook 操作指定限制。
  

## <a name="conditions"></a>条件  

对于任何触发器，可以使用一个或多个条件来确定是否要运行工作流。 例如：  

```json
"dailyReport" : {
    "type": "recurrence",
    "conditions": [ {
        "expression": "@parameters('sendReports')"
    } ],
    "recurrence": {
        "frequency": "Day",
        "interval": "1"
    }
}
```

在本例中，仅当工作流的 `sendReports` 参数设置为 true 时，才会触发报告。 最后，条件可以引用触发器的状态代码。 例如，可以做到仅当网站返回状态代码 500 时才启动某个工作流，如下所示：
  
```  
"conditions": [  
        {  
          "expression": "@equals(triggers().code, 'InternalServerError')"  
        }  
      ]  
```  
  
> [!NOTE]  
> 当任何表达式\(以任何方式\)引用触发器的状态代码时，将替换默认行为“仅当状态代码为 200 \(OK\) 时才触发”。\(\) 例如，如果希望在状态代码为 200 和 201 时均触发，必须包含 `@or(equals(triggers().code, 200),equals(triggers().code,201))` 作为条件。  
  
## <a name="start-multiple-runs-for-a-request"></a>为一个请求启动多个运行

若要启动单个请求的多个运行，可以使用 `splitOn`。例如，要轮询可能在不同的轮询间隔期之间包含多个新项的终结点时。
  
使用 `splitOn` 可以在包含项数组的响应有效负载中指定属性，并使用其中的每个项来启动触发器的运行。 例如，假设某个 API 返回以下响应：  
  
```json
{
    "Status" : "success",
    "Rows" : [
        {  
            "id" : 938109380,
            "name" : "mycoolrow"
        },
        {
            "id" : 938109381,
            "name" : "another row"
        }
    ]
}
```
  
逻辑应用只需要 Rows 的内容，因此，你可以按以下示例所示构造触发器：  
  
```json
"mysplitter" : {
    "type" : "http",
    "recurrence": {
        "frequency": "Minute",
        "interval": "1"
    },
    "intputs" : {
        "uri" : "https://mydomain.com/myAPI",
        "method" : "GET"
    },
    "splitOn" : "@triggerBody()?.Rows"
}
```
  
然后，在工作流定义中，`@triggerBody().name` 将返回第一个运行的 `mycoolrow`，以及第二个运行的 `another row`。 触发器的输出如以下示例所示：  
  
```json
{
    "body" : {
        "id" : 938109381,
        "name" : "another row"
    }
}
```

因此，如果使用 `SplitOn`，则无法获取数组外部的属性（在本例中为 `Status` 字段）。  
  
> [!NOTE]  
> 本示例使用了 `?` 运算符，以免在未提供 `Rows` 属性的情况下发生失败。 
  
## <a name="single-run-instance"></a>单个运行实例

可以通过配置触发器的重复属性，使其仅在已完成所有活动的运行时才激发触发器。 如果计划的重复出现在某个运行正在进行时，触发器将跳过执行，并等到下一个计划的重复间隔才再次进行检查。

可以通过操作选项配置此设置：

```json
"triggers": {
    "mytrigger": {
        "type": "http",
        "inputs": { ... },
        "recurrence": { ... },
        "operationOptions": "singleInstance"
    }
}
```

## <a name="types-and-inputs"></a>类型和输入  

有多种类型的操作，每种类型都有独特的行为。 集合操作可在自身内部包含其他许多操作。

### <a name="standard-actions"></a>标准操作  

-   **HTTP** - 此操作调用 HTTP Web 终结点。  
  
-   **ApiConnection** \- 此操作的行为类似于 HTTP 操作，但使用 Microsoft 托管 API。  
  
-   **ApiConnectionWebhook** \- 类似于 HTTPWebhook，但使用 Microsoft 托管 API。  
  
-   **Response** \- 此操作定义传入调用的响应。  
  
-   **Wait** \- 这个简单的操作会等待固定的时间长度，或等到特定的时间。  
  
-   **Workflow** \- 此操作表示嵌套的工作流。  

-   **Function** \- 此操作表示 Azure 函数。

### <a name="collection-actions"></a>集合操作

-   **Scope** \- 此操作是其他操作的逻辑分组。

-   **Condition** \- 此操作计算表达式并执行相应的结果分支。

-   **ForEach** \- 此循环操作循环访问数组并针对每个项执行内部操作。

-   **Until** \- 此循环操作执行内部操作，直到条件结果为 true。
  
每种类型的操作都有一组用于定义操作行为的不同**输入**。  
  
## <a name="http-action"></a>HTTP 操作  

HTTP 操作调用指定的终结点并检查响应，确定是否应运行工作流。 **inputs** 对象采用构造 HTTP 调用所需的参数集：  
  
|元素名称|必选|类型|说明|  
|----------------|------------|--------|---------------|  
|方法|是|String|可为以下 HTTP 方法之一：**GET**、**POST**、**PUT**、**DELETE**、**PATCH** 或 **HEAD**|  
|uri|是|String|调用的 http 或 https 终结点。 最大长度为 2 KB。|  
|查询|否|对象|表示要添加到 URL 的查询参数。 例如，`"queries" : { "api-version": "2015-02-01" }` 将 `?api-version=2015-02-01` 添加到 URL。|  
|headers|否|对象|表示要发送到请求的每个标头。 例如，若要在请求中设置语言和类型：`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|否|对象|表示要发送到终结点的有效负载。|  
|retryPolicy|否|对象|用于针对 4xx 或 5xx 错误自定义重试行为。|  
|operationsOptions|否|String|定义要重写的特殊行为集。|  
|authentication|否|对象|表示应该对请求执行的身份验证方法。 有关此对象的详细信息，请参阅[计划程序出站身份验证](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication)。 除了计划程序以外，还有另一个受支持的属性：`authority`。 如果未指定此值，则它默认为 `https://login.windows.net`，但你可以使用不同的受众，例如 `https://login.windows\-ppe.net`|  
  
HTTP 操作\(和 API 连接\)操作支持重试策略。 重试策略适用于具有 HTTP 状态代码 408、429 和 5xx 特征的间歇性失败，以及任何连接异常。 此策略是使用定义的 *retryPolicy* 对象描述的，如下所示：
  
```json
"retryPolicy" : {
    "type": "<type-of-retry-policy>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```
  
重试间隔以 ISO 8601 格式指定。 此间隔的最小值（也是默认值）为 20 秒，最大值为 1 小时。 最大重试计数（默认值）为 4 小时。 如果未指定重试策略定义，将为默认重试计数和间隔值使用 `fixed` 策略。 若要禁用重试策略，请将其类型设置为 `None`。  
  
例如，以下操作重试提取最新的新闻两次，如果发生间歇性失败，则总共执行三次，每次尝试之前延迟 30 秒：  
  
```json
"latestNews" : {
    "type": "http",
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
### <a name="asynchronous-patterns"></a>异步模式

默认情况下，所有基于 HTTP 的操作都支持标准异步操作模式。 因此，如果远程服务器指示已接受待处理的请求并返回 202 \(Accepted\) 响应，则逻辑应用程序引擎将不断轮询响应 location 标头中指定的 URL，直到终端状态\(不是 202 响应\)为止。  
  
若要禁用上述异步行为，请在操作输入中设置 `DisableAsyncPattern` 选项。 在本例中，操作的输出取决于来自服务器的初始 202 响应。  
  
```json
"invokeLongRunningOperation" : {
    "type": "http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```

#### <a name="asynchronous-limits"></a>异步限制

可将异步模式的持续时间限制为特定的时间间隔。  如果该时间间隔结束但未达到终端状态，系统会将操作状态标记为 `Cancelled` 并返回代码 `ActionTimedOut`。  限制超时以 ISO 8601 格式指定。  可使用以下语法指定限制：

``` json
"<action-name>": {
    "type": "workflow|webhook|http|apiconnectionwebhook|apiconnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="api-connection"></a>API 连接  

API 连接是引用 Microsoft 托管连接器的操作。
此操作需要引用有效的连接，以及有关 API 和所需参数的信息。

|元素名称|必选|类型|说明|  
|----------------|------------|--------|---------------|  
|主机|是|对象|表示连接器信息，例如 runtimeUrl 以及对连接对象的引用|
|方法|是|String|可为以下 HTTP 方法之一：**GET**、**POST**、**PUT**、**DELETE**、**PATCH** 或 **HEAD**|  
|path|是|String|API 操作的路径。|  
|查询|否|对象|表示要添加到 URL 的查询参数。 例如，`"queries" : { "api-version": "2015-02-01" }` 将 `?api-version=2015-02-01` 添加到 URL。|  
|headers|否|对象|表示要发送到请求的每个标头。 例如，若要在请求中设置语言和类型：`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|否|对象|表示要发送到终结点的有效负载。|  
|retryPolicy|否|对象|用于针对 4xx 或 5xx 错误自定义重试行为。|  
|operationsOptions|否|String|定义要重写的特殊行为集。|  

```json
"Send_Email": {
    "type": "apiconnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "post",
        "body": {
            "Subject": "New Tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
    }
```

## <a name="api-connection-webhook-action"></a>API 连接 Webhook 操作

```json
"Send_approval_email": {
    "type": "apiconnectionwebhook",
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

可以像指定 [HTTP 异步限制](#asynchronous-limits)一样，针对 Webhook 操作指定限制。
  
## <a name="response-action"></a>Response 操作  

此操作类型包含 HTTP 请求中的整个响应有效负载，包括 statusCode、正文和标头：  
  
```json
"myresponse" : {
    "type" : "response",
    "inputs" : {
        "statusCode" : 200,
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        }
    },
    "runAfter": {}
}
```
  
response 操作包含一些不适用于其他操作的特殊限制。 具体而言：  
  
-   Response 操作不能在定义中并存，因为对于传入的请求必须提供确定性的响应。  
  
-   如果 response 操作出现在传入的请求收到响应后，该操作将被视为失败\(冲突\)，运行将因此`Failed`。  
  
-   使用 Response 操作的工作流不能在其触发器中包含 `splitOn`，因为一次调用会导致多次运行。 因此，当流量为 PUT 并导致“错误的请求”时，应验证此工作流。  
  
## <a name="wait-action"></a>Wait 操作  

`wait` 操作将工作流的执行暂停指定的时间间隔。 例如，若要等待 15 分钟，可以使用以下代码片段：  
  
```json
"waitForFifteenMinutes" : {
    "type": "wait",
    "inputs": {
        "interval": {
            "unit" : "minute",
            "count" : 15
        }
    }
}
```  
  
或者，若要等到特定的时间点，可以使用以下示例：  
  
```json
"waitUntilOctober" : {
    "type": "wait",
    "inputs": {
        "until": {
            "timestamp" : "2016-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> 可以使用 **interval** 对象或 **until** 对象指定等待持续时间，但不能同时使用这两个对象来指定。  
  
|Name|必选|类型|说明|  
|--------|------------|--------|---------------|  
|interval|否|对象|基于时间长度的等待持续时间。|  
|interval unit|是|String|以下间隔之一：second、minute、hour、day、week、month 或 year。|  
|interval count|是|String|基于给定内部单位的持续时间。|  
|until|否|对象|基于时间点的等待持续时间。|  
|until timestamp|是|String|字符串&#124;等待时间失效的 UTC 时间点。|  

## <a name="query-action"></a>Query 操作

使用 `query` 操作可以根据条件筛选数组。 例如，若要选择大于 2 的数字，可以使用：

```json
"FilterNumbers" : {
    "type": "query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

`query` 操作的输出是一个数组，其中包含输入数组中满足条件的元素。

> [!NOTE]
> 如果没有任何值满足 `where` 条件，则结果为空数组。

|Name|必选|类型|说明|
|--------|------------|--------|---------------|
|from|是|Array|源数组。|
|其中|是|String|要应用到源数组的每个元素的条件。|

## <a name="select-action"></a>选择操作

通过 `select` 操作，可以将数组的每个元素投影到一个新值。
例如，若要将数字数组转换为对象数组，可以使用：

```json
"SelectNumbers" : {
    "type": "select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

`select` 操作的输出是一个数组，该数组具有与输入数组相同的基数并且每个元素按 `select` 属性定义的方式进行转换。 如果输入数组为空，则输出数组也为空。

|Name|必选|类型|说明|
|--------|------------|--------|---------------|
|from|是|Array|源数组。|
|选择|是|任意|要应用到源数组的每个元素的投影。|

## <a name="terminate-action"></a>Terminate 操作

Terminate 操作停止执行工作流运行，中止正在进行的所有操作，并跳过所有剩余的操作。 例如，若要终止状态为 **Failed** 的运行，可以使用以下代码片段：

```json
"HandleUnexpectedResponse" : {
    "type": "terminate",
    "inputs": {
        "runStatus" : "failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response.",
        }
    }
}
```

> [!NOTE]
> 已完成的操作不受 terminate 操作的影响。

|名称|必选|类型|说明|
|--------|------------|--------|---------------|
|runStatus|是|String|目标运行状态。 值为 **Failed** 或 **Cancelled**。|
|runError|否|对象|错误详细信息。 仅当 **runStatus** 设置为 **Failed** 时才受支持。|
|runError 代码|否|String|运行错误代码。|
|runError 消息|否|String|运行错误消息。|

## <a name="compose-action"></a>Compose 操作

使用 Compose 操作可以构造任意对象。 compose 操作的输出是其输入的计算结果。 例如，可以使用 compose 操作来合并多个操作的输出：

```json
"composeUserRecord" : {
    "type": "compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
        }
    }
}
```

> [!NOTE]
> **Compose** 操作可用于构造任何输出，包括对象、数组，以及逻辑应用原生支持的其他任何类型，例如 XML 和二进制数据。

## <a name="table-action"></a>表操作

`table` 允许将一个项数组转换为 CSV或 HTML 表。

假设 @triggerBody() 为

```json
[{
  "id": 0,
  "name": "apples"
},{
  "id": 1, 
  "name": "oranges"
}]
```

允许操作如下定义

```json
"ConvertToTable" : {
    "type": "table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html"
    }
}
```

上述操作会生成

<table><thead><tr><th>id</th><th>名称</th></tr></thead><tbody><tr><td>0</td><td>apples</td></tr><tr><td>1</td><td>oranges</td></tr></tbody></table>"

若要自定义表，可以显式指定列。 例如：

```json
"ConvertToTable" : {
    "type": "table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html",
        "columns": [{
          "header": "produce id",
          "value": "@item().id"
        },{
          "header": "description",
          "value": "@concat('fresh ', item().name)"
        }]
    }
}
```

上述操作会生成

<table><thead><tr><th>produce id</th><th>description</th></tr></thead><tbody><tr><td>0</td><td>fresh apples</td></tr><tr><td>1</td><td>fresh oranges</td></tr></tbody></table>"

如果 `from` 属性值为空数组，输出将为空表。

|Name|必选|类型|说明|
|--------|------------|--------|---------------|
|from|是|Array|源数组。|
|格式|是|String|格式可以为 CSV，也可以为 HTML。|
|列|否|Array|列。 允许替代该表的默认形状。|
|column header|否|String|列标题。|
|column value|是|String|列的值。|

## <a name="workflow-action"></a>Workflow 操作   

|Name|必选|类型|说明|  
|--------|------------|--------|---------------|  
|host id|是|String|要调用的工作流的资源 ID。|  
|host triggerName|是|String|要调用的触发器的名称。|  
|查询|否|对象|表示要添加到 URL 的查询参数。 例如，`"queries" : { "api-version": "2015-02-01" }` 将 `?api-version=2015-02-01` 添加到 URL。|  
|headers|否|对象|表示要发送到请求的每个标头。 例如，若要在请求中设置语言和类型：`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|否|对象|表示发送到终结点的有效负载。|  
  
```json
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName " : "mytrigger001"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },  
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "runAfter": {}
    }
```
  
将在工作流中（\(更具体地说，是触发器中\)）执行访问检查，这意味着，你需要访问工作流。  
  
`workflow` 操作的输出基于在子工作流的 `response` 操作中定义的设置。 如果未定义任何 `response` 操作，则输出为空。  

## <a name="function-action"></a>函数操作   

|Name|必选|类型|说明|  
|--------|------------|--------|---------------|  
|函数 ID|是|String|要调用的函数的资源 ID。|  
|方法|否|String|用于调用函数的 HTTP 方法。 未指定时，默认情况下，它是 `POST`。|  
|查询|否|对象|表示要添加到 URL 的查询参数。 例如，`"queries" : { "api-version": "2015-02-01" }` 将 `?api-version=2015-02-01` 添加到 URL。|  
|headers|否|对象|表示要发送到请求的每个标头。 例如，若要在请求中设置语言和类型：`"headers" : { "Accept-Language": "en-us" }`。|  
|body|否|对象|表示发送到终结点的有效负载。|  

```json
"myfunc" : {
    "type" : "Function",
    "inputs" : {
        "function" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Web/sites/myfuncapp/functions/myfunc"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },  
        "headers" : {
            "x-ms-date" : "@utcnow()"
        },
        "method" : "POST",
    "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "runAfter": {}
}
```

保存逻辑应用时，我们会对所引用的函数执行一些检查：
-   你需要有权访问该函数。
-   仅允许使用标准 HTTP 触发器或泛型 JSON webhook 触发器。
-   它不应定义任何路由。
-   仅允许“函数”和“匿名”授权级别。

在运行时检索、缓存和使用触发器 URL。 因此，如果任何操作使缓存的 URL 失效，则此操作将在运行时失败。 若要解决此问题，请重新保存逻辑应用，这将导致逻辑应用重新检索并缓存触发器 URL。

## <a name="collection-actions-scopes-and-loops"></a>集合操作（范围和循环）

某些操作类型的本身可能包含操作。 集合中的引用操作可以直接在集合外部引用。 如果在范围中定义了 `http`，`@body('http')` 仍在工作流中的任意位置有效。 集合中的操作只能在同一集合中的其他操作之后运行 (`runAfter`)。

## <a name="scope-action"></a>Scope 操作

使用 `scope` 操作可以有序地将工作流中的操作分组。

|Name|必选|类型|说明|  
|--------|------------|--------|---------------|  
|actions|是|对象|在范围中执行的内部操作|

```json
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```

## <a name="foreach-action"></a>ForEach 操作

此循环操作循环访问数组并针对每个项执行内部操作。 默认情况下，foreach 循环将并行执行（一次同时执行 20 个）。 可以使用 `operationOptions` 参数设置执行规则。

|Name|必选|类型|说明|  
|--------|------------|--------|---------------|  
|actions|是|对象|在循环中执行的内部操作|
|foreach|是|字符串|要循环访问的数组|
|operationOptions|否|字符串|行为的任何操作选项。 目前仅支持使用 `sequential` 按顺序执行迭代（默认行为是并行）|

```json
"forEach_email": {
    "type": "foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "runAfter":{
        "email_filter": [ "Succeeded" ]
    }
}
```

## <a name="until-action"></a>Until 操作

此循环操作执行内部操作，直到条件结果为 true。

|Name|必选|类型|说明|  
|--------|------------|--------|---------------|  
|actions|是|对象|在循环中执行的内部操作|
|表达式|是|字符串|每次迭代后要计算的表达式|
|limit|是|对象|循环的限制 - 必须至少定义一个限制|
|count|否|int|可执行的迭代数限制|
|timeout|否|字符串|循环持续时间的超时值。  ISO 8601 格式|


```json
 "Until_succeeded": {
    "actions": {
        "Http": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {},
    "type": "Until"
}
```

## <a name="conditions---if-action"></a>条件 - If 操作

使用 `If` 操作可以评估条件，并根据表达式计算结果是否为 `true` 来执行分支。

|名称|必选|类型|说明|  
|--------|------------|--------|---------------|  
|actions|是|对象|表达式计算结果为 `true` 时要执行的内部操作|
|表达式|是|字符串|要计算的表达式|
|else|否|对象|表达式计算结果为 `false` 时要执行的内部操作|
  
```json
"My_condition": {
    "actions": {
        "If_true": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "else": {
        "actions": {
            "if_false": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://myurl"
                },
                "runAfter": {},
                "type": "Http"
            }
        }
    },
    "expression": "@equals(triggerBody(), json(true))",
    "runAfter": {},
    "type": "If"
}
```  
  
下表显示了有关条件如何在操作中使用表达式的示例：  
  
|JSON 值|结果|  
|--------------|----------|  
|`"expression": "@parameters('hasSpecialAction')"`|计算为 true 的任何值会导致满足此条件。 仅支持布尔值表达式。 若要将其他类型转换为布尔值，请使用函数 `empty`、`equals`。|  
|`"expression": "@greater(actions('act1').output.value, parameters('threshold'))"`|支持比较函数。 在本示例中，仅当 act1 的输出大于阈值时，才执行该操作。|  
|`"expression": "@or(greater(actions('act1').output.value, parameters('threshold')), less(actions('act1').output.value, 100))"`|还支持使用逻辑函数来创建嵌套的布尔值表达式。 在本例中，当 act1 的输出高于阈值或低于 100 时，将执行该操作。|  
|`"expression": "@equals(length(actions('act1').outputs.errors), 0))"`|可以使用数组函数来检查某个数组是否包含任何项。 在本例中，当 errors 数组为空时，将执行该操作。| 
|`"expression": "parameters('hasSpecialAction')"`|错误 - 条件无效，因为条件需要 @。|  
  
如果某个条件评估成功，该条件将标记为 `Succeeded`。 `actions` 或 `else` 对象中的操作在执行并成功后将评估为 `Succeeded`；在执行但失败后将评估为 `Failed`；未执行分支时将评估为 `Skipped`。

## <a name="next-steps"></a>后续步骤

[工作流服务 REST API](https://docs.microsoft.com/rest/api/logic/workflows)

