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
ms.custom: mvc
ms.openlocfilehash: 8032a9b206be4a5eb70a1f40fd33667a1dbdfaa7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714729"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>教程：在 ASP.NET Core 应用中使用动态配置

ASP.NET Core 有可插拔的配置系统，可以从各种源读取配置数据。 它可以动态处理更改，而不会导致应用程序重启。 ASP.NET Core 支持将配置设置绑定到强类型 .NET 类。 它通过使用各种 `IOptions<T>` 模式将其注入到代码中。 其中一种模式（特别是 `IOptionsSnapshot<T>`）会在基础数据发生更改时自动重载应用程序的配置。 可将 `IOptionsSnapshot<T>` 注入应用程序的控制器，以访问 Azure 应用配置中存储的最新配置。

此外，还可以设置应用配置 ASP.NET Core 客户端库，以使用中间件动态刷新一组配置设置。 只要 Web 应用继续接收请求，配置设置就会继续使用配置存储区进行更新。

为了使设置保持更新并避免对配置存储区进行太多的调用，对每个设置使用了一个缓存。 在设置的缓存值过期前，刷新操作不会更新该值，即使该值在配置存储区中已发生更改。 每个请求的默认过期时间为 30 秒，但是，如果需要，可以重写该过期时间。

本教程演示如何在代码中实现动态配置更新。 它建立在快速入门中介绍的 Web 应用之上。 在继续操作之前，请先完成[使用应用程序配置创建 ASP.NET Core 应用](./quickstart-aspnet-core-app.md)。

你可以使用任何代码编辑器执行本教程中的步骤。 [Visual Studio Code](https://code.visualstudio.com/) 是 Windows、macOS 和 Linux 平台上提供的一个卓越选项。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 设置应用程序，使其能够更新配置以响应应用程序配置存储区中的更改。
> * 在应用程序的控制器中注入最新配置。

## <a name="prerequisites"></a>必备条件

若要完成本教程，请安装 [.NET Core SDK](https://dotnet.microsoft.com/download)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

在继续操作之前，请先完成[使用应用程序配置创建 ASP.NET Core 应用](./quickstart-aspnet-core-app.md)。

## <a name="reload-data-from-app-configuration"></a>从应用配置重载数据

1. 通过运行以下命令，添加对 `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet 包的引用：

    ```CLI
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 3.0.0-preview-011100002-1192
    ```

1. 打开 *Program.cs*，并更新 `CreateWebHostBuilder` 方法以添加 `config.AddAzureAppConfiguration()` 方法。

    #### <a name="net-core-2xtabcore2x"></a>[.NET Core 2.x](#tab/core2x)

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
                               refresh.Register("TestApp:Settings:BackgroundColor")
                                      .Register("TestApp:Settings:FontColor")
                                      .Register("TestApp:Settings:Message");
                           });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3xtabcore3x"></a>[.NET Core 3.x](#tab/core3x)

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
                                    refresh.Register("TestApp:Settings:BackgroundColor")
                                            .Register("TestApp:Settings:FontColor")
                                            .Register("TestApp:Settings:Message");
                                });
                    });
                })
            .UseStartup<Startup>());
    ```
    ---

    `ConfigureRefresh` 方法用于指定在刷新操作触发时通过应用程序配置存储区更新配置数据所用的设置。 为了实际触发刷新操作，需要对刷新中间件进行配置，以使应用程序在发生任何更改时刷新配置数据。

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

    #### <a name="net-core-2xtabcore2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

    #### <a name="net-core-3xtabcore3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
    }
    ```
    ---

4. 更新 `Configure` 方法以添加 `UseAzureAppConfiguration` 中间件，从而允许在 ASP.NET Core Web 应用继续接收请求的同时，更新已为刷新操作注册的配置设置。


    #### <a name="net-core-2xtabcore2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3xtabcore3x"></a>[.NET Core 3.x](#tab/core3x)

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
    
    该中间件使用 `Program.cs` 的 `AddAzureAppConfiguration` 方法中指定的刷新配置，以针对 ASP.NET Core Web 应用收到的每个请求触发刷新。 对于每个请求，均会触发刷新操作，并且客户端库会检查已注册的配置设置的缓存值是否过期。 对于已过期的缓存值，设置的值使用应用程序配置存储区进行更新，而其余的值保持不变。
    
    > [!NOTE]
    > 配置设置的默认缓存过期时间为 30 秒，但是，可以通过调用作为参数传递到 `ConfigureRefresh` 方法的选项初始值设定项上的 `SetCacheExpiration` 方法来重写该过期时间。

## <a name="use-the-latest-configuration-data"></a>使用最新的配置数据

1. 打开 Controllers 目录中的 *HomeController.cs*，并添加对 `Microsoft.Extensions.Options` 包的引用。

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. 更新 `HomeController` 类，通过依赖项注入接收 `Settings` 并利用其值。

    #### <a name="net-core-2xtabcore2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3xtabcore3x"></a>[.NET Core 3.x](#tab/core3x)

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
            font-size: @ViewData["FontSize"];
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

        dotnet build

2. 生成成功完成后，请运行以下命令以在本地运行 Web 应用：

        dotnet run

3. 启动浏览器窗口并转到 `http://localhost:5000`，即本地托管的 Web 应用的默认 URL。

    ![本地启动应用快速入门](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. 登录 [Azure 门户](https://portal.azure.com)。 选择“所有资源”，然后选择在快速入门中创建的应用程序配置存储区实例  。

5. 选择“配置资源管理器”  并更新以下键的值：

    | 密钥 | 值 |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Azure 应用配置中的数据 - 现可实时更新！ |

6. 刷新浏览器页面，查看新的配置设置。 可能需要对浏览器页进行多次刷新，才能使更改得到反映。

    ![本地刷新应用快速入门](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > 由于缓存配置设置的默认过期时间为 30 秒，因此，在缓存过期时，在应用程序配置存储区中对设置所做的任何更改都仅反映在 Web 应用中。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你使得 ASP.NET Core Web 应用能够从应用程序配置中动态刷新配置设置。 若要了解如何使用 Azure 托管标识来简化对应用程序配置的访问，请继续学习下一篇教程。

> [!div class="nextstepaction"]
> [托管标识集成](./howto-integrate-azure-managed-service-identity.md)
