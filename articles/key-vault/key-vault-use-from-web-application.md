---
title: 有关从 Web 应用程序使用 Azure Key Vault 的教程 | Microsoft Docs
description: 本教程有助于读者了解如何从 Web 应用程序使用 Azure Key Vault。
services: key-vault
author: adhurwit
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: adhurwit
ms.openlocfilehash: 5cd764395e91a82973318da7284b28d7a43d35ea
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37115052"
---
# <a name="tutorial-use-azure-key-vault-from-a-web-application"></a>教程：从 Web 应用程序使用 Azure Key Vault
本教程有助于读者了解如何从 Azure 中的 Web 应用程序使用 Azure Key Vault。 其中介绍了访问用于 Web 应用程序的 Azure Key Vault 中的机密的过程。 然后，本教程将基于该过程生成一个证书，并使用它来取代客户端机密。 本教程面向 Web 开发人员，他们已了解有关在 Azure 上创建 Web 应用程序的基础知识。 

本教程介绍如何执行以下操作： 

> [!div class="checklist"]
> * 将应用程序设置添加到 web.config 文件
> * 添加用于获取访问令牌的方法
> * 在应用程序启动时检索令牌
> * 使用证书进行身份验证 

如果没有 Azure 订阅，请在开始之前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

若要完成本教程，必须准备好以下各项：

* Azure Key Vault 中的机密的 URI
* 已在 Azure Active Directory 中注册的、有权访问 Key Vault 的 Web 应用程序的客户端 ID 和客户端机密
* Web 应用程序。 本教程演示适用于在 Azure 中部署为 Web 应用的 ASP.NET MVC 应用程序的步骤。

完成 [Azure Key Vault 入门](key-vault-get-started.md)中的步骤，以获取机密 URI、客户端 ID 和客户端机密，并注册应用程序。 该 Web 应用程序将访问保管库，并需要在 Azure Active Directory 中注册。 它还需要有权访问 Key Vault。 如果它没有此访问权限，请返回入门教程中的“注册应用程序”，并重复列出的步骤。 有关创建 Azure Web 应用的详细信息，请参阅 [Web 应用概述](../app-service/app-service-web-overview.md)。

此示例依赖于手动预配 Azure Active Directory 标识。 目前，预览版中有一项称为[托管服务标识 (MSI)](https://docs.microsoft.com/azure/active-directory/msi-overview) 的新功能，它可以自动预配 Azure AD 标识。 有关详细信息，请参阅 [GitHub](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) 中的示例，以及相关的 [MSI 和应用服务与函数教程](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity)。 


## <a id="packages"></a>添加 NuGet 包

需要在 Web 应用程序上安装两个包。

* Active Directory 身份验证库 - 包含用来与 Azure Active Directory 交互以及管理用户标识的方法
* Azure Key Vault 库 - 包含用来与 Azure Key Vault 交互的方法

可以在 Package Manager Console 中使用 Install-Package 命令安装这两个包。

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory 
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>修改 web.config

需要按如下所示将三个应用程序设置添加到 web.config 文件。 我们会在 Azure 门户中添加实际值以提高安全级别。

```xml
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
    <!-- If you aren't hosting your app as an Azure Web App, then you should use the actual ClientId, Client Secret, and Secret URI values -->
```



## <a id="gettoken"></a>添加用于获取访问令牌的方法

若要使用 Key Vault API，需要一个访问令牌。 Key Vault 客户端将处理对 Key Vault API 的调用，但需要为该 API 提供一个用于获取访问令牌的函数。 以下示例代码从 Azure Active Directory 获取访问令牌。 可将此代码添加在应用程序中的任意位置。 我想要添加一个 Utils 或 EncryptionHelper 类。  

```cs
//add these using statements
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Threading.Tasks;
using System.Web.Configuration;

//this is an optional property to hold the secret after it is retrieved
public static string EncryptSecret { get; set; }

//the method that will be provided to the KeyVaultClient
public static async Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                WebConfigurationManager.AppSettings["ClientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
// Using Client ID and Client Secret is a way to authenticate an Azure AD application.
// Using it in your web application allows for a separation of duties and more control over your key management. 
// However, it does rely on putting the Client Secret in your configuration settings.
// For some people, this can be as risky as putting the secret in your configuration settings.
```

## <a id="appstart"></a>在应用程序启动时检索机密

现在，我们需要添加代码来调用 Key Vault API 并检索机密。 以下代码可添加到任何位置，前提是在使用之前调用它。 我已将此代码放在 Global.asax 中的 Application Start 事件内，这样，在启动应用程序时，该代码将运行一次，并使机密可用于应用程序。

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>在 Azure 门户中添加应用设置

在 Azure 门户中，现在可将 AppSettings 的实际值添加到 Azure Web 应用中。 如果执行此步骤，实际值不会出现在 web.config 中，而是通过门户受到保护，在门户中拥有不同的访问控制功能。 这些值将取代在 web.config 中输入的值。请确保名称相同。

![Azure 门户中显示的应用程序设置][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>使用证书（而不是客户端机密）进行身份验证

现在，你已了解如何使用客户端 ID 和客户端机密对 Azure AD 应用进行身份验证，接下来让我们使用客户端 ID 和证书。 若要在 Azure Web 应用中使用证书，请使用以下步骤：

1. 获取或创建证书
2. 将证书与 Azure AD 应用程序相关联
3. 在 Web 应用中添加代码以使用证书
4. 将证书添加到 Web 应用

### <a name="get-or-create-a-certificate"></a>获取或创建证书

 我们将为本教程创建一个测试证书。 下面是用于创建自签名证书的脚本。 将目录更改为要在其中创建证书文件的位置。  对于证书的开始和结束日期，可以使用当前日期加上 1 年。

```powershell
#Create self-signed certificate and export pfx and cer files 
$PfxFilePath = "c:\data\KVWebApp.pfx" 
$CerFilePath = "c:\data\KVWebApp.cer" 
$DNSName = "MyComputer.Contoso.com" 
$Password ="MyPassword" 
$SecStringPw = ConvertTo-SecureString -String $Password -Force -AsPlainText 
$Cert = New-SelfSignedCertificate -DnsName $DNSName -CertStoreLocation "cert:\LocalMachine\My" -NotBefore 05/15/2018 -NotAfter 05/15/2019 
Export-PfxCertificate -cert $cert -FilePath $PFXFilePath -Password $SecStringPw 
Export-Certificate -cert $cert -FilePath $CerFilePath 
```

记下 .pfx 的结束日期和密码（在本示例中为：May 15, 2019 和 MyPassword）。 需要在以下脚本中使用这些信息。 
### <a name="associate-the-certificate-with-an-azure-ad-application"></a>将证书与 Azure AD 应用程序相关联

现在你已有一个证书，需要将其与 Azure AD 应用程序相关联。 可以通过 PowerShell 完成关联。 运行以下命令，将该证书与 Azure AD 应用程序相关联：

```powershell
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets all -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

运行这些命令后，可以在 Azure AD 中看到该应用程序。 搜索应用注册时，请务必在搜索对话框中选择“我的应用”而不是“所有应用”。 

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>在 Web 应用中添加代码以使用证书

现在，我们将在 Web 应用中添加代码以访问证书并使用它进行身份验证。 

首先，添加用于访问证书的代码。请注意，StoreLocation 是 CurrentUser，而不是 LocalMachine。 在 Find 方法中提供“false”，因为使用的是测试证书。

```cs
//Add this using statement
using System.Security.Cryptography.X509Certificates;  

public static class CertificateHelper
{
    public static X509Certificate2 FindCertificateByThumbprint(string findValue)
    {
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
        try
        {
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                findValue, false); // Don't validate certs, since the test root isn't installed.
            if (col == null || col.Count == 0)
                return null;
            return col[0];
        }
        finally
        {
            store.Close();
        }
    }
}
```



接下来，添加使用 CertificateHelper 并创建 ClientAssertionCertificate 的代码，这是身份验证所需的。

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

下面是用于获取访问令牌的新代码。 此代码将替换以上示例中的 GetToken 方法。 为方便起见，我为它起了不同的名称。 为了便于使用，我已将所有这些代码放到我的 Web 应用项目的 Utils 类中。

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```



最后一项代码更改在 Application_Start 方法中进行。 首先，需要调用 GetCert() 方法以加载 ClientAssertionCertificate。 然后，更改在创建新的 KeyVaultClient 时提供的回调方法。 此代码将替换以上示例中添加的代码。

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>通过 Azure 门户向 Web 应用添加证书

将证书添加到 Web 应用是一个简单的两步过程。 首先，转到 Azure 门户并导航到 Web 应用。 在 Web 应用的“设置”中，单击“SSL 设置”对应的条目。 打开此条目后，上传在以上示例中创建的证书 KVWebApp.pfx。 请务必记住该 pfx 的密码。

![在 Azure 门户中将证书添加到 Web 应用][2]

需要执行的最后一项操作是将应用程序设置添加到 Web 应用中，该设置名为 WEBSITE\_LOAD\_CERTIFICATES，值为 *。 此步骤确保加载所有证书。 如果你只想加载已上传的证书，可以输入这些证书的指纹的逗号分隔列表。


## <a name="clean-up-resources"></a>清理资源
不再需要时，请删除本教程中使用的应用服务、Key Vault 和 Azure AD 应用程序。  


## <a id="next"></a>后续步骤
> [!div class="nextstepaction"]
>[Azure Key Vault 管理 API 参考](/dotnet/api/overview/azure/keyvault/management)。


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
 