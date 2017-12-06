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
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 438eeee7353cbd1d534f27471c9c9054aecc12e8
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2017
---
# <a name="service-remoting-with-reliable-services"></a>通过 Reliable Services 进行服务远程处理
对于不依赖于特定的通信协议或堆栈的服务，如 WebAPI、Windows Communication Foundation (WCF) 或其他服务，Reliable Services 框架提供一种远程处理机制，以便快速而轻松地为这些服务设置远程过程调用。

## <a name="set-up-remoting-on-a-service"></a>在服务中设置远程处理
为服务设置远程处理包括两个简单步骤：

1. 为服务创建要实现的接口。 此接口定义可供服务的远程过程调用使用的方法。 这些方法必须是返回任务的异步方法。 此接口必须实现 `Microsoft.ServiceFabric.Services.Remoting.IService` 以表明此服务具有远程处理接口。
2. 在服务中使用远程处理侦听器。 RemotingListener 是可以提供远程处理功能的 `ICommunicationListener` 实现。 `Microsoft.ServiceFabric.Services.Remoting.Runtime` 命名空间包含一个同时适用于无状态服务和有状态服务的扩展方法 `CreateServiceRemotingListener`，可用于创建使用默认远程处理传输协议的远程处理侦听器。

>[!NOTE]
>`Remoting` 命名空间可用作名为 `Microsoft.ServiceFabric.Services.Remoting` 的单独 NuGet 包

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
若要使用远程处理堆栈在服务上调用方法，可以通过 `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` 类对此服务使用本地代理。 `ServiceProxy` 方法通过使用此服务实现的相同接口来创建本地代理。 使用此代理，可在此接口上远程调用方法。

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

此远程处理框架将服务引发的异常传播到客户端。 因此，在客户端使用 `ServiceProxy` 的异常处理逻辑可直接处理服务引发的异常。

## <a name="service-proxy-lifetime"></a>服务代理生存期
由于 ServiceProxy 创建是轻量型操作，因此用户可根据需求随意创建，数目不限。 如有需要，用户可重复使用服务代理实例。 如果远程过程调用引发了异常，用户仍可以重复使用相同的代理实例。 每个 ServiceProxy 都包含用于通过线路发送消息的通信客户端。 进行远程调用时，我们会在内部检查通信客户端是否有效。 基于该结果，我们将重新创建通信客户端（如果需要）。 因此在发生异常的情况下，用户无需重新创建 serviceproxy，但以透明方式这样做。

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory 生存期
[ServiceProxyFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) 是为不同远程接口创建代理实例的工厂。 如果使用 api `ServiceProxy.Create` 创建代理，则框架将创建 ServiceProxy 的单一实例。
在需要替代 [IServiceRemotingClientFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory) 属性时，手动创建一个 ServiceProxyFactory 是有用的。
工厂创建是一项代价高昂的操作。 ServiceProxyFactory 维护通信客户端的内部缓存。
最佳做法是尽可能久地缓存 ServiceProxyFactory。

## <a name="remoting-exception-handling"></a>远程异常处理
服务 API 引发的所有远程异常都将作为 AggregateException 发送回客户端。 RemoteExceptions 应该是 DataContract 可序列化的，否则 [ServiceException](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) 将引发至代理 API，且包含序列化错误。

ServiceProxy 对为其创建的服务分区，处理所有故障转移异常。 如果存在故障转移异常（非暂时异常），它将重新解析终结点，并通过正确的终结点重试调用。 故障转移异常的重试次数无限。
如果发生暂时性异常，代理会重试调用。

默认重试参数由 [OperationRetrySettings] 提供。 (https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings) 用户可通过将 OperationRetrySettings 对象传递至 ServiceProxyFactory 构造函数来配置这些值。
## <a name="how-to-use-remoting-v2-stack"></a>如何使用 Remoting V2 堆栈
借助 2.8 NuGet Remoting 包，可以通过相应的选项来使用 Remoting V2 堆栈。 Remoting V2 堆栈具有更高的性能，提供自定义可序列化对象等功能和更多的可插入 API。
默认情况下，如果未进行以下更改，则会继续使用 Remoting V1 堆栈。
Remoting V2 与 V1（上一个 Remoting 堆栈）不兼容，因此遵循以下文章了解如何在不影响服务可用性的情况下从 V1 升级到 V2。

### <a name="using-assembly-attribute-to-use-v2-stack"></a>通过程序集属性使用 V2 堆栈。

遵循以下步骤可更改为 V2 堆栈。

1. 在服务清单中添加名为“ServiceEndpointV2”的终结点资源。

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2.  使用远程处理扩展方法创建远程处理侦听器。

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3.  在远程处理接口上添加程序集属性。

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
  ```
不需要在客户端项目中进行更改。
使用接口程序集生成客户端程序集，确保使用上述程序集属性。

### <a name="using-explicit-v2-classes-to-create-listener-clientfactory"></a>使用显式 V2 类创建侦听器/ClientFactory
请遵循以下步骤。
1.  在服务清单中添加名为“ServiceEndpointV2”的终结点资源。

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2. 使用 [Remoting V2Listener](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingistener?view=azure-dotnet)。 使用的默认服务终结点资源名称为“ServiceEndpointV2”，必须在服务清单中定义该名称。

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
  ```

3. 使用 V2 [客户端工厂](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet)。
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>如何从 Remoting V1 升级到 Remoting V2。
若要从 V1 升级到 V2，必须执行双步升级。 按列出的顺序执行以下步骤。

1. 使用 CompactListener 属性将 V1 服务升级到 V2 服务。
此项更改可确保服务在 V1 和 V2 侦听器上侦听。

    a)  在服务清单中添加名为“ServiceEndpointV2”的终结点资源。
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b)  使用以下扩展方法创建远程处理侦听器。

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c)  在远程处理接口上添加程序集属性，以使用 CompatListener 和 V2 客户端。
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.CompatListener, RemotingClient = RemotingClient.V2Client)]

      ```
2. 使用 V2 客户端属性将 V1 客户端升级到 V2 客户端。
此步骤确保客户端使用 V2 堆栈。
不需要在客户端项目/服务中进行更改。 使用更新的接口程序集生成客户端项目便已足够。

3. 此步骤是可选的。 使用 V2Listener 属性，然后升级 V2 服务。
此步骤确保服务仅在 V2 侦听器上侦听。

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
```

## <a name="how-to-use-custom-serialization-with-remoting-v2"></a>如何在 Remoting V2 中使用自定义序列化。
以下示例在 Remoting V2 中使用 Json 序列化。
1. 实现 IServiceRemotingMessageSerializationProvider 接口，以提供自定义序列化的实现。
    以下代码片段演示了实现的大致情况。

    ```csharp
    public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
    {
      public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType,
          IEnumerable<Type> requestBodyTypes)
      {
          return new ServiceRemotingRequestJsonMessageBodySerializer(serviceInterfaceType, requestBodyTypes);
      }

      public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType,
          IEnumerable<Type> responseBodyTypes)
      {
          return new ServiceRemotingResponseJsonMessageBodySerializer(serviceInterfaceType, responseBodyTypes);
      }

      public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
      {
          return new JsonMessageFactory();
      }
     }

  class JsonMessageFactory: IServiceRemotingMessageBodyFactory
  {
      public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName,
          int numberOfParameters)
      {
          return new JsonRemotingRequestBody(new JObject());
      }

      public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName)
      {
          return  new JsonRemotingResponseBody();
      }
   }

  class ServiceRemotingRequestJsonMessageBodySerializer: IServiceRemotingRequestMessageBodySerializer
  {
      public ServiceRemotingRequestJsonMessageBodySerializer(Type serviceInterfaceType,
          IEnumerable<Type> parameterInfo)
      {
      }

      public OutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
      {
          if (serviceRemotingRequestMessageBody == null)
          {
              return null;
          }

          var json = serviceRemotingRequestMessageBody.ToString();
          var bytes = Encoding.UTF8.GetBytes(json);
          var segment = new ArraySegment<byte>(bytes);
          var segments = new List<ArraySegment<byte>> {segment};
          return new OutgoingMessageBody(segments);
      }

      public IServiceRemotingRequestMessageBody Deserialize(IncomingMessageBody messageBody)
      {
          using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

          using (JsonReader reader = new JsonTextReader(sr))
          {
              var serializer = new JsonSerializer();
              var ob = serializer.Deserialize<JObject>(reader);
              var ob2 = new JsonRemotingRequestBody(ob);
              return ob2;
          }
      }
  }

  class ServiceRemotingResponseJsonMessageBodySerializer: IServiceRemotingResponseMessageBodySerializer
  {

      public ServiceRemotingResponseJsonMessageBodySerializer(Type serviceInterfaceType,
          IEnumerable<Type> parameterInfo)
      {
      }

      public OutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
      {
          var json = JsonConvert.SerializeObject(responseMessageBody,new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
          });
          var bytes = Encoding.UTF8.GetBytes(json);
          var segment = new ArraySegment<byte>(bytes);
          var list = new List<ArraySegment<byte>> {segment};
          return new OutgoingMessageBody(list);
      }

      public IServiceRemotingResponseMessageBody Deserialize(IncomingMessageBody messageBody)
      {
          using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

          using (var reader = new JsonTextReader(sr))
          {
              var serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                  TypeNameHandling = TypeNameHandling.All
              });
              return serializer.Deserialize<JsonRemotingResponseBody>(reader);
          }
      }
  }
  internal class JsonRemotingResponseBody: IServiceRemotingResponseMessageBody
  {
      public object Value;

      public void Set(object response)
      {
          this.Value = response;
      }

      public object Get(Type paramType)
      {
          return this.Value;
      }
  }

  class JsonRemotingRequestBody: IServiceRemotingRequestMessageBody
    {
      private readonly JObject jobject;

        public JsonRemotingRequestBody(JObject ob)
        {
            this.jobject = ob;
        }

        public void SetParameter(int position, string parameName, object parameter)
        {
            this.jobject.Add(parameName, JToken.FromObject(parameter));
        }

        public object GetParameter(int position, string parameName, Type paramType)
       {
           var ob = this.jobject[parameName];
           return ob.ToObject(paramType);
       }

       public override string ToString()
        {
            return this.jobject.ToString();
        }
    }
    ```

2.    使用远程处理侦听器的 JsonSerializationProvider 重写默认的序列化提供程序。

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
  ```

3.    使用远程处理客户端工厂的 JsonSerializationProvider 重写默认的序列化提供程序。

```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
            {
                return new FabricTransportServiceRemotingClientFactory(
                    serializationProvider: new ServiceRemotingJsonSerializationProvider());
            });
  ```

## <a name="next-steps"></a>后续步骤
* [Reliable Services 中使用 OWIN 的 Web API](service-fabric-reliable-services-communication-webapi.md)
* [通过 Reliable Services 进行 WCF 通信](service-fabric-reliable-services-communication-wcf.md)
* [确保 Reliable Services 的通信安全](service-fabric-reliable-services-secure-communication.md)
