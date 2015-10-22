<properties
   pageTitle="带 OWIN 自承载的 Service Fabric Web API | Microsoft Azure"
   description="这篇 Service Fabric 文章说明如何在 Reliable Services 中使用带 OWIN 自承载的 ASP.NET Web API 实现服务通信。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="07/23/2015"
   wacn.date=""/>

# 带 OWIN 自承载的 Microsoft Azure Service Fabric Web API 服务入门

Service Fabric 让你有权决定你希望你的服务如何与用户以及相互之间进行通信。本教程重点介绍如何在 Service Fabric 的 *Reliable Services* API 中使用带 OWIN 自承载的 ASP.NET Web API 实现服务通信。我们会深入介绍 *Reliable Services* 可插入通信 API，并向你分步演示如何通过用作示例的 Web API 为服务设置自定义通信侦听器。要查看 Web API 通信侦听器的完整示例，请参阅 [GitHub 上的 Service Fabric WebApplication 示例](https://github.com/Azure/servicefabric-samples/tree/master/samples/Services/VS2015/WebApplication)。


## Service Fabric 中的 Web API 简介

ASP.NET Web API 是一个常用的强大框架，用于在 .NET Framework 之上构建 HTTP API。如果你尚不熟悉它，请立即访问 [www.asp.net/webapi](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) 以详细了解 Web API。

Service Fabric 中的 Web API 是你熟知并喜爱的相同 ASP.NET Web API。区别在于如何*承载* Web API 应用程序（提示：不会使用 IIS）。为了更好地了解区别，我们将它划分为两个部分：

 1. Web API 应用程序（控制器、模型等）
 2. 主机（Web 服务器，通常是 IIS）

Web API 应用程序本身在此处不会更改 — 它与你可能已在过去编写的 Web API 应用程序没什么不同，你应能够转移大部分应用程序代码。如果你习惯于在 IIS 上进行承载，则承载应用程序可能与你的习惯稍有不同。但在我们深入探讨承载部分之前，我们从更熟悉的部分开始：Web API 应用程序。


## 设置 Web API 应用程序

首先在 Visual Studio 2015 中创建一个新应用程序（具有单个无状态服务）：

![创建新的 Service Fabric 应用程序](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

![创建单个无状态服务](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

这为我们提供了一个空的无状态服务，它会承载 Web API 应用程序。我们将从头开始设置应用程序，以了解如何组合其所有内容。

第一步是为 Web API 拉入一些 NuGet 包。我们要使用的包是 **Microsoft.AspNet.WebApi.OwinSelfHost**。此包中包括所有所需的 Web API 包和*主机*包（这在后面会非常重要）。

![](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

安装了这些包之后，我们可以开始构建基本的 Web API 项目结构。如果你使用过 Web API，则项目结构应看上去非常熟悉。首先创建基本的 Web API 目录：

 + App\_Start
 + Controllers
 + Models

在 App\_Start 目录中添加基本的 Web API 配置类：

 + FormatterConfig.cs

```csharp

namespace WebApi
{
    using System.Net.Http.Formatting;

    public static class FormatterConfig
    {
        public static void ConfigureFormatters(MediaTypeFormatterCollection formatters)
        {
        }
    }
}

```

 + RouteConfig.cs

```csharp

namespace WebApi
{
    using System.Web.Http;

    public static class RouteConfig
    {
        public static void RegisterRoutes(HttpRouteCollection routes)
        {
            routes.MapHttpRoute(
                    name: "DefaultApi",
                    routeTemplate: "api/{controller}/{id}",
                    defaults: new { controller = "Default", id = RouteParameter.Optional }
                );
        }
    }
}

```

在 Controllers 目录中添加默认控制器：

 + DefaultController.cs

```csharp

namespace WebApi.Controllers
{
    using System.Collections.Generic;
    using System.Web.Http;

    public class DefaultController : ApiController
    {
        // GET api/values
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5
        public void Delete(int id)
        {
        }
    }
}

```

最后，在项目根目录中添加一个 Startup 类，用于注册路由、格式化程序和任何其他配置设置。这也是 Web API 插入到*主机*中的位置（会在以后再次重新访问）。设置 Startup 类时，为 Startup 类创建一个名为 *IOwinAppBuilder* 的接口，用于定义 Configuration 方法。虽然这在技术上不是 Web API 正常运行所必需的，但是通过它可以在以后更灵活地使用 Startup 类。

 + Startup.cs

```csharp

namespace WebApi
{
    using Owin;
    using System.Web.Http;

    public class Startup : IOwinAppBuilder
    {
        public void Configuration(IAppBuilder appBuilder)
        {
            HttpConfiguration config = new HttpConfiguration();

            FormatterConfig.ConfigureFormatters(config.Formatters);
            RouteConfig.RegisterRoutes(config.Routes);

            appBuilder.UseWebApi(config);
        }
    }
}

```

 + IOwinAppBuilder.cs

```csharp

namespace WebApi
{
    using Owin;

    public interface IOwinAppBuilder
    {
        void Configuration(IAppBuilder appBuilder);
    }
}

```

应用程序部分就是这样。此时，我们只是设置了基本的 Web API 项目布局。与你在过去可能已编写的 Web API 项目或基本的 Web API 模板相比，它到目前为止看起来应该差别不大。你的业务逻辑同往常一样放入控制器和模型中。

现在我们该针对承载执行什么操作以便我们可以实际运行它？


## 服务承载

在 Service Fabric 中，服务在*服务主机进程*（运行服务代码的可执行文件）中运行。使用 Reliable Services API 编写服务时，以及实际上在采用 .NET 对 Service Fabric 编写服务的大多数情况下，服务项目仅仅编译为一个 .EXE，它会注册服务类型并运行代码。事实上，如果你打开无状态服务项目中的 **Program.cs**，则应该看到：

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterServiceType(Service.ServiceTypeName, typeof(Service));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e);
            throw;
        }
    }
}

```

如果这看上去疑似控制台应用程序的入口点，那么是因为它是：

![](media/service-fabric-reliable-services-communication-webapi/webapi-projectproperties.png)

有关服务主机进程和服务注册的详细信息超出了本文的讨论范围，但当前应了解**服务代码在其自己的进程中运行**，这十分重要。

## 带 OWIN 主机的自承载 Web API

考虑到 Web API 应用程序代码在其自己的进程中承载，我们该如何将它挂接到 Web 服务器？ 进入 [OWIN](http://owin.org/)。OWIN 只是 .NET Web 应用程序与 Web 服务器之间的协定。传统上使用 ASP.NET（最高为 MVC 5）时，Web 应用程序通过 System.Web 与 IIS 紧密耦合。但是，Web API 实现 OWIN，这使你可以编写一个与承载其自身的 Web 服务器分离的 Web 应用程序。这使你能够使用可在自己进程中启动的*自承载* OWIN Web 服务器，这十分适合于我们刚刚介绍的 Service Fabric 承载模型。

在本文中，我们将使用 Katana 作为 Web API 应用程序的 OWIN 主机。Katana 是一种开源 OWIN 主机实现。

> [AZURE.NOTE]要详细了解 Katana，请访问 [Katana 站点](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana)，有关如何使用 Katana 自承载 Web API 的快速概述，请参阅有关如何[使用 OWIN 自承载 ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api) 这篇文章。


## 设置 Web 服务器

Reliable Services API 为业务逻辑提供了两个入口点：

 + 一个开放式入口点方法，可在其中开始执行任何所需的工作负荷，主要针对长时间运行的计算工作负荷：

```csharp

protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}

```

 + 一个通信入口点，可在其中插入选择的通信堆栈：

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}

```

Web 服务器（以及可能在将来使用的任何其他通信堆栈，如 WebSockets）应使用 ICommunicationListener 接口与系统正确集成。这样做的原因会在后续步骤中表现得更明显。

首先创建一个名为 OwinCommunicationListener 的类，它实现 ICommunicationListener：

 + OwinCommunicationListener.cs:

```csharp

namespace WebApi
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services;

    public class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public void Initialize(ServiceInitializationParameters serviceInitializationParameters)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}

```

ICommunicationListener 接口提供了 4 个方法来为服务管理通信侦听器：

 + **Initialize**：这通常是在其中设置服务将侦听的地址的位置。对于 Web 服务器，这是在其中设置 URL 的位置。
 + **OpenAsync**：开始侦听请求。
 + **CloseAsync**：停止侦听请求，完成任何正在进行的请求，然后正常关闭。
 + **Abort**：取消所有内容并立即停止。

要开始进行，请为 URL 路径前缀和前面创建的 **Startup** 类添加私有类成员。这些成员会通过构造函数初始化，并在后面设置侦听 URL 时使用。另请添加私有类成员以保存侦听地址和服务器句柄（分别在初始化过程中及以后在启动服务器时创建）。

```csharp

public class OwinCommunicationListener : ICommunicationListener
{
    private readonly IOwinAppBuilder startup;
    private readonly string appRoot;
    private IDisposable serverHandle;
    private string listeningAddress;

    public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup)
    {
        this.startup = startup;
        this.appRoot = appRoot;
    }

    ...

```

### Initialize

在此处设置 Web 服务器 URL。为此，需要几条信息：

 + **URL 路径前缀**。尽管是可选的，不过最好现在对此进行设置，以便你可以在应用程序中安全地承载多个 Web 服务。
 + **端口**。

在我们获取 Web 服务器的端口之前，请务必了解 Service Fabric 提供了一个应用程序层，该层充当应用程序与用于运行它的底层操作系统之间的缓冲区。因此，Service Fabric 提供了一种方法来为服务配置*终结点*。Service Fabric 负责确保终结点可供你的服务使用，因此你不必使用底层操作系统环境自己配置它。这使你可以轻松地在不同环境中承载 Service Fabric 应用程序，而不必对应用程序进行任何更改（例如，可以在 Azure 中或自己的数据中心内承载同一个应用程序）。

在 PackageRoot\\ServiceManifest.xml 中配置 HTTP 终结点：

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="80" />
    </Endpoints>
</Resources>

```

此步骤非常重要，因为服务主机进程使用受限制凭据（Windows 上的网络服务）运行，这意味着服务没有自己设置 HTTP 终结点的权限。通过使用终结点配置，Service Fabric 知道要为服务将侦听的 URL 设置正确的 ACL，同时提供了一个标准位置来配置终结点。

返回到 OwinCommunicationListener.cs 中，在 Initialize 方法中获取终结点信息以获取端口。创建服务将侦听的 URL 并将它保存到前面创建的类成员变量。这将在 OpenAsync 中用于启动 Web 服务器。

```csharp

public void Initialize(ServiceInitializationParameters serviceInitializationParameters)
{
    EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = String.Format(
        CultureInfo.InvariantCulture,
        "http://+:{0}/{1}",
        port,
        String.IsNullOrWhiteSpace(this.appRoot)
            ? String.Empty
            : this.appRoot.TrimEnd('/') + '/');
}

```

请注意，此处使用了“http://+”。这是为了确保 Web 服务器侦听所有可用的地址，包括 localhost、FQDN 和计算机 IP。

### OpenAsync

调用 Initialize 之后，平台会调用 OpenAsync。这是使用在 Initialize 中创建的地址打开 Web 服务器的位置。

实现 OpenAsync 是为何以 ICommunicationListener 形式实现 Web 服务器（或任何通信堆栈），而不是仅仅直接在服务中从 RunAsync() 打开它的最重要原因之一。OpenAsync 的返回值是 Web 服务器所侦听的地址。当此地址返回到系统时，它会向服务注册此地址。Service Fabric 提供了一个 API，使客户端或其他服务随后可以通过服务名称请求此地址。这十分重要，因为随着服务为实现资源平衡和可用性目标而在群集中四处移动，服务地址也不为静态。这是允许客户端为服务解析侦听地址的机制。

明确这一点后，OpenAsync 会启动 Web 服务器，并返回它所侦听的地址。请注意它侦听“http://+”，但在返回地址之前，“+”会替换为它当前所处节点的 IP 或 FQDN。这样做的原因是方法正返回的该地址是向系统注册的内容，并且是客户端和其他服务在请求服务地址时看到的内容。要使客户端可以正确连接到它，它们在地址中需要实际 IP 或 FQDN。

```csharp

public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));

    string resultAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    ServiceEventSource.Current.Message("Listening on {0}", resultAddress);

    return Task.FromResult(resultAddress);
}

```

请注意，这会引用在构造函数中传入到 OwinCommunicationListener 的 **Startup** 类。此 Startup 实例由 Web 服务器用于启动 Web API 应用程序。

将来在你运行应用程序时，ServiceEventSource.Current.Message() 行会出现在诊断事件窗口中，以便让你知道 Web 服务器已成功启动。

### CloseAsync 和 Abort

最后，实现 CloseAsync 和 Abort 以停止 Web 服务器。可以通过释放在 OpenAsync 过程中创建的服务器句柄来停止 Web 服务器。

```csharp

public Task CloseAsync(CancellationToken cancellationToken)
{
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.serverHandle != null)
    {
        try
        {
            this.serverHandle.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}

```

在此示例实现中，CloseAsync 和 Abort 都只是停止 Web 服务器。你可以选择在 CloseAsync 中更加妥善协调地关闭 Web 服务器；例如，在返回前等待正在进行的请求完成。

## 启动 Web 服务器

你现在已准备好创建并返回 OwinCommunicationListener 的实例以启动 Web 服务器。返回到 Service 类 (Service.cs) 中，重写 **CreateCommunicationListener()** 方法：

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
	return new OwinCommunicationListener("api", new Startup());
}

```

这是 Web API *应用程序*和 OWIN *主机*最终会面的位置：向*主机* (**OwinCommunicationListener**) 提供*应用程序*的实例（通过 **Startup** 的 Web API），Service Fabric 会管理其生命周期。通常任何通信堆栈都可以遵循这一相同模式。

## 汇总

在此示例中，你无需在 RunAsync() 方法中执行任何操作，从而可以简单地删除重写。

最终服务实现应非常简单，因为它只需要创建通信侦听器：

```csharp

namespace WebApi
{
    using Microsoft.ServiceFabric.Services;

    public class Service : StatelessService
    {
        public const string ServiceTypeName = "WebApiType";

        protected override ICommunicationListener CreateCommunicationListener()
        {
            return new OwinCommunicationListener("api", new Startup());
        }
    }
}

```

以及完整的 OwinCommunicationListener 类：

```csharp

namespace WebApi
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services;

    public class OwinCommunicationListener : ICommunicationListener
    {
        private readonly IOwinAppBuilder startup;
        private readonly string appRoot;
        private IDisposable serverHandle;
        private string listeningAddress;

        public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup)
        {
            this.startup = startup;
            this.appRoot = appRoot;
        }

        public void Initialize(ServiceInitializationParameters serviceInitializationParameters)
        {
            EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
            int port = serviceEndpoint.Port;

            this.listeningAddress = String.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/{1}",
                port,
                String.IsNullOrWhiteSpace(this.appRoot)
                    ? String.Empty
                    : this.appRoot.TrimEnd('/') + '/');
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));

            string resultAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            ServiceEventSource.Current.Message("Listening on {0}", resultAddress);

            return Task.FromResult(resultAddress);
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.serverHandle != null)
            {
                try
                {
                    this.serverHandle.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}

```

所有部分都准备就绪后，项目现在应类似于具有 Reliable Services API 入口点和 OWIN 主机的典型 Web API 应用程序：


![](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## 运行 Web 浏览器并通过它进行连接

如果尚未这样做，请[设置开发环境](service-fabric-get-started.md)。


现在，你可以生成并部署你的服务。在 Visual Studio 中按 **F5** 以生成并部署应用程序。在“诊断事件”窗口中，你应看到一条消息，指示已在 **http://localhost:80/api** 上打开了 Web 服务器


![](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE]如果该端口已由计算机上的其他进程打开，则你可能会在此处看到错误，指示无法打开侦听器。如果是这种情况，请在 ServiceManifest.xml 中的终结点配置中尝试使用不同端口。


服务运行之后，打开浏览器并导航到 [http://localhost/api](http://localhost/api) 对它进行测试。

## 将其扩展

扩展无状态 Web 应用通常意味着添加更多计算机并在其上运行 Web 应用。每当向群集添加新节点时，Service Fabric 的业务流程引擎可以为你执行此操作。创建无状态服务的实例时，可以指定要创建的实例数。Service Fabric 将该数量的实例相应地放置在群集中的节点上，确保未在任何一个节点上创建多个实例。还可以通过为实例数指定“-1”，指示 Service Fabric 始终在每个节点上创建一个实例。这可保证每当添加节点以扩展群集时，都会在新节点上创建无状态服务的实例。此值是服务实例的属性，因此它在通过 PowerShell 创建服务实例时设置：

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

或是在 Visual Studio 无状态服务项目中定义默认服务时设置：

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

有关创建应用程序和服务实例的详细信息，请参阅[如何部署和删除应用程序](/documentation/articles/service-fabric-deploy-remove-applications)。

## ASP.NET 5

在 ASP.NET 5 中，在 Web 应用程序中将*应用程序*与*主机*分离的概念和编程模型保持不变。它还适用于其他形式的通信。此外，虽然*主机*在 ASP.NET 5 中可能有所不同，不过 Web API *应用程序*层保持不变，这是大多数应用程序逻辑实际所处的位置。

## 后续步骤

[在 Visual Studio 中调试 Service Fabric 应用程序](/documentation/articles/service-fabric-debugging-your-application)

<!---HONumber=74-->