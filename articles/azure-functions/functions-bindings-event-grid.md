---
title: Azure Functions 的事件网格触发器
description: 了解如何处理 Azure Functions 中的事件网格事件。
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/26/2018
ms.author: tdykstra
ms.openlocfilehash: 52654704662b736811f429a811e10669a752b75a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="event-grid-trigger-for-azure-functions"></a>Azure Functions 的事件网格触发器

本文介绍如何处理 Azure Functions 中的[事件网格](../event-grid/overview.md)事件。

事件网格是一个 Azure 服务，它可以发送 HTTP 请求来告知发布方中发生的事件情况。 发布方是发起事件的服务或资源。 例如，Azure Blob 存储帐户是一个发布方，而 Blob 上传或删除是一个事件。 某些 [Azure 服务原生支持向事件网格发布事件](../event-grid/overview.md#event-sources)。 

事件处理程序接收并处理事件。 Azure Functions 是[原生支持处理事件网格事件的多个 Azure 服务](../event-grid/overview.md#event-handlers)之一。 本文将会介绍在收到事件网格发出的事件时，如何使用事件网格触发器调用某个函数。

如果需要，可以使用 HTTP 触发器来处理事件网格事件；请参阅本文稍后介绍的[将 HTTP 触发器用作事件网格触发器](#use-an-http-trigger-as-an-event-grid-trigger)。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>包

[Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet 包中提供了事件网格触发器。 [azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension) GitHub 存储库中提供了此包的源代码。

<!--
If you want to bind to the `Microsoft.Azure.EventGrid.Models.EventGridEvent` type instead of `JObject`, install the [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) package.
-->

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example"></a>示例

请参阅有关事件网格触发器的特定于语言的示例：

* [C#](#c-example)
* [C# 脚本 (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

有关 HTTP 触发器示例，请参阅本文稍后介绍的[如何使用 HTTP 触发器](#use-an-http-trigger-as-an-event-grid-trigger)。

### <a name="c-example"></a>C# 示例

以下示例演示绑定到 `JObject` 的 [C# 函数](functions-dotnet-class-library.md)：

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, TraceWriter log)
        {
            log.Info(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

<!--
The following example shows a [C# function](functions-dotnet-class-library.md) that binds to `EventGridEvent`:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
            public static void EventGridTest([EventGridTrigger] Microsoft.Azure.EventGrid.Models.EventGridEvent eventGridEvent, TraceWriter log)
        {
            log.Info("C# Event Grid function processed a request.");
            log.Info($"Subject: {eventGridEvent.Subject}");
            log.Info($"Time: {eventGridEvent.EventTime}");
            log.Info($"Data: {eventGridEvent.Data.ToString()}");
        }
    }
}
```
-->

有关详细信息，请参阅[包](#packages)、[特性](#attributes)、[配置](#configuration)和[用法](#usage)。

### <a name="c-script-example"></a>C# 脚本示例

以下示例演示 *function.json* 文件中的一个触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。

下面是 *function.json* 文件中的绑定数据：

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

下面是绑定到 `JObject` 的 C# 脚本代码：

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

<!--
Here's C# script code that binds to `EventGridEvent`:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.Azure.WebJobs.Extensions.EventGrid"
#r "Microsoft.Azure.EventGrid"

using Microsoft.Azure.WebJobs.Extensions.EventGrid;
Using Microsoft.Azure.EventGrid.Models;

public static void Run(EventGridEvent eventGridEvent, TraceWriter log)
{
    log.Info("C# Event Grid function processed a request.");
    log.Info($"Subject: {eventGridEvent.Subject}");
    log.Info($"Time: {eventGridEvent.EventTime}");
    log.Info($"Data: {eventGridEvent.Data.ToString()}");
}
```
-->

有关详细信息，请参阅[包](#packages)、[特性](#attributes)、[配置](#configuration)和[用法](#usage)。

### <a name="javascript-example"></a>JavaScript 示例

以下示例演示 *function.json* 文件中的一个触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。

下面是 *function.json* 文件中的绑定数据：

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

JavaScript 代码如下所示：

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```
     
## <a name="attributes"></a>属性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs) 特性。

下面是某个方法签名中的 `EventGridTrigger` 特性：

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, TraceWriter log)
{
    ...
}
 ```

有关完整示例，请参阅 [C# 示例](#c-example)。

## <a name="configuration"></a>配置

下表解释了在 *function.json* 文件中设置的绑定配置属性。 无法在 `EventGridTrigger` 特性中设置任何构造函数参数或属性。

|function.json 属性 |说明|
|---------|---------|----------------------|
| **类型** | 必需 - 必须设置为 `eventGridTrigger`。 |
| **direction** | 必需 - 必须设置为 `in`。 |
| **name** | 必需 - 在函数代码中对接收事件数据的参数使用的变量名称。 |

## <a name="usage"></a>使用情况

在 C# 和 C# 函数中，可以为事件网格触发器使用以下参数类型：

* `JObject`
* `string`
* `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` - 定义所有事件类型通用的字段的属性。 此类型已弃用，但其替代项尚未发布到 NuGet。

对于 JavaScript 函数，由 *function.json* `name` 属性命名的参数包含对事件对象的引用。

## <a name="event-schema"></a>事件架构

事件网格事件的数据在 HTTP 请求的正文中以 JSON 对象形式接收。 该 JSON 如以下示例所示：

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

显示的示例是包含一个元素的数组。 事件网格始终发送一个数组，并可能在该数组中发送多个事件。 运行时针对每个数组元素调用你的函数一次。

事件 JSON 数据中的顶级属性在所有事件类型中相同，而 `data` 属性的内容特定于每个事件类型。 显示的示例适用于 Blob 存储事件。

有关通用和特定于事件的属性的说明，请参阅事件网格文档中的[事件属性](../event-grid/event-schema.md#event-properties)。

`EventGridEvent` 类型只定义顶级属性；`Data` 属性是 `JObject`。 

## <a name="create-a-subscription"></a>创建订阅

若要开始接收事件网格 HTTP 请求，请创建一个事件网格订阅，用于指定可调用函数的终结点 URL。

### <a name="azure-portal"></a>Azure 门户

对于在 Azure 门户中使用事件网格触发器开发的函数，请选择“添加事件网格订阅”。

![在门户中创建订阅](media/functions-bindings-event-grid/portal-sub-create.png)

选择此链接时，门户将打开“创建事件订阅”页，其中预先填充了终结点 URL。

![预先填充的终结点 URL](media/functions-bindings-event-grid/endpoint-url.png)

有关如何使用 Azure 门户创建订阅的详细信息，请参阅事件网格文档中的[创建自定义事件 - Azure 门户](../event-grid/custom-event-quickstart-portal.md)。

### <a name="azure-cli"></a>Azure CLI

若要使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) 创建订阅，请运行 [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create) 命令。

该命令需要可调用函数的终结点 URL。 以下示例演示 URL 模式：

```
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

系统密钥是必须包含在事件网格触发器终结点 URL 中的授权密钥。 以下部分介绍如何获取系统密钥。

下面是一个订阅 Blob 存储帐户的示例（包含系统密钥的占位符）：

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name glengablobstorage --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://glengastorageevents.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=LUwlnhIsNtSiUjv/sNtSiUjvsNtSiUjvsNtSiUjvYb7XDonDUr/RUg==
```

有关如何创建订阅的详细信息，请参阅 [Blob 存储快速入门](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account)或其他事件网格快速入门。

### <a name="get-the-system-key"></a>获取系统密钥

可以使用以下 API (HTTP GET) 获取系统密钥：

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={adminkey}
```

这是一个管理 API，因此需要[管理密钥](functions-bindings-http-webhook.md#authorization-keys)。 请不要混淆系统密钥（用于调用事件网格触发器函数）和管理密钥（用于针对函数应用执行管理任务）。 订阅事件网格主题时，请务必使用系统密钥。

下面是提供系统密钥的响应示例：

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

有关详细信息，请参阅 HTTP 触发器参考文章中的[授权密钥](functions-bindings-http-webhook.md#authorization-keys)。 

或者，可以发送 HTTP PUT 以自行指定密钥值。

## <a name="local-testing-with-requestbin"></a>使用 RequestBin 进行本地测试

> [!NOTE]
> RequestBin 站点当前不可用，但可以改为通过 https://hookbin.com 使用此方法。 如果该站点已关闭，则可以使用 [ngrok](#local-testing-with-ngrok)。

若要在本地测试事件网格触发器，必须获取从云中的来源位置传送到本地计算机的事件网格 HTTP 请求。 实现此目的的方法之一是在线捕获请求，然后手动将其重新发送到本地计算机：

2. [创建 RequestBin 终结点](#create-a-RequestBin-endpoint)。
3. [创建事件网格订阅](#create-an-event-grid-subscription)，用于向 RequestBin 终结点发送事件。
4. [生成请求](#generate-a-request)，并从 RequestBin 站点复制请求正文。
5. [将请求手动发布](#manually-post-the-request)到事件网格触发器函数的 localhost URL。

完成测试后，可以更新终结点，将同一订阅用于生产。 使用 [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update) Azure CLI 命令。

### <a name="create-a-requestbin-endpoint"></a>创建 RequestBin 终结点

RequestBin 是一个开源工具，可接受 HTTP 请求并显示请求正文。 Azure 事件网格会对 http://requestb.in URL 进行特殊处理。 为便于测试，事件网格会将事件发送到 RequestBin URL，且无需正确响应订阅验证请求。 向另一个测试工具提供相同的处理方式：http://hookbin.com。

RequestBin 并不适合在高吞吐量方案中使用。 如果一次推送多个事件，可能不会在工具中看到所有事件。

创建终结点。

![创建 RequestBin 终结点](media/functions-bindings-event-grid/create-requestbin.png)

复制终结点 URL。

![复制 RequestBin 终结点](media/functions-bindings-event-grid/save-requestbin-url.png)

### <a name="create-an-event-grid-subscription"></a>创建事件网格订阅

创建想要测试的类型的事件网格订阅，并在其中指定你的 RequestBin 终结点。 有关如何创建订阅的信息，请参阅本文前面所述的[创建订阅](#create-a-subscription)。

### <a name="generate-a-request"></a>生成请求

触发一个事件，以便向 RequestBin 终结点生成 HTTP 流量。  例如，如果创建了 Blob 存储订阅，请上传或删除一个 Blob。 RequestBin 页中显示请求后，请复制请求正文。

首先会接收订阅验证请求，忽略任何验证请求，并复制事件请求。

![从 RequestBin 复制请求正文](media/functions-bindings-event-grid/copy-request-body.png)

### <a name="manually-post-the-request"></a>手动发布请求

在本地运行事件网格函数。

使用 [Postman](https://www.getpostman.com/) 或 [curl](https://curl.haxx.se/docs/httpscripting.html) 等工具创建 HTTP POST 请求：

* 设置 `Content-Type: application/json` 标头。
* 设置 `aeg-event-type: Notification` 标头。
* 将 RequestBin 数据粘贴到请求正文。 
* 使用以下模式发布到事件网格触发器函数的 URL：

```
http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={functionname}
``` 

`functionName` 参数必须是在 `FunctionName` 特性中指定的名称。

以下屏幕截图显示了 Postman 中的标头和请求正文：

![Postman 中的标头](media/functions-bindings-event-grid/postman2.png)

![Postman 中的请求正文](media/functions-bindings-event-grid/postman.png)

事件网格触发器函数将会执行，并显示以下示例所示的日志：

![事件网格触发器函数日志示例](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>使用 ngrok 进行本地测试

在本地测试事件网格触发器的另一种方法是自动化 Internet 与开发计算机之间的 HTTP 连接。 为此，可以使用名为 [ngrok](https://ngrok.com/) 的开源工具：

3. [创建 ngrok 终结点](#create-an-ngrok-endpoint)。
4. [运行事件网格触发器函数](#run-the-event-grid-trigger-function)。
5. [创建事件网格订阅](#create-a-subscription)，用于向 ngrok 终结点发送事件。
6. [触发事件](#trigger-an-event)。

完成测试后，可以更新终结点，将同一订阅用于生产。 使用 [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update) Azure CLI 命令。

### <a name="create-an-ngrok-endpoint"></a>创建 ngrok 终结点

从 [ngrok](https://ngrok.com/) 下载 *ngrok.exe*，然后使用以下命令运行该工具：

```
ngrok http -host-header=localhost 7071
```

之所以需要 -host-header 参数，是因为函数运行时运行于 localhost 时，预期接收来自 localhost 的请求。 7071 是在本地运行运行时所用的默认端口号。

该命令创建如下所示的输出：

```
Session Status                online
Version                       2.2.8
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://263db807.ngrok.io -> localhost:7071
Forwarding                    https://263db807.ngrok.io -> localhost:7071

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

请对事件网格订阅使用 https://{subdomain}.ngrok.io URL。

### <a name="run-the-event-grid-trigger-function"></a>运行事件网格触发器函数

事件网格不会对 ngrok URL 进行特殊处理，因此，创建订阅时，函数必须在本地运行。 否则不会发送验证响应，并且订阅创建将会失败。

### <a name="create-a-subscription"></a>创建订阅

创建想要测试的类型的事件网格订阅，并使用以下模式在其中指定你的 ngrok 终结点：

```
https://{subdomain}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={functionname}
``` 

`functionName` 参数必须是在 `FunctionName` 特性中指定的名称。

下面是使用 Azure CLI 的示例：

```
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName=EventGridTrigger
```

有关如何创建订阅的信息，请参阅本文前面所述的[创建订阅](#create-a-subscription)。

### <a name="trigger-an-event"></a>触发事件

触发一个事件，以便向 ngrok 终结点生成 HTTP 流量。  例如，如果创建了 Blob 存储订阅，请上传或删除一个 Blob。

事件网格触发器函数将会执行，并显示以下示例所示的日志：

![事件网格触发器函数日志示例](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>将 HTTP 触发器用作事件网格触发器

事件网格事件以 HTTP 请求的形式接收，因此，可以使用 HTTP 触发器而不是事件网格触发器来处理事件。 使用 HTTP 触发器的可能原因之一是能够更好地控制调用函数的终结点 URL。 

如果使用 HTTP 触发器，必须编写代码来指定事件网格触发器自动执行的操作：

* 将验证响应发送到[订阅验证请求](../event-grid/security-authentication.md#webhook-event-delivery)。
* 针对请求正文中包含的事件数组的每个元素调用该函数一次。

以下 HTTP 触发器的示例 C# 代码模拟事件网格触发器的行为：

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"], 
        "Microsoft.EventGrid.SubscriptionValidationEvent", 
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.Info("Validate request received");
        return req.CreateResponse<object>(new
        {
            validationResponse = messages[0]["data"]["validationCode"]
        });
    }

    // The request is not for subscription validation, so it's for one or more events.
    foreach (JObject message in messages)
    {
        // Handle one event.
        EventGridEvent eventGridEvent = message.ToObject<EventGridEvent>();
        log.Info($"Subject: {eventGridEvent.Subject}");
        log.Info($"Time: {eventGridEvent.EventTime}");
        log.Info($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

以下 HTTP 触发器的示例 JavaScript 代码模拟事件网格触发器的行为：

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        context.res = { status: 200, body: messages[0].data.validationCode }
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
        for (var i = 0; i < messages.length; i++) {
            // Handle one event.
            var message = messages[i];
            context.log('Subject: ' + message.subject);
            context.log('Time: ' + message.eventTime);
            context.log('Data: ' + JSON.stringify(message.data));
        }
    }
    context.done();
};
```

事件处理代码通过 `messages` 数组进入循环内部。

有关用于在本地调用函数或者在 Azure 中运行函数的 URL 的信息，请参阅 [HTTP 触发器绑定参考文档](functions-bindings-http-webhook.md) 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [详细了解事件网格](../event-grid/overview.md)
