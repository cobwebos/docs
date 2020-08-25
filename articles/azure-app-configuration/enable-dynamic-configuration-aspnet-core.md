---
title: 教程：在 ASP.NET Core 中使用应用程序配置动态配置
titleSuffix: Azure App Configuration
description: 本教程介绍如何动态更新 ASP.NET Core 应用的配置数据
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 217c564a6bdb340ec15262c1eaf54a75bbffc833
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585009"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>教程：在 ASP.NET Core 应用中使用动态配置

ASP.NET Core 有可插拔的配置系统，可以从各种源读取配置数据。 它可以动态处理更改，而不会导致应用程序重启。 ASP.NET Core 支持将配置设置绑定到强类型 .NET 类。 它通过使用各种 `IOptions<T>` 模式将其注入到代码中。 其中一种模式（特别是 `IOptionsSnapshot<T>`）会在基础数据发生更改时自动重载应用程序的配置。 可将 `IOptionsSnapshot<T>` 注入应用程序的控制器，以访问 Azure 应用配置中存储的最新配置。

此外，还可以设置应用配置 ASP.NET Core 客户端库，以使用中间件动态刷新一组配置设置。 每次只要 Web 应用收到请求，配置设置就会使用配置存储进行更新。

Azure 应用程序配置会自动缓存每项设置，避免过多调用配置存储。 刷新操作会等待，直到某项设置的已缓存值过期才更新该设置，即使其值在配置存储中发生了更改。 默认缓存过期时间为 30 秒。 可以根据需要覆盖此过期时间。

本教程演示如何在代码中实现动态配置更新。 它建立在快速入门中介绍的 Web 应用之上。 在继续操作之前，请先完成[使用应用程序配置创建 ASP.NET Core 应用](./quickstart-aspnet-core-app.md)。

你可以使用任何代码编辑器执行本教程中的步骤。 [Visual Studio Code](https://code.visualstudio.com/) 是 Windows、macOS 和 Linux 平台上提供的一个卓越选项。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 设置应用程序，使其能够更新配置以响应应用程序配置存储区中的更改。
> * 在应用程序的控制器中注入最新配置。

## <a name="prerequisites"></a>先决条件

若要完成本教程，请安装 [.NET Core SDK](https://dotnet.microsoft.com/download)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

在继续操作之前，请先完成[使用应用程序配置创建 ASP.NET Core 应用](./quickstart-aspnet-core-app.md)。

## <a name="add-a-sentinel-key"></a>添加 sentinel 键

sentinel 键  是用于在配置更改时发出信号的特殊键。 应用会监视 sentinel 键以了解更改情况。 检测到更改时，刷新所有配置值。 与监视所有键以了解更改情况相比，这种方法可减少应用对 Azure 应用程序配置发出的请求的总数。

1. 在 Azure 门户中，选择“配置资源管理器”>“创建”>“键-值”  。

1. 输入 *TestApp:Settings:Sentinel* 作为“键”。   输入 1 作为“值”。 将“标签”和“内容类型”留空   。

1. 选择“应用”。 

    > [!NOTE]
    > 如果不使用 sentinel 密钥，则需手动注册要监视的每个密钥。

## <a name="reload-data-from-app-configuration"></a>从应用配置重载数据

1. 通过运行以下命令，添加对 `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet 包的引用：

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. 打开 *Program.cs*，并更新 `CreateWebHostBuilder` 方法以添加 `config.AddAzureAppConfiguration()` 方法。

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    ---

    `ConfigureRefresh` 方法用于指定在刷新操作触发时通过应用程序配置存储区更新配置数据所用的设置。 `Register` 方法的 `refreshAll` 参数指示在 sentinel 键更改时应刷新所有配置值。

    此外，`SetCacheExpiration` 方法会重写默认的缓存过期时间（30 秒），改为指定一个 5 分钟的时间。 这会减少对 Azure 应用程序配置发出的请求数。

    > [!NOTE]
    > 出于测试目的，可能需要缩短缓存的过期时间。

    为了实际触发刷新操作，需要配置刷新中间件，使应用程序在发生任何更改时刷新配置数据。 稍后会介绍如何执行此操作。

2. 添加 Settings.cs 文件，用于定义和实现新的 `Settings` 类  。

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. 打开 Startup.cs  ，然后在 `ConfigureServices` 方法中使用 `IServiceCollection.Configure<T>` 将配置数据绑定到 `Settings` 类。

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
    }
    ```
    ---

    > [!TIP]
    > 要了解有关读取配置值时选项模式的详细信息，请参阅  [ASP.NET Core 中的选项模式](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options?view=aspnetcore-3.1)。

4. 更新 `Configure` 方法以添加 `UseAzureAppConfiguration` 中间件，从而允许在 ASP.NET Core Web 应用继续接收请求的同时，更新已为刷新操作注册的配置设置。


    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    ---
    
    该中间件使用 `Program.cs` 的 `AddAzureAppConfiguration` 方法中指定的刷新配置，以针对 ASP.NET Core Web 应用收到的每个请求触发刷新。 对于每个请求，均会触发刷新操作，并且客户端库会检查已注册的配置设置的缓存值是否过期。 如果它已过期，则会刷新它。

## <a name="use-the-latest-configuration-data"></a>使用最新的配置数据

1. 打开 Controllers 目录中的 *HomeController.cs*，并添加对 `Microsoft.Extensions.Options` 包的引用。

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. 更新 `HomeController` 类，通过依赖项注入接收 `Settings` 并利用其值。

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    ---



3. 在“视图”>“主页”目录中打开 *Index.cshtml*，并将其内容替换为以下脚本：

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"]px;
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>在本地生成并运行应用

1. 要通过使用 .NET Core CLI 生成应用，请在命令行界面中执行以下命令：

```console
        dotnet build
```

1. 生成成功完成后，请运行以下命令以在本地运行 Web 应用：

```console
        dotnet run
```

1. 打开浏览器窗口，访问 `dotnet run` 输出中显示的 URL。

    ![在本地启动快速入门应用](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“所有资源”，然后选择在快速入门中创建的应用程序配置存储区实例  。

1. 选择“配置资源管理器”  并更新以下键的值：

    | 密钥 | 值 |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Azure 应用配置中的数据 - 现可实时更新！ |
    | TestApp:Settings:Sentinel | 2 |

1. 刷新浏览器页面，查看新的配置设置。 可能需要多次刷新才能反映所做的更改。

    ![在本地启动更新的快速入门应用](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你使得 ASP.NET Core Web 应用能够从应用程序配置中动态刷新配置设置。 若要了解如何使用 Azure 托管标识来简化对应用程序配置的访问，请继续学习下一篇教程。

> [!div class="nextstepaction"]
> [托管标识集成](./howto-integrate-azure-managed-service-identity.md)
