<properties
   pageTitle="Reliable Services WCF 通信堆栈 | Microsoft Azure"
   description="Service Fabric 中的内置 WCF 通信堆栈为 Service Services 提供客户端到服务的 WCF 通信。"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="11/17/2015"
   wacn.date=""/>

# Reliable Services 基于 WCF 的通信堆栈
Reliable services 框架使服务创作者能够选择他们要用于其服务的通信堆栈。他们可以通过从 [CreateServiceReplicaListeners 或 CreateServiceInstanceListeners](/documentation/articles/service-fabric-reliable-service-communication) 方法返回的 **ICommunicationListener**，来插入所选的通信堆栈。对于想要使用基于 Windows Communication Foundation (WCF) 的通信的服务创作者，该框架提供了基于 WCF 的通信堆栈实现。

## WCF 通信侦听器
特定于 WCF 的 **ICommunicationListener** 实现由 **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** 类提供。

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    // TODO: If your service needs to handle user requests, return a list of ServiceReplicaListeners here.
    return new[] { new ServiceReplicaListener(parameters =>
        new WcfCommunicationListener(ServiceInitializationParameters,typeof(ICalculator), this)
        {
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            EndpointResourceName = "ServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            Binding = this.CreateListenBinding()
        }
    )};
}

```

## 为 WCF 通信堆栈编写客户端
为编写客户端以便使用 WCF 与服务进行通信，该框架提供了 **WcfClientCommunicationFactory**，这是特定于 WCF 的 [ClientCommunicationFactoryBase](service-fabric-reliable-service-communication.md) 实现。

```csharp

public WcfCommunicationClientFactory(
    ServicePartitionResolver servicePartitionResolver = null,
    Binding binding = null,
    object callback = null,
    IList<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null)

```

可以从 **WcfCommunicationClientFactory** 创建的 **WcfCommunicationClient** 访问 WCF 通信通道。

```csharp

public class WcfCommunicationClient<TChannel> : ICommunicationClient where TChannel : class
{
    public TChannel Channel { get; }
    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}

```

客户端代码可以使用 **WcfCommunicationClientFactory** 以及 **ServicePartitionClient** 来确定服务终结点，并与服务通信。

```csharp

//
// Create a service resolver for resolving the endpoints of the calculator service.
//
ServicePartitionResolver serviceResolver = new ServicePartitionResolver(() => new FabricClient());

//
// Create the binding.
//
NetTcpBinding binding = CreateClientConnectionBinding();

var clientFactory = new WcfCommunicationClientFactory<ICalculator>(
    serviceResolver,// ServicePartitionResolver
    binding,        // Client binding
    null,           // Callback object
    null);          // do not retry Exception types


//
// Create a client for communicating with the calc service that has been created with the
// Singleton partition scheme.
//
var calculatorServicePartitionClient = new ServicePartitionClient<WcfCommunicationClient<ICalculator>>(
    clientFactory,
    ServiceName);

//
// Call the service to perform the operation.
//
var result = calculatorServicePartitionClient.InvokeWithRetryAsync(
    client => client.Channel.AddAsync(2, 3)).Result;

```

## 后续步骤
* [使用 Reliable Services 远程控制执行远程过程调用](/documentation/articles/service-fabric-reliable-services-communication-remoting)

* [Reliable Services 中使用 OWIN 的 Web API](/documentation/articles/service-fabric-reliable-services-communication-webapi)

<!---HONumber=Mooncake_0314_2016-->