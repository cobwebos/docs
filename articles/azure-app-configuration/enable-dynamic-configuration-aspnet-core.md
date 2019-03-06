---
title: 关于在 ASP.NET Core 应用中使用 Azure 应用配置动态配置的教程 | Microsoft Docs
description: 本教程介绍如何动态更新 ASP.NET Core 应用的配置数据
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 44ae922b182874eef378d4868fb278c3c76252db
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884409"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>教程：在 ASP.NET Core 应用中使用动态配置

ASP.NET Core 拥有可插入配置系统，能够从多种来源读取配置数据并能动态处理更改，且不会造成应用程序重启。 它支持将配置设置绑定到强类型 NET 类并使用多种 `IOptions<T>` 模式将其注入代码。 其中一种模式，特别是 `IOptionsSnapshot<T>`，可在基础数据发生更改时，实现自动重载应用程序配置。 可将 `IOptionsSnapshot<T>` 注入应用程序的控制器，以访问 Azure 应用配置中存储的最新配置。 此外，可以设置应用配置 ASP.NET Core 客户端库，通过按定义的时间间隔进行轮询，持续监视和检索应用配置存储区中的任何更改。

本教程演示如何在代码中实现动态配置更新。 它建立在快速入门中介绍的 Web 应用之上。 请先完成[使用应用配置创建 ASP.NET Core 应用](./quickstart-aspnet-core-app.md)部分，然后再继续。

可使用任何代码编辑器来完成本快速入门中的步骤。 但是，[Visual Studio Code](https://code.visualstudio.com/) 是一个很好的选项，可用于 Windows、macOS 和 Linux 平台。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置应用程序，使其能够更新配置以响应应用配置存储区中的更改
> * 在应用程序的控制器中注入最新配置

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请安装 [.NET Core SDK](https://dotnet.microsoft.com/download)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>从应用配置重载数据

1. 打开 Program.cs，并通过添加 `config.AddAzureAppConfiguration()` 方法更新 `CreateWebHostBuilder` 方法。

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.Connect(settings["ConnectionStrings:AppConfig"])
                    .Watch("TestApp:Settings:BackgroundColor", TimeSpan.FromSeconds(1))
                    .Watch("TestApp:Settings:FontColor", TimeSpan.FromSeconds(1))
                    .Watch("TestApp:Settings:Message", TimeSpan.FromSeconds(1)));
            })
            .UseStartup<Startup>();
    ```

    `.Watch` 方法中的第二个参数表示轮询间隔，ASP.NET 客户端库按此间隔查询应用配置存储区，查看特定配置设置是否发生任何更改。

2. 添加 Settings.cs 文件，用于定义和实现新的 `Settings` 类。

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

3. 打开 Startup.cs 并更新 `ConfigureServices` 方法，从而将配置数据绑定到 `Settings` 类。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

## <a name="use-the-latest-configuration-data"></a>使用最新的配置数据

1. 打开“控制器”目录中的 HomeController.cs，更新 `HomeController` 类，通过依赖项注入接收 `Settings`，并利用其值。

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

2. 在“视图” > “主页”目录中打开 Index.cshtml，并将其内容替换如下：

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

1. 若要使用 .NET Core CLI 生成应用，请在命令行界面中执行以下命令：

        dotnet build

2. 成功完成应用生成后，执行以下命令，在本地运行 Web 应用：

        dotnet run

3. 启动浏览器窗口并导航到 `http://localhost:5000`，即本地托管的 Web 应用的默认 URL。

    ![本地启动应用快速入门](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. 登录到 [Azure 门户](https://aka.ms/azconfig/portal)，单击“所有资源”和快速入门中创建的应用配置存储区实例。

5. 单击“键/值资源管理器”并更新以下密钥值：

    | 密钥 | 值 |
    |---|---|
    | TestAppSettings:BackgroundColor | blue |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Azure 应用配置中的数据 - 现可实时更新！ |

6. 刷新浏览器页面，查看新的配置设置。

    ![本地刷新应用快速入门](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，通过添加 Azure 托管服务标识简化了应用配置访问并改进了应用凭据管理。 若要了解有关使用应用配置的详细信息，请继续学习 Azure CLI 示例。

> [!div class="nextstepaction"]
> [CLI 示例](./cli-samples.md)
