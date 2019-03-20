---
title: 使用 Azure 应用程序配置集成与持续集成和交付管道 |Microsoft Docs
description: 了解如何在持续集成和交付过程在 Azure 应用程序配置中使用数据来生成配置文件
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
ms.openlocfilehash: cb9fe6dc234c317daa5eabec01812324e7c81663
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224322"
---
# <a name="integrate-with-a-cicd-pipeline"></a>与 CI/CD 管道集成

您可以增强远程可能不能够访问 Azure 应用程序配置的应用程序的复原能力。 为此，请打包到与应用程序以及加载本地部署在其启动过程的文件中的当前配置数据。 这种方法可保证你的应用程序至少有默认设置值。 可用时，这些值将应用配置存储区中的任何较新更改被覆盖。

使用[导出](./howto-import-export-data.md#export-data)函数的 Azure 应用程序配置，可以自动检索作为单个文件的当前配置数据的过程。 然后将此文件嵌入在持续集成和持续部署 (CI/CD) 管道中的生成或部署步骤。

下面的示例演示如何包含应用配置为 web 应用快速入门中引入了步骤作为生成的数据。 在继续之前，完成[使用应用配置创建 ASP.NET Core 应用](./quickstart-aspnet-core-app.md)第一个。

可以使用任何代码编辑器中执行本快速入门中的步骤。 [Visual Studio Code](https://code.visualstudio.com/)是很好的选择在 Windows、 macOS 和 Linux 平台上可用。

## <a name="prerequisites"></a>必备组件

如果本地生成，下载并安装[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)如果尚未准备好。

为云生成，使用 Azure DevOps，请确保[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)安装在您的生成系统。

## <a name="export-an-app-configuration-store"></a>导出应用程序配置存储

1. 打开你 *.csproj*文件，并添加以下脚本：

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    添加*ConnectionString*与应用程序配置存储为环境变量相关联。

2. 打开 Program.cs，并更新`CreateWebHostBuilder`方法以通过调用来使用导出的 JSON 文件`config.AddJsonFile()`方法。

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

1. 设置环境变量名为**ConnectionString**，并将其设置为将存储到应用程序配置存储访问密钥。 如果使用 Windows 命令提示符处，运行以下命令并重新启动命令提示符下，才能使更改生效：

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    如果使用 Windows PowerShell，请运行以下命令：

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    如果使用 macOS 或 Linux，运行以下命令：

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. 若要通过使用.NET Core CLI 构建应用程序，请在命令行界面中运行以下命令：

        dotnet build

3. 在生成成功完成后，运行以下命令以在本地运行 web 应用：

        dotnet run

4. 打开浏览器窗口并转到`http://localhost:5000`，即本地托管的 web 应用的默认 URL。

    ![本地启动应用快速入门](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>后续步骤

* [管理 Azure 资源集成的标识](./integrate-azure-managed-service-identity.md)
