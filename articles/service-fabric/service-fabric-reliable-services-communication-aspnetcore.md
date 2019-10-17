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
ms.openlocfilehash: 39e6273382133493a77321deed2baec4718bc912
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72383668"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>Azure Service Fabric 中的 ASP.NET Core Reliable Services

ASP.NET Core 是一种开放源和跨平台框架。 此框架设计用于构建基于云的连接 internet 的应用程序，如 web 应用、IoT 应用和移动后端。

本文详细介绍了如何使用 ServiceFabric 在 Service Fabric Reliable Services 中承载 ASP.NET Core 服务 **。** NuGet 包的集合。

有关如何在 Service Fabric 中 ASP.NET Core 的介绍性教程以及如何设置开发环境的说明，请参阅[教程：使用 ASP.NET Core WEB API 前端服务和有状态后端服务创建和部署应用程序](service-fabric-tutorial-create-dotnet-app.md)。

本文的其余部分假定你已熟悉 ASP.NET Core。 否则，请通读[ASP.NET Core 基础知识](https://docs.microsoft.com/aspnet/core/fundamentals/index)。

## <a name="aspnet-core-in-the-service-fabric-environment"></a>Service Fabric 环境中的 ASP.NET Core

ASP.NET Core 和 Service Fabric 应用可在 .NET Core 或完整 .NET Framework 上运行。 可以通过两种不同的方式使用 ASP.NET Core Service Fabric：
 - **作为来宾可执行文件托管**。 这种方法主要用于在没有代码更改的 Service Fabric 上运行现有 ASP.NET Core 应用程序。
 - **在可靠服务中运行**。 这样，就可以更好地与 Service Fabric 运行时集成，并允许有状态的 ASP.NET Core 服务。

本文的其余部分介绍了如何通过随 Service Fabric SDK 提供的 ASP.NET Core 集成组件，在可靠服务中使用 ASP.NET Core。

## <a name="service-fabric-service-hosting"></a>Service Fabric 服务托管

在 Service Fabric 中，服务的一个或多个实例和/或副本在*服务主机进程*中运行：运行服务代码的可执行文件。 作为服务创作者，您拥有服务主机进程，并 Service Fabric 为您激活和监视该进程。

传统的 ASP.NET（最高为 MVC 5）通过 System.Web.dll 与 IIS 紧密耦合。 ASP.NET Core 在 Web 服务器和 Web 应用程序之间提供分隔。 这种分离使 web 应用程序可以在不同的 web 服务器之间移植。 它还允许 web 服务器*自托管*。 这意味着，你可以在自己的进程中启动 web 服务器，而不是由专用 web 服务器软件（如 IIS）拥有的进程。

若要将 Service Fabric 服务和 ASP.NET 合并为来宾可执行文件或可靠服务，必须能够在服务主机进程内启动 ASP.NET。 可借助 ASP.NET Core 的自托管功能执行此操作。

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>在可靠服务中托管 ASP.NET Core
通常情况下，自托管 ASP.NET Core 应用程序会在应用程序的入口点创建 WebHost，如 `Program.cs` 中的 `static void Main()` 方法。 在这种情况下，WebHost 的生命周期绑定到进程的生命周期。

![在进程中托管 ASP.NET Core][0]

但是，应用程序入口点并不是在可靠服务中创建 WebHost 的正确位置。 这是因为应用程序入口点仅用于向 Service Fabric 运行时注册服务类型，以便它可以创建该服务类型的实例。 WebHost 应在可靠服务中创建。 在服务主机进程中，服务实例和/或副本可以经历多个生命周期。 

Reliable Service 实例由派生自 `StatelessService` 或 `StatefulService` 的服务类表示。 服务的通信堆栈包含在服务类中的 `ICommunicationListener` 实现内。 @No__t-0 NuGet 包包含 @no__t 的实现，这些实现在可靠服务中启动和管理 Kestrel 或 http.sys 的 ASP.NET Core WebHost。

![用于在可靠服务中托管 ASP.NET Core 的关系图][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
@No__t NuGet 包中 Kestrel 和 http.sys 的 @no__t 实现具有类似的使用模式。 但它们执行特定于每个 web 服务器的操作稍有不同。 

这两种通信侦听器都能提供采用以下参数的构造函数：
 - **`ServiceContext serviceContext`** ：这是包含有关正在运行的服务的信息的 @no__t 2 对象。
 - **`string endpointName`** ：这是 servicemanifest.xml 中的 @no__t 配置的名称。 这主要是两种通信侦听器的不同之处。 HTTP.SYS 需要 `Endpoint` 配置，而 Kestrel 不*需要*。
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`** ：这是一个实现的 lambda，在其中创建并返回一个 @no__t。 它允许你以通常在 ASP.NET Core 应用程序中的方式配置 @no__t 0。 Lambda 提供一个为你生成的 URL，具体取决于你使用的 Service Fabric 集成选项和你提供的 @no__t 0 配置。 然后，你可以修改或使用该 URL 来启动 web 服务器。

## <a name="service-fabric-integration-middleware"></a>Service Fabric 集成中间件
@No__t-0 NuGet 包包含 `IWebHostBuilder` 上的 @no__t 1 扩展方法，用于添加 Service Fabric 感知中间件。 此中间件将 Kestrel @no__t 或 http.sys 配置为使用 Service Fabric 命名服务注册唯一的服务 URL。 然后，它将验证客户端请求，以确保客户端连接到正确的服务。 

若要防止客户端错误地连接到错误的服务，必须执行此步骤。 这是因为，在诸如 Service Fabric 的共享主机环境中，多个 web 应用程序可以在同一物理计算机或虚拟机上运行，但不要使用唯一的主机名。 后续部分将对此方案进行详细说明。

### <a name="a-case-of-mistaken-identity"></a>错误标识示例
服务副本（无论哪种协议）侦听唯一的 IP:port 组合。 服务副本开始侦听 IP：端口终结点后，它会将该终结点地址报告给 Service Fabric 命名服务。 在此，客户端或其他服务可以发现它。 如果服务使用动态分配的应用程序端口，服务副本可能恰巧在同一物理计算机或虚拟机上使用另一个服务的相同 IP：端口终结点。 这可能会导致客户端错误地连接到错误的服务。 如果发生以下事件顺序，则可能会出现这种情况：

 1. 服务 A 通过 HTTP 侦听 10.0.0.1:30000。 
 2. 客户端解析服务 A 并获取地址10.0.0.1：30000。
 3. 服务 A 移动到其他节点。
 4. 服务 B 放置在 10.0.0.1 并恰巧使用了同一端口 30000。
 5. 客户端尝试使用缓存地址 10.0.0.1:30000 连接到服务 A。
 6. 客户端现已成功连接到服务 B，但未意识到它已连接到错误的服务。

这可能导致在随机时间出现 bug，并且很难诊断。

### <a name="using-unique-service-urls"></a>使用唯一的服务 URL
若要防止这些错误，服务可以使用唯一标识符将终结点发布到命名服务，然后在客户端请求期间验证该唯一标识符。 这是非恶意租户受信任环境中的服务之间的协作操作。 它不会在恶意租户环境中提供安全的服务身份验证。

在受信任的环境中，由 `UseServiceFabricIntegration` 方法添加的中间件会自动在发布到命名服务的地址后面追加唯一标识符。 它将验证每个请求的标识符。 如果标识符不匹配，中间件会立即返回 HTTP 410 消失响应。

使用动态分配的端口的服务应使用此中间件。

使用固定唯一端口的服务在协作环境中不存在此问题。 固定唯一端口通常用于面向外部的服务，此类服务需要可供客户端应用程序连接到的已知端口。 例如，大多数面向 internet 的 web 应用程序将使用端口80或443进行 web 浏览器连接。 在这种情况下，不应启用唯一标识符。

下图显示了启用中间件时的请求流：

![Service Fabric ASP.NET Core 集成][2]

Kestrel 和 HTTP.SYS `ICommunicationListener` 实现使用这种机制的方式完全相同。 尽管 HTTP.SYS 可以在内部使用基础**http.sys**端口共享功能基于唯一的 URL 路径来区分请求，但 http.sys `ICommunicationListener` 实现*不*使用该功能。 这是因为它会导致前面所述的方案中出现 HTTP 503 和 HTTP 404 错误状态代码。 这进而使客户端很难确定错误的意图，因为 HTTP 503 和 HTTP 404 通常用于指示其他错误。 

因此，Kestrel 和 http.sys `ICommunicationListener` 实现在 `UseServiceFabricIntegration` 扩展方法提供的中间件上实现标准化。 因此，客户端只需对 HTTP 410 响应执行服务终结点重新解析操作。

## <a name="httpsys-in-reliable-services"></a>Reliable Services 中的 http.sys
可以通过导入**ServiceFabric AspNetCore HttpSys** NuGet 包在 Reliable Services 中使用 http.sys。 此包包含 `HttpSysCommunicationListener`，即 @no__t 的实现。 `HttpSysCommunicationListener` 允许使用 HTTP.SYS 作为 web 服务器，从而在可靠服务中创建一个 ASP.NET Core WebHost。

Http.sys 是在[WINDOWS HTTP 服务器 API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx)上构建的。 此 API 使用 http.sys**内核驱动**程序处理 http 请求，并将其路由到运行 web 应用程序的进程。 这允许同一物理计算机或虚拟机上的多个进程在同一端口上托管 web 应用程序，通过唯一 URL 路径或主机名来消除歧义。 Service Fabric 在同一群集中托管多个网站时，这些功能非常有用。

>[!NOTE]
>HTTP.SYS 实现仅适用于 Windows 平台。

下图说明了 HTTP.SYS 如何在 Windows 上使用**http.sys 内核驱动**程序进行端口共享：

![HTTP.SYS 关系图][3]

### <a name="httpsys-in-a-stateless-service"></a>无状态服务中的 http.sys
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

### <a name="httpsys-in-a-stateful-service"></a>有状态服务中的 http.sys

`HttpSysCommunicationListener` 目前不适用于有状态服务，因为它具有基础**http.sys**端口共享功能的复杂性。 有关详细信息，请参阅以下部分，了解如何通过 HTTP.SYS 动态分配动态端口。 对于有状态服务，Kestrel 是建议的 web 服务器。

### <a name="endpoint-configuration"></a>终结点配置

使用 Windows HTTP Server API 的 web 服务器（包括 HTTP.SYS）需要 `Endpoint` 配置。 使用 Windows HTTP Server API 的 Web 服务器必须首先使用 HTTP.SYS 保留其 URL （通常使用[netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx)工具实现）。 

此操作需要提升的权限，默认情况下服务不具备此权限。 Servicemanifest.xml 中的 @no__t 配置的 @no__t 的 "http" 或 "https" 选项专用于指示 Service Fabric 运行时代表你注册一个 URL，以代表你注册 URL。 使用[*强通配符*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx)URL 前缀即可实现此功能。

例如，若要为服务保留 `http://+:80`，请在 Servicemanifest.xml 中使用以下配置：

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

#### <a name="use-httpsys-with-a-static-port"></a>将 HTTP.SYS 用于静态端口
若要将静态端口与 http.sys 一起使用，请在 `Endpoint` 配置中提供端口号：

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>通过动态端口使用 http.sys
若要对 HTTP.SYS 使用动态分配的端口，请在 @no__t 配置中省略 `Port` 属性：

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

@No__t 配置分配的动态端口仅为*每个主机进程*提供一个端口。 当前 Service Fabric 承载模型允许在同一进程中托管多个服务实例和/或副本。 这意味着，在通过 `Endpoint` 配置分配时，每个端口共享同一个端口。 多个**http.sys**实例可以使用基础**http.sys**端口共享功能共享一个端口。 但 `HttpSysCommunicationListener` 不支持此方法，因为它会为客户端请求带来的复杂性。 对于动态端口使用，Kestrel 是建议的 web 服务器。

## <a name="kestrel-in-reliable-services"></a>Reliable Services 中的 Kestrel
可以通过导入**ServiceFabric AspNetCore Kestrel** NuGet 包来使用 Reliable Services 中的 Kestrel。 此包包含 `KestrelCommunicationListener`，即 @no__t 的实现。 `KestrelCommunicationListener` 使你可以通过将 Kestrel 用作 web 服务器，在可靠服务中创建一个 ASP.NET Core WebHost。

Kestrel 是一个用于 ASP.NET Core 的跨平台 web 服务器。 与 HTTP.SYS 不同，Kestrel 不使用集中式终结点管理器。 与 HTTP.SYS 不同，Kestrel 不支持在多个进程之间共享端口。 Kestrel 的每个实例必须使用唯一端口。 有关 Kestrel 的详细信息，请参阅[实现细节](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2)。

![Kestrel 图][4]

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

此示例中为 WebHost 依赖关系注入容器提供 `IReliableStateManager` 的单一实例。 这并不是必需的，但它允许在 MVC 控制器操作方法中使用 @no__t 0 和可靠集合。

有状态服务中不会为 `KestrelCommunicationListener` 提供`Endpoint` 配置名称。 后续部分会对此进行详细说明。

### <a name="configure-kestrel-to-use-https"></a>将 Kestrel 配置为使用 HTTPS
如果在服务中启用 Kestrel 的 HTTPS，则需要设置多个侦听选项。 更新 `ServiceInstanceListener` 以使用*EndpointHttps*终结点，并侦听特定端口（如端口443）。 将 web 主机配置为使用 Kestrel web 服务器时，必须将 Kestrel 配置为侦听所有网络接口上的 IPv6 地址： 

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

有关本教程中的完整示例，请参阅[将 Kestrel 配置为使用 HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https)。


### <a name="endpoint-configuration"></a>终结点配置
使用 Kestrel 不需要 `Endpoint` 配置。 

Kestrel 是简单的独立 web 服务器。 与 HTTP.SYS （或 HttpListener）不同，它不需要在 Servicemanifest.xml 中使用 @no__t 的配置，因为它在启动前不需要注册 URL。 

#### <a name="use-kestrel-with-a-static-port"></a>将 Kestrel 和静态端口配合使用
你可以 @no__t 在 Servicemanifest.xml 的配置中配置静态端口以用于 Kestrel。 尽管这不是必需的，但它提供了两个潜在好处：
 - 如果端口不在应用程序端口范围内，则会通过 Service Fabric 将其打开。
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

如果 Servicemanifest.xml 不使用 `Endpoint` 配置，请在 @no__t 的构造函数中省略该名称。 在这种情况下，它将使用动态端口。 有关此内容的详细信息，请参阅下一节。

#### <a name="use-kestrel-with-a-dynamic-port"></a>将 Kestrel 和动态端口配合使用
Kestrel 无法在 Servicemanifest.xml 中使用来自 `Endpoint` 配置的自动端口分配。 这是因为，从 @no__t 配置的自动端口分配会为每个*主机进程*分配唯一端口，并且单个主机进程可以包含多个 Kestrel 实例。 这不能用于 Kestrel，因为它不支持端口共享。 因此，每个 Kestrel 实例都必须在唯一端口上打开。

若要将动态端口分配用于 Kestrel，请完全省略 Servicemanifest.xml 中的 `Endpoint` 配置，并且不要将终结点名称传递到 @no__t 构造函数，如下所示：

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

在此配置中，`KestrelCommunicationListener` 会自动从应用程序端口范围中选择未使用的端口。

## <a name="service-fabric-configuration-provider"></a>Service Fabric 配置提供程序
ASP.NET Core 中的应用配置基于配置提供程序建立的键值对。 [ASP.NET Core 中](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/)的 "读取配置" 以了解有关常规 ASP.NET Core 配置支持的详细信息。

本部分介绍 Service Fabric 配置提供程序如何通过导入 `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet 包与 ASP.NET Core 配置集成。

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration 启动扩展
导入 `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet 包后，需要将 Service Fabric 配置源注册 ASP.NET Core 配置 API。 为此，请在 `Microsoft.ServiceFabric.AspNetCore.Configuration` 命名空间中检查 `IConfigurationBuilder` 的**AddServiceFabricConfiguration**扩展。

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

现在 ASP.NET Core 服务可以访问 Service Fabric 的配置设置，就像任何其他应用程序设置一样。 例如，可以使用选项模式将设置加载到强类型对象中。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>默认键映射
默认情况下，Service Fabric 配置提供程序包括包名称、节名称和属性名称。 它们共同构成了 ASP.NET Core 配置密钥，如下所示：
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

例如，如果你有一个名为 `MyConfigPackage` 的配置包，其中包含以下内容，则配置值将在 ASP.NET Core `IConfiguration` 到*MyConfigPackage： MyConfigSection： MyParameter*上可用。
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Service Fabric 配置选项
Service Fabric 配置提供程序还支持 @no__t 更改键映射的默认行为。

#### <a name="encrypted-settings"></a>加密设置
Service Fabric 支持加密设置，与 Service Fabric 配置提供程序一样。 默认情况下，加密的设置不会解密为 ASP.NET Core @no__t。 加密值存储在此处。 但是，如果要解密要存储在 ASP.NET Core IConfiguration 中的值，则可以在 @no__t 1 扩展中将*DecryptValue*标志设置为 false，如下所示：

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>多个配置包
Service Fabric 支持多个配置包。 默认情况下，包名称包含在配置项中。 但可以将 `IncludePackageName` 标志设置为 false，如下所示：
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
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>自定义键映射、值提取和数据填充
Service Fabric 配置提供程序还支持更高级的方案，以便通过 @no__t 自定义键映射，并在 `ExtractValueFunc` 的情况下提取值。 甚至可以使用 `ConfigAction` 将填充数据的整个过程从 Service Fabric 配置更改为 ASP.NET Core 配置。

下面的示例演示如何使用 `ConfigAction` 自定义数据填充：
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
Service Fabric 配置提供程序还支持配置更新。 你可以使用 ASP.NET Core @no__t 来接收更改通知，然后使用 `IOptionsSnapshot` 重新加载配置数据。 有关详细信息，请参阅[ASP.NET Core 选项](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)。

默认情况下支持这些选项。 不需要进一步的编码即可启用配置更新。

## <a name="scenarios-and-configurations"></a>方案和配置
本部分提供 web 服务器、端口配置、Service Fabric 集成选项和其他设置的组合，我们建议在以下情况下进行故障排除：
 - 外部公开的 ASP.NET Core 无状态服务
 - 仅限内部 ASP.NET Core 无状态服务
 - 仅限内部 ASP.NET Core 有状态服务

**外部公开的服务**是一种公开从群集外部调用的终结点（通常通过负载均衡器）的服务。

**仅限内部**的服务的终结点只能从群集内部调用。

> [!NOTE]
> 通常不应向 internet 公开有状态服务终结点。 不知道 Service Fabric 服务解析的负载均衡器（如 Azure 负载均衡器）后的群集将无法公开有状态服务。 这是因为负载均衡器无法找到流量并将其路由到相应有状态服务副本。 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>外部公开的 ASP.NET Core 无状态服务
Kestrel 是用于公开面向 internet 的外部 HTTP 终结点的前端服务的建议 web 服务器。 在 Windows 上，HTTP.SYS 可以提供端口共享功能，从而允许你使用相同的端口在同一组节点上托管多个 web 服务。 在此方案中，web 服务通过主机名或路径进行区分，而不依赖前端代理或网关提供 HTTP 路由。
 
当向 internet 公开时，无状态服务应使用可通过负载均衡器到达的已知稳定终结点。 你将向应用程序的用户提供此 URL。 建议使用以下配置：

|  |  | **说明** |
| --- | --- | --- |
| Web 服务器 | Kestrel | Kestrel 是首选 web 服务器，因为它是跨 Windows 和 Linux 的支持。 |
| 端口配置 | 静态 | 应在 ServiceManifest.xml 的 `Endpoints` 配置中配置已知静态端口，例如为 HTTP 配置 80 或为 HTTPS 配置 443。 |
| ServiceFabricIntegrationOptions | None | 配置 Service Fabric 集成中间件时，使用 `ServiceFabricIntegrationOptions.None` 选项，以便服务不会尝试验证对唯一标识符的传入请求。 应用程序的外部用户不会知道中间件使用的唯一标识信息。 |
| 实例数 | -1 | 在典型用例中，应将实例计数设置设置为 *-1*。 这样做是为了使实例在从负载均衡器接收流量的所有节点上可用。 |

如果多个外部公开的服务共享相同的节点集，则可以使用带有唯一但稳定的 URL 路径的 http.sys。 可以通过修改配置 IWebHost 时提供的 URL 来实现此目的。 请注意，这仅适用于 http.sys。

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
| Web 服务器 | Kestrel | 尽管可以使用 http.sys 来实现内部无状态服务，但 Kestrel 是允许多个服务实例共享主机的最佳服务器。  |
| 端口配置 | 动态分配 | 有状态服务的多个副本可能会共享主机进程或主机操作系统，因此将需要唯一端口。 |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 通过动态端口分配，此设置可以防止前面所述的错误标识问题。 |
| InstanceCount | 任意 | 可根据操作服务的需要将实例计数设置设置为任何值。 |

### <a name="internal-only-stateful-aspnet-core-service"></a>仅限内部的有状态 ASP.NET Core 服务
仅从群集内部调用的有状态服务应使用动态分配的端口，以确保多个服务之间的协作正常进行。 建议使用以下配置：

|  |  | **说明** |
| --- | --- | --- |
| Web 服务器 | Kestrel | @No__t 的设计目的不是由有状态服务使用，其中副本共享主机进程。 |
| 端口配置 | 动态分配 | 有状态服务的多个副本可能会共享主机进程或主机操作系统，因此将需要唯一端口。 |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 通过动态端口分配，此设置可以防止前面所述的错误标识问题。 |

## <a name="next-steps"></a>后续步骤
[使用 Visual Studio 调试 Service Fabric 应用程序](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
