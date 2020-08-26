---
title: 安全地保存 Web 应用程序的机密应用程序设置 - Azure 密钥保管库 | Microsoft Docs
description: 如何使用 ASP.NET Core 密钥保管库提供程序、用户密钥或 .NET 4.7.1 配置生成器来安全地保存密钥应用程序设置（如 Azure 凭据或第三方 API 密钥）
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 07/17/2019
ms.author: cawa
ms.openlocfilehash: df2c626de39ff4482a4dc69fa5a514fc92002ccb
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705854"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>安全地保存 Web 应用的密钥应用程序设置

## <a name="overview"></a>概述
本文介绍如何安全地保存 Azure 应用程序的密钥应用程序配置设置。

传统上，所有 Web 应用配置设置都保存在配置文件（如 Web.config）中。这一做法会导致将密钥设置（如云凭据）签入到公共源代码管理系统（如 GitHub）中。 同时，因为更改源代码和重新配置开发设置都需要开销，因此会难以遵循安全最佳实践。

为了确保开发过程的安全性，创建了工具和框架库，以安全地保存应用程序机密设置，并最大程度地减少对源代码的更改或不对其进行更改。

## <a name="aspnet-and-net-core-applications"></a>ASP.NET 和 .NET Core 应用程序

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>将密钥设置保存在源代码管理文件夹外部的用户密钥存储中。
如果你正在快速建立原型或无法访问 Internet，请先将源代码管理文件夹外部的密钥设置移动到用户密钥存储。 用户密钥存储是保存在用户探查器文件夹下的一个文件，因此密钥不会签入到源代码管理。 下图演示了[用户密钥](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio)的工作原理。

![用户密钥将密钥设置保持在源代码管理外部](../media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

如果正在运行 .NET Core 控制台应用程序，请使用密钥保管库来安全地保存你的密钥。

### <a name="save-secret-settings-in-azure-key-vault"></a>在 Azure 密钥保管库中保存密钥设置
如果正在开发一个项目，并需要安全地共享源代码，请使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。

1. 在 Azure 订阅中创建密钥保管库。 填写 UI 上的所有必填字段，然后单击边栏选项卡底部的“创建” 

    ![创建 Azure Key Vault](../media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. 授予你和团队成员访问密钥保管库的权限。 如果你的团队规模较大，可以创建 [Azure Active Directory 组](../../active-directory/active-directory-groups-create-azure-portal.md)并将该安全组访问权限添加到密钥保管库。 在“密钥权限”  下拉列表中，检查“密钥管理操作”  下的“获取”  和“列表”  。
如果已创建 Web 应用，请向 Web 应用授予对 Key Vault 的访问权限，以便它可以访问密钥保管库，而无需在应用设置或文件中存储机密配置。 按名称搜索 Web 应用，并以向用户授予访问权限的相同方式添加该应用。

    ![添加密钥保管库访问策略](../media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. 在 Azure 门户中将机密添加到 Key Vault。 对于嵌套的配置设置，请将“:”替换为“--”，以使密钥保管库密钥名称有效。 不能在密钥保管库密钥的名称中使用“:”。

    ![添加密钥保管库密钥](../media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE]
    > 在 Visual Studio 2017 V15.6 之前，我们曾建议安装 Visual Studio 的 Azure 服务身份验证扩展。 但是现在该扩展已弃用，因为它的功能已集成在 Visual Studio 中。 因此，如果你使用的是旧版本的 Visual Studio 2017，我们建议你更新至至少 VS 2017 15.6 或更高版本，以便可以本机使用此功能并使用 Visual Studio 登录标识本身访问密钥保管库。
    >

4. 将以下 NuGet 包添加到项目：

    ```
    Microsoft.Azure.KeyVault
    Microsoft.Azure.Services.AppAuthentication
    Microsoft.Extensions.Configuration.AzureKeyVault
    ```
5. 将以下代码添加到 Program.cs 文件：

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
             Host.CreateDefaultBuilder(args)
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
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
6. 将密钥保管库 URL 添加到 launchsettings.json 文件。 环境变量名称 KEYVAULT_ENDPOINT 将在步骤 6 添加的代码中进行定义  。

    ![将密钥保管库 URL 添加为项目环境变量](../media/vs-secure-secret-appsettings/add-keyvault-url.png)

7. 开始调试项目。 它应已成功运行。

## <a name="aspnet-and-net-applications"></a>ASP.NET 和 .NET 应用程序

.NET 4.7.1 支持密钥保管库和密钥配置生成器，这可以确保在不更改代码的情况下将密钥移动到源代码管理文件夹的外部。
若要继续，请[下载 .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115)，如果应用程序使用的是较旧版本的 .NET Framework，则将其进行迁移。

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>将密钥设置保存在源代码管理文件夹外部的密钥文件中。
如果正在快速编写原型，且不想预配 Azure 资源，请使用此选项。

1. 右键单击该项目，然后选择 " **管理用户机密**"。 这会安装 **Microsoft.Configuration.ConfigUserSecrets** 的 NuGet 包，创建用于保存 web.config 文件之外的机密设置的文件，并在 web.config 文件中添加节 **ConfigBuilders** 。

2. 将机密设置置于 root 元素下。 下面是一个示例

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <root>
      <secrets ver="1.0">
        <secret name="secret" value="foo"/>
        <secret name="secret1" value="foo_one" />
        <secret name="secret2" value="foo_two" />
      </secrets>
    </root>
    ```

3. 指定 appSettings 部分使用密钥配置生成器。 确保有含有虚拟值的机密设置的条目。

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. 调试应用。 它应已成功运行。

### <a name="save-secret-settings-in-an-azure-key-vault"></a>在 Azure Key Vault 中保存密钥设置
按照 ASP.NET Core 部分中的说明为你的项目配置密钥保管库。

1. 将以下 NuGet 包安装到你的项目
   ```
   Microsoft.Configuration.ConfigurationBuilders.Azure
   ```

2. 在 Web.config 中定义 Key Vault 配置生成器。请将此部分放置在 *appSettings* 部分之前。 如果你的 Key Vault 在全局 Azure 中，请将 *vaultName* 替换为 Key Vault 名称; 如果你使用的是主权 cloud，则将其替换为完整 URI。

    ```xml
     <configBuilders>
        <builders>
            <add name="Secrets" userSecretsId="695823c3-6921-4458-b60b-2b82bbd39b8d" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.UserSecrets, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
            <add name="AzureKeyVault" vaultName="[VaultName]" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
        </builders>
      </configBuilders>
    ```
3. 指定 appSettings 部分使用密钥保管库配置生成器。 确保有任何含有虚拟值的密钥设置的条目。

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. 开始调试项目。 它应已成功运行。
