---
title: Service Fabric 中使用 C# 的服务远程处理 | Microsoft Docs
description: Service Fabric 远程处理允许客户端和服务使用远程过程调用来与 C# 服务进行通信。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: e7652fe1b211e6811a4a3aa61bc2aa9d2f529dda
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205810"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>通过 Reliable Services 使用 C# 进行服务远程处理
> [!div class="op_single_selector"]
> * [Windows 上的 C#](service-fabric-reliable-services-communication-remoting.md)
> * [Linux 上的 Java](service-fabric-reliable-services-communication-remoting-java.md)
>
>

对于不依赖于特定的通信协议或堆栈的服务，如 WebAPI、Windows Communication Foundation (WCF) 或其他服务，Reliable Services 框架提供一种远程处理机制，以便快速而轻松地为这些服务设置远程过程调用。 本文讨论了如何为使用 C# 编写的服务设置远程过程调用。

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

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
     return this.CreateServiceRemotingInstanceListeners();
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

此远程处理框架将服务引发的异常传播到客户端。 因此，使用 `ServiceProxy` 时，客户端负责处理服务引起的异常。

## <a name="service-proxy-lifetime"></a>服务代理生存期
由于 ServiceProxy 创建是轻量型操作，因此可根据需求随意创建，数目不限。 如有需要，可重复使用服务代理实例。 如果远程过程调用引发了异常，仍可以重复使用相同的代理实例。 每个 ServiceProxy 都包含用于通过线路发送消息的通信客户端。 进行远程调用时，会在内部执行检查，以确认通信客户端是否有效。 根据这些检查的结果，将创建通信客户端（如有必要）。 因此，如果发生异常，无需重新创建 `ServiceProxy`。

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory 生存期
[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) 是为不同远程接口创建代理实例的工厂。 如果使用 api `ServiceProxy.Create` 创建代理，则框架将创建 ServiceProxy 的单一实例。
在需要替代 [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) 属性时，手动创建一个 ServiceProxyFactory 是有用的。
工厂创建是一项代价高昂的操作。 ServiceProxyFactory 维护通信客户端的内部缓存。
最佳做法是尽可能久地缓存 ServiceProxyFactory。

## <a name="remoting-exception-handling"></a>远程异常处理
服务 API 引发的所有远程异常都将作为 AggregateException 发送回客户端。 RemoteExceptions 应该是 DataContract 可序列化的；如果不是，则代理 API 会引发 [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception)，且包含序列化错误。

ServiceProxy 对为其创建的服务分区，处理所有故障转移异常。 如果存在故障转移异常（非暂时异常），它将重新解析终结点，并通过正确的终结点重试调用。 故障转移异常的重试次数无限。
如果发生暂时性异常，代理会重试调用。

默认重试参数由 [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings) 提供。

用户可以通过将 OperationRetrySettings 对象传递给 ServiceProxyFactory 构造函数配置这些值。

## <a name="how-to-use-the-remoting-v2-stack"></a>如何使用 Remoting V2 堆栈

从 NuGet Remoting 包版本 2.8 开始，可以通过相应的选项来使用 Remoting V2 堆栈。 Remoting V2 堆栈具有更高的性能，提供自定义可序列化对象等功能和更多的可插入 API。
模板代码使用的仍然是 Remoting V1 堆栈。
Remoting V2 与 V1（以前的 Remoting 堆栈）不兼容，因此请遵循有关如何在不影响服务可用性的情况下[从 V1 升级到 V2](#how-to-upgrade-from-remoting-v1-to-remoting-v2) 的以下说明。

以下方法可用来启用 V2 堆栈。

### <a name="using-an-assembly-attribute-to-use-the-v2-stack"></a>通过程序集属性使用 V2 堆栈

以下步骤使用一个程序集属性将模板代码更改为使用 V2 堆栈。

1. 在服务清单中将终结点资源从 `"ServiceEndpoint"` 更改为 `"ServiceEndpointV2"`。

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. 使用 `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` 扩展方法创建远程处理侦听器（对于 V1 和 V2 是相同的）。

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3. 使用 `FabricTransportServiceRemotingProvider` 属性标记包含远程处理接口的程序集。

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
  ```

不需要在客户端项目中更改代码。
使用接口程序集生成客户端程序集，以确保使用上面显示的程序集属性。

### <a name="using-explicit-v2-classes-to-use-the-v2-stack"></a>使用显式 V2 类来使用 V2 堆栈

作为使用程序集属性的替代方法，还可以通过使用显式 V2 类来启用 V2 堆栈。

以下步骤使用显式 V2 类将模板代码更改为使用 V2 堆栈。

1. 在服务清单中将终结点资源从 `"ServiceEndpoint"` 更改为 `"ServiceEndpointV2"`。

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
  </Resources>
  ```

2. 使用 `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` 命名空间中的 [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet)。

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

3. 使用 `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` 命名空间中的 [FabricTransportServiceRemotingClientFactory ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) 创建客户端。

  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>如何从 Remoting V1 升级到 Remoting V2。
若要从 V1 升级到 V2，必须执行双步升级。 按列出的顺序执行以下步骤。

1. 使用以下属性将 V1 服务升级到 V2 服务。
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

    c)  在远程处理接口上添加程序集属性，以使用 V1、V2 侦听器和 V2 客户端。
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. 使用 V2 客户端属性将 V1 客户端升级到 V2 客户端。
此步骤确保客户端使用 V2 堆栈。
不需要在客户端项目/服务中进行更改。 使用更新的接口程序集生成客户端项目便已足够。

3. 此步骤是可选的。 使用 V2Listener 属性，然后升级 V2 服务。
此步骤确保服务仅在 V2 侦听器上侦听。

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
```


## <a name="how-to-use-remoting-v2interfacecompatible-stack"></a>如何使用 Remoting V2(InterfaceCompatible) 堆栈
 Remoting V2（InterfaceCompatible 又称为 V2_1）堆栈除了是与 Remoting V1 堆栈接口兼容的堆栈外，还具有 V2 Remoting 堆栈的所有功能，但不向后兼容 V2 和 V1。 若要在不影响服务可用性的情况下从 V1 升级到 V2_1，请按照文章[如何从 V1 升级到 V2(InterfaceCompatible)](#how-to-upgrade-from-remoting-v1-to-remoting-v2interfacecompatible) 进行操作。


### <a name="using-assembly-attribute-to-use-remoting-v2interfacecompatible-stack"></a>通过程序集属性使用 Remoting V2(InterfaceCompatible) 堆栈。

遵循以下步骤可更改为 V2_1 堆栈。

1. 在服务清单中添加名为“ServiceEndpointV2_1”的终结点资源。

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
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

3.  在远程处理接口上添加[程序集属性](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet)。

  ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

  ```
不需要在客户端项目中进行更改。
使用接口程序集生成客户端程序集，确保使用上述程序集属性。

### <a name="using-explicit-remoting-classes-to-create-listener-clientfactory-for-v2interfacecompatible-version"></a>使用显式远程处理类为 V2(InterfaceCompatible) 版本创建侦听器/ClientFactory。
请遵循以下步骤。
1.  在服务清单中添加名为“ServiceEndpointV2_1”的终结点资源。

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
  </Resources>
  ```

2. 使用 [Remoting V2Listener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet)。 使用的默认服务终结点资源名称为“ServiceEndpointV2_1”，必须在服务清单中定义该名称。

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
  ```

3. 使用 V2 [客户端工厂](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet)。
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2interfacecompatible"></a>如何从 Remoting V1 升级到 Remoting V2(InterfaceCompatible)。
若要从 V1 升级到 V2（InterfaceCompatible 又称为 V2_1），必须执行双步升级。 按列出的顺序执行以下步骤。

1. 使用以下属性将 V1 服务升级到 V2_1 服务。
此项更改可确保服务在 V1 和 V2_1 侦听器上侦听。

    a)  在服务清单中添加名为“ServiceEndpointV2_1”的终结点资源。
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
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

    c)  在远程处理接口上添加程序集属性，以使用 V1、V2_1 侦听器和 V2_1 客户端。
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. 使用 V2_1 客户端属性将 V1 客户端升级到 V2_1 客户端。
此步骤确保客户端使用 V2_1 堆栈。
不需要在客户端项目/服务中进行更改。 使用更新的接口程序集生成客户端项目便已足够。

3. 此步骤是可选的。 从属性中删除 V1 侦听器版本，然后升级 V2 服务。
此步骤确保服务仅在 V2 侦听器上侦听。

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
```

### <a name="how-to-use-custom-serialization-with-remoting-wrapped-message"></a>如何在远程处理包装消息中使用自定义序列化。
在远程处理包装消息中，我们将创建一个包装对象，并将所有参数作为其中的一个字段。
步骤如下：

1. 实现 IServiceRemotingMessageSerializationProvider 接口，以提供自定义序列化的实现。
    以下代码片段演示了实现的大致情况。

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
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
