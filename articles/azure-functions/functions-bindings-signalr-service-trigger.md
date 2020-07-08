---
title: Azure Functions SignalR 服务触发器绑定
description: 了解如何从 Azure Functions 发送 SignalR Service 消息。
author: chenyl
ms.topic: reference
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: c2ad9b6c4410a62d5652050406e05be4cde5fab0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85830700"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>Azure Functions 的 SignalR 服务触发器绑定

使用*SignalR*触发器绑定来响应从 Azure SignalR 服务发送的消息。 触发函数时，传递给函数的消息将被分析为 json 对象。

若要了解设置和配置详细信息，请参阅[概述](functions-bindings-signalr-service.md)。

## <a name="example"></a>示例

下面的示例演示一个函数，该函数使用触发器绑定接收一条消息并记录该消息。

# <a name="c"></a>[C#](#tab/csharp)

适用于 c # 的 SignalR 服务触发器绑定具有两种编程模型。 基于类的模型和传统模型。 基于类的模型可提供一致的 SignalR 服务器端编程体验。 和传统模型具有更大的灵活性，与其他函数绑定类似。

### <a name="with-class-based-model"></a>具有基于类的模型

有关详细信息，请参阅[基于类的模型](../azure-signalr/signalr-concept-serverless-development-config.md#class-based-model)。

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("SignalRTest")]
    public async Task SendMessage([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
    }
}
```

### <a name="with-traditional-model"></a>传统模型

传统模型服从由 c # 开发的 Azure 函数的约定。 如果不熟悉，可以从[文档](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-class-library)学习。

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage", parameterNames: new string[] {"message"})]InvocationContext invocationContext, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

#### <a name="use-attribute-signalrparameter-to-simplify-parameternames"></a>使用特性 `[SignalRParameter]` 简化`ParameterNames`

由于使用非常麻烦 `ParameterNames` ，因此 `SignalRParameter` 提供了相同的目的。

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage")]InvocationContext invocationContext, [SignalRParameter]string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

下面是 *function.json* 文件中的绑定数据：

示例 function.json：

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

C# 脚本代码如下所示：

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using System;
using Microsoft.Azure.WebJobs.Extensions.SignalRService;
using Microsoft.Extensions.Logging;

public static void Run(InvocationContext invocation, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下面是 *function.json* 文件中的绑定数据：

示例 function.json：

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

JavaScript 代码如下所示：

```javascript
module.exports = function (context, invocation) {
    context.log(`Receive ${context.bindingData.message} from ${invocation.ConnectionId}.`)
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

下面是 *function.json* 文件中的绑定数据：

示例 function.json：

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

下面是 Python 代码：

```python
import logging
import json
import azure.functions as func

def main(invocation) -> None:
    invocation_json = json.loads(invocation)
    logging.info("Receive {0} from {1}".format(invocation_json['Arguments'][0], invocation_json['ConnectionId']))
```

---

## <a name="configuration"></a>配置

### <a name="signalrtrigger"></a>SignalRTrigger

下表解释了在 function.json 文件和 `SignalRTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**type**| 不适用 | 必须设置为 `SignalRTrigger`。|
|**direction**| 不适用 | 必须设置为 `in`。|
|**name**| 不适用 | 用于触发器调用上下文对象的函数代码中使用的变量名称。 |
|**hubName**|**HubName**| 此值必须设置为要触发的函数的 SignalR 集线器的名称。|
|**category**|**类别**| 此值必须设置为要触发的函数的消息类别。 类别可以是以下值之一： <ul><li>**连接**：包括*连接*事件和已*断开*连接的事件</li><li>**消息**：包含除*connections*类中的所有其他事件</li></ul> |
|**event**|**事件**| 此值必须设置为要触发的函数的消息事件。 对于 "*消息*类别"，事件是客户端发送的[调用消息](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)中的*目标*。 对于 "*连接*" 类别，只使用 "*连接*" 和 "*断开*连接"。 |
|**parameterNames**|**ParameterNames**| 可有可无绑定到参数的名称的列表。 |
|**connectionStringSetting**|**ConnectionStringSetting**| 应用设置的名称，该设置包含 SignalR 服务连接字符串（默认为“AzureSignalRConnectionString”） |

## <a name="payload"></a>有效负载

触发器输入类型声明为 `InvocationContext` 或自定义类型。 如果你选择 `InvocationContext` 对请求内容具有完全访问权限。 对于自定义类型，运行时会尝试分析 JSON 请求正文，以设置对象属性。

### <a name="invocationcontext"></a>InvocationContext

InvocationContext 包含 SignalR 服务发送的消息中的所有内容。

|InvocationContext 中的属性 | 描述|
|------------------------------|------------|
|自变量| 可用于*消息*类别。 在[调用消息](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)中包含*参数*|
|错误| 可用于*断开连接*的事件。 如果在没有错误的情况下关闭连接或包含错误消息，则可以为空。|
|Hub| 消息所属的中心名称。|
|类别| 消息的类别。|
|事件| 消息的事件。|
|ConnectionId| 发送消息的客户端的连接 ID。|
|UserId| 发送消息的客户端的用户标识。|
|头文件| 请求的标头。|
|查询| 客户端连接到服务时请求的查询。|
|声明| 客户端的声明。|

## <a name="using-parameternames"></a>使用 `ParameterNames`

中的 `ParameterNames` 属性 `SignalRTrigger` 允许你将调用消息的参数绑定到函数的参数。 这为你提供了一种更方便的方法来访问的参数 `InvocationContext` 。

假设你有一个 JavaScript SignalR 客户端尝试 `broadcast` 在 Azure 函数中使用两个参数调用方法。

```javascript
await connection.invoke("broadcast", message1, message2);
```

你可以从参数访问这两个参数，并通过使用为其分配参数类型 `ParameterNames` 。

### <a name="remarks"></a>注解

对于参数绑定，顺序很重要。 如果使用的是 `ParameterNames` ，中的顺序 `ParameterNames` 与您在客户端中调用的参数的顺序相匹配。 如果使用 `[SignalRParameter]` 的是 c # 中的属性，则 Azure Function 方法中的参数顺序与客户端中参数的顺序相匹配。

`ParameterNames``[SignalRParameter]`**不能**同时使用和特性，否则将出现异常。

## <a name="send-messages-to-signalr-service-trigger-binding"></a>将消息发送到 SignalR 服务触发器绑定

Azure Function 生成 SignalR 服务触发器绑定的 URL，其格式如下：

```http
https://<APP_NAME>.azurewebsites.net/runtime/webhooks/signalr?code=<API_KEY>
```

`API_KEY`由 Azure Function 生成。 `API_KEY`当你使用 SignalR 服务触发器绑定时，你可以从 Azure 门户中获取。
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="API 密钥":::

你应在 SignalR 服务的上游设置中设置此 URL `UrlTemplate` 。

## <a name="next-steps"></a>后续步骤

* [通过 Azure SignalR 服务进行的 Azure Functions 开发和配置](../azure-signalr/signalr-concept-serverless-development-config.md)
* [SignalR 服务触发器绑定示例](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples/bidirectional-chat)