<properties
   pageTitle="服务通信模型概述"
   description="本文介绍 Reliable Services API 支持的通信模型的基础知识。"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/27/2015"
   wacn.date=""/>

# 服务通信模型

Reliable Services 编程模型允许服务作者指定他们要用于公开服务终结点的通信机制，还提供客户端可以用于发现服务终结点并与之进行通信的抽象。

## 设置服务通信堆栈

Reliable Services API 使服务作者能够通过实现其服务中的以下方法，从而在服务中插入其选择的通信堆栈，

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}

```

`ICommunicationListener` 接口定义必须由通信侦听器为服务实现的接口。

```csharp

public interface ICommunicationListener
{
    void Initialize(ServiceInitializationParameters serviceInitializationParameters);

    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```
服务所需的终结点在终结点部分下的[服务清单](/documentation/articles/service-fabric-application-model)中描述。

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

通信侦听器可以从 `ServiceInitializationParameters` 中的 `CodePackageActivationContext` 访问分配给它的终结点资源，并在打开时开始侦听请求。

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE]终结点资源对于整个服务包是通用的，由 Service Fabric 在激活服务包时分配。（有关进一步的详细信息，请参阅 [Service Fabric ServiceModel](/documentation/articles/service-fabric-service-model)）。因此相同 ServiceHost 上承载的所有副本都共享相同端口。这意味着通信侦听器应支持端口共享。实现此目标的一种推荐方法是通信侦听器在生成侦听地址时使用分区 ID 和副本/实例 ID。

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

## 客户端到服务的通信
Reliable Services API 提供了以下抽象，通过它们可以轻松地编写客户端以用于与服务进行通信。

## ServicePartitionResolver
ServicePartitionResolver 类可帮助客户端在运行时确定 Service Fabric 服务的终结点。

> [AZURE.TIP]确定服务终结点的过程在 Service Fabric 术语中称为服务终结点解析。

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
> [AZURE.NOTE]FabricClient 是用于与 Service Fabric 群集通信以便在 Service Fabric 群集上实现各种管理操作的对象。

客户端代码通常无需直接使用 `ServicePartitionResolver`。它会进行创建并传递给 Reliable Service API 中的其他帮助程序类，这些类在内部使用解析程序并帮助客户端与服务进行通信。

## CommunicationClientFactory
`ICommunicationClientFactory` 定义由通信客户端工厂实现的基接口，该通信客户端工厂生成可与 ServiceFabric 服务通信的客户端。CommunicationClientFactory 的实现将取决于客户端要与之进行通信的 Service Fabric 服务所使用的通信堆栈。Reliable Service API 提供了一个 CommunicationClientFactoryBase<TCommunicationClient>，它提供此 `ICommunicationClientFactory` 接口的基实现，并执行所有通信堆栈共有的任务。（如使用 `ServicePartitionResolver` 确定服务终结点）。客户端通常实现抽象 CommunicationClientFactoryBase 类来处理通信堆栈特定逻辑。

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
    protected override void AbortClient(MyCommunicationClient1 client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(ResolvedServiceEndpoint endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(ResolvedServiceEndpoint endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

## ServicePartitionClient
`ServicePartitionClient` 使用 CommunicationClientFactory（并在内部使用 ServicePartitionResolver），通过处理常见通信和 Service Fabric 暂时异常的重试来帮助与服务进行通信。

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

一种典型使用模式如下所示，

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
* [Reliable Services 框架提供的默认通信堆栈](/documentation/articles/service-fabric-reliable-services-communication-default)

* [Reliable Services 框架提供的基于 WCF 的通信堆栈](/documentation/articles/service-fabric-reliable-services-communication-wcf)

* [使用 Reliable Services API 编写使用 WebAPI 通信堆栈的服务](/documentation/articles/service-fabric-reliable-services-communication-webapi)
 

<!---HONumber=74-->