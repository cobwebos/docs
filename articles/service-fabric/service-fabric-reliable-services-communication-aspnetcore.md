---
title: 使用 ASP.NET Core 来与服务通信 | Microsoft Docs
description: 了解如何在无状态和有状态 Reliable Services 中使用 ASP.NET Core。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 638c06e1854504dcb7ff34b1d9df56694556c421
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939791"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>Azure Service Fabric Reliable Services 中的 ASP.NET Core

ASP.NET Core 是一个开源和跨平台框架。 此框架用于构建基于云的、 已连接 internet 的应用程序，例如 web 应用、 IoT 应用和移动后端。

本文是使用承载 Service Fabric Reliable Services 中的 ASP.NET Core 服务的深入指南**Microsoft.ServiceFabric.AspNetCore。** NuGet 包的组。

有关 Service Fabric 中 ASP.NET core 的入门教程以及设置开发环境的说明，请参阅[教程：创建和部署应用程序使用 ASP.NET Core Web API 前端服务和有状态后端服务](service-fabric-tutorial-create-dotnet-app.md)。

本文的其余部分假定您已经熟悉 ASP.NET Core。 如果没有，请通读[ASP.NET Core 基础知识](https://docs.microsoft.com/aspnet/core/fundamentals/index)。

## <a name="aspnet-core-in-the-service-fabric-environment"></a>Service Fabric 环境中的 ASP.NET Core

ASP.NET Core 和 Service Fabric 应用程序可以在.NET Core 或完整的.NET Framework 上运行。 在 Service Fabric 中的两种不同方式，可以使用 ASP.NET Core:
 - **作为来宾可执行文件托管**。 此方法主要用于 Service Fabric 上运行现有 ASP.NET Core 应用程序，不更改代码。
 - **在 reliable service 内部运行**。 这种方式允许与 Service Fabric 运行时更好的集成，并允许有状态 ASP.NET Core 服务。

本文的其余部分介绍如何使用 ASP.NET Core 可靠服务，通过 Service Fabric SDK 所附带的 ASP.NET Core 集成组件内。

## <a name="service-fabric-service-hosting"></a>Service Fabric 服务托管

在 Service Fabric 中一个或多个实例和/或你的服务副本中运行*服务主机进程*： 运行服务代码的可执行文件。 作为服务的创作者，自己的服务主机进程，以及 Service Fabric 激活并监视此。

传统的 ASP.NET（最高为 MVC 5）通过 System.Web.dll 与 IIS 紧密耦合。 ASP.NET Core 在 Web 服务器和 Web 应用程序之间提供分隔。 这种分离允许 web 应用程序在另一个 web 服务器之间移植。 它还允许 web 服务器不用*自承载*。 这意味着可以在您自己的进程，而不是专用的 web 服务器软件 （如 IIS） 所拥有的进程中启动 web 服务器。

要合并 Service Fabric 服务和 ASP.NET，无论是作为来宾可执行文件或在 reliable service 中，您必须能够在服务主机进程内启动 ASP.NET。 可借助 ASP.NET Core 的自托管功能执行此操作。

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Reliable service 中托管 ASP.NET Core
通常情况下，自托管 ASP.NET Core 应用程序会在应用程序的入口点创建 WebHost，如 `Program.cs` 中的 `static void Main()` 方法。 在这种情况下，WebHost 的生命周期绑定到进程的生命周期。

![在进程中托管 ASP.NET Core][0]

但应用程序入口点不是在 reliable service 中创建 WebHost 的正确位置。 这是因为应用程序入口点仅用于向 Service Fabric 运行时，注册服务类型，以便它可以创建该服务类型的实例。 可靠服务本身中，应创建 WebHost。 在服务主机进程中，服务实例和/或副本可以经过多个生命周期。 

Reliable Service 实例由派生自 `StatelessService` 或 `StatefulService` 的服务类表示。 服务的通信堆栈包含在服务类中的 `ICommunicationListener` 实现内。 `Microsoft.ServiceFabric.AspNetCore.*` NuGet 包包含的实现`ICommunicationListener`可启动和 reliable service 中 Kestrel 或 HTTP.sys 管理 ASP.NET Core WebHost。

![用于托管 ASP.NET Core 可靠服务中的关系图][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
`ICommunicationListener` Kestrel 和 HTTP.sys 中的实现`Microsoft.ServiceFabric.AspNetCore.*`NuGet 包具有类似的使用模式。 但执行特定于每个 web 服务器的操作略有不同。 

这两种通信侦听器都能提供采用以下参数的构造函数：
 - **`ServiceContext serviceContext`**：这是`ServiceContext`对象，其中包含有关正在运行的服务的信息。
 - **`string endpointName`**：这是名称`Endpoint`ServiceManifest.xml 中的配置。 它主要是两个通信侦听器的不同之处。 HTTP.sys*需要*`Endpoint`配置，而 Kestrel 不会。
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**：这是实现，在创建并返回一个 lambda `IWebHost`。 它允许您配置`IWebHost`通常会在 ASP.NET Core 应用程序中的方法。 Lambda 为您提供了生成的 URL，具体取决于 Service Fabric 集成选项，则使用和`Endpoint`你提供的配置。 然后，您可以修改或使用该 URL 来启动 web 服务器。

## <a name="service-fabric-integration-middleware"></a>Service Fabric 集成中间件
`Microsoft.ServiceFabric.AspNetCore` NuGet 包中包含`UseServiceFabricIntegration`扩展方法`IWebHostBuilder`，它将 Service Fabric – 可识别的中间件。 此中间件将 Kestrel 和 HTTP.sys 配置`ICommunicationListener`向 Service Fabric 命名服务注册唯一的服务 URL。 它会验证客户端请求，以确保客户端连接到适当的服务。 

此步骤是必需的以防止客户端错误地连接到错误的服务。 这是因为，在 Service Fabric 等共享主机环境中，多个 web 应用程序可以在同一物理或虚拟机上运行，但不使用唯一的主机名。 后续部分将对此方案进行详细说明。

### <a name="a-case-of-mistaken-identity"></a>错误标识示例
服务副本（无论哪种协议）侦听唯一的 IP:port 组合。 一旦服务副本开始侦听 ip: port 终结点，它向 Service Fabric 命名服务报告该终结点地址。 客户端或其他服务可以发现它。 如果服务使用动态分配应用程序端口，服务副本可能恰巧使用同一 ip: port 终结点的另一个服务之前在同一物理或虚拟机。 这可能会导致客户端错误地连接到错误的服务。 这种情况下可能会导致发生以下事件序列：

 1. 服务 A 通过 HTTP 侦听 10.0.0.1:30000。 
 2. 客户端解析服务 A 并获取地址 10.0.0.1: 30000。
 3. 服务 A 移动到其他节点。
 4. 服务 B 放置在 10.0.0.1 并恰巧使用了同一端口 30000。
 5. 客户端尝试使用缓存地址 10.0.0.1:30000 连接到服务 A。
 6. 客户端现在已成功连接到服务 B，并未意识到已连接到错误的服务。

这可能导致在随机时间出现 bug，并且很难诊断。

### <a name="using-unique-service-urls"></a>使用唯一的服务 URL
为避免这些 bug，服务可以发布到命名的唯一标识符，与服务终结点，然后在客户端请求过程中验证该唯一标识符。 这是非恶意租户受信任环境中的服务之间的协作操作。 它不提供在恶意租户环境中的安全服务身份验证。

在受信任环境中，通过添加中间件`UseServiceFabricIntegration`方法自动发布到命名服务的地址追加唯一标识符。 它会验证该标识符在每个请求。 如果标识符不匹配，中间件将立即返回 HTTP 410 Gone 响应。

使用动态分配的端口的服务应使用此中间件。

在协作环境中，使用固定唯一端口的服务不存在此问题。 固定唯一端口通常用于面向外部的服务，此类服务需要可供客户端应用程序连接到的已知端口。 例如，大多数面向 internet 的 web 应用程序将使用 web 浏览器连接的端口 80 或 443。 在这种情况下，不应启用唯一标识符。

下图显示了启用中间件时的请求流：

![Service Fabric ASP.NET Core 集成][2]

Kestrel 和 HTTP.sys`ICommunicationListener`实现以完全相同的方式使用此机制。 尽管 HTTP.sys 内部区分请求通过基础基于唯一 URL 路径**HTTP.sys**端口共享功能，功能是*不*由 HTTP.sys `ICommunicationListener`实现。 这是因为这会导致上述方案中的 HTTP 503 和 HTTP 404 错误状态代码。 这进而使它困难的客户端确定的错误，因为 HTTP 503 和 HTTP 404 通常用于指示其他错误。 

因此，Kestrel 和 HTTP.sys`ICommunicationListener`提供的中间件上实现标准化`UseServiceFabricIntegration`扩展方法。 因此，客户端只需对 HTTP 410 响应执行服务终结点重新解析操作。

## <a name="httpsys-in-reliable-services"></a>Reliable Services 中的 HTTP.sys
可以通过导入在 Reliable Services 中使用 HTTP.sys **Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet 包。 此包包含`HttpSysCommunicationListener`，实现`ICommunicationListener`。 `HttpSysCommunicationListener` 可以创建与 web 服务器使用 HTTP.sys 在 reliable service 内部 ASP.NET Core WebHost。

HTTP.sys 基于[Windows HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx)。 此 API 使用**HTTP.sys**内核驱动程序以处理 HTTP 请求并将它们路由到运行 web 应用程序的进程。 这允许在相同的物理或虚拟机以托管 web 应用程序在同一端口上产生歧义的唯一 URL 路径或主机名称： 上的多个进程。 Service Fabric 在同一群集中托管多个网站时，这些功能非常有用。

>[!NOTE]
>HTTP.sys 实现仅适用于 Windows 平台。

下图说明了如何使用 HTTP.sys **HTTP.sys**内核驱动程序在 Windows 上的进行端口共享：

![HTTP.sys diagram][3]

### <a name="httpsys-in-a-stateless-service"></a>无状态服务中的 HTTP.sys
若要在无状态服务中使用 `HttpSys`，需替代 `CreateServiceInstanceListeners` 方法并返回 `HttpSysCommunicationListener` 实例：

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HTTP.sys 在有状态服务

`HttpSysCommunicationListener` 不当前适用于有状态服务由于变得复杂的基础**HTTP.sys**端口共享功能。 详细信息，请参阅关于使用 HTTP.sys 的动态端口分配的下一节。 对于有状态服务，Kestrel 是建议的 web 服务器。

### <a name="endpoint-configuration"></a>终结点配置

`Endpoint`则需要使用 Windows HTTP Server API，包括 HTTP.sys 的 web 服务器的配置。 使用 Windows HTTP Server API 的 web 服务器首先必须保留其使用 HTTP.sys 的 URL (这通常通过实现[netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx)工具)。 

此操作需要提升你的服务没有在默认情况下的权限。 "Http"或"https"选项`Protocol`属性的`Endpoint`ServiceManifest.xml 中的配置中专门用于指示 Service Fabric 运行时代表你向 HTTP.sys 注册 URL。 这是通过使用[*强通配符*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) URL 前缀。

例如，若要保留`http://+:80`对于服务，请在 ServiceManifest.xml 中使用以下配置：

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

并且必须将终结点名称传递到 `HttpSysCommunicationListener` 构造函数：

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>与静态端口使用 HTTP.sys
若要静态端口配合使用 HTTP.sys，提供中的端口号`Endpoint`配置：

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>使用动态端口使用 HTTP.sys
若要动态分配的端口配合使用 HTTP.sys，省略`Port`中的属性`Endpoint`配置：

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

动态端口分配`Endpoint`配置提供了一个端口*每个主机进程*。 当前的 Service Fabric 托管模型允许多个服务实例和/或在同一进程中承载的副本。 这意味着每个将共享相同的端口时通过分配`Endpoint`配置。 多个**HTTP.sys**实例可以共享一个端口，通过使用基础**HTTP.sys**端口共享功能。 但它不受`HttpSysCommunicationListener`此做法，因为它引入了用于客户端请求。 对于动态端口用法，Kestrel 是建议的 web 服务器。

## <a name="kestrel-in-reliable-services"></a>Reliable Services 中的 Kestrel
可以通过导入在 Reliable Services 中使用 Kestrel **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet 包。 此包包含`KestrelCommunicationListener`，实现`ICommunicationListener`。 `KestrelCommunicationListener` 可以通过将 Kestrel 用作 web 服务器中创建 ASP.NET Core WebHost 在 reliable service 内部。

Kestrel 是基于 libuv 的 ASP.NET Core 的跨平台 Web 服务器，libuv 是跨平台异步 I/O 库。 不同于 HTTP.sys，Kestrel 不使用集中式终结点管理器。 也不同于 HTTP.sys，Kestrel 不支持多个进程之间共享端口。 Kestrel 的每个实例必须使用唯一端口。

![Kestrel 关系图][4]

### <a name="kestrel-in-a-stateless-service"></a>无状态服务中的 Kestrel
若要在无状态服务中使用 `Kestrel`，需替代 `CreateServiceInstanceListeners` 方法并返回 `KestrelCommunicationListener` 实例：

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>有状态服务中的 Kestrel
若要在有状态服务中使用 `Kestrel`，需替代 `CreateServiceReplicaListeners` 方法并返回 `KestrelCommunicationListener` 实例：

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

此示例中为 WebHost 依赖关系注入容器提供 `IReliableStateManager` 的单一实例。 这并不是必需的但它允许您使用`IReliableStateManager`和可靠集合在 MVC 控制器操作方法中。

有状态服务中不会为 `KestrelCommunicationListener` 提供`Endpoint` 配置名称。 后续部分会对此进行详细说明。

### <a name="configure-kestrel-to-use-https"></a>将 Kestrel 配置为使用 HTTPS
在服务中启用 HTTPS 与 Kestrel，您将需要设置多个侦听选项。 更新`ServiceInstanceListener`若要使用*EndpointHttps*终结点和侦听特定端口 （例如端口 443）。 在配置 web 主机使用 Kestrel web 服务器时，必须配置 Kestrel 来侦听所有网络接口上的 IPv6 地址： 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

在本教程的完整示例，请参阅[配置 Kestrel 为使用 HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https)。


### <a name="endpoint-configuration"></a>终结点配置
`Endpoint`无需配置即可使用 Kestrel。 

Kestrel 是简单的独立 web 服务器。 它不需要与 HTTP.sys （或 HttpListener） 不同`Endpoint`ServiceManifest.xml 中的配置中因为它不在开始之前需要注册 URL。 

#### <a name="use-kestrel-with-a-static-port"></a>将 Kestrel 和静态端口配合使用
可以配置中的静态端口`Endpoint`ServiceManifest.xml 配置为与 Kestrel 配合使用。 尽管这不是绝对必需的它提供了两个潜在好处：
 - 如果端口不会在应用程序端口范围中的端口号，它是通过 OS 防火墙打开 Service fabric。
 - 通过 `KestrelCommunicationListener` 提供的 URL 将使用此端口。

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

如果已配置 `Endpoint`，则其名称必须传递到 `KestrelCommunicationListener` 构造函数： 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

如果不使用 ServiceManifest.xml`Endpoint`配置中省略此名称`KestrelCommunicationListener`构造函数。 在这种情况下，它将使用动态端口。 请参阅下的节，了解更多相关信息。

#### <a name="use-kestrel-with-a-dynamic-port"></a>将 Kestrel 和动态端口配合使用
Kestrel 无法使用从自动端口分配`Endpoint`ServiceManifest.xml 中的配置。 这是因为自动端口分配从`Endpoint`配置会向每个唯一的端口分配*主机进程*，并且单个主机进程可以包含多个 Kestrel 实例。 这不会使用 Kestrel，因为它不支持端口共享。 因此，每个 Kestrel 实例必须在唯一端口上打开。

若要动态端口分配配合使用 Kestrel，省略`Endpoint`ServiceManifest.xml 中的配置中完全，并不将传递到的终结点名称`KestrelCommunicationListener`构造函数中的，按如下所示：

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

在此配置中，`KestrelCommunicationListener` 会自动从应用程序端口范围中选择未使用的端口。

## <a name="service-fabric-configuration-provider"></a>Service Fabric 配置提供程序
ASP.NET Core 中的应用程序配置基于键-值对由配置提供程序。 读取[ASP.NET Core 中的配置](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/)若要了解更多在常规 ASP.NET Core 配置支持。

本部分介绍如何对 Service Fabric 配置提供程序与集成的 ASP.NET Core 配置通过导入`Microsoft.ServiceFabric.AspNetCore.Configuration`NuGet 包。

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration 启动扩展
在导入后`Microsoft.ServiceFabric.AspNetCore.Configuration`NuGet 包，您需要使用 ASP.NET Core 配置 API 注册 Service Fabric 配置源。 为此，可检查**AddServiceFabricConfiguration**中的扩展`Microsoft.ServiceFabric.AspNetCore.Configuration`命名空间针对`IConfigurationBuilder`。

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

现在的 ASP.NET Core 服务可以访问 Service Fabric 配置设置，就像任何其他应用程序设置。 例如，可以使用选项模式来加载到强类型化对象的设置。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>默认密钥映射
默认情况下，Service Fabric 配置提供程序包括包名称、 部分名称和属性名称。 它们共同构成 ASP.NET Core 配置项，按如下所示：
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

例如，如果你拥有名为配置包`MyConfigPackage`包含以下内容，然后配置值可在 ASP.NET Core`IConfiguration`通过*MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Service Fabric 配置选项
Service Fabric 配置提供程序还支持`ServiceFabricConfigurationOptions`若要更改键映射的默认行为。

#### <a name="encrypted-settings"></a>加密的设置
Service Fabric 配置提供程序一样，Service Fabric 支持加密的设置。 加密的设置不会解密到 ASP.NET Core`IConfiguration`默认情况下。 加密的值存储中而存在。 但如果你想要解密要在 ASP.NET Core IConfiguration 中存储的值，则可以设置*DecryptValue*标志设为 false 在`AddServiceFabricConfiguration`扩展，按如下所示：

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = true); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>多个配置包
Service Fabric 支持多个配置包。 默认情况下，包名称包含在配置项。 但您可以设置`IncludePackageName`标志为 false，按如下所示：
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>自定义密钥映射、 值提取和数据填充
Service Fabric 配置提供程序还支持更高级的方案以自定义的键映射`ExtractKeyFunc`和自定义提取的值与`ExtractValueFunc`。 您甚至可以更改通过使用填充来自 Service Fabric 配置 ASP.NET Core 配置的数据的整个过程`ConfigAction`。

以下示例说明了如何使用`ConfigAction`自定义数据填充：
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>配置更新
Service Fabric 配置提供程序还支持配置更新。 可以使用 ASP.NET Core`IOptionsMonitor`接收更改通知，然后使用`IOptionsSnapshot`重新加载配置数据。 有关详细信息，请参阅[ASP.NET Core 选项](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)。

默认情况下支持这些选项。 需要无需再编写代码以使配置更新。

## <a name="scenarios-and-configurations"></a>方案和配置
本部分提供 web 服务器、 端口配置、 Service Fabric 集成选项和我们建议对以下方案进行故障排除的其他设置的组合：
 - 外部公开的 ASP.NET Core 无状态服务
 - 仅限内部的 ASP.NET Core 无状态服务
 - 仅限内部的 ASP.NET Core 有状态服务

**外部公开的服务**是一个公开从通常通过负载均衡器在群集外部调用的终结点。

**仅限内部的**服务是一个其调用终结点是仅从群集内。

> [!NOTE]
> 有状态服务终结点通常不应公开到 internet。 无法识别 Service Fabric 服务解析，如 Azure 负载均衡器的负载均衡器后面的群集将无法公开有状态服务。 这是因为负载均衡器将无法找到并将流量路由到相应有状态服务副本。 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>外部公开的 ASP.NET Core 无状态服务
Kestrel 是公开外部、 面向 internet 的 HTTP 终结点的前端服务的建议的 web 服务器。 在 Windows 中，HTTP.sys 可以提供端口共享功能，可用于承载多个节点使用相同的端口在同一组上的 web 服务。 在此方案中，web 服务区分按主机名称或路径，而无需依赖前端代理或网关来提供 HTTP 路由。
 
向 internet 公开时，无状态服务应使用的已知稳定终结点可通过负载均衡器访问的。 你将向应用程序的用户提供此 URL。 建议使用以下配置：

|  |  | **说明** |
| --- | --- | --- |
| Web 服务器 | Kestrel | Kestrel 是首选的 web 服务器，因为它跨 Windows 和 Linux 受支持。 |
| 端口配置 | 静态 | 应在 ServiceManifest.xml 的 `Endpoints` 配置中配置已知静态端口，例如为 HTTP 配置 80 或为 HTTPS 配置 443。 |
| ServiceFabricIntegrationOptions | 无 | 使用`ServiceFabricIntegrationOptions.None`选项时配置 Service Fabric 集成中间件，以确保该服务不会尝试验证入站请求的唯一标识符。 应用程序的外部用户不会知道中间件使用的唯一标识信息。 |
| 实例计数 | -1 | 在典型的使用情况下，实例计数设置应设置为*为-1*。 这样，以便可从负载均衡器接收流量的所有节点上的实例。 |

如果多个外部公开的服务共享相同的节点集，可以使用唯一但稳定的 URL 路径中使用 HTTP.sys。 可以修改配置 IWebHost 时提供的 URL 来完成此操作。 请注意，这仅适用于 HTTP.sys。

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>仅限内部的无状态 ASP.NET Core 服务
仅从群集内部调用的无状态服务应使用唯一的 URL 和动态分配的端口，以确保多个服务之间的协作正常进行。 建议使用以下配置：

|  |  | **说明** |
| --- | --- | --- |
| Web 服务器 | Kestrel | 尽管对于内部无状态服务，可以使用 HTTP.sys，Kestrel 将是最佳的服务器，以允许多个服务实例能够共享主机。  |
| 端口配置 | 动态分配 | 有状态服务的多个副本可能会共享主机进程或主机操作系统，因此需要唯一的端口。 |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 通过动态端口分配，此设置可以防止前面所述的错误标识问题。 |
| InstanceCount | 任意 | 可根据操作服务的需要将实例计数设置设置为任何值。 |

### <a name="internal-only-stateful-aspnet-core-service"></a>仅限内部的有状态 ASP.NET Core 服务
仅从群集内部调用的有状态服务应使用动态分配的端口，以确保多个服务之间的协作正常进行。 建议使用以下配置：

|  |  | **说明** |
| --- | --- | --- |
| Web 服务器 | Kestrel | `HttpSysCommunicationListener`不能用于有状态服务副本在其中共享主机进程。 |
| 端口配置 | 动态分配 | 有状态服务的多个副本可能会共享主机进程或主机操作系统，因此需要唯一的端口。 |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 通过动态端口分配，此设置可以防止前面所述的错误标识问题。 |

## <a name="next-steps"></a>后续步骤
[使用 Visual Studio 调试 Service Fabric 应用程序](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
