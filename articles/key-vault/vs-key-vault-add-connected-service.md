---
title: 使用 Visual Studio 将 Key Vault 支持添加到 ASP.NET 项目 | Microsoft Docs
description: 本教程介绍如何将 Key Vault 支持添加到 ASP.NET 或 ASP.NET Core Web 应用程序。
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: b4fed559b6364149170dc8b1da421c9c3ee1203c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635757"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>使用 Visual Studio 连接服务将 Key Vault 添加到 Web 应用程序

本教程介绍如何轻松添加所需的设置，以开始使用 Azure Key Vault 在 Visual Studio 中管理 Web 项目的机密，不管使用的是 ASP.NET Core 还是任何类型的 ASP.NET 项目。 使用 Visual Studio 2017 中的连接服务功能，可让 Visual Studio 自动添加所需的所有 NuGet 包和配置设置，以连接到 Azure 中的 Key Vault。 

有关连接服务为了启用 Key Vault 而在项目中所做的更改的详细信息，请参阅 [Key Vault 连接服务 - 我的 ASP.NET 4.7.1 项目发生了什么情况](vs-key-vault-aspnet-what-happened.md)或 [Key Vault 连接服务 - 我的 ASP.NET Core 项目发生了什么情况](vs-key-vault-aspnet-core-what-happened.md)。

## <a name="prerequisites"></a>先决条件

- **一个 Azure 订阅**。 如果没有帐户，可以注册一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
- **Visual Studio 2017 版本 15.7**（装有 **Web 开发**工作负荷）。 [立即下载](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)。
- 对于 ASP.NET（非 Core），需要安装 .NET Framework 4.7.1 开发工具，默认情况下未安装这些工具。 若要安装这些工具，请启动 Visual Studio 安装程序，依次选择“修改”、“单个组件”，在右侧展开“ASP.NET 和 Web 开发”，然后选择“.NET Framework 4.7.1 开发工具”。
- 已打开一个 ASP.NET 4.7.1 或 ASP.NET Core 2.0 Web 项目。

## <a name="add-key-vault-support-to-your-project"></a>将 Key Vault 支持添加到项目

1. 在“解决方案资源管理器”中，选择“添加” > “连接服务”。
   此时会显示“连接服务”页，其中包含可添加到项目的服务。
1. 在可用服务的菜单中，选择“使用 Azure Key Vault 来保护机密”。

   ![选择“使用 Azure Key Vault 来保护机密”](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   如果已登录到 Visual Studio，并且有与帐户关联的 Azure 订阅，则会显示一个页面，其中提供了订阅下拉列表。
1. 选择要使用的订阅，然后选择新的或现有的 Key Vault，或选择“编辑”链接来修改自动生成的名称。

   ![选择订阅](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. 键入 Key Vault 的名称。

   ![重命名 Key Vault，并选择一个资源组](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. 选择现有资源组，或选择使用自动生成的唯一名称创建新的资源组。  如果想要使用不同的名称创建新组，可以使用 [Azure 门户](https://portal.azure.com)，然后关闭页面并重启，以重新加载资源组列表。
1. 选择要在其中创建 Key Vault 的区域。 如果 Web 应用程序托管在 Azure 中，请选择托管 Web 应用程序的区域，以获得最佳性能。
1. 选择定价模型。 有关详细信息，请参阅 [Key Vault 定价](https://azure.microsoft.com/pricing/details/key-vault/)。
1. 选择“确定”接受配置选项。
1. 选择“添加”创建 Key Vault。 如果选择的名称已被使用，则创建过程可能会失败。  如果发生这种情况，请使用“编辑”链接重命名 Key Vault，然后重试。

   ![将连接服务添加到项目](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. 现在，请在 Azure 上的 Key Vault 中添加机密。 若要转到门户中的相应位置，请单击“管理此 Key Vault 中存储的机密”对应的链接。 如果已关闭该页或项目，可以在 [Azure 门户](https://portal.azure.com)中通过选择“安全性”下的“所有服务”导航到它，选择 **Key Vault**，然后选择刚刚创建的 Key Vault。

   ![导航到门户](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. 在创建的 Key Vault 的 Key Vault 部分，依次选择“机密”、“生成/导入”。

   ![生成/导入机密](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. 输入一个机密（例如“MySecret”），并为其提供任何字符串值作为测试，然后选择“创建”按钮。

   ![创建机密](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. （可选）输入另一个机密，但这一次通过将其命名为“Secrets--MySecret”将其放入某个类别。 此语法指定的类别“Secrets”包含机密“MySecret”。
 
现在，可以在代码中访问机密。 后续步骤根据使用的是 ASP.NET 4.7.1 还是 ASP.NET Core 而有所不同。

## <a name="access-your-secrets-in-code-aspnet-core-projects"></a>在代码中访问机密（ASP.NET Core 项目）

1. 在 Visual Studio 中的 ASP.NET Core 项目内，现在可以通过在代码中使用以下表达式来引用这些机密：
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

1. 在某个 .cshtml 页面（例如 About.cshtml）上，在文件的靠近顶部位置添加 @inject 指令，以设置可用于访问 Key Vault 配置的变量。

   ```cshtml
      @inject Microsoft.Extensions.Configuration.IConfiguration config
   ```

1. 作为测试，可以通过在某个页面上显示机密，来确认该机密的值是否可用。 使用 @config 引用配置变量。
 
   ```cshtml
      <p> @config["MySecret"] </p>
      <p> @config.GetSection("Secrets")["MySecret"] </p>
      <p> @config["Secrets:MySecret"] </p>
   ```

1. 生成并运行 Web 应用程序，导航到 About 页面，并查看“secret”值。

## <a name="access-your-secrets-in-code-aspnet-471-projects"></a>在代码中访问机密（ASP.NET 4.7.1 项目）

1. 按如下所示修改 web.config。 这些键是占位符，AzureKeyVault ConfigurationBuilder 会将其替换为 Key Vault 中的机密值。

   ```xml
     <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="MySecret" value="dummy1"/>
       <add key="Secrets--MySecret" value="dummy2"/>
     </appSettings>
   ```

1. 在 HomeController 的 About 控制器方法中添加以下行，以检索机密并将其存储在 ViewBag 中。
 
   ```csharp
            var secret = ConfigurationManager.AppSettings["MySecret"];
            var secret2 = ConfigurationManager.AppSettings["Secrets--MySecret"];
            ViewBag.Secret = $"Secret: {secret}";
            ViewBag.Secret2 = $"Secret2: {secret2}";
   ```

1. 在 About.cshtml 视图中添加以下内容，以显示机密值（仅限测试）。

   ```csharp
      <h3>@ViewBag.Secret</h3>
      <h3>@ViewBag.Secret2</h3>
   ```

恭喜，现已确认 Web 应用能够使用 Key Vault 安全访问存储的机密。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将其删除。 这会删除 Key Vault 和相关的资源。 若要通过门户删除资源组，请执行以下操作：

1. 在门户顶部的“搜索”框中输入资源组的名称。 在搜索结果中看到在本快速入门中使用的资源组后，请将其选中。
2. 选择“删除资源组”。
3. 在“键入资源组名称:”框中，键入资源组的名称，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

在 [Key Vault 开发人员指南](key-vault-developers-guide.md)中了解如何使用 Key Vault 进行开发