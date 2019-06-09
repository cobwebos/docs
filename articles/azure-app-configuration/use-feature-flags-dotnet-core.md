---
title: 有关在 .NET Core 应用中使用功能标志的教程 | Microsoft Docs
description: 本教程介绍如何在 .NET Core 应用中实施功能标志。
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: fc5215f71af45d3273da437fc796bf0d396ba3f9
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393514"
---
# <a name="tutorial-use-feature-flags-in-a-net-core-app"></a>教程：在 .NET Core 应用中使用功能标志

.NET Core 功能管理库能够顺畅地支持在 .NET 或 ASP.NET Core 应用程序中实施功能标志。 使用这些库能够以声明方式将功能标志添加到代码中，因此不需要手动编写这些功能标志的所有 `if` 语句。

功能管理库还会在幕后管理功能标志生命周期。 例如，这些库可刷新和缓存标志状态，或者保证标志状态在请求调用期间保持不变。 此外，ASP.NET Core 库提供现成的集成，包括 MVC 控制器操作、视图、路由和中间件。

[将功能标志添加到 ASP.NET Core 应用快速入门](./quickstart-feature-flag-aspnet-core.md)介绍了在 ASP.NET Core 应用程序中添加功能标志的多种方法。 本教程将更详细地介绍这些方法。 有关完整参考信息，请参阅 [ASP.NET Core 功能管理文档](https://go.microsoft.com/fwlink/?linkid=2091410)。

在本教程中，将了解如何：

> [!div class="checklist"]
> * 在应用程序的关键组件中添加功能标志，以控制功能可用性。
> * 使用应用程序配置来管理功能标志时，与应用程序配置相集成。

## <a name="set-up-feature-management"></a>设置功能管理

.NET Core 功能管理器 `IFeatureManager` 从框架的本机配置系统获取功能标志。 因此，可以使用 .NET Core 支持的任何配置源（包括本地 *appsettings.json* 文件或环境变量）来定义应用程序的功能标志。 `IFeatureManager` 依赖于 .NET Core 依赖项注入。 可以使用标准约定来注册功能管理服务。

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

默认情况下，功能管理器从 .NET Core 配置数据的 `"FeatureManagement"` 节检索功能标志。 以下示例告知功能管理器要从名为 `"MyFeatureFlags"` 的另一个节读取数据：

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

如果你在功能标志中使用筛选器，则需要包含并注册一个额外的库。 以下示例演示如何使用名为 `PercentageFilter` 的内置功能筛选器：

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

我们建议将功能标志保留在应用程序的外部，并单独对其进行管理。 这样便可以随时修改标志状态，并使这些更改在应用程序中立即生效。 应用程序配置提供一个中心位置用于通过专用门户 UI 来组织和控制所有功能标志。 应用程序配置还直接通过其 .NET Core 客户端库将标志传送到应用程序。

将 ASP.NET Core 应用程序连接到应用程序配置的最简单方法是使用配置提供程序 `Microsoft.Extensions.Configuration.AzureAppConfiguration`。 若要使用此 NuGet 包，请将以下代码添加到 *Program.cs* 文件：

```csharp
using Microsoft.Extensions.Configuration.AzureAppConfiguration;

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
           .ConfigureAppConfiguration((hostingContext, config) => {
               var settings = config.Build();
               config.AddAzureAppConfiguration(options => {
                   options.Connect(settings["ConnectionStrings:AppConfig"])
                          .UseFeatureFlags();
                });
           })
           .UseStartup<Startup>();
```

功能标志值预期会不断变化。 默认情况下，功能管理器每隔 30 秒刷新功能标志值。 以下代码演示如何在 `options.UseFeatureFlags()` 调用中将轮询间隔更改为 5 秒：

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.PollInterval = TimeSpan.FromSeconds(5);
           });
});
```

## <a name="feature-flag-declaration"></a>功能标志声明

每个功能标志有两个组成部分：一个名称，以及用于评估功能状态是否为“打开”（即，其值为 `True`）的一个或多个筛选器的列表。  筛选器定义有关何时应启用某个功能的用例。

如果某个功能标志包含多个筛选器，则会按顺序遍历筛选器列表，直到其中一个筛选器确定应启用该功能。 此时，该功能标志为“打开”，并会跳过所有剩余的筛选器结果。  如果没有任何筛选器指示应启用该功能，则表示该功能标志为“关闭”。 

功能管理器支持将 *appsettings.json* 用作功能标志的配置源。 以下示例演示如何在 JSON 文件中设置功能标志：

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

按照约定，此 JSON 文档的 `FeatureManagement` 节用于功能标志设置。 以上示例演示了三个功能标志，其筛选器已在 `EnabledFor` 属性中定义：

* `FeatureA` 状态为“打开”。 
* `FeatureB` 状态为“关闭”。 
* `FeatureC` 指定包含 `Parameters` 属性的名为 `Percentage` 的筛选器。 `Percentage` 是可配置的筛选器。 在此示例中，`Percentage` 指定打开 `FeatureC` 标志的概率为 50%。 

## <a name="feature-flag-references"></a>功能标志引用

若要在代码中轻松引用功能标志，应将其定义为 `enum` 变量：

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>功能标志检查

功能管理的基本模式是首先检查功能标志是否设置为“打开”。  如果是，则功能管理器将运行该功能包含的操作。 例如：

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>依赖项注入

在 ASP.NET Core MVC 中，可以通过依赖项注入访问功能管理器 `IFeatureManager`。

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>控制器操作

在 MVC 控制器中，使用 `Feature` 属性控制是要启用整个控制器类还是特定的操作。 以下 `HomeController` 控制器要求 `FeatureA` 状态为“打开”，才能执行控制器类包含的任何操作： 

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

以下 `Index` 操作要求 `FeatureA` 状态为“打开”才能运行该操作： 

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

当由于控制功能标志状态为“关闭”而阻止了 MVC 控制器或操作时，将调用已注册的 `IDisabledFeaturesHandler`接口。  默认的 `IDisabledFeaturesHandler` 接口向客户端返回 404 状态代码，但不返回响应正文。

## <a name="mvc-views"></a>MVC 视图

在 MVC 视图中，可以使用 `<feature>` 标记根据是否启用了某个功能标志来呈现内容：

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC 筛选器

可以设置 MVC 筛选器，以根据功能标志的状态激活这些筛选器。 以下代码添加名为 `SomeMvcFilter` 的 MVC 筛选器。 仅当已启用 `FeatureA` 时，才会在 MVC 管道内部触发此筛选器。

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="routes"></a>路由

可以使用功能标志来动态公开路由。 以下代码添加一个路由，仅当已启用 `FeatureA` 时，此路由才将 `Beta` 设置为默认控制器。

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>中间件

还可以使用功能标志按条件添加应用程序分支和中间件。 仅当已启用 `FeatureA` 时，以下代码才会在请求管道中插入中间件组件。

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

此代码生成更通用的功能来根据功能标志分支整个应用程序。

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>后续步骤

本教程已介绍如何使用 `Microsoft.FeatureManagement` 库在 ASP.NET Core 应用程序中实施功能标志。 有关 ASP.NET Core 和应用程序配置中的功能管理支持的详细信息，请参阅以下资源：

* [ASP.NET Core 功能标志示例代码](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Microsoft.FeatureManagement 文档](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [管理功能标志](./manage-feature-flags.md)
