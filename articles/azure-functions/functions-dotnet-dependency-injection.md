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
ms.date: 05/28/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 1ebb2fd77830074648a580dddad98e05e10c9c75
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850020"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>在 .NET Azure Functions 中使用依赖项注入

Azure Functions 支持依赖项注入 (DI) 软件设计模式，这是在类及其依赖项之间实现[控制反转 (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的一种技术。

Azure Functions 基于 ASP.NET Core 依赖项注入功能。 在 Azure Functions 应用中使用 DI 功能之前，建议了解 [ASP.NET Core 依赖项注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)的服务、生存期和设计模式。

对于依赖关系注入的支持从 Azure Functions 1.x 开始。

## <a name="prerequisites"></a>先决条件

必须先安装以下 NuGet 包，然后才能使用依赖项注入：

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions 包](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)版本 1.0.28 或更高版本

- 可选：仅在启动时注册 HttpClient 所需的[Microsoft extension](https://www.nuget.org/packages/Microsoft.Extensions.Http/)

## <a name="register-services"></a>注册服务

若要注册服务，可以创建一个方法来配置组件并将组件添加到 `IFunctionsHostBuilder` 实例。  Azure Functions 主机创建 `IFunctionsHostBuilder` 的实例并将其直接传递到你的方法。

若要注册方法，请添加 `FunctionsStartup` 程序集属性来指定在启动期间使用的类型名称。 此外, 代码还在 Nuget 上引用[Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)的预发布版本。

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="use-injected-dependencies"></a>使用注入的依赖项

ASP.NET Core 使用构造函数注入来使依赖项可供你的函数使用。 以下示例演示了 `IMyService` 和 `HttpClient` 依赖项是如何注入到 HTTP 触发的函数中的。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();;
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

使用构造函数注入意味着，如果你希望利用依赖项注入，则不应当使用静态函数。

## <a name="service-lifetimes"></a>服务生存期

Azure Functions 应用提供与 [ASP.NET 依赖项注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)相同的服务生存期：暂时、具有作用域和单一实例。

在函数应用中，具有作用域的服务生存期与函数执行生存期相匹配。 具有作用域的服务针对每次执行创建一次。 在执行期间针对该服务的后续请求将重用现有的服务实例。 单一实例服务生存期与主机生存期相匹配，并且在该实例上的各个函数执行之间重用。

对于连接和客户端（例如 `SqlConnection`、`CloudBlobClient` 或 `HttpClient` 实例），建议使用单一实例生存期服务。

在 GitHub 上查看或下载[不同服务生存期的示例](https://aka.ms/functions/di-sample)。

## <a name="logging-services"></a>日志记录服务

如果需要自己的日志记录提供程序, 建议的方法是注册`ILoggerProvider`实例。 Azure Functions 自动添加 Application Insights。

> [!WARNING]
> 不要添加`AddApplicationInsightsTelemetry()`到服务集合, 因为它注册的服务与环境提供的服务发生冲突。

## <a name="function-app-provided-services"></a>函数应用提供的服务

函数主机注册许多服务。 以下服务可以安全地用作应用程序中的依赖项：

|服务类型|生存期|描述|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|单一实例|运行时配置|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|单一实例|负责提供主机实例的 ID|

如果存在你要依赖的其他服务，请[在 GitHub 上创建一个问题并提出它们](https://github.com/azure/azure-functions-host)。

### <a name="overriding-host-services"></a>替代主机服务

目前不支持替代由主机提供的服务。  如果存在你要替代的服务，请[在 GitHub 上创建一个问题并提出它们](https://github.com/azure/azure-functions-host)。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

- [如何监视函数应用](functions-monitoring.md)
- [适用于函数的最佳做法](functions-best-practices.md)
