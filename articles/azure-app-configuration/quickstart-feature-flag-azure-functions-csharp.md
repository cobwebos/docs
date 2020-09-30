---
title: 有关将功能标志添加到 Azure Functions 的快速入门 | Microsoft Docs
description: 在本快速入门中，将 Azure Functions 与 Azure 应用程序配置中的功能标志一起使用，并在本地测试函数。
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 8/26/2020
ms.author: alkemper
ms.openlocfilehash: 5542dca6d9b1ab18cf1b9b93e20fa64da0eb1dd4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90998291"
---
# <a name="quickstart-add-feature-flags-to-an-azure-functions-app"></a>快速入门：向 Azure Functions 应用添加功能标志

在本快速入门中，你将使用 Azure 应用程序配置在 Azure Functions 应用中创建功能管理的实现。 你将使用应用程序配置服务集中存储所有功能标志并控制其状态。 

.NET 功能管理库使用功能标志支持扩展该框架。 这些库在 .NET 配置系统的基础上构建。 它们通过其 .NET 配置提供程序与应用程序配置集成。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
- 包含 **Azure 开发**工作负载的 [Visual Studio 2019](https://visualstudio.microsoft.com/vs)。
- [Azure Functions 工具](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>创建应用配置存储区

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. 选择“功能管理器” > “+添加”以添加名为 `Beta` 的功能标志。  

    > [!div class="mx-imgBorder"]
    > ![启用名为 Beta 的功能标志](media/add-beta-feature-flag.png)

    暂时不定义 `label` 和 `Description`。

7. 选择“应用”  以保存新功能标志。

## <a name="create-a-functions-app"></a>创建 Functions 应用

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>连接到应用程序配置存储区

1. 右键单击项目，然后选择“管理 NuGet 包”  。 在“浏览”选项卡中，搜索以下 NuGet 包并将其添加到项目中  。 查看 `Microsoft.Extensions.DependencyInjection`，验证你是否在使用最新的稳定版本。 

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration
    Microsoft.FeatureManagement
    ```


1. 打开 Function1.cs，并添加这些包的命名空间。

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.FeatureManagement;
    using Microsoft.Extensions.DependencyInjection;
    ```

1. 添加下面的 `Function1` 静态构造函数，启动 Azure 应用程序配置提供程序。 接下来，添加两个 `static` 成员：一个名为 `ServiceProvider` 的字段用于创建 `ServiceProvider` 的单一实例，一个在 `Function1` 下名为 `FeatureManager` 的属性用于创建 `IFeatureManager` 的单一实例。 然后，通过调用 `AddAzureAppConfiguration()` 连接到 `Function1` 中的应用程序配置。 此过程将在应用程序启动时加载配置。 同一配置实例稍后将用于所有 Functions 调用。 

    ```csharp
        // Implements IDisposable, cached for life time of function
        private static ServiceProvider ServiceProvider; 

        static Function1()
        {
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            var services = new ServiceCollection();                                                                             
            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            ServiceProvider = services.BuildServiceProvider(); 
        }

        private static IFeatureManager FeatureManager => ServiceProvider.GetRequiredService<IFeatureManager>();
    ```

1. 更新 `Run` 方法，根据功能标志的状态更改显示的消息的值。

    ```csharp
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
                [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
                ILogger log)
            {
                string message = await FeatureManager.IsEnabledAsync("Beta")
                     ? "The Feature Flag 'Beta' is turned ON"
                     : "The Feature Flag 'Beta' is turned OFF";
                
                return (ActionResult)new OkObjectResult(message); 
            }
    ```

## <a name="test-the-function-locally"></a>在本地测试函数

1. 设置一个名为 ConnectionString 的环境变量，其中值是之前在“访问密钥”下的应用程序配置存储中检索到的访问密钥 。 如果使用 Windows 命令提示符，则请运行以下命令并重启命令提示符，这样更改才会生效：

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    如果使用 Windows PowerShell，请运行以下命令：

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    如果使用 macOS 或 Linux，则请运行以下命令：

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. 按 F5 测试函数。 如果系统提示，请按 Visual Studio 的请求下载和安装 Azure Functions Core (CLI) 工具。 你还需要启用防火墙例外，这样工具才能处理 HTTP 请求。

1. 从 Azure Functions 运行时输出复制函数的 URL。

    ![在 VS 中的函数调试快速入门](./media/quickstarts/function-visual-studio-debugging.png)

1. 将 HTTP 请求的 URL 粘贴到浏览器的地址栏。 下图显示了指出功能标志 `Beta` 已被禁用的响应。 

    ![已禁用快速入门函数功能标志](./media/quickstarts/functions-launch-ff-disabled.png)

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“所有资源”，然后选择你创建的应用程序配置存储区实例。

1. 选择“功能管理器”，将“Beta”密钥的状态更改为“启用”。   

1. 回到命令提示符，按 `Ctrl-C` 取消正在运行的进程。  按 F5 重启应用程序。 

1. 按照步骤 3 中相同的流程，从 Azure Functions 运行时输出复制函数的 URL。 将 HTTP 请求的 URL 粘贴到浏览器的地址栏。 浏览器响应应该已更改，它现指示功能标志 `Beta` 已打开，如下图所示。
 
    ![已启用快速入门函数功能标志](./media/quickstarts/functions-launch-ff-enabled.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个功能标志，并通过[应用程序配置提供程序](https://go.microsoft.com/fwlink/?linkid=2074664)将它用于 Azure Functions 应用。

- 详细了解[功能管理](./concept-feature-management.md)。
- [管理功能标志](./manage-feature-flags.md)。
- [在 Azure Functions 应用中使用动态配置](./enable-dynamic-configuration-azure-functions-csharp.md)
