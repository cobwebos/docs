<properties
   pageTitle="Service Fabric 提供的默认通信堆栈"
   description="本文介绍了由 Reliable Service 的服务框架提供的默认通信堆栈以及要进行通信的客户端。"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/27/2015"
   wacn.date=""/>

# Reliable Services 框架提供的默认通信堆栈
对于不受限于特定通信堆栈实现（WebAPI、WCF 等）的服务作者，该框架提供可用于在服务与客户端之间设置通信的客户端和服务端通信部分。

> [AZURE.NOTE]请更新到最新 nuget 包以获取下面提到的功能。

## 服务通信侦听器
服务的默认通信侦听器在 `ServiceCommunicationListener` 类中实现

```csharp

public class ServiceCommunicationListener<TServiceImplementation> : ICommunicationListener where TServiceImplementation : class
{
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType);
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType, string endpointResourceName);

    public void Abort();
    public Task CloseAsync(CancellationToken cancellationToken);
    public void Initialize(ServiceInitializationParameters serviceInitializationParameters);
    public Task<string> OpenAsync(CancellationToken cancellationToken);
}

```
服务实现并且要向其客户端公开的方法会在从 `IService` 接口继承的接口中定义为异步方法。服务随后可以只需实例化 `ServiceCommunicationListener` 对象并在 [`CreateCommunicationListener` 方法](/documentation/articles/service-fabric-reliable-services-communication)中返回它。例如，用于设置此通信堆栈的 HelloWorld 服务代码可以按如下所示进行定义。

```csharp

[DataContract]
public class Message
{
    [DataMember]
    public string Content;
}

public interface IHelloWorld : IService
{
    Task<Message> GetGreeting();
}

public class HelloWorldService : StatelessService, IHelloWorld
{
    public const string ServiceTypeName = "HelloWorldUsingDefaultCommunicationType";

    private Message greeting = new Message() { Content = "Default greeting" };

    protected override ICommunicationListener CreateCommunicationListener()
    {
        return new ServiceCommunicationListener<HelloWorldService>(this);
    }

    public Task<Message> GetGreeting()
    {
        return Task.FromResult(this.greeting);
    }
}

```
> [AZURE.NOTE]服务接口（例如上面的 Message 类）中的参数和返回类型应由 .net [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx) 进行序列化。


## 编写客户端以便与 ServiceCommunicationListener 进行通信
为了使客户端使用 `ServiceCommunicationListener` 与服务进行通信，框架还提供了 `ServiceProxy` 类。

```csharp

public abstract class ServiceProxy : IServiceProxy
{
...

    public static TServiceInterface Create<TServiceInterface>(Uri serviceName);

...
}

```

客户端可以实例化实现对应服务接口的服务代理对象，并对代理对象调用方法。

```csharp

var helloWorldClient = ServiceProxy.Create<IHelloWorld>(helloWorldServiceName);

var message = await helloWorldClient.GetGreeting();

Console.WriteLine("Greeting is {0}", message.Content);


```

>[AZURE.NOTE]通信框架负责将服务中引发的异常传播到客户端。因此使用 ServiceProxy 的客户端中的异常处理逻辑可以直接处理服务可能引发的异常。
 

<!---HONumber=74-->