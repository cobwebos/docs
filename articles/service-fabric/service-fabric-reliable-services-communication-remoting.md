---
title: 使用C# Service Fabric 中的服务远程处理
description: Service Fabric 远程处理允许客户端和服务使用远程过程调用来与 C# 服务进行通信。
author: vturecek
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 0d59275f25931a11b2d551a2e9eb019838e4c1b3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433880"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>通过 Reliable Services 使用 C# 进行服务远程处理

> [!div class="op_single_selector"]
> * [Windows 上的 C#](service-fabric-reliable-services-communication-remoting.md)
> * [Linux 上的 Java](service-fabric-reliable-services-communication-remoting-java.md)
>
>

对于不依赖于特定的通信协议或堆栈的服务，如 Web API、Windows Communication Foundation 或其他服务，Reliable Services 框架提供一种远程处理机制，以便快速而轻松地为这些服务设置远程过程调用。 本文讨论了如何为使用 C# 编写的服务设置远程过程调用。

## <a name="set-up-remoting-on-a-service"></a>为服务设置远程处理

可通过两个简单步骤为服务设置远程处理：

1. 为服务创建要实现的接口。 此接口定义可供服务的远程过程调用使用的方法。 这些方法必须是返回任务的异步方法。 此接口必须实现 `Microsoft.ServiceFabric.Services.Remoting.IService` 以表明此服务具有远程处理接口。
2. 在服务中使用远程处理侦听器。 远程侦听器是提供远程处理功能的 `ICommunicationListener` 实现。 `Microsoft.ServiceFabric.Services.Remoting.Runtime` 命名空间包含一个同时适用于无状态服务和有状态服务的扩展方法 `CreateServiceRemotingInstanceListeners`，可用于创建使用默认远程处理传输协议的远程处理侦听器。

>[!NOTE]
>`Remoting` 命名空间可用作名为 `Microsoft.ServiceFabric.Services.Remoting` 的单独 NuGet 包。

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
> 服务接口中的参数和返回类型可以是任何简单、复杂或自定义的类型，但它们必须能够由 .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx) 序列化。
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

由于服务代理创建是轻量型操作，因此可根据需求随意创建，数目不限。 如有需要，可重复使用服务代理实例。 如果远程过程调用引发了异常，仍可以重复使用相同的代理实例。 每个服务代理包含用于通过线路发送消息的通信客户端。 进行远程调用时，会在内部执行检查，以确认通信客户端是否有效。 根据这些检查的结果，将创建通信客户端（如有必要）。 因此，如果发生异常，无需重新创建 `ServiceProxy`。

### <a name="service-proxy-factory-lifetime"></a>服务代理工厂生存期

[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) 是为不同远程接口创建代理实例的工厂。 如果使用 API `ServiceProxyFactory.CreateServiceProxy` 创建代理，则框架将创建单一实例服务代理。
在需要替代 [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) 属性时，手动创建一个 ServiceProxyFactory 是有用的。
工厂创建是一项代价高昂的操作。 服务代理工厂维护通信客户端的内部缓存。
最佳做法是尽可能久地缓存服务代理工厂。

## <a name="remoting-exception-handling"></a>远程异常处理

服务 API 引发的所有远程异常都将作为 AggregateException 发送回客户端。 远程异常应该可由 DataContract 序列化。 否则，代理 API 将引发 [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) 并在其中返回序列化错误。

服务代理对为其创建的服务分区，处理所有故障转移异常。 如果存在故障转移异常（非暂时异常），它将重新解析终结点，并通过正确的终结点重试调用。 故障转移异常的重试次数无限。
如果发生暂时性异常，代理会重试调用。

默认重试参数由 [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings) 提供。

用户可以通过将 OperationRetrySettings 对象传递给 ServiceProxyFactory 构造函数来配置这些值。

## <a name="use-the-remoting-v2-stack"></a>使用远程处理 V2 堆栈

从 NuGet 远程处理包版本 2.8 开始，可以通过相应的选项来使用远程处理 V2 堆栈。 远程处理 V2 堆栈的性能更好。 它还提供自定义序列化等功能和其他可插入的 API。
模板代码使用的仍然是远程处理 V1 堆栈。
远程处理 V2 与 V1（旧式远程处理堆栈）不兼容。 请遵照[从 V1 升级到 V2](#upgrade-from-remoting-v1-to-remoting-v2) 一文中的说明来避免对服务可用性的影响。

以下方法可用来启用 V2 堆栈。

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>通过程序集属性使用 V2 堆栈

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

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>通过显式 V2 类使用 V2 堆栈

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

2. 在 `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` 命名空间中使用 [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet)。

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

3. 在 `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` 命名空间中使用 [FabricTransportServiceRemotingClientFactory ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) 创建客户端。

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>从远程处理 V1 升级到远程处理 V2

若要从 V1 升级到 V2，必须执行双步升级。 请按顺序执行以下步骤。

1. 使用以下属性将 V1 服务升级到 V2 服务。
此项更改可确保服务在 V1 和 V2 侦听器上侦听。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在服务清单中添加名为“ServiceEndpointV2”的终结点资源。
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b.保留“数据库类型”设置，即设置为“共享”。 使用以下扩展方法创建远程处理侦听器。

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. 在远程处理接口上添加程序集属性，以使用 V1、V2 侦听器和 V2 客户端。
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. 使用 V2 客户端属性将 V1 客户端升级到 V2 客户端。
此步骤可确保客户端使用 V2 堆栈。
不需要在客户端项目/服务中进行更改。 使用更新的接口程序集生成客户端项目便已足够。

3. 此步骤是可选的。 使用 V2 侦听器属性，然后升级 V2 服务。
此步骤确保服务仅在 V2 侦听器上侦听。

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>使用远程处理 V2（接口兼容）堆栈

 远程处理 V2（与接口兼容，称为 V2_1）堆栈具有 V2 远程处理堆栈的所有功能。 其接口堆栈与远程处理 V1 堆栈兼容，但不与 V2 和 V1 向后兼容。 若要从 V1 升级到 V2_1 且不影响服务可用性，请遵循“从 V1 升级到 V2（接口兼容）”一文中的步骤。


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>通过程序集属性使用远程处理 V2（接口兼容）堆栈

执行以下步骤来改用 V2_1 堆栈。

1. 在服务清单中添加名为“ServiceEndpointV2_1”的终结点资源。

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. 使用远程处理扩展方法创建远程处理侦听器。

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. 在远程处理接口上添加一个[程序集属性](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet)。

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

不需要在客户端项目中进行更改。
使用接口程序集生成客户端程序集，以确保使用上述程序集属性。

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>使用显式远程处理类为 V2（接口兼容）版本创建侦听器/客户端工厂。

执行以下步骤:

1. 在服务清单中添加名为“ServiceEndpointV2_1”的终结点资源。

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. 使用[远程处理 V2 侦听器](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet)。 使用的默认服务终结点资源名称为“ServiceEndpointV2_1”。 必须在服务清单中定义此名称。

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

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>从远程处理 V1 升级到远程处理 V2（接口兼容）

若要从 V1 升级到 V2（接口兼容，称为 V2_1），必须执行双步升级。 请按顺序执行以下步骤。

> [!NOTE]
> 从 V1 升级到 V2 时，请确保更新 `Remoting` 命名空间以使用 V2。 例如： "ServiceFabric" （"FabricTransport"）
>
>

1. 使用以下属性将 V1 服务升级到 V2_1 服务。
此项更改可确保服务在 V1 和 V2_1 侦听器上侦听。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在服务清单中添加名为“ServiceEndpointV2_1”的终结点资源。
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b.保留“数据库类型”设置，即设置为“共享”。 使用以下扩展方法创建远程处理侦听器。

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. 在远程处理接口上添加程序集属性，以使用 V1、V2_1 侦听器和 V2_1 客户端。
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
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>在远程处理包装消息中使用自定义序列化

对于远程处理包装消息，我们将创建一个包装对象，并将所有参数作为其中的一个字段。
执行以下步骤:

1. 实现 `IServiceRemotingMessageSerializationProvider` 接口，以提供自定义序列化的实现。
    此代码片段演示该实现的大致形式。

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

2. 使用远程处理侦听器的 `JsonSerializationProvider` 重写默认序列化提供程序。

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

3. 使用远程处理客户端工厂的 `JsonSerializationProvider` 重写默认序列化提供程序。

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>后续步骤

* [Reliable Services 中使用 OWIN 的 Web API](service-fabric-reliable-services-communication-webapi.md)
* [Reliable Services 的 Windows Communication Foundation 通信](service-fabric-reliable-services-communication-wcf.md)
* [确保 Reliable Services 的通信安全](service-fabric-reliable-services-secure-communication.md)
