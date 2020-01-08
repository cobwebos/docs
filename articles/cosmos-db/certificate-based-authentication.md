---
title: Azure Cosmos DB 和 Active Directory 的基于证书的身份验证
description: 了解如何为基于证书的身份验证配置 Azure AD 标识，以便从 Azure Cosmos DB 访问密钥。
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 9e9dd529edea23b27de2e3841079244558d6689a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442102"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Azure AD 标识的基于证书的身份验证，用于访问 Azure Cosmos DB 帐户中的密钥

基于证书的身份验证使客户端应用程序能够通过使用 Azure Active Directory （Azure AD）和客户端证书进行身份验证。 你可以在需要标识的计算机（例如本地计算机或 Azure 中的虚拟机）上执行基于证书的身份验证。 然后，应用程序可以读取 Azure Cosmos DB 密钥，而无需在应用程序中直接包含密钥。 本文介绍如何创建 Azure AD 应用程序示例，将其配置为基于证书的身份验证，使用新应用程序标识登录到 Azure，然后从 Azure Cosmos 帐户中检索密钥。 本文使用 Azure PowerShell 设置标识，并提供一个C#可从 Azure Cosmos 帐户对密钥进行身份验证和访问的示例应用。  

## <a name="prerequisites"></a>必备组件

* 安装[最新版本](/powershell/azure/install-az-ps)的 Azure PowerShell。

* 如果还没有 [Azure 订阅](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="register-an-app-in-azure-ad"></a>在 Azure AD 中注册应用

在此步骤中，将在 Azure AD 帐户中注册一个示例 web 应用程序。 稍后将使用此应用程序读取 Azure Cosmos DB 帐户中的密钥。 使用以下步骤注册应用程序： 

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开 "Azure **Active Directory** " 窗格，中转到 "**应用注册**" 窗格，然后选择 "**新注册**"。 

   ![Active Directory 中的新应用程序注册](./media/certificate-based-authentication/new-app-registration.png)

1. 填写 "**注册应用程序**" 窗体，并提供以下详细信息：  

   * **名称**-提供应用程序的名称，可以是任意名称，如 "sampleapp.exe"。
   * **受支持的帐户类型**–选择 **"仅此组织目录中的帐户" （默认目录）** ，以允许当前目录中的资源访问此应用程序。 
   * **重定向 URL** -选择类型为 " **Web** " 的应用程序并提供托管应用程序的 url，可以是任何 url。 在此示例中，你可以提供测试 URL （如 `https://sampleApp.com` 即使应用不存在也是一样的。

   ![注册示例 web 应用程序](./media/certificate-based-authentication/register-sample-web-app.png)

1. 填写表单后，选择 "**注册**"。

1. 注册应用后，记下 "**应用程序（客户端） id** " 和 "**对象 id**"，接下来的步骤中将使用这些详细信息。 

   ![获取应用程序和对象 Id](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>安装 AzureAD 模块

在此步骤中，你将安装 Azure AD PowerShell 模块。 此模块需要获取你在上一步中注册的应用程序的 ID，并将自签名证书关联到该应用程序。 

1. 以管理员权限打开 Windows PowerShell ISE。 如果尚未执行此操作，请安装 AZ PowerShell 模块并连接到你的订阅。 如果有多个订阅，可以设置当前订阅的上下文，如以下命令中所示：

   ```powershell

   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. 安装和导入[AzureAD](/powershell/module/azuread/?view=azureadps-2.0)模块

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>登录到 Azure AD

登录到已注册应用程序的 Azure AD。 使用 AzureAD 命令登录你的帐户，在弹出窗口中输入你的 Azure 帐户凭据。 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>创建自签名证书

打开 Windows PowerShell ISE 的另一个实例，并运行以下命令以创建自签名证书并读取与证书关联的密钥：

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>创建基于证书的凭据 

接下来，运行以下命令以获取应用程序的对象 ID，并创建基于证书的凭据。 在此示例中，我们将证书设置为一年后过期，你可以将其设置为任何所需的结束日期。

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

上述命令将生成类似于以下屏幕截图的输出：

![基于证书的凭据创建输出](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>将 Azure Cosmos 帐户配置为使用新标识

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 导航到你的 Azure Cosmos 帐户，打开 "**访问控制（IAM）** " 边栏选项卡。

1. 选择 "**添加**并**添加角色分配**"。 将在上一步中创建的 Sampleapp.exe 添加到 "**参与者**" 角色，如以下屏幕截图所示：

   ![将 Azure Cosmos 帐户配置为使用新标识](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. 填写表单后，请选择 "**保存**"

## <a name="register-your-certificate-with-azure-ad"></a>使用 Azure AD 注册证书

你可以将基于证书的凭据与 Azure AD 中的客户端应用程序关联 Azure 门户。 若要关联凭据，必须上载证书文件，步骤如下：

在客户端应用程序的 Azure 应用注册中：

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开 "Azure **Active Directory** " 窗格，中转到 "**应用注册**" 窗格，并打开在上一步中创建的示例应用。 

1. 选择**证书 & 机密**，然后**上传证书**。 浏览在上一步中创建的证书文件，以便上传。

1. 选择 **添加** 。 上载证书后，将显示指纹、开始日期和过期值。

## <a name="access-the-keys-from-powershell"></a>从 PowerShell 访问密钥

在此步骤中，你将使用应用程序和创建的证书登录到 Azure，并访问你的 Azure Cosmos 帐户的密钥。 

1. 最初清除了用于登录帐户的 Azure 帐户凭据。 可以使用以下命令清除凭据：

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. 接下来，请验证是否可以使用应用程序的凭据登录 Azure 门户，并访问 Azure Cosmos DB 密钥：

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <Resource_Group_Name_of_your_Azure_Cosmos_account> -ResourceName <Your_Azure_Cosmos_Account_Name> 
   ```

上面的命令将显示 Azure Cosmos 帐户的主密钥和辅助主密钥。 可以查看 Azure Cosmos 帐户的活动日志，验证 "获取密钥" 请求是否成功，以及 "Sampleapp.exe" 应用程序是否启动了该事件。 
 
![验证 Azure AD 中的获取密钥调用](./media/certificate-based-authentication/activity-log-validate-results.png)


## <a name="access-the-keys-from-a-c-application"></a>从C#应用程序访问密钥 

还可以通过访问C#应用程序中的密钥来验证此方案。 下面C#的控制台应用程序，它可以使用在 Active Directory 中注册的应用程序访问 Azure Cosmos DB 密钥。 请确保在运行代码之前，更新 tenantId、clientID、certName、资源组名称、订阅 ID、Azure Cosmos 帐户名称的详细信息。 

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
    }
}
```

此脚本输出主密钥和辅助主密钥，如以下屏幕截图所示：

![csharp 应用程序输出](./media/certificate-based-authentication/csharp-application-output.png)

与上一节类似，可以查看 Azure Cosmos 帐户的活动日志，以验证 "获取密钥请求" 事件是否由 "Sampleapp.exe" 应用程序启动。 


## <a name="next-steps"></a>后续步骤

* [使用 Azure Key Vault 保护 Azure Cosmos 密钥](access-secrets-from-keyvault.md)

* [Azure Cosmos DB 的安全控制](cosmos-db-security-controls.md)
