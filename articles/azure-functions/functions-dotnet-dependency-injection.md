---
title: 在 .NET 中使用依赖关系注入 Azure Functions
description: 了解如何在 .NET 函数中使用依赖关系注入来注册和使用服务
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: df2acedd7f472b96d55d9ecc294d47e7173c5f90
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78329010"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>在 .NET 中使用依赖关系注入 Azure Functions

Azure Functions 支持依赖关系注入（DI）软件设计模式，这是在类及其依赖项之间实现[控制反转（IoC）](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)的一种技术。

- Azure Functions 中的依赖关系注入基于 .NET Core 依赖项注入功能构建。 建议熟悉[.Net Core 依赖项注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)。 但是，在使用消耗计划时，如何覆盖依赖项以及如何使用 Azure Functions 读取配置值。

- 对于依赖关系注入的支持从 Azure Functions 1.x 开始。

## <a name="prerequisites"></a>先决条件

使用依赖关系注入之前，必须安装以下 NuGet 包：

- [Microsoft。](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- 1\.0.28[包](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)版本或更高版本

## <a name="register-services"></a>注册服务

若要注册服务，请创建方法以配置组件并将其添加到 `IFunctionsHostBuilder` 的实例。  Azure Functions 主机创建 `IFunctionsHostBuilder` 的实例，并将其直接传递到方法中。

若要注册方法，请添加指定在启动过程中使用的类型名称的 `FunctionsStartup` 程序集特性。

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>注意事项

一系列注册步骤在运行时处理 startup 类之前和之后运行。 因此，请记住以下事项：

- *Startup 类仅用于安装和注册。* 避免在启动过程中使用在启动时注册的服务。 例如，不要尝试在启动期间注册的记录器中记录消息。 注册过程的这一时间点太早，你的服务将无法使用。 运行 `Configure` 方法后，函数运行时将继续注册其他依赖项，这可能会影响服务的运行方式。

- *依赖关系注入容器只保存显式注册类型*。 唯一可用作 injectable 类型的服务是 `Configure` 方法中的设置。 因此，在安装过程中或 injectable 类型时，函数特定类型（如 `BindingContext` 和 `ExecutionContext`）将不可用。

## <a name="use-injected-dependencies"></a>使用注入的依赖项

构造函数注入用于使您的依赖项在函数中可用。 使用构造函数注入要求您不要使用静态类。

下面的示例演示如何将 `IMyService` 和 `HttpClient` 依赖项注入到 HTTP 触发的函数中。 此示例使用在启动时注册 `HttpClient` 所需的[Microsoft extension. Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/)包。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## <a name="service-lifetimes"></a>服务生存期

Azure Functions 应用提供与[ASP.NET 依赖关系注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)相同的服务生存期。 对于函数应用，不同服务生存期的行为如下所示：

- **暂时性**：在每次服务请求时创建暂时性服务。
- **作用域**：作用域内服务生存期与函数执行生存期匹配。 作用域内服务每次执行创建一次。 以后请求该服务时，将重新使用现有的服务实例。
- **Singleton**：单一实例服务生存期与主机生存期匹配，并在该实例上的函数执行之间重复使用。 建议为连接和客户端使用单一实例生存期服务，例如 `SqlConnection` 或 `HttpClient` 实例。

查看或下载 GitHub 上[不同服务生存期的示例](https://aka.ms/functions/di-sample)。

## <a name="logging-services"></a>日志记录服务

如果需要自己的日志记录提供程序，请将自定义类型注册为 `ILoggerProvider` 实例。 Azure Functions 自动添加 Application Insights。

> [!WARNING]
> - 不要将 `AddApplicationInsightsTelemetry()` 添加到服务集合，因为它注册的服务与环境提供的服务发生冲突。
> - 如果使用内置的 Application Insights 功能，请勿注册您自己的 `TelemetryConfiguration` 或 `TelemetryClient`。 如果需要配置自己的 `TelemetryClient` 实例，请通过插入的 `TelemetryConfiguration` 创建一个实例，如[监视器 Azure Functions](./functions-monitoring.md#version-2x-and-later-2)中所示。

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> 和 ILoggerFactory

宿主会将 `ILogger<T>` 和 `ILoggerFactory` 服务注入构造函数中。  但是，默认情况下，将筛选出函数日志中的新的日志记录筛选器。  你将需要修改 `host.json` 文件，以选择加入其他筛选器和类别。  下面的示例演示如何添加包含将由主机公开的日志 `ILogger<HttpTrigger>`。

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

和一个添加日志筛选器的 `host.json` 文件。

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingExcludedTypes": "Request",
            "samplingSettings": {
                "isEnabled": true
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

## <a name="function-app-provided-services"></a>函数应用提供的服务

函数主机将注册多个服务。 以下服务可作为依赖项在应用程序中安全执行：

|服务类型|生存期|说明|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|实体|运行时配置|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|实体|负责提供主机实例的 ID|

如果有其他服务要依赖于其他服务，请[在 GitHub 上创建一个问题并对其进行建议](https://github.com/azure/azure-functions-host)。

### <a name="overriding-host-services"></a>重写主机服务

当前不支持替代主机提供的服务。  如果有想要替代的服务，请[在 GitHub 上创建一个问题并对其进行建议](https://github.com/azure/azure-functions-host)。

## <a name="working-with-options-and-settings"></a>使用选项和设置

在 "[应用设置](./functions-how-to-use-azure-function-app-settings.md#settings)" 中定义的值可用于 `IConfiguration` 实例，这允许你读取 startup 类中的应用设置值。

可以从 `IConfiguration` 实例将值提取到自定义类型中。 如果将应用设置值复制到自定义类型，则通过使这些值 injectable，可以轻松地测试你的服务。 读入配置实例的设置必须是简单的键/值对。

请考虑下面的类，该类包含一个名为与应用程序设置一致的属性：

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

还可以按如下所示构造自定义设置的 `local.settings.json` 文件：
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

从 `Startup.Configure` 方法中，可以使用以下代码将 `IConfiguration` 实例中的值提取到自定义类型中：

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

调用 `Bind` 会将具有匹配的属性名称的值从配置复制到自定义实例中。 在 IoC 容器中现在可以使用 options 实例插入函数。

选项对象将作为泛型 `IOptions` 接口的实例注入到函数中。 使用 `Value` 属性访问在配置中找到的值。

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

有关使用选项的更多详细信息，请参阅[ASP.NET Core 中的选项模式](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)。

> [!WARNING]
> 避免尝试读取文件中的值，*如* *appsettings。环境} json* 。 从与触发器连接相关的这些文件中读取的值在应用规模上不可用，因为宿主基础结构不能访问配置信息。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

- [如何监视函数应用](functions-monitoring.md)
- [函数的最佳实践](functions-best-practices.md)
