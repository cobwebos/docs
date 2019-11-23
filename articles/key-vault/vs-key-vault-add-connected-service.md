---
title: 使用 Visual Studio 将密钥保管库支持添加到 ASP.NET 项目 - Azure 密钥保管库 | Microsoft Docs
description: 使用本教程来了解如何将 Key Vault 支持添加到 ASP.NET 或 ASP.NET Core Web 应用程序。
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: bbc9daf580d2f2641701c344d69e993ca7b2bd1c
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422837"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>使用 Visual Studio 连接服务将 Key Vault 添加到 Web 应用程序

本教程介绍如何轻松添加所需的设置，以开始使用 Azure Key Vault 在 Visual Studio 中管理 Web 项目的机密，不管使用的是 ASP.NET Core 还是任何类型的 ASP.NET 项目。 By using the Connected Services feature in Visual Studio, you can have Visual Studio automatically add all the NuGet packages and configuration settings you need to connect to Key Vault in Azure.

有关连接服务为了启用 Key Vault 而在项目中所做的更改的详细信息，请参阅 [Key Vault 连接服务 - 我的 ASP.NET 4.7.1 项目发生了什么情况](#how-your-aspnet-framework-project-is-modified)或 [Key Vault 连接服务 - 我的 ASP.NET Core 项目发生了什么情况](#how-your-aspnet-core-project-is-modified)。

## <a name="prerequisites"></a>必备组件

- 一个 Azure 订阅。 If you don't have a subscription, sign up for a [free account](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2019 version 16.3** or later, or **Visual Studio 2017 version 15.7** with the **Web Development** workload installed. [立即下载](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)。
- For ASP.NET (not Core) with Visual Studio 2017, you need the .NET Framework 4.7.1 or later Development Tools, which are not installed by default. 若要安装这些工具，请启动 Visual Studio 安装程序，依次选择“修改”、“单个组件”，在右侧展开“ASP.NET 和 Web 开发”，然后选择“.NET Framework 4.7.1 开发工具”。
- An ASP.NET 4.7.1 or later, or ASP.NET Core 2.0 or later web project open. With Visual Studio 2019, you need an ASP.NET Core web project; adding a Key Vault connected service to an ASP.NET project is not supported in Visual Studio 2019.

## <a name="add-key-vault-support-to-your-project"></a>将 Key Vault 支持添加到项目

Before you begin, make sure that you're signed into Visual Studio. Sign in with the same account that you use for your Azure subscription. Then open an ASP.NET 4.7.1 or later, or ASP.NET Core 2.0 web project, and do the follow steps:

1. In **Solution Explorer**, right-click the project that you want to add the Key Vault support to, and choose **Add** > **Connected Service**.
   此时会显示“连接服务”页，其中包含可添加到项目的服务。
1. 在可用服务的菜单中，选择“使用 Azure Key Vault 来保护机密”。

   ![选择“使用 Azure Key Vault 来保护机密”](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Select the subscription you want to use, and then choose a new or existing Key Vault. If you choose the new Key Vault, an **Edit** link appears. Select it to configure your new Key Vault.

   ![选择订阅](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. In **Edit Azure Key Vault**, enter the name you want to use for the Key Vault.

1. Select an existing **Resource Group**, or choose to create a new one with an automatically generated unique name.  If you want to create a new group with a different name, you can use the [Azure portal](https://portal.azure.com), and then close the page and restart to reload the list of resource groups.
1. Choose the **Location** in which to create the Key Vault. 如果 Web 应用程序托管在 Azure 中，请选择托管 Web 应用程序的区域，以获得最佳性能。
1. Choose a **Pricing tier**. 有关详细信息，请参阅 [Key Vault 定价](https://azure.microsoft.com/pricing/details/key-vault/)。
1. Choose **OK** to accept the configuration choices.
1. After you select an existing Key Vault or have configured a new Key Vault, in the **Azure Key Vault** tab of Visual Studio, select **Add** to add the Connected Service.
1. Select the **Manage secrets stored in this Key Vault** link to open the **Secrets** page for your Key Vault. If you closed the page or the project, you can navigate to it in the [Azure portal](https://portal.azure.com) by choosing **All Services** and, under **Security**, choosing **Key Vault**, then choose your Key Vault.
1. In the Key Vault section for the Key Vault you created, choose **Secrets**, then **Generate/Import**.

   ![生成/导入机密](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Enter a secret, such as *MySecret* and give it any string value as a test, then select the **Create** button.

   ![创建机密](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. （可选）输入另一个机密，但这一次通过将其命名为“Secrets--MySecret”将其放入某个类别。 This syntax specifies a category "Secrets" that contains a secret "MySecret".

现在，可以在代码中访问机密。 后续步骤根据使用的是 ASP.NET 4.7.1 还是 ASP.NET Core 而有所不同。

## <a name="access-your-secrets-in-code-aspnet-core"></a>Access your secrets in code (ASP.NET Core)

1. In Solution Explorer, right-click on your project, and select **Manage NuGet Packages**. In the **Browse** tab, locate and install these two NuGet packages: [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) and for .NET Core 2, add [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) or for .NET Core 3, add[Microsoft.Azure.KeyVault.Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core).

1. For .NET Core 2, select the `Program.cs` tab and change the `BuildWebHost` definition in the Program class to the following:

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

   For .NET Core 3, use the following code.

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

1. Next open one of the page files, such as *Index.cshtml.cs* and write the following code:
   1. Include a reference to `Microsoft.Extensions.Configuration` by this using directive:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Add the configuration variable.

      ```csharp
      private static readonly IConfiguration _configuration;
      ```

   1. Add this constructor or replace the existing constructor with this:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Update the `OnGet` method. Update the placeholder value shown here with the secret name you created in the above commands.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. To confirm the value at runtime, add code to display `ViewData["Message"]` to the *.cshtml* file to display the secret in a message.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

You can run the app locally to verify that the secret is obtained successfully from the Key Vault.

## <a name="access-your-secrets-aspnet"></a>Access your secrets (ASP.NET)

You can set up the configuration so that the web.config file has a dummy value in the `appSettings` element that is replaced by the true value at runtime. You can then access this via the `ConfigurationManager.AppSettings` data structure.

1. Edit your web.config file.  Find the appSettings tag, add an attribute `configBuilders="AzureKeyVault"`, and add a line:

   ```xml
      <add key="mysecret" value="dummy"/>
   ```

1. Edit the `About` method in *HomeController.cs*, to display the value for confirmation.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["mysecret"];
   }
   ```
1. Run the app locally under the debugger, switch to the **About** tab, and verify that the value from the Key Vault is displayed.

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，即可将其删除。 这将删除 Key Vault 及相关资源。 要通过门户删除资源组，请执行以下操作：

1. 在门户顶部的“搜索”框中输入资源组的名称。 在搜索结果中看到在本快速入门中使用的资源组后，将其选中。
2. 选择“删除资源组”。
3. In the **TYPE THE RESOURCE GROUP NAME:** box, enter in the name of the resource group and select **Delete**.

## <a name="troubleshooting"></a>故障排除

If your Key Vault is running on an different Microsoft account than the one you're logged in to Visual Studio (for example, the Key Vault is running on your work account, but Visual Studio is using your private account) you get an error in your Program.cs file, that Visual Studio can't get access to the Key Vault. 解决此问题：

1. Go to the [Azure portal](https://portal.azure.com) and open your Key Vault.

1. Choose **Access policies**, then **Add Access Policy**, and choose the account you are logged in with as Principal.

1. In Visual Studio, choose **File** > **Account Settings**.
Select **Add an account** from the **All account** section. Sign in with the account you have chosen as Principal of your access policy.

1. Choose **Tools** > **Options**, and look for **Azure Service Authentication**. Then select the account you just added to Visual Studio.

Now, when you debug your application, Visual Studio connects to the account your Key Vault is located on.

## <a name="how-your-aspnet-core-project-is-modified"></a>How your ASP.NET Core project is modified

This section identifies the exact changes made to an ASP.NET project when adding the Key Vault connected service using Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Added references for ASP.NET Core

Affects the project file .NET references and NuGet package references.

| Type | 参考 |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Added files for ASP.NET Core

- `ConnectedService.json` added, which records some information about the Connected Service provider, version, and a link the documentation.

### <a name="project-file-changes-for-aspnet-core"></a>Project file changes for ASP.NET Core

- Added the Connected Services ItemGroup and `ConnectedServices.json` file.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings.json changes for ASP.NET Core

- 将以下环境变量条目添加到 IIS Express 配置文件和匹配 Web 项目名称的配置文件：

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Changes on Azure for ASP.NET Core

- 创建了资源组（或使用了现有资源组）。
- 在指定的资源组中创建了密钥保管库。

## <a name="how-your-aspnet-framework-project-is-modified"></a>How your ASP.NET Framework project is modified

This section identifies the exact changes made to an ASP.NET project when adding the Key Vault connected service using Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Added references for ASP.NET Framework

Affects the project file .NET references and `packages.config` (NuGet references).

| Type | 参考 |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Added files for ASP.NET Framework

- `ConnectedService.json` added, which records some information about the Connected Service provider, version, and a link to the documentation.

### <a name="project-file-changes-for-aspnet-framework"></a>Project file changes for ASP.NET Framework

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

### <a name="changes-on-azure-for-aspnet-framework"></a>Changes on Azure for ASP.NET Framework

- 创建了资源组（或使用了现有资源组）。
- 在指定的资源组中创建了密钥保管库。

## <a name="next-steps"></a>后续步骤

If you followed this tutorial, your Key Vault permissions are set up to run with your own Azure subscription, but that might not be desirable for a production scenario. You can create a managed identity to manage Key Vault access for your app. See [Provide Key Vault authentication with a managed identity](/azure/key-vault/managed-identity).

Learn more about Key Vault development by reading the [Key Vault Developer's Guide](key-vault-developers-guide.md).
