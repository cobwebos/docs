---
title: 如何在 Azure SignalR 服务中通过多个实例进行缩放
description: 在许多扩展方案中，客户通常需要预配多个实例，并将其配置为将它们一起使用，以创建大规模部署。 例如，分片需要多个实例支持。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: 1e31bc4133cced793d793c07d2e0ee3df29efddb
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672340"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>如何缩放具有多个实例的 SignalR 服务？
最新的 SignalR 服务 SDK 支持 SignalR 服务实例的多个终结点。 您可以使用此功能来缩放并发连接，或将其用于跨区域消息。

## <a name="for-aspnet-core"></a>对于 ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>如何从配置添加多个终结点？

带有密钥 `Azure:SignalR:ConnectionString` 的配置或 SignalR Service 连接字符串的 `Azure:SignalR:ConnectionString:`。

如果密钥以 `Azure:SignalR:ConnectionString:`开头，则它的格式应为 `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`，其中 `Name` 和 `EndpointType` 是 `ServiceEndpoint` 对象的属性，可从代码进行访问。

可以使用以下 `dotnet` 命令添加多个实例连接字符串：

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>如何从代码添加多个终结点？

引入 `ServicEndpoint` 类来描述 Azure SignalR 服务终结点的属性。
使用 Azure SignalR 服务 SDK 时，可以通过以下操作配置多个实例终结点：
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

默认情况下，SDK 使用[DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs)选取终结点。

#### <a name="default-behavior"></a>默认行为 
1. 客户端请求路由

    当客户端与应用服务器 `/negotiate` 时。 默认情况下，SDK 会从可用的服务终结点集合中**随机选择**一个终结点。

2. 服务器消息路由

    当 * 向特定的 * * 连接发送消息，并且目标连接路由到当前服务器时，该消息将直接转到该连接的终结点。 否则，会将消息广播到每个 Azure SignalR 终结点。

#### <a name="customize-routing-algorithm"></a>自定义路由算法
当你有特殊知识来识别消息应到达的终结点时，你可以创建自己的路由器。

下面定义了自定义路由器，作为从 `east-` 开始的组始终转向名为 `east`的终结点时的示例：

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

下面的另一个示例，重写默认协商行为，以选择终结点取决于应用服务器所在的位置。

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

请不要忘记使用以下操作将路由器注册到 DI 容器：

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

### <a name="how-to-add-multiple-endpoints-from-config"></a>如何从配置添加多个终结点？

带有密钥 `Azure:SignalR:ConnectionString` 的配置或 SignalR Service 连接字符串的 `Azure:SignalR:ConnectionString:`。

如果密钥以 `Azure:SignalR:ConnectionString:`开头，则它的格式应为 `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`，其中 `Name` 和 `EndpointType` 是 `ServiceEndpoint` 对象的属性，可从代码进行访问。

可以将多个实例连接字符串添加到 `web.config`：

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

### <a name="how-to-add-multiple-endpoints-from-code"></a>如何从代码添加多个终结点？

引入 `ServicEndpoint` 类来描述 Azure SignalR 服务终结点的属性。
使用 Azure SignalR 服务 SDK 时，可以通过以下操作配置多个实例终结点：

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

ASP.NET SignalR 和 ASP.NET Core SignalR 的唯一区别在于 `GetNegotiateEndpoint`的 http 上下文类型。 对于 ASP.NET SignalR，其类型为[IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) 。

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

请不要忘记使用以下操作将路由器注册到 DI 容器：

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

`ServiceEndpoint` 对象具有值 `primary` 或 `secondary`的 `EndpointType` 属性。

`primary` 终结点是接收客户端流量的首选终结点，并被视为具有更可靠的网络连接;`secondary` 终结点被视为具有不太可靠的网络连接，并且仅用于使服务器进入客户端流量（例如广播消息），而不是使用客户端到服务器的流量。

在跨区域情况下，网络可能不稳定。 对于*美国东部*的一个应用服务器，位于同一*美国东部*区域的 SignalR 服务终结点可以配置为 `primary`，并将其他区域中的终结点配置为 `secondary`。 在此配置中，其他区域中的服务终结点可以从*美国东部*应用服务器**接收**消息，但将不会向此应用服务器路由**跨区域**的客户端。 下图显示了此体系结构：

![跨地区](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

当客户端使用默认路由器尝试在应用服务器 `/negotiate` 时，SDK 将从可用的 `primary` 终结点集中**随机选择**一个终结点。 如果主终结点不可用，SDK 会从所有可用的 `secondary` 终结点中**随机选择**。 当服务器和服务终结点之间的连接处于活动状态时，终结点将标记为**可用**。

在跨区域方案中，当客户端尝试与*美国东部*托管的应用服务器 `/negotiate` 时，默认情况下，它始终返回位于同一区域中的 `primary` 终结点。 当所有 "*美国东部*" 终结点都不可用时，客户端将重定向到其他区域中的终结点。 下面的故障转移部分详细介绍了该方案。

![正常协商](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>故障转移

当所有 `primary` 终结点都不可用时，客户端 `/negotiate` 从可用的 `secondary` 终结点中进行选择。 这种故障转移机制要求每个终结点应作为至少一个应用服务器 `primary` 终结点。

![故障转移](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>后续步骤

本指南介绍了如何在同一应用程序中配置多个实例，以便进行缩放、分片和跨区域方案。

还可以在高可用性和灾难恢复方案中使用多个终结点。

> [!div class="nextstepaction"]
> [设置 SignalR 服务以实现灾难恢复和高可用性](./signalr-concept-disaster-recovery.md)
