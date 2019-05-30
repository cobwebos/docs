---
title: 在.NET Azure Functions 中使用依赖关系注入
description: 了解如何注册和使用服务在.NET 函数中使用依赖关系注入
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: azure functions, 函数, 无服务器体系结构
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/28/2019
ms.author: jehollan, glenga, cshoe
ms.openlocfilehash: 2f2e3db47bbd02ed0351033a694aa826e0e3e9f2
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66396707"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>在.NET Azure Functions 中使用依赖关系注入

Azure Functions 支持依赖关系注入 (DI) 软件设计模式，这种方法来实现[控制反转 (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)类和其依赖项之间。

ASP.NET Core 依赖关系注入功能的基础上构建 azure 函数。 了解服务、 生命周期和的设计模式[ASP.NET Core 依赖关系注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)在使用 Azure Functions 中的 DI 功能之前应用建议。

## <a name="prerequisites"></a>必备组件

可以使用依赖关系注入之前，必须安装以下 NuGet 包：

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions 包](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)1.0.28 版本或更高版本

## <a name="register-services"></a>注册服务

若要注册服务，可以创建一个方法来配置并将组件添加到`IFunctionsHostBuilder`实例。  Azure Functions 主机创建的实例`IFunctionsHostBuilder`并将其直接传入你的方法。

若要注册的方法，将添加`FunctionsStartup`在启动期间使用指定的类型名称的程序集属性。

```csharp
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

## <a name="use-injected-dependencies"></a>使用注入依赖关系

ASP.NET Core 使用构造函数注入到你的函数提供将依赖项。 下面的示例演示如何`IMyService`和`HttpClient`依赖关系注入到 HTTP 触发的函数。

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, HttpClient client)
        {
            _service = service;
            _client = client;
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

构造函数注入的使用意味着，如果你想要充分利用依赖关系注入，应使用静态函数。

## <a name="service-lifetimes"></a>服务生存期

提供的 azure 函数应用中为相同的服务生存期[ASP.NET 依赖关系注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)： 暂时、 作用域，和单一实例。

在函数应用中，作用域内的服务生存期匹配函数执行生存期。 每次执行一次创建作用域的服务。 对该服务在执行过程的更高版本请求重复使用现有的服务实例。 单一实例服务生存期匹配主机生存期，并在该实例上的函数执行重复使用。

单例生存期服务建议使用连接和客户端，例如`SqlConnection`， `CloudBlobClient`，或`HttpClient`实例。

查看或下载[示例的不同服务生存期](https://aka.ms/functions/di-sample)GitHub 上。

## <a name="logging-services"></a>日志记录服务

如果您需要自己的日志记录提供程序，建议的方法是注册`ILoggerProvider`实例。 Application Insights 会自动添加 Azure functions。

> [!WARNING]
> 请不要添加`AddApplicationInsightsTelemetry()`寄存器服务冲突的服务集合，与由环境提供的服务。

## <a name="function-app-provided-services"></a>函数提供应用服务

函数主机注册许多服务。 以下服务是安全地将作为你的应用程序中的依赖项：

|服务类型|生存期|描述|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|单一实例|运行时配置|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|单一实例|负责提供的主机实例的 ID|

如果你想要创建一个依赖项，其他服务[创建一个问题和提出这些建议在 GitHub 上](https://github.com/azure/azure-functions-host)。

### <a name="overriding-host-services"></a>重写主机服务

目前不支持重写由主机提供服务。  如果你想要重写，服务[创建一个问题和提出这些建议在 GitHub 上](https://github.com/azure/azure-functions-host)。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

- [如何监视 function app](functions-monitoring.md)
- [Functions 最佳做法](functions-best-practices.md)
