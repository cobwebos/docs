---
title: 在 .NET Azure Functions 中使用依赖项注入
description: 了解如何使用依赖项注入在 .NET 函数中注册和使用服务
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: azure functions, 函数, 无服务器体系结构
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: e1cf67abcc44a3ca134e5435137869d4fff1a7eb
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162361"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>在 .NET Azure Functions 中使用依赖项注入

Azure Functions 支持依赖项注入 (DI) 软件设计模式，这是在类及其依赖项之间实现[控制反转 (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的一种技术。

- Azure Functions 中的依赖关系注入基于 .NET Core 依赖项注入功能构建。 建议熟悉[.Net Core 依赖项注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)。 但是，在使用消耗计划时，如何覆盖依赖项以及如何使用 Azure Functions 读取配置值。

- 对依赖项注入的支持始于 Azure Functions 2.x。

## <a name="prerequisites"></a>先决条件

必须先安装以下 NuGet 包，然后才能使用依赖项注入：

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions 包](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)版本 1.0.28 或更高版本

## <a name="register-services"></a>注册服务

若要注册服务，请创建方法以配置组件并将其`IFunctionsHostBuilder`添加到实例。  Azure Functions 主机创建 `IFunctionsHostBuilder` 的实例并将其直接传递到你的方法。

若要注册方法，请添加 `FunctionsStartup` 程序集属性来指定在启动期间使用的类型名称。

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

- *Startup 类仅用于安装和注册。* 避免在启动过程中使用在启动时注册的服务。 例如，不要尝试在启动期间注册的记录器中记录消息。 注册过程的这一时间点太早，你的服务将无法使用。 运行该`Configure`方法后，函数运行时将继续注册其他依赖项，这可能会影响服务的运行方式。

- *依赖关系注入容器只保存显式注册类型*。 唯一可用作 injectable 类型的服务是在`Configure`方法中设置的。 因此，在安装过程中或作为 injectable `BindingContext`类型`ExecutionContext`时，函数特定类型（如和）将不可用。

## <a name="use-injected-dependencies"></a>使用注入的依赖项

构造函数注入用于使您的依赖项在函数中可用。 使用构造函数注入要求您不要使用静态类。

以下示例演示了 `IMyService` 和 `HttpClient` 依赖项是如何注入到 HTTP 触发的函数中的。 此示例使用在启动`HttpClient`时注册时所需的[Microsoft extension. Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/)包。

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

- **暂时性**：每次服务请求时都会创建暂时性服务。
- **作用域**：作用域内服务生存期与函数执行生存期匹配。 具有作用域的服务针对每次执行创建一次。 在执行期间针对该服务的后续请求将重用现有的服务实例。
- **单一实例**：单独服务生存期与主机生存期匹配，并在该实例上的函数执行之间重复使用。 建议为连接和客户端使用单一实例生存期服务， `SqlConnection`例如`HttpClient`或实例。

在 GitHub 上查看或下载[不同服务生存期的示例](https://aka.ms/functions/di-sample)。

## <a name="logging-services"></a>日志记录服务

如果需要自己的日志记录提供程序，请将自定义类型`ILoggerProvider`注册为实例。 Azure Functions 自动添加 Application Insights。

> [!WARNING]
> - 不要添加`AddApplicationInsightsTelemetry()`到服务集合, 因为它注册的服务与环境提供的服务发生冲突。
> - 如果你使用的是`TelemetryConfiguration`内置`TelemetryClient` Application Insights 功能，请不要注册自己的或。

## <a name="function-app-provided-services"></a>函数应用提供的服务

函数主机注册许多服务。 以下服务可以安全地用作应用程序中的依赖项：

|服务类型|生存期|描述|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|单一实例|运行时配置|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|单一实例|负责提供主机实例的 ID|

如果存在你要依赖的其他服务，请[在 GitHub 上创建一个问题并提出它们](https://github.com/azure/azure-functions-host)。

### <a name="overriding-host-services"></a>替代主机服务

目前不支持替代由主机提供的服务。  如果存在你要替代的服务，请[在 GitHub 上创建一个问题并提出它们](https://github.com/azure/azure-functions-host)。

## <a name="working-with-options-and-settings"></a>使用选项和设置

在[应用程序设置](./functions-how-to-use-azure-function-app-settings.md#settings)中定义的值可`IConfiguration`用于实例中，这使您能够读取 startup 类中的应用设置值。

可以将实例中的`IConfiguration`值提取到自定义类型中。 如果将应用设置值复制到自定义类型，则通过使这些值 injectable，可以轻松地测试你的服务。 请考虑下面的类，该类包含一个名为与应用程序设置一致的属性。

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

在方法中，可以使用以下代码将实例中`IConfiguration`的值提取到自定义类型中： `Startup.Configure`

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.Bind(settings);
                                           });
```

调用`Bind`将与配置中的属性名称匹配的值复制到自定义实例中。 在 IoC 容器中现在可以使用 options 实例插入函数。

选项对象将作为泛型`IOptions`接口的实例注入到函数中。 `Value`使用属性可访问在配置中找到的值。

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _service = service;
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
- [适用于函数的最佳做法](functions-best-practices.md)
