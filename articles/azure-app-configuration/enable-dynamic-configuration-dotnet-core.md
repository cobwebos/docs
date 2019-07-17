---
title: 关于在 .NET Core 应用中使用 Azure 应用配置动态配置的教程 | Microsoft Docs
description: 本教程介绍如何动态更新 .NET Core 应用的配置数据
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: abarora
ms.openlocfilehash: 1649fefda5073761d616fc48c602cab84d293ed0
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799086"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>教程：在 .NET Core 应用中使用动态配置

应用配置 .NET Core 客户端库支持按需更新一组配置设置，不需让应用程序重启。 可以这样实现这一点：首先从配置提供程序的选项中获取 `IConfigurationRefresher` 的实例，然后在代码中的任何位置调用该实例上的 `Refresh`。

为了确保更新设置并避免对配置存储区进行太多的调用，可将缓存用于每个设置。 在设置的缓存值过期前，刷新操作不会更新该值，即使该值在配置存储区中已发生更改。 每个请求的默认过期时间为 30 秒，但是，如果需要，可以重写该过期时间。

本教程演示如何在代码中实现动态配置更新。 它建立在快速入门中介绍的应用之上。 在继续操作之前，请先完成[使用应用程序配置创建 .NET Core 应用](./quickstart-dotnet-core-app.md)。

你可以使用任何代码编辑器执行本教程中的步骤。 [Visual Studio Code](https://code.visualstudio.com/) 是 Windows、macOS 和 Linux 平台上提供的一个卓越选项。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置应用程序，使其能够按需使用应用配置存储更新配置。
> * 在应用程序的控制器中注入最新配置。

## <a name="prerequisites"></a>先决条件

若要完成本教程，请安装 [.NET Core SDK](https://dotnet.microsoft.com/download)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>从应用配置重载数据

打开 *Program.cs* 并更新该文件，在 `AddAzureAppConfiguration` 方法中指定刷新配置，并使用 `Refresh` 方法触发手动刷新。

```csharp
class Program
{
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;

    static void Main(string[] args)
    {
        IConfigurationRefresher refresher = null;

        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                               .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });
                    
                    _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }

    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
}
```

`ConfigureRefresh` 方法用于指定在刷新操作触发时通过应用程序配置存储区更新配置数据所用的设置。 可以在提供给 `AddAzureAppConfiguration` 方法的选项上调用 `GetRefresher` 方法，以便检索 `IConfigurationRefresher` 的实例，然后可以使用此实例上的 `Refresh` 方法在代码中的任意位置触发刷新操作。
    
> [!NOTE]
> 配置设置的默认缓存过期时间为 30 秒，但是，可以通过对作为参数传递到 `ConfigureRefresh` 方法的选项初始值设定项调用 `SetCacheExpiration` 方法来重写该过期时间。

## <a name="build-and-run-the-app-locally"></a>在本地生成并运行应用

1. 设置名为“ConnectionString”的环境变量，并将其设置为应用程序配置存储区的访问键  。 如果使用 Windows 命令提示符，则请运行以下命令并重启命令提示符，这样更改才会生效：

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    如果使用 Windows PowerShell，请运行以下命令：

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    如果使用 macOS 或 Linux，则请运行以下命令：

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. 运行以下命令以生成控制台应用：

        dotnet build

1. 生成成功完成后，请运行以下命令以在本地运行应用：

        dotnet run

    ![本地启动应用快速入门](./media/quickstarts/dotnet-core-app-run.png)

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择“所有资源”，然后选择在快速入门中创建的应用程序配置存储区实例  。

1. 选择“配置资源管理器”  并更新以下键的值：

    | 密钥 | 值 |
    |---|---|
    | TestApp:Settings:Message | Azure 应用配置的数据 - 已更新 |

1. 按 Enter 键触发刷新并在命令提示符或 PowerShell 窗口中输出更新的值。

    ![本地刷新应用快速入门](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > 由于在指定刷新操作的配置时已使用 `SetCacheExpiration` 方法将缓存过期时间设置为 10 秒，因此只有在针对该设置进行刷新并已过去至少 10 秒以后，才会更新此配置设置的值。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，通过添加 Azure 托管服务标识简化了应用配置访问并改进了应用凭据管理。 若要了解有关如何使用应用程序配置的更多信息，请继续阅读 Azure CLI 示例。

> [!div class="nextstepaction"]
> [CLI 示例](./cli-samples.md)
