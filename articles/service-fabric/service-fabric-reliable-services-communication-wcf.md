<properties
   pageTitle="Reliable Services API 提供的基于 WCF 的通信堆栈"
   description="本文介绍 Reliable Services API 提供的基于 WCF 的通信堆栈。"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/27/2015"
   wacn.date=""/>

# Reliable Services 基于 WCF 的通信堆栈
Reliable services 框架使服务作者能够决定他们要用于其服务的通信堆栈。他们可以通过从 [`CreateCommunicationListener`](/documentation/articles/service-fabric-reliable-service-communication) 方法返回的 `ICommunicationListener` 插入所选择的通信堆栈。该框架为要使用基于 WCF 的通信的服务作者提供了通信堆栈的基于 WCF 的实现。

## WCF 通信侦听器
特定于 WCF 的 `ICommunicationListener` 实现由 `WcfCommunicationListener` 类提供。

```csharp

public WcfCommunicationListener(
    Type communicationInterfaceType,
    Type communicationImplementationType);

protected override ICommunicationListener CreateCommunicationListener()
    {
        WcfCommunicationListener communicationListener = new WcfCommunicationListener(typeof(ICalculator), this)
        {
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // which identifies the endpoint that the wcf servicehost should listen on.
            //
            EndpointResourceName = "ServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            Binding = this.CreateListenBinding()
        };

        return communicationListener;
    }

```

## 为 WCF 通信堆栈编写客户端
为编写客户端以便使用 WCF 与服务进行通信，该框架提供了 `WcfClientCommunicationFactory`，这是特定于 WCF 的 [`ClientCommunicationFactoryBase`](/documentation/articles/service-fabric-reliable-service-communication) 实现。

```csharp

public WcfCommunicationClientFactory(
    ServicePartitionResolver servicePartitionResolver = null,
    Binding binding = null,
    object callback = null,
    IList<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null)

```

可以从 `WcfCommunicationClientFactory` 创建的 `WcfCommunicationClient` 访问 WCF 通信通道

```csharp

public class WcfCommunicationClient<TChannel> : ICommunicationClient where TChannel : class
{
    public TChannel Channel { get; }
    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}

```

客户端代码可以将 `WcfCommunicationClientFactory` 与 `ServicePartitionClient` 一起使用来确定服务终结点并与服务进行通信。

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
    null);          // donot retry Exception types


//
// Create a client for communicating with the calc service which has been created with
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
 

<!---HONumber=74-->