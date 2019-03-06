---
title: 将 Azure 应用配置与 .NET Core 结合使用的快速入门| Microsoft Docs
description: 将 Azure 应用配置与 .NET Core 应用结合使用的快速入门
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: cd4115aaeec15d14d48dcb71cbdc75212c6dc2db
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960665"
---
# <a name="quickstart-create-an-net-core-app-with-app-configuration"></a>快速入门：使用应用配置创建 .NET Core 应用

Azure 应用配置是 Azure 中的托管配置服务。 通过它可以在与代码分离的一个位置轻松存储和管理所有应用程序设置。 本快速入门将介绍如何将服务合并到 .NET Core 控制台应用中。

可使用任何代码编辑器来完成本快速入门中的步骤。 但是，[Visual Studio Code](https://code.visualstudio.com/) 是一个很好的选项，可用于 Windows、macOS 和 Linux 平台。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请安装 [.NET Core SDK](https://dotnet.microsoft.com/download)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>创建应用配置存储区

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-core-console-app"></a>创建 .NET Core 控制台应用

你将使用 [.NET Core 命令行接口 (CLI)](https://docs.microsoft.com/dotnet/core/tools/) 创建新的 .NET Core 控制台应用项目。 通过 Visual Studio 使用 .NET Core CLI 的优点是，它可用于 Windows、macOS 和 Linux 平台。

1. 为项目新建一个文件夹。

2. 在新文件夹中，执行以下命令，创建新的 ASP.NET Core MVC Web 应用项目：

        dotnet new console

## <a name="connect-to-app-configuration-store"></a>连接到应用配置存储区

1. 通过执行以下命令，添加对 `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet 包的引用：

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. 执行以下命令，还原项目包。

        dotnet restore

3. 打开 Program.cs 并更新 `Main` 方法以通过调用 `builder.AddAzureAppConfiguration()` 方法使用应用配置。

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>在本地生成并运行应用

1. 设置名为 ConnectionString 的环境变量，并将其设置为应用配置存储区的访问密钥。 如果你使用的是 Windows 命令提示符，请执行以下命令并重新启动命令提示符以使更改生效：

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    如果你使用的是 Windows PowerShell，请执行以下命令：

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    如果你使用的是 macOS 或 Linux，请执行以下命令：

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. 执行以下命令以生成控制台应用：

        dotnet build

3. 成功完成应用生成后，执行以下命令，在本地运行应用：

        dotnet run

    ![快速入门应用运行](./media/quickstarts/dotnet-core-app-run.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已经创建了一个新的应用配置存储区，并将其用于 .NET Core 控制台应用。 要了解有关使用应用配置的详细信息，请继续阅读下一个演示身份验证的教程。

> [!div class="nextstepaction"]
> [Azure 资源集成的托管标识](./integrate-azure-managed-service-identity.md)
