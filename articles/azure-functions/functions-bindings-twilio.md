---
title: Azure Functions Twilio 绑定
description: 了解如何将 Azure Functions 与 Twilio 绑定配合使用。
author: craigshoemaker
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee78234b43e94a5c537161556410ee6246e625ca
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230945"
---
# <a name="twilio-binding-for-azure-functions"></a>Azure Functions 的 Twilio 绑定

本文介绍如何使用 Azure Functions 中的 [Twilio](https://www.twilio.com/) 绑定发送短信。 Azure Functions 支持 Twilio 的输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>包 - Functions 2.x

[Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet 包 1.x 版中提供了 Twilio 绑定。 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>包 - Functions 2.x

[Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio) NuGet 包 3.x 版中提供了 Twilio 绑定。 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example---functions-1x"></a>示例 - Functions 1.x

参阅语言特定的示例：

* [C#](#c-example)
* [C# 脚本 (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>C# 示例

以下示例演示在队列消息触发时发送短信的 [C# 函数](functions-dotnet-class-library.md)。

```cs
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

此示例使用包含方法返回值的 `TwilioSms` 特性。 一种替代方法是使用包含 `out SMSMessage` 参数或者 `ICollector<SMSMessage>` 或 `IAsyncCollector<SMSMessage>` 参数的特性。

### <a name="c-script-example"></a>C# 脚本示例

以下示例演示 *function.json* 文件中的一个 Twilio 输出绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数使用 `out` 参数发送短信。

下面是 *function.json* 文件中的绑定数据：

示例 function.json：

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

C# 脚本代码如下所示：

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

不能在异步代码中使用 out 参数。 下面是异步 C# 脚本代码的示例：

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

### <a name="javascript-example"></a>JavaScript 示例

以下示例演示 *function.json* 文件中的一个 Twilio 输出绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。

下面是 *function.json* 文件中的绑定数据：

示例 function.json：

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
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

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="example---functions-2x"></a>示例 - Functions 2.x

参阅语言特定的示例：

* [2.x C#](#2x-c-example)
* [2.x C# 脚本 (.csx)](#2x-c-script-example)
* [2.x JavaScript](#2x-javascript-example)

### <a name="2x-c-example"></a>2.x C# 示例

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

### <a name="2x-c-script-example"></a>2.x C# 脚本示例

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

### <a name="2x-javascript-example"></a>2.x JavaScript 示例

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

## <a name="attributes"></a>属性

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

有关完整示例，请参阅 [C# 示例](#c-example)。

## <a name="configuration"></a>配置

下表解释了在 function.json 文件和 `TwilioSms` 特性中设置的绑定配置属性。

| v1 function.json 属性 | v2 function.json 属性 | Attribute 属性 |描述|
|---------|---------|---------|----------------------|
|类型|类型| 必须设置为 `twilioSms`。|
|direction|direction| 必须设置为 `out`。|
|name|name| 在 Twilio 短信的函数代码中使用的变量名。 |
|**accountSid**|**AccountSidSetting**| **AccountSidSetting**| 此值必须设置为保留 Twilio 帐户 Sid 的应用设置的名称，例如 TwilioAccountSid。 如果未设置，默认应用设置名称为“AzureWebJobsTwilioAccountSid”。 |
|**authToken**|**AuthTokenSetting**|**AuthTokenSetting**| 此值必须设置为保留 Twilio 身份验证令牌的应用设置的名称，例如 TwilioAccountAuthToken。 如果未设置，默认应用设置名称为“AzureWebJobsTwilioAuthToken”。 |
|**to**| 不适用 - 在代码中指定 | **收件人**| 此值设置为短信将发送到的电话号码。|
|**from**|**from** | **From**| 此值设置为发送短信的电话号码。|
|**body**|**body** | **正文**| 如果不需要在函数的代码中动态设置短信，则可以使用此值对其进行硬编码。 |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
