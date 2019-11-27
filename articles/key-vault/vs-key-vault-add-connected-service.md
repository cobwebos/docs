---
title: 使用 Visual Studio 将密钥保管库支持添加到 ASP.NET 项目 - Azure 密钥保管库 | Microsoft Docs
description: 本教程介绍如何将 Key Vault 支持添加到 ASP.NET 或 ASP.NET Core Web 应用程序。
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 1df0ff3b6fea335dde5a3200f824adf14f924d9c
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452367"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>使用 Visual Studio 连接服务将 Key Vault 添加到 Web 应用程序

本教程介绍如何轻松添加所需的设置，以开始使用 Azure Key Vault 在 Visual Studio 中管理 Web 项目的机密，不管使用的是 ASP.NET Core 还是任何类型的 ASP.NET 项目。 使用 Visual Studio 中的连接服务功能，可让 Visual Studio 自动添加所需的所有 NuGet 包和配置设置，以连接到 Azure 中的 Key Vault。

有关连接服务为了启用 Key Vault 而在项目中所做的更改的详细信息，请参阅 [Key Vault 连接服务 - 我的 ASP.NET 4.7.1 项目发生了什么情况](#how-your-aspnet-framework-project-is-modified)或 [Key Vault 连接服务 - 我的 ASP.NET Core 项目发生了什么情况](#how-your-aspnet-core-project-is-modified)。

## <a name="prerequisites"></a>先决条件

- **一个 Azure 订阅**。 如果没有订阅，请注册[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
- **Visual studio 2019 版本 16.3**或更高版本，或者安装了**Web 开发**工作负荷的**visual studio 2017 版本 15.7** 。 [立即下载](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)。
- 对于包含 Visual Studio 2017 的 ASP.NET（非 Core），需要安装 .NET Framework 4.7.1 或更高版本的开发工具，默认情况下未安装这些工具。 若要安装这些工具，请启动 Visual Studio 安装程序，依次选择“修改”、“单个组件”，在右侧展开“ASP.NET 和 Web 开发”，然后选择“.NET Framework 4.7.1 开发工具”。
- ASP.NET 4.7.1 或更高版本，或者 ASP.NET Core 2.0 或更高版本的 web 项目打开。

## <a name="add-key-vault-support-to-your-project"></a>将 Key Vault 支持添加到项目

在开始之前，请确保已登录 Visual Studio： 登录时使用的帐户与用于 Azure 订阅的帐户相同。 然后打开 ASP.NET 4.7.1 或更高版本或 ASP.NET Core 2.0 Web 项目，执行以下步骤：

1. 在**解决方案资源管理器**中，右键单击要向其添加 Key Vault 支持的项目，然后选择 "**添加** > 连接的**服务**"。
   此时会显示“连接服务”页，其中包含可添加到项目的服务。
1. 在可用服务的菜单中，选择“使用 Azure Key Vault 来保护机密”。

   ![选择“使用 Azure Key Vault 来保护机密”](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. 选择要使用的订阅，然后选择新的或现有的 Key Vault。 如果选择新的 Key Vault，则会显示“编辑”链接。 选中该链接，以便配置新的 Key Vault。

   ![选择订阅](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. 在“编辑 Azure Key Vault”中，输入需要用于 Key Vault 的名称。

1. 选择现有**资源组**，或选择使用自动生成的唯一名称创建新的资源组。  如果想要使用不同的名称创建新组，可以使用 [Azure 门户](https://portal.azure.com)，然后关闭页面并重启，以重新加载资源组列表。
1. 选择要在其中创建 Key Vault 的“位置”。 如果 Web 应用程序托管在 Azure 中，请选择托管 Web 应用程序的区域，以获得最佳性能。
1. 选择一个**定价层**。 有关详细信息，请参阅 [Key Vault 定价](https://azure.microsoft.com/pricing/details/key-vault/)。
1. 选择“确定”，接受配置选项。
1. 在 Visual Studio 的“Azure Key Vault”选项卡中选择现有 Key Vault 或配置新的 Key Vault 以后，请选择“添加”，以便添加连接的服务。
1. 选择“管理存储在此 Key Vault 中的机密”链接，打开 Key Vault 的“机密”页。 如果已关闭该页或项目，可以在 [Azure 门户](https://portal.azure.com)中通过选择“安全性”下的“所有服务”导航到它，选择 **Key Vault**，然后选择你的 Key Vault。
1. 在创建的 Key Vault 的 "Key Vault" 部分中，选择 "**密码**"，然后选择 "**生成/导入**"。

   ![生成/导入机密](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. 输入一个机密（例如 *MySecret*），并为其提供任何字符串值作为测试，然后选择“创建”按钮。

   ![创建机密](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. （可选）输入另一个机密，但这一次通过将其命名为“Secrets--MySecret”将其放入某个类别。 此语法指定的类别“Secrets”包含机密“MySecret”。

现在，可以在代码中访问机密。 后续步骤根据使用的是 ASP.NET 4.7.1 还是 ASP.NET Core 而有所不同。

## <a name="access-your-secrets-in-code-aspnet-core"></a>在代码中访问机密（ASP.NET Core）

1. 在解决方案资源管理器中右键单击项目，然后选择“管理 NuGet 包”。 在 "**浏览**" 选项卡中，找到并安装以下两个 NuGet 包： [MICROSOFT.AZURE.SERVICES.APPAUTHENTICATION](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) ; 对于 .net core 2，请添加[KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)或 For .net core 3，添加[KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core)。

1. 对于 ".NET Core 2"，请选择 "`Program.cs`" 选项卡，然后将 Program 类中的 "`BuildWebHost` 定义" 更改为以下内容：

   ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((ctx, builder) =>
               {
                   var keyVaultEndpoint = GetKeyVaultEndpoint();
                   if (!string.IsNullOrEmpty(keyVaultEndpoint))
                   {
                       var azureServiceTokenProvider = new AzureServiceTokenProvider();
                       var keyVaultClient = new KeyVaultClient(
                           new KeyVaultClient.AuthenticationCallback(
                               azureServiceTokenProvider.KeyVaultTokenCallback));
                       builder.AddAzureKeyVault(
                           keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                   }
               }
            ).UseStartup<Startup>();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

   对于 .NET Core 3，请使用以下代码。

   ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((context, config) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        config.AddAzureKeyVault(keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        private static string GetKeyVaultEndpoint() => "https://WebApplication4-3-kv.vault.azure.net";
    ```

1. 接下来，打开一个页面文件（如*Index.cshtml.cs* ）并编写以下代码：
   1. 使用指令包括对 `Microsoft.Extensions.Configuration` 的引用：

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. 添加配置变量。

      ```csharp
      private static readonly IConfiguration _configuration;
      ```

   1. 添加此构造函数或将现有构造函数替换为：

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. 更新 `OnGet` 方法。 使用在上述命令中创建的机密名称更新此处显示的占位符值。

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. 若要在运行时确认该值，请添加代码以显示 `ViewData["Message"]` 到*cshtml*文件中，以在消息中显示机密。

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

你可以在本地运行应用程序，以验证是否已成功从 Key Vault 获取密码。

## <a name="access-your-secrets-aspnet"></a>访问机密（ASP.NET）

您可以设置配置，使 web.config 文件在 `appSettings` 元素中具有一个虚拟值，该元素在运行时被替换为 true 值。 然后，可以通过 `ConfigurationManager.AppSettings` 的数据结构访问此。

1. 编辑 web.config 文件。  找到 appSettings 标记，添加属性 `configBuilders="AzureKeyVault"`，并添加一行：

   ```xml
      <add key="mysecret" value="dummy"/>
   ```

1. 编辑*HomeController.cs*中的 `About` 方法，以显示确认的值。

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["mysecret"];
   }
   ```
1. 在调试器下本地运行该应用程序，切换到 "**关于**" 选项卡，并验证是否显示了来自 Key Vault 的值。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将其删除。 这会删除 Key Vault 和相关的资源。 若要通过门户删除资源组，请执行以下操作：

1. 在门户顶部的“搜索”框中输入资源组的名称。 在搜索结果中看到在本快速入门中使用的资源组后，将其选中。
2. 选择“删除资源组”。
3. 在“键入资源组名称”框中，输入资源组的名称，然后选择“删除”。

## <a name="troubleshooting"></a>故障排除

如果你的 Key Vault 运行的 Microsoft 帐户不同于你在 Visual Studio 中登录的其他（例如，Key Vault 在你的工作帐户上运行，但 Visual Studio 正在使用你的专用帐户），则你在 Program.cs 文件中收到错误，则 Visual Studio 无法访问 Key Vault。 解决此问题：

1. 中转到[Azure 门户](https://portal.azure.com)并打开 Key Vault。

1. 选择 "**访问策略**"，然后选择 "**添加访问策略**"，然后选择你作为主体登录的帐户。

1. 在 Visual Studio 中，选择 "**文件** > **帐户设置**"。
从 "**所有帐户**" 部分中选择 "**添加帐户**"。 使用你选择作为访问策略的主体的帐户登录。

1. 选择 "**工具**" > **选项**，并查找 " **Azure 服务身份验证**"。 然后选择刚添加到 Visual Studio 的帐户。

现在，调试应用程序时，Visual Studio 会连接到 Key Vault 所在的帐户。

## <a name="how-your-aspnet-core-project-is-modified"></a>如何修改 ASP.NET Core 项目

此部分介绍在使用 Visual Studio 添加 Key Vault 连接服务时，对 ASP.NET 项目所做的具体更改。

### <a name="added-references-for-aspnet-core"></a>为 ASP.NET Core 添加了引用

影响项目文件 .NET 引用和 NuGet 包引用。

| 类型 | 参考 |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>为 ASP.NET Core 添加了文件

- 添加了 `ConnectedService.json`，其中记录了有关连接服务提供程序、版本和指向文档的链接的一些信息。

### <a name="project-file-changes-for-aspnet-core"></a>ASP.NET Core 的项目文件更改

- 添加了连接服务 ItemGroup 和 `ConnectedServices.json` 文件。

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>适用于 ASP.NET Core 的 launchsettings.json 更改

- 将以下环境变量条目添加到 IIS Express 配置文件和匹配 Web 项目名称的配置文件：

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Azure 上适用于 ASP.NET Core 的更改

- 创建了资源组（或使用了现有资源组）。
- 在指定的资源组中创建了密钥保管库。

## <a name="how-your-aspnet-framework-project-is-modified"></a>如何修改 ASP.NET Framework 项目

此部分介绍在使用 Visual Studio 添加 Key Vault 连接服务时，对 ASP.NET 项目所做的具体更改。

### <a name="added-references-for-aspnet-framework"></a>为 ASP.NET Framework 添加了引用

影响项目文件 .NET 引用和 `packages.config`（NuGet 引用）。

| 类型 | 参考 |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>为 ASP.NET Framework 添加了文件

- 添加了 `ConnectedService.json`，其中记录了有关连接服务提供程序、版本和指向文档的链接的一些信息。

### <a name="project-file-changes-for-aspnet-framework"></a>ASP.NET Framework 的项目文件更改

- 添加了连接服务 ItemGroup 和 ConnectedServices.json 文件。
- 请参考[添加的引用](#added-references-for-aspnet-framework)部分中所述的 .NET 程序集。

### <a name="webconfig-or-appconfig-changes"></a>web.config 或 app.config 发生更改

- 添加了以下配置条目：

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure-for-aspnet-framework"></a>Azure 上适用于 ASP.NET Framework 的更改

- 创建了资源组（或使用了现有资源组）。
- 在指定的资源组中创建了密钥保管库。

## <a name="next-steps"></a>后续步骤

如果遵循本教程，你的 Key Vault 权限将设置为使用你自己的 Azure 订阅运行，但这可能不适合于生产方案。 你可以创建托管标识，以管理应用 Key Vault 访问。 请参阅[使用托管标识提供 Key Vault 身份验证](/azure/key-vault/managed-identity)。

在 [Key Vault 开发人员指南](key-vault-developers-guide.md)中了解如何使用 Key Vault 进行开发。
