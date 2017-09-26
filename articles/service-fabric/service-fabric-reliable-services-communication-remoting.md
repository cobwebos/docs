
---
title: "Service Fabric 中的服务远程处理 | Microsoft 文档"
description: "Service Fabric 远程处理允许客户端和服务使用远程过程调用来与服务进行通信。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 92a8894f24c234fbf38eda086531b524cceccfc1
ms.contentlocale: zh-cn
ms.lasthandoff: 06/29/2017

---
# <a name="service-remoting-with-reliable-services"></a>通过 Reliable Services 进行服务远程处理
对于不依赖于特定的通信协议或堆栈的服务，如 WebAPI、Windows Communication Foundation (WCF) 或其他服务，Reliable Services 框架提供一种远程处理机制，以便快速而轻松地为这些服务设置远程过程调用。

## <a name="set-up-remoting-on-a-service"></a>为服务设置远程处理
为服务设置远程处理包括两个简单步骤：

1. 为你的服务创建要实现的接口。 此接口定义可供你的服务的远程过程调用使用的方法。 这些方法必须是返回任务的异步方法。 此接口必须实现 `Microsoft.ServiceFabric.Services.Remoting.IService` 以表明此服务具有远程处理接口。
2. 在服务中使用远程处理侦听器。 这是可以提供远程处理功能的 `ICommunicationListener` 实现。 `Microsoft.ServiceFabric.Services.Remoting.Runtime` 命名空间包含一个同时适用于无状态服务和有状态服务的扩展方法 `CreateServiceRemotingListener`，可用于创建使用默认远程处理传输协议的远程处理侦听器。

注意：`Remoting` 命名空间可用作名为 `Microsoft.ServiceFabric.Services.Remoting` 的单独 nuget 包 

例如，以下无状态服务公开了一个方法，此方法通过远程过程调用获取“Hello World”。

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context =>            this.CreateServiceRemotingListener(context)) };
    }
}
```
> [!NOTE]
> 服务接口中的参数和返回类型可以是任何简单、复杂或自定义的类型，但它们必须是 .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx) 可序列化的类型。
>
>

## <a name="call-remote-service-methods"></a>调用远程服务的方法
若要使用远程处理堆栈在服务上调用方法，可以通过 `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` 类对此服务使用本地代理。 `ServiceProxy` 方法通过使用此服务实现的相同接口来创建本地代理。 使用此代理，你只需在此接口上远程调用方法。

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

此远程处理框架将服务引发的异常传播到客户端。 因此，在客户端使用 `ServiceProxy` 的异常处理逻辑可直接处理服务引发的异常。

## <a name="service-proxy-lifetime"></a>服务代理生存期
由于 ServiceProxy 创建是轻量型操作，因此用户可根据需求随意创建，数目不限。 如有需要，用户可重复使用服务代理。 用户可以在异常情况下重复使用相同的代理。 每个 ServiceProxy 都包含用于通过线路发送消息的通信客户端。 在调用 API 时，我们通过内部检查来查看使用的通信客户端是否有效。 基于该结果，我们将重新创建通信客户端。 因此在异常情况下，用户无需重新创建 serviceproxy。

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory 生存期
[ServiceProxyFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) 是为不同远程接口创建代理的工厂。 如果使用 API ServiceProxy.Create 来创建代理，那么框架将创建单个 ServiceProxyFactory。
在需要替代 [IServiceRemotingClientFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory) 属性时，手动创建一个 ServiceProxyFactory 是有用的。
Factory 是一项高成本操作。 ServiceProxyFactory 维护通信客户端的缓存。
最佳做法是尽可能久地缓存 ServiceProxyFactory。

## <a name="remoting-exception-handling"></a>远程异常处理
服务 API 引发的所有远程异常都将作为 AggregateException 发送回客户端。 RemoteExceptions 应该是 DataContract 可序列化的，否则 [ServiceException](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) 将引发至代理 API，且包含序列化错误。

ServiceProxy 对为其创建的服务分区，处理所有故障转移异常。 如果存在故障转移异常（非暂时异常），它将重新解析终结点，并通过正确的终结点重试调用。 故障转移异常的重试次数无限。
在 TransientExceptions 情况下，它仅重试调用。

默认重试参数由 [OperationRetrySettings] 提供。 (https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings) 用户可通过将 OperationRetrySettings 对象传递至 ServiceProxyFactory 构造函数来配置这些值。

## <a name="next-steps"></a>后续步骤
* [Reliable Services 中使用 OWIN 的 Web API](service-fabric-reliable-services-communication-webapi.md)
* [通过 Reliable Services 进行 WCF 通信](service-fabric-reliable-services-communication-wcf.md)
* [确保 Reliable Services 的通信安全](service-fabric-reliable-services-secure-communication.md)

