---
title: 关于在 Azure Functions 应用中使用 Azure 应用程序配置动态配置的教程 | Microsoft Docs
description: 本教程介绍如何动态更新 Azure Functions 应用的配置数据
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: ba70d5f186c1424b2019716ab7a87aeae85f8913
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74187291"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>教程：在 Azure Functions 应用中使用动态配置

应用程序配置 .NET Standard 配置提供程序支持缓存和动态刷新由应用程序活动驱动的配置。 本教程演示如何在代码中实现动态配置更新。 它建立在本快速入门中介绍的 Azure Functions 应用之上。 在继续本教程之前，请先完成[使用 Azure 应用程序配置创建 Azure Functions 应用](./quickstart-azure-functions-csharp.md)这一教程。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置 Azure Functions 应用，使其能够更新配置以响应应用程序配置存储区中的更改。
> * 将最新配置注入到 Azure Functions 调用中。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- 包含 Azure 开发工作负载的 [Visual Studio 2019](https://visualstudio.microsoft.com/vs) 
- [Azure Functions 工具](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- 完成快速入门[使用 Azure 应用程序配置创建 Azure Functions 应用](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>从应用配置重载数据

1. 打开 Function1.cs  。 除了 `static` 属性 `Configuration` 之外，还可添加新的 `static` 属性 `ConfigurationRefresher` 以保留 `IConfigurationRefresher` 的单一实例，该实例在稍后调用 Functions 时将用于通知配置更新。

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. 更新此构造函数并使用 `ConfigureRefresh` 方法指定从应用程序配置存储区中刷新此设置。 使用 `GetRefresher` 方法检索 `IConfigurationRefresher` 的实例。 我们还可以将配置缓存过期时间范围从默认的 30 秒更改为 1 分钟。

    ```csharp
    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .ConfigureRefresh(refreshOptions =>
                        refreshOptions.Register("TestApp:Settings:Message")
                                      .SetCacheExpiration(TimeSpan.FromSeconds(60))
            );
            ConfigurationRefresher = options.GetRefresher();
        });
        Configuration = builder.Build();
    }
    ```

3. 更新 `Run` 方法，并在 Functions 调用开始时通知使用 `Refresh` 方法刷新配置。 如果未到达缓存过期时间范围，则将不会进行操作。 如果希望在不受阻止的情况下刷新配置，请删除 `await` 运算符。

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await ConfigurationRefresher.Refresh();

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>在本地测试函数

1. 设置名为“ConnectionString”的环境变量，并将其设置为应用程序配置存储区的访问键  。 如果使用 Windows 命令提示符，则请运行以下命令并重启命令提示符，这样更改才会生效：

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    如果使用 Windows PowerShell，请运行以下命令：

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    如果使用 macOS 或 Linux，则请运行以下命令：

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. 若要测试函数，请按 F5。 如果系统提示，请按 Visual Studio 的请求下载和安装 Azure Functions Core (CLI) 工具  。 你还需要启用防火墙例外，这样工具才能处理 HTTP 请求。

3. 从 Azure Functions 运行时输出复制函数的 URL。

    ![在 VS 中的函数调试快速入门](./media/quickstarts/function-visual-studio-debugging.png)

4. 将 HTTP 请求的 URL 粘贴到浏览器的地址栏中。 下图显示了浏览器中函数返回的本地 GET 请求的响应。

    ![本地函数启动快速入门](./media/quickstarts/dotnet-core-function-launch-local.png)

5. 登录到 [Azure 门户](https://portal.azure.com)。 选择“所有资源”，然后选择在快速入门中创建的应用程序配置存储区实例  。

6. 选择“配置资源管理器”并更新以下密钥值  ：

    | 密钥 | 值 |
    |---|---|
    | TestApp:Settings:Message | Azure 应用配置的数据 - 已更新 |

7. 多次刷新浏览器。 如果缓存的设置在一分钟后过期，则页面会显示 Functions 调用的响应已有更新的值。

    ![本地函数刷新快速入门](./media/quickstarts/dotnet-core-function-refresh-local.png)

本教程中使用的示例代码可从[应用程序配置 GitHub 存储库](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)下载

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你学习了如何使 Azure Functions 应用能够动态刷新来自应用程序配置的配置设置。 若要了解如何使用 Azure 托管标识来简化对应用程序配置的访问，请继续学习下一篇教程。

> [!div class="nextstepaction"]
> [托管标识集成](./howto-integrate-azure-managed-service-identity.md)
