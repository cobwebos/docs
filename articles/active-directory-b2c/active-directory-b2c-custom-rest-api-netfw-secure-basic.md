---
title: Azure Active Directory B2C：使用 HTTP 基本身份验证保护 RESTful 服务
description: 使用 HTTP 基本身份验证保护 Azure AD B2C 中的自定义 REST API 声明交换
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 09/25/2017
ms.author: davidmu
ms.openlocfilehash: 749157d16c1c394b173545dddb8751d58fdcfd56
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>使用 HTTP 基本身份验证保护 RESTful 服务

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在[相关的 Azure AD B2C 文章](active-directory-b2c-custom-rest-api-netfw.md)中，我们已创建一个无需身份验证即可集成到 Azure Active Directory B2C (Azure AD B2C) 用户旅程的 RESTful 服务 (Web API)。 

本文介绍如何将 HTTP 基本身份验证添加到 RESTful 服务，以便只有经过验证的用户（包括 B2C）可以访问你的 API。 使用 HTTP 基本身份验证在自定义策略中设置用户凭据（应用 ID 和应用机密）。 

有关详细信息，请参阅 [ASP.NET Web API 中的基本身份验证](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication)。

## <a name="prerequisites"></a>先决条件
完成[在 Azure AD B2C 用户旅程中集成 REST API 声明交换](active-directory-b2c-custom-rest-api-netfw.md)一文中所述的步骤。

## <a name="step-1-add-authentication-support"></a>步骤 1：添加身份验证支持

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>步骤 1.1：将应用程序设置添加到项目的 web.config 文件
1. 打开先前创建的 Visual Studio 项目。 

2. 将以下应用程序设置添加到 web.config 文件中的 `appSettings` 元素下面：

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. 创建密码并设置 `WebApp:ClientSecret` 值。

    若要生成复杂密码，请运行以下 PowerShell 代码。 可以使用任意值。

    ```PowerShell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>步骤 1.2：安装 OWIN 库
首先，使用 Visual Studio 包管理器控制台将 OWIN 中间件 NuGet 包添加到项目：

```
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>步骤 1.3：添加身份验证中间件类
在 *App_Start* 文件夹下添加 `ClientAuthMiddleware.cs` 类。 为此，请执行以下操作：

1. 右键单击“App_Start”文件夹，依次选择“添加”、“类”。

   ![在 App_Start 文件夹中添加 ClientAuthMiddleware.cs 类](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. 在“名称”框中，键入 **ClientAuthMiddleware.cs**。

   ![创建新的 C# 类](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. 打开 *App_Start\ClientAuthMiddleware.cs* 文件并将文件内容替换为以下代码：

    ```csharp
    
    using Microsoft.Owin;
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using System.Security.Principal;
    using System.Text;
    using System.Threading.Tasks;
    using System.Web;
    
    namespace Contoso.AADB2C.API
    {
        /// <summary>
        /// Class to create a custom owin middleware to check for client authentication
        /// </summary>
        public class ClientAuthMiddleware
        {
            private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
            private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];
    
            /// <summary>
            /// Gets or sets the next owin middleware
            /// </summary>
            private Func<IDictionary<string, object>, Task> Next { get; set; }
    
            /// <summary>
            /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
            /// </summary>
            /// <param name="next"></param>
            public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
            {
                this.Next = next;
            }
    
            /// <summary>
            /// Invoke client authentication middleware during each request.
            /// </summary>
            /// <param name="environment">Owin environment</param>
            /// <returns></returns>
            public Task Invoke(IDictionary<string, object> environment)
            {
                // Get wrapper class for the environment
                var context = new OwinContext(environment);
    
                // Check whether the authorization header is available. This contains the credentials.
                var authzValue = context.Request.Headers.Get("Authorization");
                if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
                {
                    // Process next middleware
                    return Next(environment);
                }
    
                // Get credentials
                var creds = authzValue.Substring("Basic ".Length).Trim();
                string clientId;
                string clientSecret;
    
                if (RetrieveCreds(creds, out clientId, out clientSecret))
                {
                    // Set transaction authenticated as client
                    context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
                }
    
                return Next(environment);
            }
    
            /// <summary>
            /// Retrieve credentials from header
            /// </summary>
            /// <param name="credentials">Authorization header</param>
            /// <param name="clientId">Client identifier</param>
            /// <param name="clientSecret">Client secret</param>
            /// <returns>True if valid credentials were presented</returns>
            private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
            {
                string pair;
                clientId = clientSecret = string.Empty;
    
                try
                {
                    pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
                }
                catch (FormatException)
                {
                    return false;
                }
                catch (ArgumentException)
                {
                    return false;
                }
    
                var ix = pair.IndexOf(':');
                if (ix == -1)
                {
                    return false;
                }
    
                clientId = pair.Substring(0, ix);
                clientSecret = pair.Substring(ix + 1);
    
                // Return whether credentials are valid
                return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                    string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
            }
        }
    }
    ```

### <a name="step-14-add-an-owin-startup-class"></a>步骤 1.4：添加 OWIN 启动类
将名为 `Startup.cs` 的 OWIN 启动类添加到 API。 为此，请执行以下操作：
1. 右键单击项目，选择“添加” > “新建项”，并搜索“OWIN”。

   ![添加 OWIN 启动类](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

2. 打开 *Startup.cs* 文件并将文件内容替换为以下代码：

    ```csharp
    using Microsoft.Owin;
    using Owin;
    
    [assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
    namespace Contoso.AADB2C.API
    {
        public class Startup
        {
            public void Configuration(IAppBuilder app)
            {
                    app.Use<ClientAuthMiddleware>();
            }
        }
    }
    ```

### <a name="step-15-protect-the-identity-api-class"></a>步骤 1.5：保护标识 API 类
打开 Controllers\IdentityController.cs，将 `[Authorize]` 标记添加到控制器类。 此标记将控制器的访问权限限制给符合授权要求的用户。

![将 Authorize 标记添加到控制器](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>步骤 2：发布到 Azure
若要发布项目，请在解决方案资源管理器中右键单击“Contoso.AADB2C.API”项目，选择“发布”。

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>步骤 3：将 RESTful 服务应用 ID 和应用机密添加到 Azure AD B2C
RESTful 服务受客户端 ID（用户名）和机密的保护后，必须将凭据存储在 Azure AD B2C 租户中。 自定义策略在调用 RESTful 服务时会提供凭据。 

### <a name="step-31-add-a-restful-services-client-id"></a>步骤 3.1：添加 RESTful 服务客户端 ID
1. 在 Azure AD B2C 租户中，选择“B2C 设置” > “标识体验框架”。


2. 选择“策略密钥”，查看租户中的可用密钥。

3. 选择 **添加** 。

4. 对于“选项”，请选择“手动”。

5. 对于“名称”，请键入 **B2cRestClientId**。  
    可能会自动添加前缀 *B2C_1A_*。

6. 在“机密”框中，输入前面定义的应用 ID。

7. 对于“密钥用法”，请选择“机密”。

8. 选择**创建**。

9. 确认已创建 `B2C_1A_B2cRestClientId` 密钥。

### <a name="step-32-add-a-restful-services-client-secret"></a>步骤 3.2：添加 RESTful 服务客户端机密
1. 在 Azure AD B2C 租户中，选择“B2C 设置” > “标识体验框架”。

2. 选择“策略密钥”，查看租户中的可用密钥。

3. 选择 **添加** 。

4. 对于“选项”，请选择“手动”。

5. 对于“名称”，请键入 **B2cRestClientSecret**。  
    可能会自动添加前缀 *B2C_1A_*。

6. 在“机密”框中，输入前面定义的应用机密。

7. 对于“密钥用法”，请选择“机密”。

8. 选择**创建**。

9. 确认已创建 `B2C_1A_B2cRestClientSecret` 密钥。

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>步骤 4：更改技术配置文件以支持扩展策略中的基本身份验证
1. 在工作目录中，打开扩展策略文件 (TrustFrameworkExtensions.xml)。

2. 搜索包含 `Id="REST-API-SignUp"` 的 `<TechnicalProfile>` 节点。

3. 找到 `<Metadata>` 元素。

4. 将 *AuthenticationType* 更改为 *Basic*，如下所示：
    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. 紧靠在 `<Metadata>` 元素右括号的后面添加以下 XML 片段： 

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```
    添加该片段后，技术配置文件应类似于以下 XML 代码：
    
    ![添加基本身份验证 XML 元素](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>步骤 5：将策略上传到租户

1. 在 [Azure 门户](https://portal.azure.com)中，切换到 [Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)，打开“Azure AD B2C”。

2. 选择“标识体验框架”。

3. 打开“所有策略”。

4. 选择“上传策略”。

5. 选中“覆盖策略(如果存在)”复选框。

6. 上传 *TrustFrameworkExtensions.xml* 文件，并确保它能够通过验证。

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>步骤 6：使用“立即运行”测试自定义策略
1. 打开“Azure AD B2C 设置”，选择“标识体验框架”。

    >[!NOTE]
    >“立即运行”需要在租户中至少预先注册一个应用程序。 在 Azure AD B2C [入门](active-directory-b2c-get-started.md)或[应用程序注册](active-directory-b2c-app-registration.md)文章中了解如何注册应用程序。

2. 打开已上传的信赖方 (RP) 自定义策略 **B2C_1A_signup_signin**，然后选择“立即运行”。

3. 在“名”框中键入 **Test** 来测试该过程。  
    Azure AD B2C 会在窗口顶部显示一条错误消息。

    ![测试标识 API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. 在“名”框中键入一个名称（不要键入“Test”）。  
    Azure AD B2C 会注册该用户，然后将会员号发送到应用程序。 请注意此示例中的编号：

    ```
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
    }.{
      "exp": 1507125903,
      "nbf": 1507122303,
      "ver": "1.0",
      "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
      "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
      "acr": "b2c_1a_signup_signin",
      "nonce": "defaultNonce",
      "iat": 1507122303,
      "auth_time": 1507122303,
      "loyaltyNumber": "290",
      "given_name": "Emily",
      "emails": ["B2cdemo@outlook.com"]
    }
    ```

## <a name="optional-download-the-complete-policy-files-and-code"></a>（可选）下载完整的策略文件和代码
* 完成[自定义策略入门](active-directory-b2c-get-started-custom.md)演练后，我们建议你使用自己的自定义策略文件来构建方案。 我们已提供[示例策略文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic)用于参考。
* 可以从 [Visual Studio 解决方案参考示例](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API)下载完整代码。

## <a name="next-steps"></a>后续步骤
* [使用客户端证书保护 RESTful API](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

