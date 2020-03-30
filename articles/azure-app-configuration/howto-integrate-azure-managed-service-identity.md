---
title: 使用 Azure 托管标识进行身份验证
titleSuffix: Azure App Configuration
description: 使用 Azure 托管标识对 Azure 应用配置进行身份验证
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 2/25/2020
ms.openlocfilehash: bf97a1eae758778efc8d800666af4a5fcb574429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056839"
---
# <a name="integrate-with-azure-managed-identities"></a>与 Azure 托管标识集成

Azure 活动目录[托管标识](../active-directory/managed-identities-azure-resources/overview.md)简化了云应用程序的秘密管理。 使用托管标识，代码可以使用为其运行的 Azure 服务创建的服务主体。 使用托管标识而不是存储在 Azure Key Vault 中的单独凭据或本地连接字符串。 

Azure 应用配置及其 .NET Core、.NET 框架和 Java Spring 客户端库已内置了托管标识支持。 尽管不需要使用它，但托管标识无需使用包含机密的访问令牌。 您的代码只能使用服务终结点访问应用配置存储。 您可以直接将此 URL 嵌入到代码中，而无需公开任何机密。

本文演示如何利用托管标识访问应用配置。 它建立在快速入门中介绍的 Web 应用之上。 在继续之前，首先[使用应用配置创建ASP.NET核心应用](./quickstart-aspnet-core-app.md)。

本文还演示如何将托管标识与应用配置的密钥保管库引用结合使用。 使用单个托管标识，您可以无缝地从密钥保管库访问密钥库和来自应用配置的配置值。 如果要探索此功能，首先使用[ASP.NET核心密钥保管库引用](./use-key-vault-references-dotnet-core.md)。

你可以使用任何代码编辑器执行本教程中的步骤。 [Visual Studio Code](https://code.visualstudio.com/) 是 Windows、macOS 和 Linux 平台上提供的一个卓越选项。

在本文中，学习如何：

> [!div class="checklist"]
> * 授予对应用程序配置的托管身份访问权限。
> * 配置应用以在连接到应用程序配置时使用托管标识。
> * 或者，将应用配置为在通过应用配置密钥保管库引用连接到密钥保管库时使用托管标识。

## <a name="prerequisites"></a>先决条件

若要完成本教程，必须满足以下先决条件：

* [.NET 核心 SDK](https://www.microsoft.com/net/download/windows).
* [已配置的 Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>添加托管标识

要在门户中设置托管标识，请先创建应用程序，然后启用该功能。

1. 在[Azure 门户](https://portal.azure.com)中创建应用服务实例，与通常一样。 在门户网站中转到它。

1. 在左侧窗格向下滚动到“设置”组，然后选择“标识”********。

1. 在“系统分配”选项卡中，将“状态”切换为“启用”并选择“保存”****************。

1. 当提示启用系统分配的托管标识时，回答 **"是**"。

    ![在应用服务中设置托管标识](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>授予对应用配置的访问权限

1. 在[Azure 门户](https://portal.azure.com)中，选择**所有资源**并选择在快速入门中创建的应用配置存储。

1. 选择**访问控制 （IAM）。**

1. 在“检查访问权限”选项卡中，选择“添加角色分配”卡 UI 中的“添加”************。

1. 在**角色**下，选择**应用配置数据读取器**。 将“访问权限分配对象”下，选择“应用服务”（在“系统分配的托管标识”下）************。

1. 在“订阅”下，选择 Azure 订阅****。 选择应用的应用服务资源。

1. 选择“保存”。****

    ![添加托管标识](./media/add-managed-identity.png)

1. 可选：如果您希望也授予对密钥保管库的访问权限，请按照[提供具有托管标识的密钥保管库身份验证](https://docs.microsoft.com/azure/key-vault/managed-identity)中的说明进行操作。

## <a name="use-a-managed-identity"></a>使用托管标识

1. 添加对*Azure.标识*包的引用：

    ```cli
    dotnet add package Azure.Identity
    ```

1. 查找应用配置存储的终结点。 此 URL 列在 Azure 门户中存储的 **"访问密钥**"选项卡上。

1. 打开“appsettings.json”，并添加以下脚本**。 *\<将service_endpoint>*（包括括号）替换为应用配置商店的 URL。 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. 打开*Program.cs*，并添加对`Azure.Identity`和`Microsoft.Azure.Services.AppAuthentication`命名空间的引用：

    ```csharp-interactive
    using Azure.Identity;
    ```

1. 如果只想访问直接存储在应用配置中的值，请通过替换 方法`CreateWebHostBuilder`更新`config.AddAzureAppConfiguration()`方法。

    > [!IMPORTANT]
    > `CreateHostBuilder` 替换 .NET Core 3.0 中的 `CreateWebHostBuilder`。  根据环境选择正确的语法。

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```
    ---

1. 要同时使用应用配置值和密钥保管库引用，请更新*Program.cs*如下所示。 此代码使用 创建`KeyVaultClient`一个`AzureServiceTokenProvider`新代码，并将此引用传递给 方法`UseAzureKeyVault`的调用。

    ### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
            public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
                WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        var credentials = new ManagedIdentityCredential();

                        config.AddAzureAppConfiguration(options =>
                        {
                            options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                                    .ConfigureKeyVault(kv =>
                                    {
                                        kv.SetCredential(credentials);
                                    });
                        });
                    })
                    .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        var credentials = new ManagedIdentityCredential();

                        config.AddAzureAppConfiguration(options =>
                        {
                            options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                                    .ConfigureKeyVault(kv =>
                                    {
                                        kv.SetCredential(credentials);
                                    });
                        });
                    })
                    .UseStartup<Startup>());
    ```
    ---

    现在，您可以像访问任何其他应用配置密钥一样访问密钥保管库引用。 配置提供程序将使用`KeyVaultClient`配置为对密钥保管库进行身份验证并检索该值的 提供程序。

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>从本地 Git 进行部署

使用 Kudu 生成服务器为应用启用本地 Git 部署的最简单方法是使用[Azure 云外壳](https://shell.azure.com)。

### <a name="configure-a-deployment-user"></a>配置部署用户

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>使用 Kudu 启用本地 Git
如果没有应用的本地 git 存储库，则需要初始化一个存储库。 要初始化本地 git 存储库，请从应用的项目目录中运行以下命令：

```cmd
git init
git add .
git commit -m "Initial version"
```

要使用 Kudu 生成服务器为应用启用本地 Git 部署[`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git)，请在云外壳中运行。

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

此命令为您提供了类似于以下输出的内容：

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>部署项目

在_本地终端窗口中_，将 Azure 远程添加到本地 Git 存储库。 将_\<url>_ 替换为从[启用本地 Git 获得](#enable-local-git-with-kudu)的 Git 遥控器的 URL 与 Kudu 。

```bash
git remote add azure <url>
```

使用以下命令推送到 Azure 远程功能以部署应用。 当系统提示输入密码时，请输入你在[配置部署用户](#configure-a-deployment-user)中创建的密码。 请勿使用用于登录 Azure 门户的密码。

```bash
git push azure master
```

在输出中可能会看到特定于运行时的自动化，如 MSBuild for ASP.NET、`npm install` for Node.js 和 `pip install` for Python。

### <a name="browse-to-the-azure-web-app"></a>浏览到 Azure Web 应用

使用浏览器浏览到 Web 应用以验证是否已部署内容。

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>使用其他语言的托管标识

适用于 .NET Framework 和 Java Spring 的应用配置提供程序也有针对托管标识的内置支持。 配置这些提供程序之一时，可以使用商店的 URL 终结点而不是其完整连接字符串。 

例如，您可以更新在快速入门中创建的 .NET 框架控制台应用，以便在*App.config 文件中*指定以下设置：

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤
在本教程中，您添加了 Azure 托管标识，以简化对应用配置的访问并改进应用的凭据管理。 若要了解有关如何使用应用程序配置的更多信息，请继续阅读 Azure CLI 示例。

> [!div class="nextstepaction"]
> [CLI 示例](./cli-samples.md)
