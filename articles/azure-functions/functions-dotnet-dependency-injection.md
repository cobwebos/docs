---
title: 在 .NET Azure Functions 中使用依赖项注入
description: 了解如何在 .NET 函数中使用依赖项注入来注册和使用服务
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 97e8a34f3b8639990f8de736a8f1f7429ebfd448
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83739135"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>在 .NET Azure Functions 中使用依赖项注入

Azure Functions 支持依赖项注入 (DI) 软件设计模式，这是一种在类与其依赖项之间实现[控制反转 (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的方法。

- Azure Functions 中的依赖项注入基于 .NET Core 依赖项注入功能构建。 建议熟悉 [.NET Core 依赖项注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)。 代替依赖项的方式与使用 Azure Functions 对消耗计划读取配置值的方式之间存在差异。

- 从 Azure Functions 2.x 支持依赖项注入。

## <a name="prerequisites"></a>先决条件

使用依赖项注入之前，必须安装以下 NuGet 包：

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) 包版本 1.0.28 或更高版本

## <a name="register-services"></a>注册服务

若要注册服务，请创建方法以配置组件并将其添加到 `IFunctionsHostBuilder` 实例。  Azure Functions 主机会创建 `IFunctionsHostBuilder` 的实例，并将其直接传递到方法中。

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

在运行时处理启动类之前和之后会运行一系列注册步骤。 因此，请记住以下事项：

- 启动类仅用于设置和注册。 避免在启动期间使用在启动时注册的服务。 例如，请勿尝试在启动期间注册的记录器中记录消息。 注册过程的这一时间点太早，你的服务尚无法使用。 运行 `Configure` 方法之后，Functions 运行时会继续注册其他依赖项，这可能会影响服务的运行方式。

- 依赖项注入容器只保存显式注册的类型。 唯一可用作可注入类型的服务是在 `Configure` 方法中设置的服务。 因此，特定于 Functions 的类型（如 `BindingContext` 和 `ExecutionContext`）在设置期间不可用，也不可用作可注入类型。

## <a name="use-injected-dependencies"></a>使用注入的依赖项

构造函数注入用于使依赖项在函数中可用。 使用构造函数注入要求不使用静态类。

下面的示例演示如何将 `IMyService` 和 `HttpClient` 依赖项注入到 HTTP 触发的函数中。 此示例使用在启动时注册 `HttpClient` 所需的 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) 包。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, HttpClient httpClient)
        {
            _service = service;
            _client = httpClient;
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

Azure Functions 应用提供与 [ASP.NET 依赖项注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)相同的服务生存期。 对于函数应用，不同服务生存期的行为如下所示：

- 暂时性：每次请求服务时都会创建暂时性服务。
- 作用域：作用域服务生存期与函数执行生存期匹配。 作用域服务在每次执行时创建一次。 在执行期间对该服务的后续请求会重复使用现有服务实例。
- 单一实例：单一实例服务生存期与主机生存期匹配，会在该实例上的函数执行之间重复使用。 建议为连接和客户端使用单一实例生存期服务，例如 `SqlConnection` 或 `HttpClient` 实例。

可在 GitHub 上查看或下载[不同服务生存期的示例](https://aka.ms/functions/di-sample)。

## <a name="logging-services"></a>日志记录服务

如果需要自己的日志记录提供程序，请将自定义类型注册为 `ILoggerProvider` 实例。 Azure Functions 会自动添加 Application Insights。

> [!WARNING]
> - 请勿将 `AddApplicationInsightsTelemetry()` 添加到服务集合，因为它注册的服务与环境提供的服务发生冲突。
> - 如果使用内置 Application Insights 功能，请勿注册自己的 `TelemetryConfiguration` 或 `TelemetryClient`。 如果需要配置自己的 `TelemetryClient` 实例，请通过插入的 `TelemetryConfiguration` 创建一个实例，如[监视 Azure Functions](./functions-monitoring.md#version-2x-and-later-2) 中所示。

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> 和 ILoggerFactory

主机会将 `ILogger<T>` 和 `ILoggerFactory` 服务注入构造函数中。  但是在默认情况下，会从函数日志中筛选出新日志记录筛选器。  需要修改 `host.json` 文件，以选择加入其他筛选器和类别。  下面的示例演示如何添加 `ILogger<HttpTrigger>`，其中具有将由主机公开的日志。

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

以及一个添加日志筛选器的 `host.json` 文件。

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

函数主机会注册许多服务。 以下服务可安全地在应用程序中用作依赖项：

|服务类型|生存期|说明|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|单一实例|运行时配置|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|单一实例|负责提供主机实例的 ID|

如果有其他要依赖的服务，[请在 GitHub上创建并提出问题](https://github.com/azure/azure-functions-host)。

### <a name="overriding-host-services"></a>替代主机服务

当前不支持替代主机提供的服务。  如果有要替代的服务，[请在 GitHub上创建并提出问题](https://github.com/azure/azure-functions-host)。

## <a name="working-with-options-and-settings"></a>使用选项和设置

[应用设置](./functions-how-to-use-azure-function-app-settings.md#settings)中定义的值在 `IConfiguration` 实例中可用，这使你可以在启动类中读取应用设置值。

可以从 `IConfiguration` 实例将值提取到自定义类型中。 如果将应用设置值复制到自定义类型，则通过使这些值可注入，可以轻松地测试服务。 读入到配置实例中的设置必须是简单的键/值对。

请考虑下面的类，它包含一个名称与应用设置一致的属性：

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

以及一个 `local.settings.json` 文件，它可以按如下所示构造自定义设置：
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

在 `Startup.Configure` 方法内，可以使用以下代码从 `IConfiguration` 实例将值提取到自定义类型中：

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

调用 `Bind` 会将具有匹配属性名称的值从配置复制到自定义实例中。 选项实例现在可以在 IoC 容器中注入到函数中。

选项对象会作为泛型 `IOptions` 接口的实例注入到函数中。 使用 `Value` 属性可访问在配置中找到的值。

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

有关使用选项的更多详细信息，请参阅 [ASP.NET Core 中的选项模式](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)。

> [!WARNING]
> 避免尝试从有关消耗计划的 local.settings.json 或 appsettings.{environment}.json 等文件中读取值。 从与触发器连接相关的这些文件中读取的值会在应用缩放时不可用，因为托管基础结构无法访问配置信息。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

- [如何监视函数应用](functions-monitoring.md)
- [函数的最佳做法](functions-best-practices.md)
