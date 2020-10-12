---
title: 使用多个实例进行扩展 - Azure SignalR 服务
description: 在许多扩展方案中，客户往往需要预配多个实例，并将其配置为一同使用，以创建大规模部署。 例如，分片就需要支持多个实例。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: fd6ac8c4d4fc4c3fec4f549f8ef4f955e2b1c637
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89439208"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>如何使用多个实例扩展 SignalR 服务？
最新的 SignalR 服务 SDK 支持对 SignalR 服务实例使用多个终结点。 可以使用此功能来扩展并发连接，或将其用于跨区域的消息传送。

## <a name="for-aspnet-core"></a>对于 ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>如何通过配置添加多个终结点？

使用 SignalR 服务连接字符串的密钥 `Azure:SignalR:ConnectionString` 或 `Azure:SignalR:ConnectionString:` 进行配置。

如果密钥以 `Azure:SignalR:ConnectionString:`开头，则它应采用 `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` 格式，其中，`Name` 和 `EndpointType` 是 `ServiceEndpoint` 对象的属性（可从代码访问）。

可以使用以下 `dotnet` 命令添加多个实例连接字符串：

```cmd
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>如何通过代码添加多个终结点？

我们已引入 `ServicEndpoint` 类来描述 Azure SignalR 服务终结点的属性。
使用 Azure SignalR 服务 SDK 时，可通过以下代码配置多个实例终结点：
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>如何自定义终结点路由器？

默认情况下，SDK 使用 [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) 来选取终结点。

#### <a name="default-behavior"></a>默认行为 
1. 客户端请求路由

    当客户端通过 `/negotiate` 与应用服务器协商时， SDK 默认会从可用服务终结点集内**随机选择**一个终结点。

2. 服务器消息路由

    向特定的连接发送消息时，如果目标连接路由到当前服务器，则消息将直接转到已连接的该终结点。 否则，消息将广播到每个 Azure SignalR 终结点。

#### <a name="customize-routing-algorithm"></a>自定义路由算法
如果你具备专业的知识，可以识别消息会转到哪些终结点，则可以创建自己的路由器。

下面定义了一个自定义路由器示例，演示以 `east-` 开头的组的消息始终转到名为 `east` 的终结点：

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

下面提供了另一个示例，它替代了默认的协商行为，根据应用服务器所在的位置选择终结点。

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

请不要忘记使用以下代码将路由器注册到 DI 容器：

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>对于 ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>如何通过配置添加多个终结点？

使用 SignalR 服务连接字符串的密钥 `Azure:SignalR:ConnectionString` 或 `Azure:SignalR:ConnectionString:` 进行配置。

如果密钥以 `Azure:SignalR:ConnectionString:`开头，则它应采用 `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` 格式，其中，`Name` 和 `EndpointType` 是 `ServiceEndpoint` 对象的属性（可从代码访问）。

可将多个实例连接字符串添加到 `web.config`：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>如何通过代码添加多个终结点？

我们已引入 `ServicEndpoint` 类来描述 Azure SignalR 服务终结点的属性。
使用 Azure SignalR 服务 SDK 时，可通过以下代码配置多个实例终结点：

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>如何自定义路由器？

ASP.NET SignalR 与 ASP.NET Core SignalR 之间的唯一差别在于 `GetNegotiateEndpoint` 的 HTTP 上下文类型。 ASP.NET SignalR 的 HTTP 上下文类型为 [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19)。

下面是 ASP.NET SignalR 的自定义协商示例：

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

请不要忘记使用以下代码将路由器注册到 DI 容器：

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>跨区域方案中的配置

`ServiceEndpoint` 对象包含值为 `primary` 或 `secondary` 的 `EndpointType` 属性。

`primary` 终结点是接收客户端流量的首选终结点，我们认为其网络连接更可靠；`secondary` 终结点的网络连接被认为较不可靠，仅用于接收从服务器到客户端的流量（例如广播消息），而不用于接收客户端到服务器的流量。

在跨区域案例中，网络可能不稳定。 对于 *美国东部*的一个应用服务器，位于同一 *美国东部* 区域的 SignalR 服务终结点可以配置为 `primary` ，其他区域中标记为的终结点 `secondary` 。 在此配置中，其他区域中的服务终结点可以从*美国东部*应用服务器**接收**消息，但将不会向此应用服务器路由**跨区域**的客户端。 下图显示了体系结构：

![跨地域基础结构](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

当客户端尝试使用默认路由器通过 `/negotiate` 来与应用服务器协商时，SDK 会从可用的 `primary` 终结点集内**随机选择**一个终结点。 当主终结点不可用时，SDK 会从所有可用的 `secondary` 终结点中**随机选择**。 当服务器与服务终结点之间的连接处于活动状态时，终结点将标记为**可用**。

在跨区域方案中，当客户端尝试 `/negotiate` 使用 *美国东部*托管的应用程序服务器时，默认情况下，它始终返回 `primary` 位于同一区域中的终结点。 当所有 " *美国东部* " 终结点都不可用时，客户端将重定向到其他区域中的终结点。 以下故障转移部分详细介绍了该方案。

![正常协商](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>故障转移

当所有 `primary` 终结点都不可用时，客户端的 `/negotiate` 将从可用的 `secondary` 终结点中进行选择。 此故障转移机制要求每个终结点充当至少一个应用服务器的 `primary` 终结点。

![故障转移](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>后续步骤

本指南介绍了如何在同一应用程序中为扩展、分片和跨区域方案配置多个实例。

还可以在高可用性和灾难恢复方案中使用多个终结点。

> [!div class="nextstepaction"]
> [设置 SignalR 服务以实现灾难恢复和高可用性](./signalr-concept-disaster-recovery.md)
