---
title: 开发 & 配置 Azure Functions 应用-Azure SignalR
description: 有关如何使用 Azure Functions 和 Azure SignalR 服务开发和配置无服务器实时应用程序的详细信息
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: 68ada90699fe9a9db6faeb32a04e8eb02c176944
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2019
ms.locfileid: "74157654"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>通过 Azure SignalR 服务 Azure Functions 开发和配置

Azure Functions 应用程序可以利用[Azure SignalR 服务绑定](../azure-functions/functions-bindings-signalr-service.md)来添加实时功能。 客户端应用程序使用几种语言提供的客户端 Sdk 连接到 Azure SignalR 服务并接收实时消息。

本文介绍了用于开发和配置与 SignalR 服务集成的 Azure Function app 的概念。

## <a name="signalr-service-configuration"></a>SignalR 服务配置

可以在不同模式下配置 Azure SignalR 服务。 与 Azure Functions 一起使用时，必须在*无服务器*模式下配置服务。

在 Azure 门户中，找到 SignalR 服务资源的 "*设置*" 页。 将*服务模式*设置为*无服务器*。

![SignalR 服务模式](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions 开发

使用 Azure Functions 和 Azure SignalR 服务构建的无服务器实时应用程序通常需要两个 Azure Functions：

* "协商" 函数，客户端调用该函数以获取有效的 SignalR 服务访问令牌和服务终结点 URL
* 用于发送消息或管理组成员身份的一个或多个函数

### <a name="negotiate-function"></a>negotiate 函数

客户端应用程序需要使用有效的访问令牌来连接到 Azure SignalR 服务。 访问令牌可以是匿名的，也可以是通过身份验证的给定用户 ID。 无服务器 SignalR 服务应用程序需要名为 "协商" 的 HTTP 终结点才能获取令牌和其他连接信息，如 SignalR 服务终结点 URL。

使用 HTTP 触发的 Azure 函数和*SignalRConnectionInfo*输入绑定生成连接信息对象。 函数必须具有以 `/negotiate`结尾的 HTTP 路由。

有关如何创建 negotiate 函数的详细信息，请参阅[ *SignalRConnectionInfo*输入绑定引用](../azure-functions/functions-bindings-signalr-service.md#signalr-connection-info-input-binding)。

若要了解如何创建经过身份验证的令牌，请参阅[使用应用服务身份验证](#using-app-service-authentication)。

### <a name="sending-messages-and-managing-group-membership"></a>发送消息和管理组成员身份

使用*SignalR*输出绑定将消息发送到连接到 Azure SignalR 服务的客户端。 你可以将消息广播到所有客户端，或者可以将其发送到使用特定用户 ID 进行身份验证或已添加到特定组的一部分客户端。

用户可以添加到一个或多个组。 还可以使用*SignalR*输出绑定在组中添加或删除用户。

有关详细信息，请参阅[ *SignalR*输出绑定引用](../azure-functions/functions-bindings-signalr-service.md#signalr-output-binding)。

### <a name="signalr-hubs"></a>SignalR 中心

SignalR 具有 "集线器" 的概念。 每个客户端连接和从 Azure Functions 发送的每个消息的范围限定为特定的中心。 可以使用集线器作为将连接和消息分为逻辑命名空间的一种方法。

## <a name="client-development"></a>客户端开发

SignalR 客户端应用程序可以使用多种语言之一中的 SignalR 客户端 SDK 轻松连接到 Azure SignalR 服务并从其接收消息。

### <a name="configuring-a-client-connection"></a>配置客户端连接

若要连接到 SignalR 服务，客户端必须完成由以下步骤组成的成功连接协商：

1. 向上述*协商*HTTP 终结点发出请求，以获取有效的连接信息
1. 使用服务终结点 URL 和从*协商*终结点获取的访问令牌连接到 SignalR 服务

SignalR 客户端 Sdk 已经包含执行协商握手所需的逻辑。 向 SDK 的 `HubConnectionBuilder`传递 negotiate 终结点的 URL，减去 `negotiate` 段。 下面是 JavaScript 中的一个示例：

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

按照约定，SDK 会自动将 `/negotiate` 追加到 URL，并使用该 URL 开始协商。

> [!NOTE]
> 如果在浏览器中使用 JavaScript/TypeScript SDK，则需要在 Function App 上[启用跨域资源共享（CORS）](#enabling-cors) 。

有关如何使用 SignalR 客户端 SDK 的详细信息，请参阅适用于你的语言的文档：

* [.NET 标准](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>将消息从客户端发送到服务

尽管 SignalR SDK 允许客户端应用程序在 SignalR 中心调用后端逻辑，但在 Azure Functions 使用 SignalR 服务时，尚不支持此功能。 使用 HTTP 请求调用 Azure Functions。

## <a name="azure-functions-configuration"></a>Azure Functions 配置

与 Azure SignalR 服务集成的 azure Function apps 可以像使用[持续部署](../azure-functions/functions-continuous-deployment.md)、 [zip 部署](../azure-functions/deployment-zip-push.md)和[从包运行](../azure-functions/run-functions-from-deployment-package.md)这样的技术一样部署，如任何典型的 azure function app。

但对于使用 SignalR 服务绑定的应用，有几个特殊的注意事项。 如果客户端在浏览器中运行，则必须启用 CORS。 如果应用需要身份验证，则可以将协商终结点与应用服务身份验证集成。

### <a name="enabling-cors"></a>启用 CORS

JavaScript/TypeScript 客户端向 negotiate 函数发出 HTTP 请求，以启动连接协商。 如果客户端应用程序承载于 Azure Function app 以外的域中，则必须在 Function app 上启用跨域资源共享（CORS），否则，浏览器会阻止请求。

#### <a name="localhost"></a>主机

在本地计算机上运行函数应用时，可以将 `Host` 部分添加到*本地。设置*为启用 CORS。 在 `Host` 部分中，添加两个属性：

* `CORS`-输入作为客户端应用程序来源的基本 URL
* `CORSCredentials`-将其设置为 `true` 以允许 "withCredentials" 请求

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

#### <a name="cloud---azure-functions-cors"></a>云 Azure Functions CORS

若要在 Azure Function app 上启用 CORS，请在 Azure 门户中的函数应用的 "*平台功能*" 选项卡下，中转到 "cors 配置" 屏幕。

> [!NOTE]
> CORS 配置在 Azure Functions Linux 消耗计划中尚不可用。 使用[AZURE API 管理](#cloud---azure-api-management)启用 CORS。

必须为 SignalR 客户端启用具有访问控制-允许凭据的 CORS，才能调用 negotiate 函数。 选中复选框以启用它。

在 "*允许的来源*" 部分中，添加一个条目，其中包含 web 应用程序的源基 URL。

![配置 CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>云-Azure API 管理

Azure API 管理提供了一个 API 网关，可向现有后端服务添加功能。 可以使用它将 CORS 添加到 function app。 它提供使用按操作付费定价和每月免费赠与的消耗层。

有关如何[导入 Azure Function app](../api-management/import-function-app-as-api.md)的信息，请参阅 API 管理文档。 导入后，可以添加一个入站策略，以启用具有访问控制允许的 CORS 支持的 CORS。

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

将 SignalR 客户端配置为使用 API 管理 URL。

### <a name="using-app-service-authentication"></a>使用应用服务身份验证

Azure Functions 提供内置身份验证，支持 Facebook、Twitter、Microsoft 帐户、Google 和 Azure Active Directory 等常用访问接口。 此功能可以与*SignalRConnectionInfo*绑定集成，以创建到已通过用户 ID 身份验证的 Azure SignalR 服务的连接。 应用程序可以使用以该用户 ID 为目标的*SignalR*输出绑定来发送消息。

在 Azure 门户的函数应用的 "*平台功能*" 选项卡中，打开 "*身份验证/授权*设置" 窗口。 按照[应用服务身份验证](../app-service/overview-authentication-authorization.md)的文档，使用所选的标识提供者配置身份验证。

配置后，经过身份验证的 HTTP 请求将分别包含 `x-ms-client-principal-name` 和 `x-ms-client-principal-id` 标头，其中包含经过身份验证的标识的用户名和用户 ID。

你可以在*SignalRConnectionInfo*绑定配置中使用这些标头来创建经过身份验证的连接。 下面是一个使用C# `x-ms-client-principal-id` 标头的 negotiate 函数示例。

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

然后，可以通过设置 SignalR 消息的 `UserId` 属性向该用户发送消息。

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

有关其他语言的信息，请参阅[Azure SignalR 服务绑定](../azure-functions/functions-bindings-signalr-service.md)Azure Functions 引用。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 Azure Functions 开发和配置无服务器 SignalR 服务应用程序。 尝试使用[SignalR 服务概述页](index.yml)上的快速入门或教程之一自行创建应用程序。