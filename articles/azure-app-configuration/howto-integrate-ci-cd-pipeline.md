---
title: 将与使用 Azure 应用程序配置的持续集成和交付管道集成 |Microsoft Docs
description: 了解如何生成在持续集成和交付过程在 Azure 应用程序配置中使用数据配置文件
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 7b2e919bc46810e8478956675ffeb1cb0542da85
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957362"
---
# <a name="integrate-with-a-cicd-pipeline"></a>与 CI/CD 管道集成

若要增强远程可能不能够访问 Azure 应用程序配置的应用程序的复原能力，应打包当前的配置数据到其启动过程中部署与应用程序和本地加载的文件. 此方法可保证你的应用程序将至少具有默认设置值。 可用时，这些值将覆盖较新应用程序配置存储区中所做的任何更改。

使用[**导出**](./howto-import-export-data.md#export-data)函数的 Azure 应用程序配置，可以自动检索作为单个文件的当前配置数据的过程。 然后可以在生成或部署的步骤在持续集成和持续部署管道中嵌入此文件。

下面的示例演示如何包含应用配置为 web 应用快速入门中引入了步骤作为生成的数据。 请先完成[使用应用配置创建 ASP.NET Core 应用](./quickstart-aspnet-core-app.md)部分，然后再继续。

可使用任何代码编辑器来完成本快速入门中的步骤。 但是，[Visual Studio Code](https://code.visualstudio.com/) 是一个很好的选项，可用于 Windows、macOS 和 Linux 平台。

## <a name="prerequisites"></a>必备组件

如果本地生成，下载并安装[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)如果尚未准备好。

如果你想要执行的云生成，使用 Azure DevOps，请确保[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)安装在您的生成系统。

## <a name="export-app-configuration-store"></a>导出应用程序配置存储

1. 打开你 *.csproj*文件，并添加以下：

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    *ConnectionString*关联您应用的配置存储区应添加为环境变量。

2. 打开*Program.cs* ，并更新`CreateWebHostBuilder`方法以通过调用来使用导出的 json 文件`config.AddJsonFile()`方法。

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

## <a name="build-and-run-the-app-locally"></a>在本地生成并运行应用

1. 设置名为 ConnectionString 的环境变量，并将其设置为应用配置存储区的访问密钥。 如果你使用的是 Windows 命令提示符，请执行以下命令并重新启动命令提示符以使更改生效：

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    如果你使用的是 Windows PowerShell，请执行以下命令：

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    如果你使用的是 macOS 或 Linux，请执行以下命令：

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. 若要使用 .NET Core CLI 生成应用，请在命令行界面中执行以下命令：

        dotnet build

3. 成功完成应用生成后，执行以下命令，在本地运行 Web 应用：

        dotnet run

4. 启动浏览器窗口并导航到 `http://localhost:5000`，即本地托管的 Web 应用的默认 URL。

    ![本地启动应用快速入门](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>后续步骤

* [Azure 资源集成的托管标识](./integrate-azure-managed-service-identity.md)
