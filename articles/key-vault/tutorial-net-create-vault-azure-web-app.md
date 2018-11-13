---
title: 教程 - 如何将 Azure Key Vault 与通过 .NET 编写的 Azure Web 应用配合使用 |Microsoft Docs
description: 教程：将 ASP.NET Core 应用程序配置为从 Key Vault 读取机密
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 9cc22e158a9473b7b60f7e8bcb57174abc1fb8cc
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218546"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-web-app-in-net"></a>教程：如何将 Azure Key Vault 与以 .NET 编写的 Azure Web 应用配合使用

Azure Key Vault 用于保护机密，例如访问应用程序、服务和 IT 资源所需的 API 密钥、数据库连接字符串。

本教程演练将 Azure Web 应用程序配置为使用 Azure 资源的托管标识，以从 Azure Key Vault 读取信息所要执行的步骤。 本教程基于 [Azure Web 应用](../app-service/app-service-web-overview.md)。 下面介绍如何：

> [!div class="checklist"]
> * 创建密钥保管库。
> * 在密钥保管库中存储机密。
> * 从密钥保管库检索机密。
> * 创建 Azure Web 应用程序。
> * 为 Web 应用启用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。
> * 授予所需的权限，让 Web 应用程序从密钥保管库读取数据。
> * 在 Azure 上运行 Web 应用程序

在我们进一步讨论之前，请阅读[基本概念](key-vault-whatis.md#basic-concepts)。

## <a name="prerequisites"></a>先决条件

* 在 Windows 上：
  * [.NET Core 2.1 SDK 或更高版本](https://www.microsoft.com/net/download/windows)

* 在 Mac 上：
  * 请参阅 [Visual Studio for Mac 中的新增功能](https://visualstudio.microsoft.com/vs/mac/)。

* 所有平台：
  * Git（[下载](https://git-scm.com/downloads)）。
  * Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 或更高版本。 适用于 Windows、Mac 和 Linux。
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>什么是托管服务标识？其工作原理是什么？
 在进一步讨论之前，让我们了解 MSI。 Azure Key Vault 可以安全地存储凭据，因此不需将凭据置于代码中，但若要检索这些凭据，需向 Azure Key Vault 进行身份验证。 若要向 Key Vault 进行身份验证，需提供凭据！ 经典的启动问题。 通过 Azure 和 Azure AD，MSI 提供一个“启动标识”，可以大为简化启动过程。

工作方式如下！ 为 Azure 服务（例如虚拟机、应用服务或 Functions）启用 MSI 时，Azure 会为 Azure Active Directory 中的服务实例创建一个[服务主体](key-vault-whatis.md#basic-concepts)，并将服务主体的凭据注入服务实例中。 

![MSI](media/MSI.png)

接下来，代码会调用 Azure 资源上提供的本地元数据服务，以便获取访问令牌。
代码使用从本地 MSI_ENDPOINT 获取的访问令牌，以便向 Azure Key Vault 服务进行身份验证。 

现在，让我们开始本教程。

## <a name="log-in-to-azure"></a>登录 Azure

若要使用 Azure CLI 登录到 Azure，请输入：

```azurecli
az login
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#az-group-create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

选择一个资源组名称，然后将其填充在占位符中。
以下示例在“美国西部”位置创建一个资源组：

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

刚刚创建的资源组将在整篇文章中使用。

## <a name="create-a-key-vault"></a>创建 key vault

接下来，在上一步骤创建的资源组中创建密钥保管库。 提供以下信息：

* 密钥保管库名称：名称必须为 3-24 个字符的字符串，并且只能包含 0-9、a-z、A-Z 和 -。
* 资源组名称。
* 位置：**美国西部**。

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

目前，只有你的 Azure 帐户才有权对这个新保管库执行任何操作。

## <a name="add-a-secret-to-the-key-vault"></a>向密钥保管库添加机密

我们将添加机密以帮助说明这是如何工作的。 可以存储需要安全保存的，但同时也要提供给应用程序使用的 SQL 连接字符串或其他任何信息。

键入以下命令，在名为 **AppSecret** 的密钥保管库中创建机密。 此机密将存储值“MySecret”。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

若要查看机密中包含的纯文本形式的值，请执行以下命令：

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

此命令显示机密信息，包括 URI。 完成这些步骤后，密钥保管库中会出现某个机密的 URI。 请记下此信息， 后面的步骤需要用到。

## <a name="create-a-net-core-web-app"></a>创建一个 .NET Core Web 应用

按此[教程](../app-service/app-service-web-get-started-dotnet.md)的说明创建一个 .NET Core Web 应用，然后将其**发布**到 Azure **或**观看下面的视频
> [!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>打开并编辑解决方案

1. 导航到“页面”> About.cshtml.cs 文件。
2. 安装这 2 个 Nuget 包
    - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
    - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. 在 About.cshtml.cs 文件中导入以下内容

    ```
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
    ```
4. AboutModel 类中的代码应如下所示
    ```
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

在 Visual Studio 2017 的主菜单中，选择“调试” > “在调试/不调试的情况下启动”。 显示浏览器时，转到“关于”页。 此时会显示 **AppSecret** 的值。

## <a name="enable-a-managed-identity-for-the-web-app"></a>为 Web 应用启用托管标识

虽然 Azure Key Vault 可用于安全存储凭据以及其他密钥和机密，但代码需要通过 Key Vault 的身份验证才能检索它们。 [Azure 资源的托管标识概述](../active-directory/managed-identities-azure-resources/overview.md)为 Azure 服务提供了 Azure Active Directory (Azure AD) 中的自动托管标识，更巧妙地解决了这个问题。 此标识可用于通过支持 Azure AD 身份验证的任何服务（包括 Key Vault）的身份验证，这样就无需在代码中插入任何凭据了。

1. 返回 Azure CLI。
2. 运行 assign-identity 命令，为此应用程序创建标识： 

   ```azurecli
   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
   ```
   请注意，必须将 <YourAppName> 替换为 Azure 上已发布应用的名称，也就是说，如果已发布应用的名称为 MyAwesomeapp.azurewebsites.net，则将 <YourAppName> 替换为 MyAwesomeapp
 
 上述命令的输出如下所示。请记下将应用程序发布到 Azure 时的 PrincipalId。 它应该采用以下格式：
   ```
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
  ```
>[!NOTE]
>此过程中的命令等同于转到[门户](https://portal.azure.com)并在 Web 应用程序属性中将“标识/系统分配”设置切换为“打开”。

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>为应用程序分配从 Key Vault 读取机密的权限
        
然后，使用密钥保管库名称和 **PrincipalId** 值运行以下命令：

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

## <a name="publish-the-web-application-to-azure"></a>将 Web 应用程序发布到 Azure

再次将此应用发布到 Azure，看其是否可以作为 Web 应用运行，并看能否提取机密值。

1. 在 Visual Studio 中选择 **key-vault-dotnet-core-quickstart** 项目。
2. 选择“发布” > “开始”。
3. 选择“创建”。

上面的命令中向应用服务的标识 (MSI) 提供了在 Key Vault 上执行“get”和“list”操作的权限。 <br />
现在运行应用程序时，会看到检索到的机密值。 

这就是所有的操作。 现已成功地在 .NET 中创建 Web 应用，该应用可以在 Key Vault 中存储和提取其机密。
