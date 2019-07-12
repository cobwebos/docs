---
title: Azure Active Directory 基于证书的身份验证使用 Azure Cosmos DB
description: 了解如何从 Azure Cosmos DB 将 Azure AD 标识进行基于证书的身份验证配置为访问密钥。
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: cc39cc09259c1ae681e1fee070777575e2788323
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827848"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>从 Azure Cosmos DB 帐户访问密钥向 Azure AD 标识的基于证书的身份验证

基于证书的身份验证的客户端证书，客户端应用程序以使用 Azure Active Directory (Azure AD) 进行身份验证。 您可以在需要标识，如本地计算机上或在 Azure 中的虚拟机的计算机上执行基于证书的身份验证。 你的应用程序然后可以读取 Azure Cosmos DB 密钥，而无需直接在应用程序中的密钥。 这篇文章介绍了如何创建示例 Azure AD 应用程序、 将其配置基于证书的身份验证、 登录到 Azure 中使用新的应用程序标识，，然后从你的 Azure Cosmos 帐户检索的密钥。 本文使用 Azure PowerShell 来设置标识，并提供了C#示例应用程序进行身份验证，从你的 Azure Cosmos 帐户访问密钥。  

## <a name="prerequisites"></a>系统必备

* 安装[最新版本](/powershell/azure/install-az-ps)的 Azure PowerShell。

* 如果还没有 [Azure 订阅](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="register-an-app-in-azure-ad"></a>在 Azure AD 中注册应用

在此步骤中，将 Azure AD 帐户中注册示例 web 应用程序。 此应用程序更高版本用于读取 Azure Cosmos DB 帐户的密钥。 使用以下步骤注册一个应用程序： 

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开 Azure **Active Directory**窗格中，转到应用程序注册窗格，然后选择**新注册**。 

   ![在 Active Directory 中的新应用程序注册](./media/certificate-based-authentication/new-app-registration.png)

1. 填充**注册应用程序**具有以下详细信息窗体：  

   * **名称**– 提供你的应用程序的名称，这可以是任何名称，如"示例应用程序"。
   * **支持的帐户类型**– 选择**此组织仅限目录 （默认目录） 中的帐户**可以访问此应用程序在当前目录中的资源。 
   * **重定向 URL** – 选择类型的应用程序**Web**并提供其中承载你的应用程序的 URL，它可以是任何 URL。 对于此示例中，您可以提供测试 URL 如`https://sampleApp.com`如果即使应用不存在，也没关系。

   ![注册示例 web 应用程序](./media/certificate-based-authentication/register-sample-web-app.png)

1. 选择**注册**填充窗体后。

1. 注册应用后，请记下的**Application(client) ID**并**对象 ID**，将在下一步骤中使用这些详细信息。 

   ![获取应用程序和对象 Id](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>安装 azure Ad 模块

在此步骤中，您将安装 Azure AD PowerShell 模块。 此模块需要获取上一步中注册的应用程序的 ID 并将关联到该应用程序的自签名的证书。 

1. 使用管理员权限打开 Windows PowerShell ISE。 如果尚未做，安装 AZ PowerShell 模块并连接到你的订阅。 如果有多个订阅，可以设置当前订阅的上下文，如以下命令中所示：

   ```powershell

   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. 安装并导入[AzureAD](/powershell/module/azuread/?view=azureadps-2.0)模块

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>登录到你的 Azure AD

登录到 Azure AD 注册应用程序。 使用 Connect-azuread 命令登录到你的帐户，请在弹出窗口中输入你的 Azure 帐户凭据。 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>创建自签名证书

打开的 Windows PowerShell ISE 中，另一个实例并运行以下命令以创建自签名的证书和读取与证书关联的密钥：

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>创建基于证书的凭据 

接下来运行以下命令，以获取你的应用程序的对象 ID 和创建基于证书的凭据。 在此示例中，我们将设置要在一年后过期的证书，可以将其设置为任何所需的结束日期。

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

上述命令生成类似于下面的屏幕截图的输出：

![基于证书的凭据创建输出](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>配置 Azure Cosmos 帐户以使用新的标识

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 导航到 Azure Cosmos 帐户，打开**访问控制 (IAM)** 边栏选项卡。

1. 选择**外**并**添加的角色分配**。 添加在上一步中创建示例应用程序**参与者**角色，如以下屏幕截图中所示：

   ![Azure Cosmos 帐户配置为使用新的标识](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. 选择**保存**填写表单后


## <a name="access-the-keys-from-powershell"></a>从 PowerShell 访问密钥

在此步骤中，您将使用登录到 Azure 应用程序并创建和访问 Azure Cosmos 帐户的密钥的证书。 

1. 最初，请清除具有用于登录到你的帐户的 Azure 帐户的凭据。 您可以通过使用以下命令清除凭据：

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. 接下来验证，可以使用应用程序的凭据登录到 Azure 门户并访问 Azure Cosmos DB 密钥：

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <Resource_Group_Name_of_your_Azure_Cosmos_account> -ResourceName <Your_Azure_Cosmos_Account_Name> 
   ```

前一个命令将显示的 Azure Cosmos 帐户的主要和辅助主密钥。 可以查看你的 Azure Cosmos 帐户验证获取密钥请求成功，并且事件发起者"示例应用程序"应用程序的活动日志。 
 
![验证 Azure AD 中的 get 键调用](./media/certificate-based-authentication/activity-log-validate-results.png)


## <a name="access-the-keys-from-a-c-application"></a>访问密钥从C#应用程序 

此外可以通过访问中的密钥来验证这种情况下C#应用程序。 以下C#控制台应用程序，可以通过使用 Active Directory 中注册的应用访问 Azure Cosmos DB 密钥。 请务必更新 tenantId、 clientID、 certName、 资源组名称、 订阅 ID、 运行代码前，Azure Cosmos 帐户名称详细信息。 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
 
 
        /// <summary>
        /// Get an access token from Azure AD using client credentials.
        /// If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each
        /// </summary>
        private static async Task<AuthenticationResult> GetAccessToken(AuthenticationContext authContext, string resourceUri, ClientAssertionCertificate cert)
        {
            //
            // Get an access token from Azure AD using client credentials.
            // If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each.
            //
            AuthenticationResult result = null;
            int retryCount = 0;
            bool retry = false;
 
            do
            {
                retry = false;
                errorCode = 0;
 
                try
                {
                    result = await authContext.AcquireTokenAsync(resourceUri, cert);
                }
                catch (AdalException ex)
                {
                    if (ex.ErrorCode == "temporarily_unavailable")
                    {
                        retry = true;
                        retryCount++;
                        Thread.Sleep(3000);
                    }
 
                    Console.WriteLine(
                        String.Format("An error occurred while acquiring a token\nTime: {0}\nError: {1}\nRetry: {2}\n",
                        DateTime.Now.ToString(),
                        ex.ToString(),
                        retry.ToString()));
 
                    errorCode = -1;
                }
 
            } while ((retry == true) && (retryCount < 3));
            return result;
        }
    }
}
```

此脚本输出主要和辅助主密钥，如以下屏幕截图中所示：

![c# 应用程序输出](./media/certificate-based-authentication/csharp-application-output.png)

类似于上一节，你可以查看你的 Azure Cosmos 帐户验证"示例应用程序"应用程序启动的 get 键请求事件的活动日志。 


## <a name="next-steps"></a>后续步骤

* [确保使用 Azure 密钥保管库的 Azure Cosmos 密钥的安全](access-secrets-from-keyvault.md)

* [Azure Cosmos DB 的安全属性](cosmos-db-security-attributes.md)
