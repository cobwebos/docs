---
title: 将 Azure 应用配置与 Azure Functions 结合使用的快速入门 | Microsoft Docs
description: 将 Azure 应用程序配置与 Azure Functions 集合使用的快速入门。
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: Azure Functions
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 5f28e213a5f824562df62a05b98f0f92f71bc591
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957430"
---
# <a name="quickstart-create-an-azure-function-with-app-configuration"></a>快速入门：使用应用配置创建 Azure 函数

Azure 应用配置是 Azure 中的托管配置服务。 通过它可以在与代码分离的一个位置轻松存储和管理所有应用程序设置。 本快速入门将介绍如何将服务合并到 Azure Functions 中。 

可使用任何代码编辑器来完成本快速入门中的步骤。 但是，[Visual Studio Code](https://code.visualstudio.com/) 是一个很好的选项，可用于 Windows、macOS 和 Linux 平台。

![在本地完成快速入门](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="prerequisites"></a>先决条件

要完成此快速入门，请安装 [Visual Studio 2017](https://visualstudio.microsoft.com/vs)（并确保已安装“Azure 开发”工作负载）和[最新的 Azure Functions 工具](../azure-functions/functions-develop-vs.md#check-your-tools-version)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>创建应用配置存储区

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-function-app"></a>创建函数应用

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-app-configuration-store"></a>连接到应用配置存储区

1. 打开 Function1.cs 并添加对应用配置 .NET Core 配置提供程序的引用。

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

2. 通过调用 `builder.AddAzureAppConfiguration()` 更新 `Run` 方法以使用应用配置。

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        var config = builder.Build();
        string message = config["TestApp:Settings:Message"];
        message = message ?? req.Query["message"];

        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
        message = message ?? data?.message;

        return message != null
            ? (ActionResult) new OkObjectResult(message)
            : new BadRequestObjectResult("Please pass a message from a configuration store, on the query string or in the request body");
    }
    ```

## <a name="test-the-function-locally"></a>在本地测试函数

1. 设置名为 ConnectionString 的环境变量，并将其设置为应用配置存储区的访问密钥。 如果你使用的是 Windows 命令提示符，请执行以下命令并重新启动命令提示符以使更改生效：

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    如果你使用的是 Windows PowerShell，请执行以下命令：

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    如果你使用的是 macOS 或 Linux，请执行以下命令：

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. 若要测试函数，请按 F5。 如果系统提示，请按 Visual Studio 的请求下载和安装 Azure Functions Core (CLI) 工具。 可能还需启用一个防火墙例外，以便这些工具能够处理 HTTP 请求。

3. 从 Azure Functions 运行时输出复制函数的 URL。

    ![在 VS 中的函数调试快速入门](./media/quickstarts/function-visual-studio-debugging.png)

4. 将 HTTP 请求的 URL 粘贴到浏览器的地址栏中。 下面演示浏览器中函数返回的对本地 GET 请求的响应：

    ![本地函数启动快速入门](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已经创建了一个新的应用配置存储区，并将用于 Azure 函数。 要了解有关使用应用配置的详细信息，请继续阅读下一个演示身份验证的教程。

> [!div class="nextstepaction"]
> [Azure 资源集成的托管标识](./integrate-azure-managed-service-identity.md)
