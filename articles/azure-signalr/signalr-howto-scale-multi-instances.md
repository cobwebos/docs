---
title: 如何为 Azure SignalR 服务具有多个实例缩放
description: 许多缩放的情况下，客户通常需要预配多个实例和配置在一起，使用它们来创建大规模的部署。 例如，分片需要多个实例支持。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: e284a0492774e02cab79db6d9006c1718a7fcfc9
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58623178"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>如何使用多个实例缩放 SignalR 服务？
最新的 SignalR 服务 SDK 支持 SignalR 服务实例的多个终结点。 可以使用此功能可缩放的并发连接，或将其用于跨区域消息传送。

## <a name="for-aspnet-core"></a>For ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>如何从配置中添加多个终结点？

配置与键`Azure:SignalR:ConnectionString`或`Azure:SignalR:ConnectionString:`SignalR 服务连接字符串。

如果密钥开头`Azure:SignalR:ConnectionString:`，它应采用格式`Azure:SignalR:ConnectionString:{Name}:{EndpointType}`，其中`Name`并`EndpointType`是属性的`ServiceEndpoint`对象，并可从代码访问。

您可以添加多个实例连接字符串使用以下`dotnet`命令：

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>如何从代码中添加多个终结点？

一个`ServicEndpoint`类引入 Azure SignalR 服务终结点的属性进行说明。
使用通过 Azure SignalR 服务 SDK 时，你可以配置多个实例终结点：
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

默认情况下，使用 SDK [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs)选取终结点。

#### <a name="default-behavior"></a>默认行为 
1. 客户端请求路由

    当客户端`/negotiate`与应用程序服务器。 默认情况下，SDK**随机选择**组可用的服务终结点从一个终结点。

2. 服务器消息路由

    当 * 将消息发送到特定 * * 连接 * * *，并将目标连接路由到当前服务器中，消息将直接向该连接的终结点。 否则，将消息广播到每个 Azure SignalR 终结点。

#### <a name="customize-routing-algorithm"></a>自定义路由算法
当你有特殊知识来标识哪些消息应转到的终结点时，可以创建你自己的路由器。

自定义路由器下面定义作为示例时开头`east-`始终定向到名为终结点`east`:

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

另一个示例中，重写默认值 negotiate 行为、 选择终结点取决于应用服务器所在的位置。

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

别忘了注册到 DI 容器使用的路由器：

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

## <a name="for-aspnet"></a>For ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>如何从配置中添加多个终结点？

配置与键`Azure:SignalR:ConnectionString`或`Azure:SignalR:ConnectionString:`SignalR 服务连接字符串。

如果密钥开头`Azure:SignalR:ConnectionString:`，它应采用格式`Azure:SignalR:ConnectionString:{Name}:{EndpointType}`，其中`Name`并`EndpointType`是属性的`ServiceEndpoint`对象，并可从代码访问。

您可以添加多个实例的连接字符串`web.config`:

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

### <a name="how-to-add-multiple-endpoints-from-code"></a>如何从代码中添加多个终结点？

一个`ServicEndpoint`类引入 Azure SignalR 服务终结点的属性进行说明。
使用通过 Azure SignalR 服务 SDK 时，你可以配置多个实例终结点：

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

ASP.NET SignalR 和 ASP.NET Core SignalR 的唯一区别是的 http 上下文类型`GetNegotiateEndpoint`。 ASP.NET SignalR 的它属于[IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19)类型。

下面是自定义 ASP.NET SignalR 的协商示例：

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

别忘了注册到 DI 容器使用的路由器：

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

`ServiceEndpoint`对象具有`EndpointType`具有值的属性`primary`或`secondary`。

`primary` 终结点是首选的终结点来接收客户端流量，被认为具有更可靠的网络连接;`secondary`终结点被视为具有不太可靠的网络连接，仅适用于采用服务器到客户端流量广播消息，而不进行客户端到服务器的通信使用。

在跨区域的情况下，网络可能是不稳定。 为一个应用程序服务器位于*美国东部*，SignalR 服务终结点位于相同*美国东部*区域可以配置为`primary`和其他区域中的终结点标记为`secondary`。 在此配置中，可以在其他区域中的服务终结点**接收**从此消息*美国东部*应用程序服务器，但是将存在没有**跨区域**客户端路由到此应用程序服务器。 下图中显示的体系结构：

![基础结构跨地域](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

当客户端会尝试`/negotiate`与应用程序服务器，与默认路由器，SDK**随机选择**一个终结点的可用集中`primary`终结点。 当终结点是否可用，然后 SDK**随机选择**从所有可用`secondary`终结点。 终结点标记为**可用**时服务器和服务终结点之间的连接处于活动状态。

在跨区域方案中，当客户端会尝试`/negotiate`与应用程序服务器中托管*美国东部*，也可由默认始终返回`primary`终结点位于同一区域中。 当所有*美国东部*终结点不可用，客户端将重定向到其他区域中的终结点。 下面的故障转移部分介绍详细的方案。

![正常协商](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>故障转移

当所有`primary`终结点不可用，客户端`/negotiate`从可用选取`secondary`终结点。 此故障转移机制要求每个终结点应充当`primary`到至少一个应用程序服务器的终结点。

![故障转移](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>后续步骤

在本指南中，您学习了如何在同一应用程序进行缩放、 分片中和跨区域方案中配置多个实例。

此外可以在高可用性和灾难恢复方案中使用多个终结点支持。

> [!div class="nextstepaction"]
> [为灾难恢复和高可用性设置 SignalR 服务](./signalr-concept-disaster-recovery.md)
