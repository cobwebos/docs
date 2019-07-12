---
title: 使用 Visual Studio 将密钥保管库支持添加到 ASP.NET 项目 - Azure 密钥保管库 | Microsoft Docs
description: 本教程介绍如何将 Key Vault 支持添加到 ASP.NET 或 ASP.NET Core Web 应用程序。
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: ghogen
ms.openlocfilehash: f84662ce17d604f1a60b6700d259d322415ae91e
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672216"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>使用 Visual Studio 连接服务将 Key Vault 添加到 Web 应用程序

本教程介绍如何轻松添加所需的设置，以开始使用 Azure Key Vault 在 Visual Studio 中管理 Web 项目的机密，不管使用的是 ASP.NET Core 还是任何类型的 ASP.NET 项目。 使用 Visual Studio 中的连接服务功能，可让 Visual Studio 自动添加所需的所有 NuGet 包和配置设置，以连接到 Azure 中的 Key Vault。 

有关连接服务为了启用 Key Vault 而在项目中所做的更改的详细信息，请参阅 [Key Vault 连接服务 - 我的 ASP.NET 4.7.1 项目发生了什么情况](#how-your-aspnet-framework-project-is-modified)或 [Key Vault 连接服务 - 我的 ASP.NET Core 项目发生了什么情况](#how-your-aspnet-core-project-is-modified)。

## <a name="prerequisites"></a>系统必备

- 一个 Azure 订阅  。 如果没有帐户，可以注册一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
- **Visual Studio 2019** 或 **Visual Studio 2017 版本 15.7**（装有 Web 开发工作负荷）。  [立即下载](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)。
- 对于包含 Visual Studio 2017 的 ASP.NET（非 Core），需要安装 .NET Framework 4.7.1 或更高版本的开发工具，默认情况下未安装这些工具。 若要安装这些工具，请启动 Visual Studio 安装程序，依次选择“修改”、“单个组件”，在右侧展开“ASP.NET 和 Web 开发”，然后选择“.NET Framework 4.7.1 开发工具”。    
- 已打开一个 ASP.NET 4.7.1（或更高版本）或 ASP.NET Core 2.0 Web 项目。

## <a name="add-key-vault-support-to-your-project"></a>将 Key Vault 支持添加到项目

1. 在“解决方案资源管理器”中，选择“添加” > “连接服务”。   
   此时会显示“连接服务”页，其中包含可添加到项目的服务。
1. 在可用服务的菜单中，选择“使用 Azure Key Vault 来保护机密”。 

   ![选择“使用 Azure Key Vault 来保护机密”](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   如果已登录到 Visual Studio，并且有与帐户关联的 Azure 订阅，则会显示一个页面，其中提供了订阅下拉列表。 请确保你已登录到 Visual Studio，并且你登录的帐户与用于 Azure 订阅的帐户相同。

1. 选择要使用的订阅，然后选择新的或现有的 Key Vault，或选择“编辑”链接来修改自动生成的名称。

   ![选择订阅](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. 键入 Key Vault 的名称。

   ![重命名 Key Vault，并选择一个资源组](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. 选择现有资源组，或选择使用自动生成的唯一名称创建新的资源组。  如果想要使用不同的名称创建新组，可以使用 [Azure 门户](https://portal.azure.com)，然后关闭页面并重启，以重新加载资源组列表。
1. 选择要在其中创建 Key Vault 的区域。 如果 Web 应用程序托管在 Azure 中，请选择托管 Web 应用程序的区域，以获得最佳性能。
1. 选择定价模型。 有关详细信息，请参阅 [Key Vault 定价](https://azure.microsoft.com/pricing/details/key-vault/)。
1. 选择“确定”接受配置选项。
1. 选择“添加”创建 Key Vault。  如果选择的名称已被使用，则创建过程可能会失败。  如果发生这种情况，请使用“编辑”链接重命名 Key Vault，然后重试。 

   ![将连接服务添加到项目](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. 现在，请在 Azure 上的 Key Vault 中添加机密。 若要转到门户中的相应位置，请单击“管理此 Key Vault 中存储的机密”对应的链接。 如果已关闭该页或项目，可以在 [Azure 门户](https://portal.azure.com)中通过选择“安全性”  下的“所有服务”  导航到它，选择 **Key Vault**，然后选择已创建的 Key Vault。

   ![导航到门户](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. 在创建的 Key Vault 的 Key Vault 部分，依次选择“机密”  、“生成/导入”  。

   ![生成/导入机密](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. 输入一个机密（例如“MySecret”），并为其提供任何字符串值作为测试，然后选择“创建”  按钮。

   ![创建机密](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. （可选）输入另一个机密，但这一次通过将其命名为“Secrets--MySecret”将其放入某个类别。 此语法指定的类别“Secrets”包含机密“MySecret”。
 
现在，可以在代码中访问机密。 后续步骤根据使用的是 ASP.NET 4.7.1 还是 ASP.NET Core 而有所不同。

## <a name="access-your-secrets-in-code"></a>在代码中访问机密

1. 安装这两个 nuget 包：[AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 和 [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet 库。

2. 打开 Program.cs 文件，使用以下代码更新代码： 
   ```
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
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
            ).UseStartup<Startup>()
             .Build();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

3. 接下来打开 About.cshtml.cs 文件并编写以下代码：
   1. 包括对 Microsoft.Extensions.Configuration 此 using 语句：

       ```csharp
       using Microsoft.Extensions.Configuration
       ```

   1. 添加此构造函数：

       ```csharp
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. 更新 OnGet 方法。 更新上述命令中创建的机密名称如下所示的占位符值。

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

在本地运行应用，通过浏览到关于页面。 应会看到检索机密值。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将其删除。 这会删除 Key Vault 和相关的资源。 要通过门户删除资源组，请执行以下操作：

1. 在门户顶部的“搜索”框中输入资源组的名称。 在搜索结果中看到在本快速入门中使用的资源组后，请将其选中。
2. 选择“删除资源组”  。
3. 在“键入资源组名称:”框中，键入资源组的名称，然后选择“删除”   。

## <a name="how-your-aspnet-core-project-is-modified"></a>如何修改 ASP.NET Core 项目

此部分介绍在使用 Visual Studio 添加 Key Vault 连接服务时，对 ASP.NET 项目所做的具体更改。

### <a name="added-references"></a>添加的引用

影响项目文件 .NET 引用和 NuGet 包引用。

| 类型 | 参考 |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files"></a>添加的文件

- 添加了 ConnectedService.json，其中记录了有关连接服务提供程序、版本和指向文档的链接的一些信息。

### <a name="project-file-changes"></a>项目文件更改

- 添加了连接服务 ItemGroup 和 ConnectedServices.json 文件。

### <a name="launchsettingsjson-changes"></a>launchsettings.json 更改

- 将以下环境变量条目添加到 IIS Express 配置文件和匹配 Web 项目名称的配置文件：

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure"></a>对 Azure 的更改

- 创建了资源组（或使用了现有资源组）。
- 在指定的资源组中创建了密钥保管库。

## <a name="how-your-aspnet-framework-project-is-modified"></a>如何修改 ASP.NET Framework 项目

此部分介绍在使用 Visual Studio 添加 Key Vault 连接服务时，对 ASP.NET 项目所做的具体更改。

### <a name="added-references"></a>添加的引用

影响项目文件 .NET 引用和 `packages.config`（NuGet 引用）。

| 类型 | 参考 |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files"></a>添加的文件

- 添加了 ConnectedService.json，其中记录了有关连接服务提供程序、版本和指向文档的链接的一些信息。

### <a name="project-file-changes"></a>项目文件更改

- 添加了连接服务 ItemGroup 和 ConnectedServices.json 文件。
- 请参考[添加的引用](#added-references)部分中所述的 .NET 程序集。

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

### <a name="changes-on-azure"></a>对 Azure 的更改

- 创建了资源组（或使用了现有资源组）。
- 在指定的资源组中创建了密钥保管库。

## <a name="next-steps"></a>后续步骤

在 [Key Vault 开发人员指南](key-vault-developers-guide.md)中了解如何使用 Key Vault 进行开发