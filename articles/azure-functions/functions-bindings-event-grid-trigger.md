---
title: Azure Functions 的 Azure 事件网格触发器
description: 了解如何在调度 Azure Functions 中的事件网格事件时运行代码。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 4673bcb71fc2f45c3b02f8fe5e463184395e8ef8
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891278"
---
# <a name="azure-event-grid-trigger-for-azure-functions"></a>Azure Functions 的 Azure 事件网格触发器

使用函数触发器来响应发送到事件网格主题的事件。

若要了解设置和配置详细信息，请参阅[概述](./functions-bindings-event-grid.md)。

## <a name="example"></a>示例

# <a name="c"></a>[C#](#tab/csharp)

如需 HTTP 触发器示例，请参阅[将事件接收到 HTTP 终结点](../event-grid/receive-events.md)。

### <a name="c-2x-and-higher"></a>C#（2.x 及更高版本）

以下示例演示绑定到 [ 的 ](functions-dotnet-class-library.md)C# 函数`EventGridEvent`：

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

有关详细信息，请参阅包、[特性](#attributes-and-annotations)、[配置](#configuration)和[用法](#usage)。

### <a name="version-1x"></a>版本 1.x

以下示例演示绑定到 [ 的 Functions 1.x ](functions-dotnet-class-library.md)C# 函数`JObject`：

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

以下示例演示 function.json  文件中的一个触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。

下面是 function.json  文件中的绑定数据：

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

### <a name="version-2x-and-higher"></a>版本 2.x 及更高版本

下面是绑定到 `EventGridEvent` 的示例：

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

有关详细信息，请参阅包、[特性](#attributes-and-annotations)、[配置](#configuration)和[用法](#usage)。

### <a name="version-1x"></a>版本 1.x

下面是绑定到 `JObject` 的 Functions 1.x C# 脚本代码：

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示 function.json  文件中的一个触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。

下面是 function.json  文件中的绑定数据：

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

# <a name="python"></a>[Python](#tab/python)

以下示例演示 function.json** 文件中的一个触发器绑定以及使用该绑定的 [Python 函数](functions-reference-python.md)。

下面是 function.json** 文件中的绑定数据：

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "event",
      "direction": "in"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

下面是 Python 代码：

```python
import json
import logging

import azure.functions as func

def main(event: func.EventGridEvent):

    result = json.dumps({
        'id': event.id,
        'data': event.get_json(),
        'topic': event.topic,
        'subject': event.subject,
        'event_type': event.event_type,
    })

    logging.info('Python EventGrid trigger processed an event: %s', result)
```

# <a name="java"></a>[Java](#tab/java)

本部分包含以下示例：

* [事件网格触发器、字符串参数](#event-grid-trigger-string-parameter)
* [事件网格触发器、POJO 参数](#event-grid-trigger-pojo-parameter)

下面的示例演示了使用绑定并输出事件的[Java](functions-reference-java.md)中的触发器绑定，第一种是以 POJO `String`的形式接收事件。

### <a name="event-grid-trigger-string-parameter"></a>事件网格触发器、字符串参数

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: " + content);      
  }
```

### <a name="event-grid-trigger-pojo-parameter"></a>事件网格触发器、POJO 参数

此示例使用以下 POJO 表示事件网格事件的顶级属性：

```java
import java.util.Date;
import java.util.Map;

public class EventSchema {

  public String topic;
  public String subject;
  public String eventType;
  public Date eventTime;
  public String id;
  public String dataVersion;
  public String metadataVersion;
  public Map<String, Object> data;

}
```

到达后，事件的 JSON 有效负载被反序列化为 ```EventSchema``` POJO 以供函数使用。 此过程允许函数以面向对象的方式访问事件的属性。

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对其值将来自 EventGrid 的参数使用 `EventGridTrigger` 注释。 带有这些注释的参数会导致函数在事件到达时运行。  可以将此注释与本机 Java 类型、POJO 或使用了 `Optional<T>` 的可为 null 的值一起使用。

---

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs) 特性。

下面是某个方法签名中的 `EventGridTrigger` 特性：

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

有关完整示例，请参阅 C# 示例。

# <a name="c-script"></a>[C # 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

# <a name="java"></a>[Java](#tab/java)

[EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java)批注允许您通过提供配置值以声明方式配置事件网格绑定。 有关更多详细信息，请参阅[示例](#example)和[配置](#configuration)部分。

---

## <a name="configuration"></a>配置

下表解释了在 function.json** 文件中设置的绑定配置属性。 无法在 `EventGridTrigger` 特性中设置任何构造函数参数或属性。

|function.json 属性 |说明|
|---------|---------|
| type  | 必需 - 必须设置为 `eventGridTrigger`。 |
| **方向键** | 必需 - 必须设置为 `in`。 |
| **name** | 必需 - 在函数代码中对接收事件数据的参数使用的变量名称。 |

## <a name="usage"></a>用法

# <a name="c"></a>[C#](#tab/csharp)

在 Azure Functions 1.x 中，可以为事件网格触发器使用以下参数类型：

* `JObject`
* `string`

在 Azure Functions 2.x 及更高版本中，还可以选择对事件网格触发器使用以下参数类型：

* `Microsoft.Azure.EventGrid.Models.EventGridEvent` - 定义所有事件类型通用的字段的属性。

> [!NOTE]
> 在 Functions v1 中，如果尝试绑定到 `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`，编译器将显示“已弃用”消息，并建议你改用 `Microsoft.Azure.EventGrid.Models.EventGridEvent`。 若要使用较新类型，请引用 [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet 包，并通过为 `EventGridEvent` 类型名称添加 `Microsoft.Azure.EventGrid.Models` 前缀来对其进行完全限定。

# <a name="c-script"></a>[C # 脚本](#tab/csharp-script)

在 Azure Functions 1.x 中，可以对事件网格触发器使用以下参数类型：

* `JObject`
* `string`

在 Azure Functions 1.x 和更高版本中，还可以选择将以下参数类型用于事件网格触发器：

* `Microsoft.Azure.EventGrid.Models.EventGridEvent` - 定义所有事件类型通用的字段的属性。

> [!NOTE]
> 在 Functions v1 中，如果尝试绑定到 `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`，编译器将显示“已弃用”消息，并建议你改用 `Microsoft.Azure.EventGrid.Models.EventGridEvent`。 若要使用较新类型，请引用 [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet 包，并通过为 `EventGridEvent` 类型名称添加 `Microsoft.Azure.EventGrid.Models` 前缀来对其进行完全限定。 有关如何在 C# 脚本函数中引用 NuGet 包的信息，请参阅[使用 NuGet 包](functions-reference-csharp.md#using-nuget-packages)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

事件网格实例可通过*函数 json*文件的`name`属性中配置的参数使用。

# <a name="python"></a>[Python](#tab/python)

事件网格实例可通过类型为`func.EventGridEvent`的*函数 json*文件的`name`属性中配置的参数提供。

# <a name="java"></a>[Java](#tab/java)

事件网格事件实例可通过关联到类型为的`EventGridTrigger`属性的参数提供。 `EventSchema` 有关更多详细信息，请参阅[示例](#example)。

---

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

对于在 Azure 门户中使用事件网格触发器开发的函数，请选择“添加事件网格订阅”。****

![在门户中创建订阅](media/functions-bindings-event-grid/portal-sub-create.png)

选择此链接时，门户将打开“创建事件订阅”页，其中预先填充了终结点 URL。****

![预先填充的终结点 URL](media/functions-bindings-event-grid/endpoint-url.png)

有关如何使用 Azure 门户创建订阅的详细信息，请参阅事件网格文档中的[创建自定义事件 - Azure 门户](../event-grid/custom-event-quickstart-portal.md)。

### <a name="azure-cli"></a>Azure CLI

若要使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) 创建订阅，请运行 [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create) 命令。

该命令需要可调用函数的终结点 URL。 以下示例显示特定于版本的 URL 模式：

#### <a name="version-2x-and-higher-runtime"></a>版本 2.x（及更高版本）运行时

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>1.x 版运行时

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

系统密钥是必须包含在事件网格触发器终结点 URL 中的授权密钥。 以下部分介绍如何获取系统密钥。

下面是一个订阅 Blob 存储帐户的示例（包含系统密钥的占位符）：

#### <a name="version-2x-and-higher-runtime"></a>版本 2.x（及更高版本）运行时

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>1.x 版运行时

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

有关如何创建订阅的详细信息，请参阅 [Blob 存储快速入门](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account)或其他事件网格快速入门。

### <a name="get-the-system-key"></a>获取系统密钥

可以使用以下 API (HTTP GET) 获取系统密钥：

#### <a name="version-2x-and-higher-runtime"></a>版本 2.x（及更高版本）运行时

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>1.x 版运行时

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

这是一个管理 API，因此它需要函数应用[主密钥](functions-bindings-http-webhook-trigger.md#authorization-keys)。 请不要混淆系统密钥（用于调用事件网格触发器函数）和主密钥（用于针对函数应用执行管理任务）。 订阅事件网格主题时，请务必使用系统密钥。

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

可以从门户中的“函数应用设置”选项卡获取函数应用的主密钥****。

> [!IMPORTANT]
> 主密钥提供对函数应用的管理员访问权限。 不要与第三方共享此密钥或将其分发到本机客户端应用程序中。

有关详细信息，请参阅 HTTP 触发器参考文章中的[授权密钥](functions-bindings-http-webhook-trigger.md#authorization-keys)。

或者，可以发送 HTTP PUT 以自行指定密钥值。

## <a name="local-testing-with-viewer-web-app"></a>使用查看器 Web 应用进行本地测试

若要在本地测试事件网格触发器，必须获取从云中的来源位置传送到本地计算机的事件网格 HTTP 请求。 实现此目的的方法之一是在线捕获请求，然后手动将其重新发送到本地计算机：

1. [创建查看器 Web 应用](#create-a-viewer-web-app)，用于捕获事件消息。
1. [创建事件网格订阅](#create-an-event-grid-subscription)，用于向查看器应用发送事件。
1. [生成请求](#generate-a-request)，并从查看器应用复制请求正文。
1. [将请求手动发布](#manually-post-the-request)到事件网格触发器函数的 localhost URL。

完成测试后，可以更新终结点，将同一订阅用于生产。 使用 [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI 命令。

### <a name="create-a-viewer-web-app"></a>创建查看器 Web 应用

若要简化事件消息捕获，可部署用于显示事件消息的[预建 Web 应用](https://github.com/Azure-Samples/azure-event-grid-viewer)。 所部署的解决方案包括应用服务计划、应用服务 Web 应用和 GitHub 中的源代码。

选择“部署到 Azure”  将解决方案部署到你的订阅。 在 Azure 门户中，为参数提供值。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

部署可能需要几分钟才能完成。 部署成功后，请查看 Web 应用以确保它正在运行。 在 Web 浏览器中导航到 `https://<your-site-name>.azurewebsites.net`

查看站点，但是尚未有事件发布到它。

![查看新站点](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>创建事件网格订阅

创建要测试的类型的事件网格订阅，并将 Web 应用中的 URL 作为事件通知的终结点。 Web 应用的终结点必须包括后缀 `/api/updates/`。 因此，完整的 URL 是 `https://<your-site-name>.azurewebsites.net/api/updates`

有关如何使用 Azure 门户创建订阅的信息，请参阅事件网格文档中的[创建自定义事件 - Azure 门户](../event-grid/custom-event-quickstart-portal.md)。

### <a name="generate-a-request"></a>生成请求

触发一个事件，以便向 Web 应用终结点生成 HTTP 流量。  例如，如果创建了 Blob 存储订阅，请上传或删除一个 Blob。 Web 应用中显示请求后，请复制请求正文。

首先会接收订阅验证请求，忽略任何验证请求，并复制事件请求。

![从 Web 应用复制请求正文](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>手动发布请求

在本地运行事件网格函数。

使用 [Postman](https://www.getpostman.com/) 或 [curl](https://curl.haxx.se/docs/httpscripting.html) 等工具创建 HTTP POST 请求：

* 设置 `Content-Type: application/json` 标头。
* 设置 `aeg-event-type: Notification` 标头。
* 将 RequestBin 数据粘贴到请求正文。
* 发布到事件网格触发器函数的 URL。
  * 对于 2.x 及更高版本，请使用以下模式：

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * 对于 1.x，请使用：

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

`functionName` 参数必须是在 `FunctionName` 特性中指定的名称。

以下屏幕截图显示了 Postman 中的标头和请求正文：

![Postman 中的标头](media/functions-bindings-event-grid/postman2.png)

![Postman 中的请求正文](media/functions-bindings-event-grid/postman.png)

事件网格触发器函数将会执行，并显示类似于以下示例的日志：

![事件网格触发器函数日志示例](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>后续步骤

* [调度事件网格事件](./functions-bindings-event-grid-output.md)
