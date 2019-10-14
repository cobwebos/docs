---
title: 关于在 ASP.NET Core 应用中使用 Azure 应用程序配置 Key Vault 引用的教程 | Microsoft Docs
description: 本教程介绍如何在 ASP.NET Core 应用中使用 Azure 应用程序配置的 Key Vault 引用
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: a14cb3035c159c82df44f686da7f7b78ef942943
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035812"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>教程：在 ASP.NET Core 应用中使用 Key Vault 引用

本教程介绍如何将 Azure 应用程序配置服务与 Azure Key Vault 配合使用。 这些是互补的服务，在大多数应用程序部署中可以并行使用。 为了方便你将它们结合在一起使用，应用程序配置允许你创建密钥来引用存储在 Key Vault 中的值。 当你这样做时，应用程序配置会存储 Key Vault 值的 URI，而不是值本身。 应用程序会使用应用程序配置客户端提供程序检索该密钥的值，就像对待应用程序配置中存储的任何其他密钥一样。 客户端提供程序将其视为 Key Vault 引用，通过调用 Key Vault 来检索值。 应用程序负责向应用程序配置和 Key Vault 进行适当的身份验证。 这两项服务不直接通信。

本教程介绍如何在代码中实现 Key Vault 引用。 它建立在快速入门中介绍的 Web 应用之上。 在继续操作之前，请先完成[使用应用程序配置创建 ASP.NET Core 应用](./quickstart-aspnet-core-app.md)。

你可以使用任何代码编辑器执行本教程中的步骤。 [Visual Studio Code](https://code.visualstudio.com/) 是 Windows、macOS 和 Linux 平台上提供的一个卓越选项。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建一个应用程序配置密钥，以便引用在 Key Vault 中存储的值
> * 从 ASP.NET Core Web 应用程序访问该密钥的值

## <a name="prerequisites"></a>先决条件

若要完成本教程，请安装 [.NET Core SDK](https://dotnet.microsoft.com/download)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>创建保管库

1. 选择 Azure 门户左上角的“创建资源”选项 

    ![Key Vault 创建完成后的输出](./media/quickstarts/search-services.png)
2. 在“搜索”框中输入“Key Vault”  。
3. 从结果列表中选择“Key Vault”  。
4. 在“Key Vault”部分，选择“创建”  。
5. 在“创建密钥保管库”部分，提供以下信息： 
    - **名称**：必须提供唯一的名称。 对于本快速入门，我们使用 **Contoso-vault2**。 
    - **订阅**：选择订阅。
    - 在“资源组”  下选择“新建”  ，然后输入资源组名称。
    - 在“位置”下拉菜单中选择一个位置。 
    - 让其他选项保留默认值。
6. 提供上述信息后，选择“创建”  。

目前，只有你的 Azure 帐户有权访问这个新保管库。

![Key Vault 创建完成后的输出](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>向 Key Vault 添加机密

只需再执行几个步骤即可向保管库添加机密。 在此示例中，我们将添加一条消息，用于测试 Key Vault 检索功能。 此消息名为 **Message**，我们在其中存储的值为“Hello from Key Vault”。 

1. 在 Key Vault 属性页中，选择“机密”  。
1. 单击“生成/导入”  。
1. 在“创建机密”  屏幕上，选择以下值：
    - **上传选项**：手动。
    - **名称**：消息
    - **值**：Hello from Key Vault
    - 让其他值保留默认设置。 单击“创建”。 

## <a name="add-a-key-vault-reference-to-app-config"></a>向应用程序配置添加 Key Vault 引用

1. 登录到 [Azure 门户](https://portal.azure.com)。 选择“所有资源”，然后选择在快速入门中创建的应用程序配置存储区实例  。

1. 单击“配置资源管理器” 

1. 单击“+ 创建”   >   “Key Vault 引用”，然后选择以下值：
    - **密钥**：TestApp:Settings:KeyVaultMessage
    - **标签**：留空
    - **订阅**、**资源组**、**密钥保管库**：选择与在上一部分创建的 Key Vault 相对应的选项。
    - **机密**：选择在上一部分创建的名为 **Message** 的机密。

## <a name="connect-to-key-vault"></a>连接到 Key Vault

1. 在本教程中，我们将使用一个服务主体向 KeyVault 进行身份验证。 若要创建该服务主体，请使用 Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令：

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    此操作将返回一系列键/值对。 

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. 运行以下命令，允许服务主体访问密钥保管库：

        az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey

1. 向机密管理器添加 *clientId* 和 *clientSecret* 的机密。 必须在 .csproj 文件所在的目录中执行以下命令  。

        dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>        
        dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>

> [!NOTE]
> 这些 Key Vault 凭据只在应用程序中使用。 应用程序使用这些凭据直接向 Key Vault 进行身份验证。 这些凭据从不传递到应用程序配置服务。

## <a name="update-your-code-to-use-a-key-vault-reference"></a>更新代码以使用 Key Vault 引用

1. 打开 *Program.cs*，添加对所需包的引用。

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. 通过调用 `config.AddAzureAppConfiguration()` 方法，更新 `CreateWebHostBuilder` 方法以使用应用配置。 包括 `UseAzureKeyVault` 选项，传入对 Key Vault 的全新 `KeyVaultClient` 引用。

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                KeyVaultClient kvClient = new KeyVaultClient(async (authority, resource, scope) =>
                {
                    var adCredential = new ClientCredential(settings["ConnectionStrings:KeyVaultClientId"], settings["ConnectionStrings:KeyVaultClientSecret"]);
                    var authenticationContext = new AuthenticationContext(authority, null);
                    return (await authenticationContext.AcquireTokenAsync(resource, adCredential)).AccessToken;
                });

                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .UseAzureKeyVault(kvClient); });
            })
            .UseStartup<Startup>();
    ```

1. 在初始化应用程序配置连接的过程中将 *KeyVaultClient* 引用传递到 `UseAzureKeyVault` 方法以后，即可访问 Key Vault 引用的值，所用方式与访问常规应用程序配置密钥的值时所用的方式相同。 若要在实际操作中查看此过程，请在“视图”>“主页”目录中打开 *Index.cshtml*。 将其内容替换为以下代码：

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    访问 Key Vault 引用 *TestApp:Settings:KeyVaultMessage* 的值时，所用方式与访问配置值 *TestApp:Settings:Message* 时所用的方式相同

## <a name="build-and-run-the-app-locally"></a>在本地生成并运行应用

1. 要通过使用 .NET Core CLI 生成应用，请在命令行界面中执行以下命令：

        dotnet build

2. 生成成功完成后，请运行以下命令以在本地运行 Web 应用：

        dotnet run

3. 启动浏览器窗口并转到 `http://localhost:5000`，即本地托管的 Web 应用的默认 URL。

    ![本地启动应用快速入门](./media/key-vault-reference-launch-local.png)


## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，通过添加 Azure 托管服务标识简化了应用配置访问并改进了应用凭据管理。 若要了解有关如何使用应用程序配置的更多信息，请继续阅读 Azure CLI 示例。

> [!div class="nextstepaction"]
> [CLI 示例](./cli-samples.md)
