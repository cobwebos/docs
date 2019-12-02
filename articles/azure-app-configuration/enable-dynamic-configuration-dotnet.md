---
title: 关于在 .NET Framework 应用中使用 Azure 应用配置动态配置的教程 | Microsoft Docs
description: 本教程介绍如何动态更新 .NET Framework 应用的配置数据
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
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7cb76d5836055ce352373fa13449e27d81e84022
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185235"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>教程：在 .NET Framework 应用中使用动态配置

无需重启应用程序，应用配置 .NET 客户端库也支持更新一组配置设置。 可以这样实现这一点：首先从配置提供程序的选项中获取 `IConfigurationRefresher` 的实例，然后在代码中的任何位置调用该实例上的 `Refresh`。

为了确保更新设置并避免对配置存储区进行太多的调用，可将缓存用于每个设置。 在设置的缓存值过期前，刷新操作不会更新该值，即使该值在配置存储区中已发生更改。 每个请求的默认过期时间为 30 秒，但是，如果需要，可以重写该过期时间。

本教程演示如何在代码中实现动态配置更新。 它建立在快速入门中介绍的应用之上。 在继续操作之前，请先完成[使用应用配置创建 .NET Framework 应用](./quickstart-dotnet-app.md)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置 .NET Framework 应用，使其能够更新配置以响应应用程序配置存储区中的更改。
> * 在应用程序中注入最新配置。
## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 或更高版本](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>创建应用配置存储区

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. 选择“配置资源管理器” > “+创建”来添加以下键值对   ：

    | 密钥 | 值 |
    |---|---|
    | TestApp:Settings:Message | Azure 应用配置的数据 |

    暂时将“标签”和“内容类型”保留为空   。

## <a name="create-a-net-framework-console-app"></a>创建 .NET Framework 控制台应用

1. 启动 Visual Studio，并选择“文件” > “新建” > “项目”    。

1. 在**创建新项目**中，针对“控制台”  项目类型进行筛选，然后单击“控制台应用(.NET Framework)”  。 单击“下一步”。 

1. 在**配置新项目**中，输入项目名称。 在“框架”  下，选择“.NET Framework 4.7.1”  或更高版本。 单击“创建”。 

## <a name="reload-data-from-app-configuration"></a>从应用配置重载数据
1. 右键单击项目，然后选择“管理 NuGet 包”  。 在“浏览”选项卡上，搜索“Microsoft.Extensions.Configuration.AzureAppConfiguration”NuGet 包，并将其添加到你的项目中   。 如果找不到，请选择“包含预发行版”复选框  。

1. 打开 *Program.cs*，并添加对 .NET Core 应用程序配置提供程序的引用。

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. 添加两个变量来存储与配置相关的对象。

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. 更新 `Main` 方法，以便能用指定的刷新选项连接到应用配置。

    ```csharp
    static void Main(string[] args)
    {
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
    ```
    `ConfigureRefresh` 方法用于指定在刷新操作触发时通过应用程序配置存储区更新配置数据所用的设置。 可以在提供给 `AddAzureAppConfiguration` 方法的选项上调用 `GetRefresher` 方法，以便检索 `IConfigurationRefresher` 的实例，然后可以使用此实例上的 `Refresh` 方法在代码中的任意位置触发刷新操作。

    > [!NOTE]
    > 配置设置的默认缓存过期时间为 30 秒，但是，可以通过调用作为参数传递到 `ConfigureRefresh` 方法的选项初始值设定项上的 `SetCacheExpiration` 方法来重写该过期时间。

1. 添加一个名为 `PrintMessage()` 的方法，该方法触发手动刷新应用配置中的配置数据。

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>在本地生成并运行应用

1. 设置名为“ConnectionString”的环境变量，并将其设置为应用程序配置存储区的访问键  。 如果使用 Windows 命令提示符，则请运行以下命令并重启命令提示符，这样更改才会生效：

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    如果使用 Windows PowerShell，请运行以下命令：

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. 重启 Visual Studio 以便使所做更改生效。 

1. 按 Ctrl+F5 生成并运行控制台应用。

    ![本地启动应用](./media/dotnet-app-run.png)

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择“所有资源”，然后选择在快速入门中创建的应用程序配置存储区实例  。

1. 选择“配置资源管理器”  并更新以下键的值：

    | 密钥 | 值 |
    |---|---|
    | TestApp:Settings:Message | Azure 应用配置的数据 - 已更新 |

1. 回到运行中的应用程序，按 Enter 键触发刷新，并在命令提示符或 PowerShell 窗口中打印更新的值。

    ![本地刷新应用](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > 由于在指定刷新操作的配置时已使用 `SetCacheExpiration` 方法将缓存过期时间设置为 10 秒，因此只有在针对该设置进行刷新并已过去至少 10 秒以后，才会更新此配置设置的值。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你已启用 .NET Framework 应用，以通过应用程序配置动态刷新配置设置。 若要了解如何使用 Azure 托管标识来简化对应用程序配置的访问，请继续学习下一篇教程。

> [!div class="nextstepaction"]
> [托管标识集成](./howto-integrate-azure-managed-service-identity.md)
