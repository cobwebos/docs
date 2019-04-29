---
title: 开发和配置 Azure Functions SignalR 服务应用程序
description: 有关如何开发和配置使用 Azure Functions 和 Azure SignalR 服务的无服务器实时应用程序的详细信息
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: 9b68b9d0bbac984c29759cf4b7b026a559a9d819
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808998"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure Functions 开发和使用 Azure SignalR 服务的配置

Azure Functions 应用程序可以利用[Azure SignalR 服务绑定](../azure-functions/functions-bindings-signalr-service.md)添加实时功能。 客户端应用程序使用客户端 Sdk 提供了多种语言连接到 Azure SignalR 服务并接收实时消息。

本文介绍有关开发和配置 Azure Function app 与 SignalR 服务相集成的概念。

## <a name="signalr-service-configuration"></a>SignalR 服务配置

Azure SignalR 服务可以在不同模式下配置。 此服务时与 Azure Functions 一起使用，必须在配置*无服务器*模式。

在 Azure 门户中，找到*设置*SignalR 服务资源页面。 设置*服务模式*到*无服务器*。

![SignalR 服务模式](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions 开发

通常构建使用 Azure Functions 和 Azure SignalR 服务的无服务器实时应用程序需要两个 Azure 函数：

* 客户端调用以获取有效的 SignalR 服务的"协商"函数访问令牌和服务终结点 URL
* 将消息发送或管理组成员身份的一个或多个函数

### <a name="negotiate-function"></a>协商函数

客户端应用程序要求用于连接到 Azure SignalR 服务的有效的访问令牌。 访问令牌可以是匿名的或经过身份验证为给定的用户 id。 无服务器 SignalR 服务应用程序需要 HTTP 终结点命名为"协商"来获取令牌和其他连接信息，例如 SignalR 服务终结点 URL。

使用 HTTP 触发 Azure 函数和*SignalRConnectionInfo*输入绑定生成的连接信息对象。 该函数必须具有在结束一个 HTTP 路由`/negotiate`。

有关如何创建 negotiate 函数的详细信息，请参阅[ *SignalRConnectionInfo*输入绑定引用](../azure-functions/functions-bindings-signalr-service.md#signalr-connection-info-input-binding)。

若要了解有关如何创建一个经过身份验证的令牌，请参阅[使用应用服务身份验证](#using-app-service-authentication)。

### <a name="sending-messages-and-managing-group-membership"></a>发送消息，以及管理组成员资格

使用*SignalR*输出绑定将消息发送到客户端连接到 Azure SignalR 服务。 你已可以将消息广播给所有客户端，或者可以将其发送到客户端使用特定的用户 ID 进行身份验证或已添加到特定组的子集。

用户可以添加到一个或多个组。 此外可以使用*SignalR*输出绑定来添加或删除用户/组。

有关详细信息，请参阅[ *SignalR*输出绑定引用](../azure-functions/functions-bindings-signalr-service.md#signalr-output-binding)。

### <a name="signalr-hubs"></a>SignalR 集线器

SignalR 有一个"中心"的概念。 从 Azure Functions 发送每条消息以及每个客户端连接应用范围限定为特定的中心。 作为一种方法来连接和消息分成逻辑命名空间，可以使用中心。

## <a name="client-development"></a>客户端开发

SignalR 客户端应用程序可以利用 SignalR 客户端 SDK 中有几种语言之一，可轻松连接并接收来自 Azure SignalR 服务的消息。

### <a name="configuring-a-client-connection"></a>配置客户端连接

若要连接到 SignalR 服务，客户端必须完成这些步骤组成的成功连接协商：

1. 向发送请求*协商*HTTP 终结点上面讨论以获取有效的连接信息
1. 连接到 SignalR 服务使用的服务终结点 URL，并从获取访问令牌*协商*终结点

SignalR 客户端 Sdk 尚未包含执行协商握手所需的逻辑。 Negotiate 终结点的 URL 传递减`negotiate`段，到 SDK 的`HubConnectionBuilder`。 下面是 JavaScript 中的一个示例：

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

按照约定，SDK 会自动追加`/negotiate`到 URL 并使用它来开始协商。

> [!NOTE]
> 如果在浏览器中使用 JavaScript/TypeScript SDK，则需要[启用跨域资源共享 (CORS)](#enabling-cors)在 Function App 上。

有关如何使用 SignalR 客户端 SDK 的详细信息，请参阅您的语言的文档：

* [.NET 标准](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>从客户端向服务发送消息

尽管 SignalR SDK 允许客户端应用程序以调用中的 SignalR hub 的后端逻辑，但此功能尚不支持 Azure Functions 中使用 SignalR 服务时。 使用 HTTP 请求来调用 Azure 函数。

## <a name="azure-functions-configuration"></a>Azure Functions 配置

与 Azure SignalR 服务集成的 azure 函数应用可部署任何典型的 Azure 函数应用，如使用技术一样[连续部署](../azure-functions/functions-continuous-deployment.md)， [zip 部署](../azure-functions/deployment-zip-push.md)，并且[包从运行](../azure-functions/run-functions-from-deployment-package.md)。

但是，有几个使用 SignalR 服务绑定的应用程序的特殊注意事项。 如果客户端运行在浏览器中，必须启用 CORS。 如果应用需要身份验证，可以使用应用服务身份验证集成 negotiate 终结点。

### <a name="enabling-cors"></a>启用 CORS

JavaScript/TypeScript 客户端发出 HTTP 请求到 negotiate 函数，以启动连接协商。 当在不同的域的 Azure 函数应用托管客户端应用程序，则必须在 Function app 上启用跨域资源共享 (CORS)，或浏览器会阻止请求。

#### <a name="localhost"></a>localhost

在本地计算机上运行函数应用，你可以添加`Host`部分*local.settings.json*启用 CORS。 在`Host`部分中，添加两个属性：

* `CORS` -输入是源客户端应用程序的基 URL
* `CORSCredentials` -将其设置为`true`以允许"withCredentials"的请求

示例：

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="azure"></a>Azure

要在一个 Azure Function app 上启用 CORS，请转到下 CORS 配置屏幕*平台功能*Function app 在 Azure 门户中的选项卡。

必须为 SignalR 客户端调用 negotiate 函数启用 CORS 的访问控制的允许的凭据。 选中复选框来启用它。

在中*允许的来源*部分中，添加一个具有 web 应用程序的源基 URL 的项。

![配置 CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

### <a name="using-app-service-authentication"></a>使用应用服务身份验证

Azure Functions 有内置的身份验证，支持常用的提供程序，如 Facebook、 Twitter、 Microsoft 帐户、 Google 和 Azure Active Directory。 此功能可以与集成*SignalRConnectionInfo*绑定以创建连接到 Azure SignalR 服务的已验证了用户 id。 你的应用程序可以使用发送消息*SignalR*输出绑定的目标为该用户 id。

在 Azure 门户中 Function app*平台功能*选项卡上，打开*身份验证/授权*设置窗口。 按照文档中的[应用服务身份验证](../app-service/overview-authentication-authorization.md)配置使用所选的标识提供者的身份验证。

配置后，将包括已经过身份验证的 HTTP 请求`x-ms-client-principal-name`和`x-ms-client-principal-id`分别包含经过身份验证的标识的用户名和用户 ID 的标头。

可以使用这些标头中的您*SignalRConnectionInfo*绑定配置来创建经过身份验证的连接。 下面是一个示例C#协商使用函数`x-ms-client-principal-id`标头。

```csharp
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

您可以将发送消息给该用户通过设置`UserId`SignalR 消息的属性。

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

有关其他语言的信息，请参阅[Azure SignalR 服务绑定](../azure-functions/functions-bindings-signalr-service.md)的 Azure 函数引用。

## <a name="next-steps"></a>后续步骤

在本文中，你学习了如何开发和配置使用 Azure Functions 的无服务器 SignalR 服务应用程序。 尝试创建应用程序上使用其中一个快速入门或教程自己[SignalR 服务概述页](index.yml)。