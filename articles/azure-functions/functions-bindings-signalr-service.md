---
title: Azure Functions SignalR 服务绑定
description: 了解如何将 Azure Functions 与 SignalR 服务绑定配合使用。
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
editor: ''
tags: ''
keywords: Azure Functions，函数，事件处理，动态计算，无服务体系结构
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: f0d4a607676285ed4f0f91d8ce8c83ddf1313b89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306810"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Azure Functions 的 SignalR Service 绑定

本文介绍如何使用 Azure Functions 中的 SignalR 服务绑定进行身份验证，并向连接到 [Azure SignalR 服务](https://azure.microsoft.com/services/signalr-service/)的客户端发送实时消息。 Azure Functions 支持 SignalR 服务的输入和输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>包 - Functions 2.x

中提供 SignalR 服务绑定[Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) NuGet 包，版本 1.*。 [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Java 注释

若要在 Java 函数中使用 SignalR 服务批注，您需要添加依赖项*azure-函数-java-库-signalr*到 pom.xml 项目 （版本 1.0 或更高版本）。

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> 若要在 Java 中使用 SignalR 服务绑定，请确保使用的是 Azure Functions Core Tools（主机版本 2.0.12332）2.4.419 版或更高版本。

## <a name="using-signalr-service-with-azure-functions"></a>使用 Azure Functions 和 SignalR 服务

有关如何配置和将 SignalR 服务和 Azure Functions 一起使用的详细信息，请参阅[Azure Functions 开发和使用 Azure SignalR 服务配置](../azure-signalr/signalr-concept-serverless-development-config.md)。

## <a name="signalr-connection-info-input-binding"></a>SignalR 连接信息输入绑定

客户端在连接到 Azure SignalR 服务之前，必须检索服务终结点 URL 和有效的访问令牌。 *SignalRConnectionInfo* 输入绑定生成 SignalR 服务终结点 URL 和有效的令牌，这两者可以用来连接到服务。 由于此令牌有时间限制，并且可以用来对需要连接的特定用户进行身份验证，因此不应缓存此令牌，也不应在客户端之间共享它。 使用此绑定的 HTTP 触发器可供客户端用来检索连接信息。

参阅语言特定的示例：

* [2.x C#](#2x-c-input-examples)
* [2.x JavaScript](#2x-javascript-input-examples)
* [2.x Java](#2x-java-input-examples)

有关如何使用此绑定创建一个可供 SignalR 客户端 SDK 的"协商"函数的详细信息，请参阅[Azure Functions 开发和配置项目](../azure-signalr/signalr-concept-serverless-development-config.md)SignalR 服务概念文档。

### <a name="2x-c-input-examples"></a>2.xC#输入示例

以下示例演示了一个 [C# 函数](functions-dotnet-class-library.md)，该函数使用输入绑定获取 SignalR 连接信息，并通过 HTTP 将其返回。

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>已进行身份验证的令牌

如果此函数由经过身份验证的客户端触发，则可向生成的令牌添加用户 ID 声明。 您可以轻松地将身份验证添加到函数应用使用[应用服务身份验证](../app-service/overview-authentication-authorization.md)。

应用服务身份验证会设置名为 `x-ms-client-principal-id` 和 `x-ms-client-principal-name`（分别包含经身份验证的用户的客户端主体 ID 和名称）的 HTTP 标头。 可以使用[绑定表达式](./functions-bindings-expressions-patterns.md) `{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}` 将绑定的 `UserId` 属性设置为任一标头中的值。 

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

### <a name="2x-javascript-input-examples"></a>2.x JavaScript 输入的示例

以下示例演示 *function.json* 文件中的一个 SignalR 连接信息输入绑定，以及使用该绑定来返回连接信息的 [JavaScript 函数](functions-reference-node.md)。

下面是 *function.json* 文件中的绑定数据：

示例 function.json：

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

JavaScript 代码如下所示：

```javascript
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

#### <a name="authenticated-tokens"></a>已进行身份验证的令牌

如果此函数由经过身份验证的客户端触发，则可向生成的令牌添加用户 ID 声明。 您可以轻松地将身份验证添加到函数应用使用[应用服务身份验证](../app-service/overview-authentication-authorization.md)。

应用服务身份验证会设置名为 `x-ms-client-principal-id` 和 `x-ms-client-principal-name`（分别包含经身份验证的用户的客户端主体 ID 和名称）的 HTTP 标头。 可以使用[绑定表达式](./functions-bindings-expressions-patterns.md) `{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}` 将绑定的 `userId` 属性设置为任一标头中的值。 

示例 function.json：

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

JavaScript 代码如下所示：

```javascript
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

### <a name="2x-java-input-examples"></a>2.x Java 输入的示例

下面的示例演示[Java 函数](functions-reference-java.md)，获取使用输入的绑定的 SignalR 连接信息并将其返回通过 HTTP。

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>已进行身份验证的令牌

如果此函数由经过身份验证的客户端触发，则可向生成的令牌添加用户 ID 声明。 您可以轻松地将身份验证添加到函数应用使用[应用服务身份验证](../app-service/overview-authentication-authorization.md)。

应用服务身份验证会设置名为 `x-ms-client-principal-id` 和 `x-ms-client-principal-name`（分别包含经身份验证的用户的客户端主体 ID 和名称）的 HTTP 标头。 可以使用[绑定表达式](./functions-bindings-expressions-patterns.md) `{headers.x-ms-client-principal-id}` 或 `{headers.x-ms-client-principal-name}` 将绑定的 `UserId` 属性设置为任一标头中的值。

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

## <a name="signalr-output-binding"></a>SignalR 输出绑定

使用 Azure SignalR 服务，通过 *SignalR* 输出绑定发送一条或多条消息。 可以将消息广播给所有连接的客户端，也可以将消息仅广播给已针对给定用户进行身份验证的已连接客户端。

此外可以使用它来管理用户所属的组。

参阅语言特定的示例：

* [2.x C#](#2x-c-send-message-output-examples)
* [2.x JavaScript](#2x-javascript-send-message-output-examples)
* [2.x Java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>2.xC#发送消息输出示例

#### <a name="broadcast-to-all-clients"></a>广播到所有客户端

以下示例演示一个 [C# 函数](functions-dotnet-class-library.md)，该函数使用输出绑定将一条消息发送给所有连接的客户端。 `Target` 是需要在每个客户端上调用的方法的名称。 `Arguments` 属性是一个数组，其中包含要传递给客户端方法的零个或多个对象。

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

#### <a name="send-to-a-user"></a>发送给用户

可以设置 SignalR 消息的 `UserId` 属性，以便将消息只发送给已针对某个用户进行身份验证的连接。

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

#### <a name="send-to-a-group"></a>发送到组

您可以将一条消息发送到通过设置添加到组的连接仅`GroupName`SignalR 消息的属性。

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

### <a name="2x-c-group-management-output-examples"></a>2.xC#组管理输出示例

SignalR 服务允许用户添加到组中。 然后可以将消息发送到的组。 可以使用`SignalRGroupAction`类的`SignalR`输出绑定来管理用户的组成员身份。

#### <a name="add-user-to-a-group"></a>将用户添加到组

下面的示例将用户添加到组。

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    string userId,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userId,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>从组中删除用户

下面的示例从组中删除用户。

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    string userId,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userId,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

### <a name="2x-javascript-send-message-output-examples"></a>2.x JavaScript 发送消息输出示例

#### <a name="broadcast-to-all-clients"></a>广播到所有客户端

以下示例演示 *function.json* 文件中的一个 SignalR 输出绑定以及使用该绑定通过 Azure SignalR 服务发送消息的 [JavaScript 函数](functions-reference-node.md)。 将输出绑定设置为一个数组，其中包含一条或多条 SignalR 消息。 SignalR 消息包含一个 `target` 属性，用于指定要在每个客户端上调用的方法的名称；以及一个 `arguments` 属性，该属性是一个数组，其中包含要作为参数传递给客户端方法的对象。

下面是 *function.json* 文件中的绑定数据：

示例 function.json：

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

JavaScript 代码如下所示：

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-user"></a>发送给用户

可以设置 SignalR 消息的 `userId` 属性，以便将消息只发送给已针对某个用户进行身份验证的连接。

*function.json* 保持不变。 JavaScript 代码如下所示：

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-group"></a>发送到组

您可以将一条消息发送到通过设置添加到组的连接仅`groupName`SignalR 消息的属性。

*function.json* 保持不变。 JavaScript 代码如下所示：

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

### <a name="2x-javascript-group-management-output-examples"></a>2.x JavaScript 组管理输出示例

SignalR 服务允许用户添加到组中。 然后可以将消息发送到的组。 可以使用`SignalR`输出绑定来管理用户的组成员身份。

#### <a name="add-user-to-a-group"></a>将用户添加到组

下面的示例将用户添加到组。

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

#### <a name="remove-user-from-a-group"></a>从组中删除用户

下面的示例从组中删除用户。

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

### <a name="2x-java-send-message-output-examples"></a>2.x Java 发送消息输出示例

#### <a name="broadcast-to-all-clients"></a>广播到所有客户端

下面的示例演示[Java 函数](functions-reference-java.md)发送使用所有连接的客户端的输出绑定的消息。 `target` 是需要在每个客户端上调用的方法的名称。 `arguments` 属性是一个数组，其中包含要传递给客户端方法的零个或多个对象。

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-user"></a>发送给用户

可以设置 SignalR 消息的 `userId` 属性，以便将消息只发送给已针对某个用户进行身份验证的连接。

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-group"></a>发送到组

您可以将一条消息发送到通过设置添加到组的连接仅`groupName`SignalR 消息的属性。

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

### <a name="2x-java-group-management-output-examples"></a>2.x Java 组管理输出示例

SignalR 服务允许用户添加到组中。 然后可以将消息发送到的组。 可以使用`SignalRGroupAction`类的`SignalROutput`输出绑定来管理用户的组成员身份。

#### <a name="add-user-to-a-group"></a>将用户添加到组

下面的示例将用户添加到组。

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

#### <a name="remove-user-from-a-group"></a>从组中删除用户

下面的示例从组中删除用户。

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

## <a name="configuration"></a>配置

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

下表解释了在 function.json 文件和 `SignalRConnectionInfo` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type|| 必须设置为 `signalRConnectionInfo`。|
|direction|| 必须设置为 `in`。|
|name|| 变量名称，在连接信息对象的函数代码中使用。 |
|**hubName**|**HubName**| 此值必须设置为 SignalR 中心（将为其生成连接信息）的名称。|
|**userId**|**UserId**| 可选：将要在访问密钥令牌中设置的用户标识符声明的值。 |
|**connectionStringSetting**|**ConnectionStringSetting**| 应用设置的名称，该设置包含 SignalR 服务连接字符串（默认为“AzureSignalRConnectionString”） |

### <a name="signalr"></a>SignalR

下表解释了在 function.json 文件和 `SignalR` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type|| 必须设置为 `signalR`。|
|direction|| 必须设置为 `out`。|
|name|| 变量名称，在连接信息对象的函数代码中使用。 |
|**hubName**|**HubName**| 此值必须设置为 SignalR 中心（将为其生成连接信息）的名称。|
|**connectionStringSetting**|**ConnectionStringSetting**| 应用设置的名称，该设置包含 SignalR 服务连接字符串（默认为“AzureSignalRConnectionString”） |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Azure Functions 开发和使用 Azure SignalR 服务的配置](../azure-signalr/signalr-concept-serverless-development-config.md)
