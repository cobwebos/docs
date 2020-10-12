---
title: 将 Azure 应用配置与 ASP.NET Core 结合使用的快速入门| Microsoft Docs
description: 使用 Azure 应用程序配置创建 ASP.NET Core 应用，集中存储和管理 ASP.NET Core 应用程序的应用程序设置。
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp, contperfq1
ms.topic: quickstart
ms.date: 09/25/2020
ms.author: lcozzens
ms.openlocfilehash: 5fd042b91ede91491590a53abf4dec552fbf6487
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91440406"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>快速入门：使用 Azure 应用配置创建 ASP.NET Core 应用

本快速入门将使用 Azure 应用程序配置来集中存储和管理 ASP.NET Core 应用的应用程序设置。 ASP.NET Core 使用由应用指定的一个或多个数据源的设置，生成基于键值的单个配置对象。 这些数据源称为配置提供程序  。 由于应用程序配置的 .NET Core 客户端作为配置提供程序实现，因此服务就像是另一个数据源。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/dotnet)
* [.NET Core SDK](https://dotnet.microsoft.com/download)

> [!TIP]
> Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的命令行指令。 它预安装了常用的 Azure 工具，其中包括 .NET Core SDK。 如果你已登录到 Azure 订阅，请从 shell.azure.com 启动 [Azure Cloud Shell](https://shell.azure.com)。 若要详细了解 Azure Cloud Shell，请[阅读我们的文档](../cloud-shell/overview.md)

## <a name="create-an-app-configuration-store"></a>创建应用配置存储区

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

7. 选择“操作” > “配置资源管理器” > “创建” > “键-值”来添加以下键值对   ：

    | Key                                | Value                               |
    |------------------------------------|-------------------------------------|
    | `TestApp:Settings:BackgroundColor` | #FFF                              |
    | `TestApp:Settings:FontColor`       | #000                              |
    | `TestApp:Settings:FontSize`        | *24*                                |
    | `TestApp:Settings:Message`         | *Azure 应用配置的数据* |

    暂时将“标签”和“内容类型”保留为空 。 选择“应用”。

## <a name="create-an-aspnet-core-web-app"></a>创建一个 ASP.NET Core Web 应用

使用 [.NET Core 命令行接口 (CLI)](/dotnet/core/tools) 创建新的 ASP.NET Core MVC 项目。 [Azure Cloud Shell](https://shell.azure.com) 提供了这些工具。 它们还可在 Windows、macOS 和 Linux 平台上使用。

在新的 TestAppConfig 文件夹中，运行以下命令，创建 ASP.NET Core MVC 项目：

```dotnetcli
dotnet new mvc --no-https --output TestAppConfig
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-the-app-configuration-store"></a>连接到应用程序配置存储区

1. 运行以下命令，添加 [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) NuGet 包引用：

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. 在 .csproj 文件所在的同一目录中运行以下命令。 该命令使用机密管理器存储名为 `ConnectionStrings:AppConfig` 的机密，该机密存储应用程序配置存储区的连接字符串。 将 `<your_connection_string>` 占位符替换为应用程序配置存储区的连接字符串。 可以在 Azure 门户的“访问密钥”  下找到该连接字符串。

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    > [!IMPORTANT]
    > 有些 shell 会截断连接字符串，除非将连接字符串括在引号中。 确保 `dotnet user-secrets` 命令的输出显示整个连接字符串。 如果未显示，请重新运行该命令，并将连接字符串括在引号中。

    机密管理器仅用于本地测试 web 应用程序。 将应用部署到 [Azure 应用服务](https://azure.microsoft.com/services/app-service/web)后，可以使用应用服务中的“连接字符串”应用程序设置，而无需使用机密管理器来存储连接字符串。

    使用 .NET Core 配置 API 访问此机密。 在所有支持的平台上，冒号 (`:`) 可以在配置 API 的配置名称中使用。 有关详细信息，请参阅[配置键和值](/aspnet/core/fundamentals/configuration#configuration-keys-and-values)。

1. 在 Program.cs 中，添加对 .NET Core 配置提供程序 API 命名空间的引用：

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

1. 通过调用 `AddAzureAppConfiguration` 方法，更新 `CreateWebHostBuilder` 方法以使用应用配置。

    > [!IMPORTANT]
    > `CreateHostBuilder` 替换 .NET Core 3.x 中的 `CreateWebHostBuilder`。 根据环境选择正确的语法。

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(connection);
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                var connection = settings.GetConnectionString("AppConfig");
                config.AddAzureAppConfiguration(connection);
            })
            .UseStartup<Startup>();
    ```

    ---

    进行上述更改后，[应用程序配置的配置提供程序](https://go.microsoft.com/fwlink/?linkid=2074664)就已注册到 .NET Core 配置 API。

## <a name="read-from-the-app-configuration-store"></a>从应用程序配置存储区读取数据

完成下面的步骤，读取和显示存储在应用程序配置存储区中的值。 可使用 .NET Core 配置 API 访问此存储区。 使用 Razor 语法显示键值。

打开 \<app root>/Views/Home/Index.cshtml，并将其内容替换为以下代码：

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<style>
    body {
        background-color: @Configuration["TestApp:Settings:BackgroundColor"]
    }
    h1 {
        color: @Configuration["TestApp:Settings:FontColor"];
        font-size: @Configuration["TestApp:Settings:FontSize"]px;
    }
</style>

<h1>@Configuration["TestApp:Settings:Message"]</h1>
```

在上述代码中，按如下所示使用应用程序配置存储区的键：

* `TestApp:Settings:BackgroundColor` 键的值分配给 CSS `background-color` 属性。
* `TestApp:Settings:FontColor` 键的值分配给 CSS `color` 属性。
* `TestApp:Settings:FontSize` 键的值分配给 CSS `font-size` 属性。
* `TestApp:Settings:Message` 键的值显示为标题。

## <a name="build-and-run-the-app-locally"></a>在本地生成并运行应用

1. 若要使用 .NET Core CLI 生成应用，请导航到项目的根目录。 在命令行界面中运行以下命令：

    ```dotnetcli
    dotnet build
    ```

1. 生成成功完成后，请运行以下命令以在本地运行 Web 应用：

    ```dotnetcli
    dotnet run
    ```

1. 如果要在本地计算机上操作，请使用浏览器导航到 `http://localhost:5000`。 此地址是在本地托管的 Web 应用的默认 URL。 如果要在 Azure Cloud Shell 中操作，请依次选择“Web 预览”按钮和“配置”   。

    ![找到“Web 预览”按钮](./media/quickstarts/cloud-shell-web-preview.png)

    系统提示配置预览端口时，请输入“5000”，然后选择“打开并浏览”。 网页将显示“数据来自 Azure 应用程序配置”。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，请执行以下操作：

* 预配新的应用程序配置存储区。
* 注册应用程序配置存储区的 .NET Core 配置提供程序。
* 使用配置提供程序读取应用程序配置存储区的键。
* 使用 Razor 语法显示应用程序配置存储区的键值。

若要了解如何配置 ASP.NET Core 应用以动态刷新配置设置，请继续学习下一个教程。

> [!div class="nextstepaction"]
> [启用动态配置](./enable-dynamic-configuration-aspnet-core.md)
