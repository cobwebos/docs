---
title: Azure Functions Twilio 绑定
description: 了解如何将 Azure Functions 与 Twilio 绑定配合使用。
author: craigshoemaker
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: devx-track-csharp, H1Hack27Feb2017
ms.openlocfilehash: 31d5c528112ccacab47282729b1b5f8a72f19af0
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999291"
---
# <a name="twilio-binding-for-azure-functions"></a>Azure Functions 的 Twilio 绑定

本文介绍如何使用 Azure Functions 中的 [Twilio](https://www.twilio.com/) 绑定发送短信。 Azure Functions 支持 Twilio 的输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>包 - Functions 1.x

[Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet 包 1.x 版中提供了 Twilio 绑定。 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>包 - Functions 2.x 及更高版本

[Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet 包 3.x 版中提供了 Twilio 绑定。 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

<a id="example"></a>

## <a name="example---functions-2x-and-higher"></a>示例-函数2.x 和更高版本

# <a name="c"></a>[C#](#tab/csharp)

以下示例演示在队列消息触发时发送短信的 [C# 函数](functions-dotnet-class-library.md)。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;
namespace TwilioQueueOutput
{
    public static class QueueTwilio
    {
        [FunctionName("QueueTwilio")]
        [return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
        public static CreateMessageOptions Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
        ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed: {order}");

            var message = new CreateMessageOptions(new PhoneNumber(order["mobileNumber"].ToString()))
            {
                Body = $"Hello {order["name"]}, thanks for your order!"
            };

            return message;
        }
    }
}
```

此示例使用包含方法返回值的 `TwilioSms` 特性。 一种替代方法是使用包含 `out CreateMessageOptions` 参数或者 `ICollector<CreateMessageOptions>` 或 `IAsyncCollector<CreateMessageOptions>` 参数的特性。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

以下示例演示 *function.json* 文件中的一个 Twilio 输出绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数使用 `out` 参数发送短信。

下面是 *function.json* 文件中的绑定数据：

示例 function.json：

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

C# 脚本代码如下所示：

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static void Run(string myQueueItem, out CreateMessageOptions message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    message = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    message.Body = msg;
}
```

不能在异步代码中使用 out 参数。 下面是异步 C# 脚本代码的示例：

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static async Task Run(string myQueueItem, IAsyncCollector<CreateMessageOptions> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    CreateMessageOptions smsText = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    smsText.Body = msg;

    await message.AddAsync(smsText);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示 *function.json* 文件中的一个 Twilio 输出绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。

下面是 *function.json* 文件中的绑定数据：

示例 function.json：

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

JavaScript 代码如下所示：

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message in the binding, you must at least
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. The "To" number 
    // must be specified in code. 
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

下面的示例演示如何使用输出绑定发送 SMS 消息，如下面 *function.js*中所定义。

```json
    {
      "type": "twilioSms",
      "name": "twilioMessage",
      "accountSidSetting": "TwilioAccountSID",
      "authTokenSetting": "TwilioAuthToken",
      "from": "+1XXXXXXXXXX",
      "direction": "out",
      "body": "Azure Functions Testing"
    }
```

可以将序列化的 JSON 对象传递到 `func.Out` 参数以发送 SMS 消息。

```python
import logging
import json
import azure.functions as func

def main(req: func.HttpRequest, twilioMessage: func.Out[str]) -> func.HttpResponse:

    message = req.params.get('message')
    to = req.params.get('to')

    value = {
      "body": message,
      "to": to
    }

    twilioMessage.set(json.dumps(value))

    return func.HttpResponse(f"Message sent")
```

# <a name="java"></a>[Java](#tab/java)

下面的示例演示如何使用 [TwilioSmsOutput](/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) 批注发送短信。 `to` `from` 属性定义中需要、和的值， `body` 即使您以编程方式重写它们也是如此。

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class TwilioOutput {

    @FunctionName("TwilioOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST },
                authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @TwilioSmsOutput(
                name = "twilioMessage",
                accountSid = "AzureWebJobsTwilioAccountSID",
                authToken = "AzureWebJobsTwilioAuthToken",
                to = "+1XXXXXXXXXX",
                body = "From Azure Functions",
                from = "+1XXXXXXXXXX") OutputBinding<String> twilioMessage,
            final ExecutionContext context) {

        String message = request.getQueryParameters().get("message");
        String to = request.getQueryParameters().get("to");

        StringBuilder builder = new StringBuilder()
            .append("{")
            .append("\"body\": \"%s\",")
            .append("\"to\": \"%s\"")
            .append("}");

        final String body = String.format(builder.toString(), message, to);

        twilioMessage.setValue(body);

        return request.createResponseBuilder(HttpStatus.OK).body("Message sent").build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) 特性。

有关可以配置的特性属性的信息，请参阅[配置](#configuration)。 下面是某个方法签名中的 `TwilioSms` 特性示例：

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
public static CreateMessageOptions Run(
[QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, ILogger log)
{
    ...
}
 ```

有关完整示例，请参阅 [c # 示例](#example)。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

# <a name="java"></a>[Java](#tab/java)

将 [TwilioSmsOutput](/java/api/com.microsoft.azure.functions.annotation.twiliosmsoutput) 批注放置在 [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) 参数上 `T` ，其中可以是任何本机 Java 类型 `int` ，如、 `String` 、 `byte[]` 或 POJO 类型。

---

## <a name="configuration"></a>配置

下表解释了在 function.json 文件和 `TwilioSms` 特性中设置的绑定配置属性。

| v1 function.json 属性 | v2 function.json 属性 | Attribute 属性 |说明|
|---------|---------|---------|----------------------|
|**type**|**type**| 必须设置为 `twilioSms`。|
|**direction**|**direction**| 必须设置为 `out`。|
|name|name| 在 Twilio 短信的函数代码中使用的变量名。 |
|**accountSid**|**accountSidSetting**| **AccountSidSetting**| 此值必须设置为保留 Twilio 帐户 Sid 的应用设置的名称 (`TwilioAccountSid`) 。 如果未设置，默认应用设置名称为“AzureWebJobsTwilioAccountSid”。 |
|**authToken**|**AuthTokenSetting**|**AuthTokenSetting**| 此值必须设置为保留 Twilio 身份验证令牌 () 的应用设置的名称 `TwilioAccountAuthToken` 。 如果未设置，默认应用设置名称为“AzureWebJobsTwilioAuthToken”。 |
|**to**| 不适用 - 在代码中指定 | **To**| 此值设置为短信将发送到的电话号码。|
|**from**|**from** | **From**| 此值设置为发送短信的电话号码。|
|**body**|**body** | **正文**| 如果不需要在函数的代码中动态设置短信，则可以使用此值对其进行硬编码。 |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
