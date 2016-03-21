<properties
   pageTitle="Reliable Services 通信概述 | Microsoft Azure"
   description="概述 Reliable Services 通信模型，包括在服务上打开侦听器、解析终结点，以及在服务之间进行通信。"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="11/17/2015"
   wacn.date=""/>

# Reliable Services 通信模型

“Azure Service Fabric 即平台”完全不受服务间通信的影响。所有协议和堆栈（从 UDP 到 HTTP）都可接受。至于服务应以哪种方式通信，完全由服务开发人员选择。Reliable Services 应用程序框架提供了一些预先构建的通信堆栈和工具，供你用来推出自定义通信堆栈。其中包括客户端可用来发现服务终结点并与其通信的抽象。

## 设置服务通信

Reliable Services API 为服务通信使用一个简单的接口。若要打开服务的终结点，只需实现此接口即可：

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

然后，可以通过在基于服务的类方法重写中返回通信侦听器实现来添加该实现。

对于无状态服务：

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
```

对于有状态服务：

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
```

在这两种情况下，都将返回侦听器的集合。这样，服务便可以轻松使用多个侦听器。例如，你可能有一个 HTTP 侦听器和一个单独的 WebSocket 侦听器。当客户端请求服务实例或分区的侦听地址时，每个侦听器将获取一个名称，生成的“名称 : 地址”对集合以 JSON 对象形式表示。

在无状态服务中，重写将返回 ServiceInstanceListeners 的集合。ServiceInstanceListener 包含一个用于创建 ICommunicationListener 并为其命名的函数。对于有状态服务，重写将返回 ServiceReplicaListeners 集合。这与它的无状态对应项稍有不同，因为 ServiceReplicaListener 可以选择在辅助副本上打开 ICommunicationListener。你不仅可以在服务中使用多个通信侦听器，而且还可以指定哪些侦听器要在辅助副本上接受请求，以及哪些侦听器只能在主副本上进行侦听。

例如，你可以创建一个只在主副本上接受 RPC 调用的 ServiceRemotingListener，并创建另一个可在辅助副本上接受读取请求的自定义侦听器：

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(initParams =>
            new MyCustomListener(initParams),
            "customReadonlyEndpoint",
            true),

        new ServiceReplicaListener(initParams =>
            new ServiceRemotingListener<IMyStatefulInterface2>(initParams, this),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

最后，可以在[服务清单](/documentation/articles/service-fabric-application-model)中有关终结点的节下面描述服务所需的终结点。

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

通信侦听器可以从 `ServiceInitializationParameters` 中的 `CodePackageActivationContext` 访问分配给它的终结点资源。然后侦听器在打开时开始侦听请求。

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE] 终结点资源对于整个服务包是通用的，由 Service Fabric 在激活服务包时分配。所有托管在同一 ServiceHost 中的副本共享同一个端口。这意味着通信侦听器应支持端口共享。实现此目标的一种推荐方法是通信侦听器在生成侦听地址时使用分区 ID 和副本/实例 ID。

```csharp

var replicaOrInstanceId = 0;
var parameters = this.serviceInitializationParameters as StatelessServiceInitializationParameters;
if (parameters != null)
{
   replicaOrInstanceId = parameters.InstanceId;
}
else
{
   replicaOrInstanceId = ((StatefulServiceInitializationParameters) this.serviceInitializationParameters).ReplicaId;
}

var nodeContext = FabricRuntime.GetNodeContext();

var listenAddress = new Uri(
    string.Format(
        CultureInfo.InvariantCulture,
        "{0}://{1}:{2}/{5}/{3}-{4}",
        scheme,
        nodeContext.IPAddressOrFQDN,
        port,
        this.serviceInitializationParameters.PartitionId,
        replicaOrInstanceId,
        Guid.NewGuid().ToString()));

```

有关如何编写 `ICommunicationListener` 的完整演练，请参阅 [Service Fabric Web API 服务与 OWIN 自托管](/documentation/articles/service-fabric-reliable-services-communication-webapi)

### 客户端到服务的通信
Reliable Services API 提供以下抽象来方便你编写用来与服务通信的客户端。

#### ServicePartitionResolver
此实用程序类可帮助客户端在运行时确定 Service Fabric 服务的终结点。确定服务终结点的过程在 Service Fabric 术语中称为 *服务终结点解析*。

```csharp

public delegate FabricClient CreateFabricClientDelegate();

// ServicePartitionResolver methods

public ServicePartitionResolver(CreateFabricClientDelegate createFabricClient);

Task<ResolvedServicePartition> ResolveAsync(Uri serviceName,
   long partitionKey,
   CancellationToken cancellationToken);

Task<ResolvedServicePartition> ResolveAsync(ResolvedServicePartition previousRsp,
   CancellationToken cancellationToken);


```
> [AZURE.NOTE] FabricClient 是用于与 Service Fabric 群集通信以便在群集上实现各种管理操作的对象。

客户端代码通常不需要直接处理 `ServicePartitionResolver`。该类在创建后即会传递给 Reliable Services API 中的其他帮助器类。这些类在内部使用解析程序，并帮助客户端与服务通信。

#### CommunicationClientFactory
`ICommunicationClientFactory` 定义由通信客户端工厂实现的基接口，该通信客户端工厂生成可与 Service Fabric 服务通信的客户端。CommunicationClientFactory 的实现将取决于客户端要与之进行通信的 Service Fabric 服务所使用的通信堆栈。Reliable Services API 提供 `CommunicationClientFactoryBase<TCommunicationClient>`。这提供了 `ICommunicationClientFactory` 接口的基实现，并可执行所有通信堆栈通用的任务。（这些任务包括使用 `ServicePartitionResolver` 来确定服务终结点）。客户端通常实现抽象 CommunicationClientFactoryBase 类来处理通信堆栈特定的逻辑。

```csharp

protected CommunicationClientFactoryBase(
   ServicePartitionResolver servicePartitionResolver = null,
   IEnumerable<IExceptionHandler> exceptionHandlers = null,
   IEnumerable<Type> doNotRetryExceptionTypes = null);


public class MyCommunicationClient : ICommunicationClient
{
   public MyCommunicationClient(MyCommunicationChannel communicationChannel)
   {
      this.CommunicationChannel = communicationChannel;
   }
   public MyCommunicationChannel CommunicationChannel { get; private set; }
   public ResolvedServicePartition ResolvedServicePartition;
}

public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

#### ServicePartitionClient
`ServicePartitionClient` 使用 CommunicationClientFactory（并在内部使用 ServicePartitionResolver）。它还可以针对一般通信和 Service Fabric 暂时性异常处理重试，从而帮助实现与服务的通信。

```csharp

public ServicePartitionClient(
    ICommunicationClientFactory<TCommunicationClient> factory,
    Uri serviceName,
    long partitionKey);

public async Task<TResult> InvokeWithRetryAsync<TResult>(
    Func<TCommunicationClient, Task<TResult>> func,
    CancellationToken cancellationToken,
    params Type[] doNotRetryExceptionTypes)

```

下面显示了一种典型的使用模式：

```csharp

public MyCommunicationClientFactory myCommunicationClientFactory;
public Uri myServiceUri;

... other client code ..

// Call the service corresponding to the partitionKey myKey.

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(
   client =>
   {
      // Communicate with the service using the client.
      throw new NotImplementedException();
   },
   CancellationToken.None);


... other client code ...

```

## 后续步骤
* [使用 Reliable Services 远程控制执行远程过程调用](/documentation/articles/service-fabric-reliable-services-communication-remoting)

* [Reliable Services 中使用 OWIN 的 Web API](/documentation/articles/service-fabric-reliable-services-communication-webapi)

* [使用 Reliable Services 的 WCF 通信](/documentation/articles/service-fabric-reliable-services-communication-wcf)
 

<!---HONumber=Mooncake_0314_2016-->