---
title: Azure Service Fabric 中的服务远程处理 | Microsoft Docs
description: Service Fabric 远程处理允许客户端和服务使用远程过程调用来与服务进行通信。
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 074c428662abb5c3acf86835f6fedbf3f8791acf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212970"
---
# <a name="service-remoting-with-reliable-services"></a>通过 Reliable Services 进行服务远程处理
> [!div class="op_single_selector"]
> * [Windows 上的 C#](service-fabric-reliable-services-communication-remoting.md)
> * [Linux 上的 Java](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Reliable Services 框架提供一种远程处理机制，可轻松快速地为服务设置远程过程调用。

## <a name="set-up-remoting-on-a-service"></a>为服务设置远程处理
为服务设置远程处理包括两个简单步骤：

1. 为服务创建要实现的接口。 此接口定义可供服务的远程过程调用使用的方法。 这些方法必须是返回任务的异步方法。 此接口必须实现 `microsoft.serviceFabric.services.remoting.Service` 以表明此服务具有远程处理接口。
2. 在服务中使用远程处理侦听器。 这是可以提供远程处理功能的 `CommunicationListener` 实现。 可使用 `FabricTransportServiceRemotingListener` 和默认远程处理传输协议来创建远程处理侦听器。

例如，以下无状态服务公开了一个方法，此方法通过远程过程调用获取“Hello World”。

```java
import java.util.ArrayList;
import java.util.concurrent.CompletableFuture;
import java.util.List;
import microsoft.servicefabric.services.communication.runtime.ServiceInstanceListener;
import microsoft.servicefabric.services.remoting.Service;
import microsoft.servicefabric.services.runtime.StatelessService;

public interface MyService extends Service {
    CompletableFuture<String> helloWorldAsync();
}

class MyServiceImpl extends StatelessService implements MyService {
    public MyServiceImpl(StatelessServiceContext context) {
       super(context);
    }

    public CompletableFuture<String> helloWorldAsync() {
        return CompletableFuture.completedFuture("Hello!");
    }

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
        listeners.add(new ServiceInstanceListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        return listeners;
    }
}
```

> [!NOTE]
> 服务接口中的参数和返回类型可以是任何简单、复杂或自定义的类型，但它们必须可序列化。
>
>

## <a name="call-remote-service-methods"></a>调用远程服务的方法
若要使用远程处理堆栈在服务上调用方法，可以通过 `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` 类对此服务使用本地代理。 `ServiceProxyBase` 方法通过使用此服务实现的相同接口来创建本地代理。 使用此代理，只需在此接口上远程调用方法。

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

此远程处理框架将服务引发的异常传播到客户端。 因此，在客户端使用 `ServiceProxyBase` 的异常处理逻辑可直接处理服务引发的异常。

## <a name="service-proxy-lifetime"></a>服务代理生存期
由于 ServiceProxy 创建是轻量型操作，因此用户可根据需求随意创建，数目不限。 如有需要，用户可重复使用服务代理。 用户可以在异常情况下重复使用相同的代理。 每个 ServiceProxy 都包含用于通过线路发送消息的通信客户端。 在调用 API 时，我们通过内部检查来查看使用的通信客户端是否有效。 基于该结果，我们将重新创建通信客户端。 因此在异常情况下，用户无需重新创建 serviceproxy。

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory 生存期
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client._fabric_service_proxy_factory) 是为不同远程接口创建代理的工厂。 如果使用 API `ServiceProxyBase.create`创建代理，则框架创建 `FabricServiceProxyFactory`。
在需要替代 [ServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client._service_remoting_client_factory) 属性时，手动创建一个 FabricServiceProxyFactory 是有用的。
Factory 是一项高成本操作。 `FabricServiceProxyFactory` 维护通信客户端的缓存。
最佳做法是尽可能久地缓存 `FabricServiceProxyFactory`。

## <a name="remoting-exception-handling"></a>远程异常处理
服务 API 引发的所有远程异常都作为 RuntimeException 或 FabricException 发送回客户端。

ServiceProxy 对为其创建的服务分区，处理所有故障转移异常。 如果存在故障转移异常（非暂时异常），它将重新解析终结点，并通过正确的终结点重试调用。 故障转移异常的重试次数无限。
在 TransientExceptions 情况下，它仅重试调用。

默认重试参数由 [OperationRetrySettings] 提供。 https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client._operation_retry_settings)用户可以通过将 OperationRetrySettings 对象传递给 ServiceProxyFactory 构造函数来配置这些值。

## <a name="next-steps"></a>后续步骤
* [确保 Reliable Services 的通信安全](service-fabric-reliable-services-secure-communication.md)
