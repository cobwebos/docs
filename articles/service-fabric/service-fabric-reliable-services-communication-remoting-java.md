---
title: "Azure Service Fabric 中的服务远程处理 | Microsoft Docs"
description: "Service Fabric 远程处理允许客户端和服务使用远程过程调用来与服务进行通信。"
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 03/09/2017
ms.author: pakunapa
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 11e300b3b1d0433bd4790332593ada2d3eede883
ms.lasthandoff: 04/03/2017


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

1. 为你的服务创建要实现的接口。 此接口定义可供服务的远程过程调用使用的方法。 这些方法必须是返回任务的异步方法。 此接口必须实现 `microsoft.serviceFabric.services.remoting.Service` 以表明此服务具有远程处理接口。
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
若要使用远程处理堆栈在服务上调用方法，可以通过 `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` 类对此服务使用本地代理。 `ServiceProxyBase` 方法通过使用此服务实现的相同接口来创建本地代理。 使用此代理，你只需在此接口上远程调用方法。

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

此远程处理框架将服务引发的异常传播到客户端。 因此，在客户端使用 `ServiceProxyBase` 的异常处理逻辑可直接处理服务引发的异常。

## <a name="next-steps"></a>后续步骤
* [确保 Reliable Services 的通信安全](service-fabric-reliable-services-secure-communication.md)

