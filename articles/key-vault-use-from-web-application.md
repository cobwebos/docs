<properties
	pageTitle="从 Web 应用程序使用 Azure 密钥保管库 | 概述"
	description="本教程帮助你了解如何从 Web 应用程序使用 Azure 密钥保管库。"
	services="key-vault"
	documentationCenter=""
	authors="adamhurwitz"
	manager=""/>

<tags
	ms.service="key-vault"
	ms.date="05/11/2015"
	wacn.date=""/>

# 从 Web 应用程序使用 Azure 密钥保管库 #

## 介绍  
本教程帮助你了解如何从 Azure 中的 Web 应用程序使用 Azure 密钥保管库（目前以预览版提供）。其中将会引导你访问 Azure 密钥保管库中的机密，以便可以在 Web 应用程序中使用该机密。

**估计完成时间：**15 分钟


有关 Azure 密钥保管库的概述信息，请参阅[什么是 Azure 密钥保管库？](key-vault-whatis)

## 先决条件

若要完成本教程，你必须准备好以下各项：

- Azure 密钥保管库中的机密的 URI
- 已在 Azure Active Directory 中注册的、有权访问你的密钥保管库的 Web 应用程序的客户端 ID 和客户端密钥
- Web 应用程序。我们将演示针对 Azure 中作为 Web 应用程序部署的 ASP.NET MVC 应用程序的步骤。

[AZURE.NOTE]你必须已完成 [Azure 密钥保管库入门](keyvault-get-started)中列出的适用于本教程的步骤，以便获取 Web 应用程序的机密、客户端 ID 和客户端密钥的 URI。

要访问密钥保管库的 Web 应用程序已在 Azure Active Directory 中注册，因此有权访问你的密钥保管库。如果这不是这样，请返回入门教程中的“注册应用程序”，并重复列出的步骤。

本教程面向 Web 开发人员，他们已经了解有关在 Azure 上创建 Web 应用程序的基本知识。有关 Azure Web Apps 的详细信息，请参阅 [Web Apps 概述](app-service-web-overview)。



## <a id="packages"></a>添加 NuGet 包 ##
需要在 Web 应用程序上安装三个包。

- Active Directory 身份验证库 - 包含用来与 Azure Active Directory 交互以及管理用户标识的方法
- Azure 密钥保管库库 - 包含用来与 Azure 密钥保管库交互的方法


可以在 Package Manager Console 中使用 Install-Package 命令安装所有这三个包。

	// this is currently the latest stable version of ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	//this is a preview version of the Key Vault Library
	Install-Package Microsoft.Azure.KeyVault -Pre


## <a id="webconfig"></a>修改 Web.Config ##
需要按如下所示将三个应用程序设置添加到 web.config 文件。


    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />


    <add key="SecretUri" value="secreturi" />


如果你不打算将应用程序作作为 Azure Web 应用程序托管，则应在 web.config 中添加实际的客户端 Id、客户端密钥和机密 URI 值。否则，请将这些虚构值，因为我们将在 Azure 门户中添加实际值以提高安全级别。


## <a id="gettoken"></a>添加方法以获取访问令牌 ##
若要使用密钥保管库 API，你需要一个访问令牌。密钥保管库客户端将处理对密钥保管库 API 的调用，但你需要为该 API 提供一个用于获取访问令牌的函数。

以下代码可从 Azure Active Directory 获取访问令牌。可将此代码添加在应用程序中的任意位置。我想要添加一个 Utils 或 EncryptionHelper 类。

	//add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Web.Configuration;

	//this is an optional property to hold the secret after it is retrieved
	public static string EncryptSecret { get; set; }

	//the method that will be provided to the KeyVaultClient
	public async static Task<string> GetToken(string authority, string resource, string scope)
    {
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
	    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

	    if (result == null)
	    	throw new InvalidOperationException("Failed to obtain the JWT token");

	    return result.AccessToken;
    }

## <a id="appstart"></a>在 Application Start 中检索机密 ##
现在，我们需要添加代码来调用密钥保管库 API 并检索机密。以下代码可添加到任何位置，前提是在使用之前调用它。我已将此代码放在 Global.asax 中的 Application Start 事件内，这样，在启动应用程序时，该代码将运行一次，并使机密可用于应用程序。

	//add these using statements
	using Microsoft.Azure.KeyVault;
	using System.Web.Configuration;

	// I put my GetToken method in a Utils class. Change for wherever you placed your method.
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

	var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;

	//I put a variable in a Utils class to hold the secret for general  application use.
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>在 Azure 门户中添加应用程序设置（可选） ##
如果你有一个 Azure Web 应用程序，则现在可以在 Azure 门户中添加 AppSettings 的实际值。如果这样做，实际值不会出现在 web.config 中，而是通过门户受到保护，你在门户中拥有不同的访问控制功能。这些值将取代为你在 web.config 中输入的值。请确保名称相同。

![Azure 门户中显示的应用程序设置][1]



## <a id="next"></a>后续步骤 ##


有关编程参考，请参阅 [Azure 密钥保管库 C# 客户端 API 参考](https://msdn.microsoft.com/zh-cn/library/azure/dn903628.aspx)。


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png

<!---HONumber=61-->