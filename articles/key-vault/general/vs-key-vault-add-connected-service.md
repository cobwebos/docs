---
title: 使用 Visual Studio 将密钥保管库支持添加到 ASP.NET 项目 - Azure 密钥保管库 | Microsoft Docs
description: 本教程介绍如何将 Key Vault 支持添加到 ASP.NET 或 ASP.NET Core Web 应用程序。
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure, devx-track-csharp
ms.topic: how-to
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: addccf337f82b1695c76ae975c4a33f44ba50f8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448135"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>使用 Visual Studio 连接服务将 Key Vault 添加到 Web 应用程序

本教程介绍如何轻松添加所需的设置，以开始使用 Azure Key Vault 在 Visual Studio 中管理 Web 项目的机密，不管使用的是 ASP.NET Core 还是任何类型的 ASP.NET 项目。 使用 Visual Studio 中的连接服务功能，可让 Visual Studio 自动添加所需的所有 NuGet 包和配置设置，以连接到 Azure 中的 Key Vault。

有关连接服务为了启用 Key Vault 而在项目中所做的更改的详细信息，请参阅 [Key Vault 连接服务 - 我的 ASP.NET 4.7.1 项目发生了什么情况](#how-your-aspnet-framework-project-is-modified)或 [Key Vault 连接服务 - 我的 ASP.NET Core 项目发生了什么情况](#how-your-aspnet-core-project-is-modified)。

## <a name="prerequisites"></a>先决条件

- **一个 Azure 订阅**。 如果没有订阅，请注册 [免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
- Visual Studio 2019 版本 16.3 或更高版本 [立即下载](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)。


## <a name="add-key-vault-support-to-your-project"></a>将 Key Vault 支持添加到项目

在开始之前，请确保已登录 Visual Studio： 登录时使用的帐户与用于 Azure 订阅的帐户相同。 然后打开 ASP.NET 4.7.1 或更高版本或 ASP.NET Core 2.0 Web 项目，执行以下步骤：

1. 在“解决方案资源管理器”中，右键单击要将 Key Vault 支持添加到其中的项目，然后选择“添加” > “连接服务” > “添加”。   
   此时会显示“连接服务”页，其中包含可添加到项目的服务。
1. 在可用服务的菜单中，选择“Azure Key Vault”，然后单击“下一步”。

   ![选择“Azure Key Vault”](../media/vs-key-vault-add-connected-service/key-vault-connected-service.png)

1. 选择要使用的订阅，接着选择现有的 Key Vault，然后单击“完成”。 

   ![选择订阅](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

现在已建立与 Key Vault 的连接，你可以通过代码访问机密了。 后续步骤根据使用的是 ASP.NET 4.7.1 还是 ASP.NET Core 而有所不同。

## <a name="access-your-secrets-in-code-aspnet-core"></a>在代码中访问机密 (ASP.NET Core)

1. 打开页面文件之一（例如 Index.cshtml.cs）并编写以下代码：
   1. 通过此 using 指令包含对 `Microsoft.Extensions.Configuration` 的引用：

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. 添加配置变量。

      ```csharp
      private static IConfiguration _configuration;
      ```

   1. 添加此构造函数，或将现有的构造函数替换为：

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
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameStoredInKeyVault>"];
       }
       ```

   1. 若要在运行时确认该值，请在 *.cshtml* 文件中添加用于显示 `ViewData["Message"]` 的代码，以显示消息中的机密。

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

可以在本地运行应用，以验证是否能够成功地从 Key Vault 获取机密。

## <a name="access-your-secrets-aspnet"></a>访问机密 (ASP.NET)
可以设置配置，使 web.config 文件中的 `appSettings` 元素中包含一个虚拟值，在运行时，该值将由 true 值替换。 然后，可以通过 `ConfigurationManager.AppSettings` 数据结构访问此配置。

1. 在解决方案资源管理器中右键单击项目，然后选择“管理 NuGet 包”。 在“浏览”选项卡中，找到并安装 [Microsoft.Configuration.ConfigurationBuilders.Azure](https://www.nuget.org/packages/Microsoft.Configuration.ConfigurationBuilders.Azure/)
 
1. 打开 web.config 文件并编写以下代码：
    1. 添加 `configSections` 和 `configBuilders`：
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
    1. 找到 appSettings 标记，添加属性 `configBuilders="AzureKeyVault"`，然后添加一行：
        ```xml
         <add key="<secretNameInYourKeyVault>" value="dummy"/>
        ```

1. 编辑 *HomeController.cs* 中的 `About` 方法，以显示要确认的值。

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["<secretNameInYourKeyVault>"];
   }
   ```
1. 在调试器本地运行应用，切换到“关于”选项卡，确认是否显示了 Key Vault 中的值。

## <a name="troubleshooting"></a>故障排除

如果运行 Key Vault 的 Microsoft 帐户不是用于登录 Visual Studio 的帐户（例如，Key Vault 在工作帐户中运行，但 Visual Studio 使用的是私人帐户），则 Program.cs 文件中会出现一条错误，指出 Visual Studio 无法访问 Key Vault。 解决此问题：

1. 转到 [Azure 门户](https://portal.azure.com)并打开 Key Vault。

1. 依次选择“访问策略”、“添加访问策略”，然后选择用作主体的用于登录的帐户。 

1. 在 Visual Studio 中，选择“文件” > “帐户设置”。 
在“所有帐户”部分选择“添加帐户”。  使用选作访问策略主体的帐户登录。

1. 选择“工具” > “选项”，找到“Azure 服务身份验证”。   然后选择刚刚添加到 Visual Studio 的帐户。

现在，在调试应用程序时，Visual Studio 将连接到 Key Vault 所在的帐户。

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
| .NET; NuGet | Azure.Identity |
| .NET; NuGet | Azure.Security.KeyVault.Keys |
| .NET; NuGet | Azure.Security.KeyVault.Secrets |

> [!IMPORTANT] 
> 默认情况下，Azure 会安装，它不支持 Visual Studio 凭据。 你可以将包引用手动更新为 1.2 + 使用 Visual Studio 凭据。

### <a name="added-files-for-aspnet-framework"></a>为 ASP.NET Framework 添加了文件

- 添加了 `ConnectedService.json`，其中记录了有关连接服务提供程序、版本和指向文档的链接的一些信息。

### <a name="project-file-changes-for-aspnet-framework"></a>ASP.NET Framework 的项目文件更改

- 添加了连接服务 ItemGroup 和 ConnectedServices.json 文件。
- 请参考[添加的引用](#added-references-for-aspnet-framework)部分中所述的 .NET 程序集。

## <a name="next-steps"></a>后续步骤

如果已根据本教程完成上述操作，则为你设置的 Key Vault 权限适合与你自己的 Azure 订阅配合运行，但可能不适合于生产方案。 可以创建托管标识来管理应用的 Key Vault 访问权限。 请参阅[如何向 Key Vault 进行身份验证](/azure/key-vault/general/authentication)和[分配 Key Vault 访问策略](/azure/key-vault/general/assign-access-policy-portal)。

在 [Key Vault 开发人员指南](developers-guide.md)中了解如何使用 Key Vault 进行开发。
