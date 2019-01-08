---
title: 教程 - 如何将 Azure Key Vault 与 .NET Azure Web 应用配合使用 - Azure Key Vault | Microsoft Docs
description: 教程 - 将 ASP.NET Core 应用程序配置为从 Key Vault 读取机密
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: b6dbae0f721983920c2073927fff74100528678e
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998789"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>教程：如何将 Azure Key Vault 与 .NET Azure Web 应用配合使用

Azure Key Vault 可帮助你保护机密，例如 API 密钥和数据库连接字符串。 使用 Key Vault 可以访问应用程序、服务和 IT 资源。

本教程介绍如何创建可从 Azure Key Vault 读取信息的 Azure Web 应用程序。 该过程使用 Azure 资源的托管标识。 有关 Azure Web 应用的详细信息，请参阅 [Azure 应用服务](../app-service/overview.md)。

本文介绍以下操作：

> [!div class="checklist"]
> * 创建密钥保管库。
> * 在密钥保管库中存储机密。
> * 从密钥保管库检索机密。
> * 创建 Azure Web 应用程序。
> * 为 Web 应用启用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。
> * 授予所需的权限，让 Web 应用程序从密钥保管库读取数据。
> * 在 Azure 上运行 Web 应用程序。

在继续之前，请阅读 [Key Vault 的基本概念](key-vault-whatis.md#basic-concepts)。

## <a name="prerequisites"></a>先决条件

* 在 Windows 上：
  * [.NET Core 2.1 SDK 或更高版本](https://www.microsoft.com/net/download/windows)

* 在 Mac 上：
  * [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)

* 所有平台：
  * [Git](https://git-scm.com/downloads)
  * Azure 订阅 <br />（如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。）
  * 适用于 Windows、Mac 和 Linux 的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 或更高版本
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="managed-service-identity-and-how-it-works"></a>托管服务标识及其工作原理

Azure Key Vault 可以安全地存储凭据，因此不需要在代码中提供凭据。 但是，需要对 Azure Key Vault 进行身份验证才能检索密钥。 若要对 Key Vault 进行身份验证，需要提供凭据。 因此，在启动过程中，这是一个难以兼顾的典型问题。 托管服务标识 (MSI) 提供简化该过程的启动标识，可以解决此问题。

为 Azure 服务启用 MSI 时（例如：虚拟机、应用服务或 Functions），Azure 会创建[服务主体](key-vault-whatis.md#basic-concepts)。 MSI 针对 Azure Active Directory (Azure AD) 中的服务实例提供启动标识，并将服务主体的凭据注入该实例。

![MSI 示意图](media/MSI.png)

接下来，代码会调用 Azure 资源上提供的本地元数据服务，以获取访问令牌。 代码使用从本地 MSI_ENDPOINT 获取的访问令牌，以便向 Azure Key Vault 服务进行身份验证。

## <a name="sign-in-to-azure"></a>登录 Azure

若要使用 Azure CLI 登录到 Azure，请输入：

```azurecli
az login
```

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

1. 使用 [az group create](/cli/azure/group#az-group-create) 命令创建资源组。
1. 选择一个资源组名称，然后将其填充在占位符中。 以下示例在“美国西部”位置创建一个资源组：

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

本教程通篇使用此资源组。

## <a name="create-a-key-vault"></a>创建 key vault

若要在资源组中创建 Key Vault，请提供以下信息：

* Key Vault 名称：由 3 到 24 个字符构成的字符串，可以包含数字、字母和连字符（例如：0-9、a-z、A-Z 和 -）
* 资源组名称
* 位置：**美国西部**

在 Azure CLI 中输入以下命令：

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

目前，只有你的 Azure 帐户才有权对这个新保管库执行任何操作。

## <a name="add-a-secret-to-the-key-vault"></a>向密钥保管库添加机密

现在可以添加机密。 机密可以是 SQL 连接字符串，或者需要安全保存的、可供应用程序使用的其他任何信息。

键入以下命令，在名为 **AppSecret** 的 Key Vault 中创建机密。 此机密将存储值 **MySecret**。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

若要查看机密中包含的纯文本形式的值，请输入以下命令：

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

此命令显示机密信息，包括 URI。 完成这些步骤后，密钥保管库中会出现某个机密的 URI。 请记下此信息， 后面的步骤需要用到。

## <a name="create-a-net-core-web-app"></a>创建 .NET Core Web 应用

遵循[此教程](../app-service/app-service-web-get-started-dotnet.md)创建一个 .NET Core Web 应用，并将其**发布**到 Azure。 也可以观看以下视频：

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>打开并编辑解决方案

1. 浏览到“Pages” > “About.cshtml.cs”文件。
2. 安装以下 NuGet 包：
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. 在 About.cshtml.cs 文件中导入以下代码：

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

4. AboutModel 类中的代码应如下所示：

   ```csharp
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The below 4 lines of code shows you how to use AppAuthentication library to fetch secrets from your Key Vault*/
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;

                /* The below do while logic is to handle throttling errors thrown by Azure Key Vault. It shows how to do exponential backoff which is the recommended client side throttling*/
                do
                {
                    long waitTime = Math.Min(getWaitTime(retries), 2000000);
                    secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                    retry = false;
                } 
                while(retry && (retries++ < 10));
            }
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
                if((int)keyVaultException.Response.StatusCode == 429)
                    retry = true;
            }
        }

        // This method implements exponential backoff incase of 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-app"></a>运行应用

1. 在 Visual Studio 2017 的主菜单中，选择“调试” > “开始执行(调试)”或“开始执行(不调试)”。 
1. 显示浏览器时，转到“关于”页。
1. 此时会显示 **AppSecret** 的值。

## <a name="enable-a-managed-identity-for-the-web-app"></a>为 Web 应用启用托管标识

虽然 Azure Key Vault 提供安全存储凭据及其他机密的方式，但代码需要对 Key Vault 进行身份验证才能检索这些凭据和机密。 [Azure 资源的托管标识概述](../active-directory/managed-identities-azure-resources/overview.md)可帮助你解决此问题，其中介绍了如何在 Azure AD 中为 Azure 服务提供自动托管的标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务（包括 Key Vault）的身份验证，这样就无需在代码中插入任何凭据了。

1. 在 Azure CLI 中运行 assign-identity 命令，为此应用程序创建标识：

   ```azurecli

   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"

   ```

   >[!NOTE]
   >必须将 \<YourAppName\> 替换为 Azure 上发布的应用的名称。 例如，如果发布的应用名为 **MyAwesomeapp.azurewebsites.net**，请将 \<YourAppName\> 替换为 **MyAwesomeapp**。

1. 将应用程序发布到 Azure 时，请记下 `PrincipalId`。 步骤 1 中命令的输出应采用以下格式：

   ```json
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
   ```

>[!NOTE]
>此过程中的命令等同于转到[门户](https://portal.azure.com)并在 Web 应用程序属性中将“标识/系统分配”设置切换为“打开”。

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>为应用程序分配从 Key Vault 读取机密的权限

在以下命令中，请将 \<YourKeyVaultName\> 替换为 Key Vault 的名称，将 \<PrincipalId\> 替换为 **PrincipalId** 的值：

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

此命令为应用服务的标识 (MSI) 提供针对 Key Vault 执行 **get** 和 **list** 操作的权限。

## <a name="publish-the-web-application-to-azure"></a>将 Web 应用程序发布到 Azure

再次将 Web 应用发布到 Azure，以查看实时 Web 应用是否可以提取机密值。

1. 在 Visual Studio 中选择 **key-vault-dotnet-core-quickstart** 项目。
2. 选择“发布” > “开始”。
3. 选择“创建”。

运行该应用程序时应会看到，它可以检索机密值。

现已成功地在 .NET 中创建一个 Web 应用，该应用可在 Key Vault 中存储和提取其机密。

## <a name="next-steps"></a>后续步骤

>[!div class="nextstepaction"]
>[Azure Key Vault 开发人员指南](https://docs.microsoft.com/azure/key-vault/key-vault-developers-guide)
