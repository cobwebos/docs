---
title: "Azure Active Directory B2C：使用 HTTP 基本身份验证保护 RESTful 服务"
description: "演示如何使用 HTTP 基本身份验证保护 Azure AD B2C 中自定义 REST API 声明交换的示例"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/25/2017
ms.author: yoelh
ms.openlocfilehash: 226ecbe7e4d9d95fd4ba3255c95ec6baa1a86576
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-secure-your-restful-services-using-http-basic-authentication"></a>Azure Active Directory B2C：使用 HTTP 基本身份验证保护 RESTful 服务
在[相关文章](active-directory-b2c-custom-rest-api-netfw.md)中，我们已创建一个无需身份验证即可集成到 Azure AD B2C 用户旅程的 RESTful 服务 (Web API)。 本文介绍如何将 HTTP 基本身份验证添加到 RESTful 服务，以便只有经过验证的用户（包括 B2C）可以访问你的 API。 使用 HTTP 基本身份验证，在自定义策略中设置用户凭据（应用 ID 和应用机密）来使用凭据。 

> [!NOTE]
>
>以下文章更详细介绍了 [ASP.NET Web API 中的基本身份验证](https://docs.microsoft.com/en-us/aspnet/web-api/overview/security/basic-authentication)。

## <a name="prerequisites"></a>先决条件
完成前一文章[在 Azure AD B2C 用户旅程中集成 REST API 声明交换](active-directory-b2c-custom-rest-api-netfw.md)中所述的步骤

## <a name="step-1-add-authentication-support"></a>步骤 1：添加身份验证支持

### <a name="step-11-add-application-settings-to-projects-webconfig-file"></a>步骤 1.1：将应用程序设置添加到项目的 web.config 文件
1. 打开前面创建的 Visual Studio 项目 
2. 将以下应用程序设置添加到 web.config 文件中的 `appSettings` 元素下面：

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. 创建密码并设置 `WebApp:ClientSecret` 值

    若要生成 coplex 密码，可运行以下 PowerShell。 但可以使用任意值。

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

### <a name="step-13-add-authentication-middleware-class"></a>步骤 1.3：添加身份验证中间件类
在 `App_Start` 文件夹下添加 `ClientAuthMiddleware.cs` 类。 右键单击 `App_Start` 文件夹，依次选择“添加”、“类”

![在 App_Start 文件夹下添加 ClientAuthMiddleware.cs 类](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

键入 `ClientAuthMiddleware.cs` 作为类名

![创建新的 C# 类](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

打开 `App_Start\ClientAuthMiddleware.cs` 文件并将文件内容替换为以下代码：


```C#

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
将 OWIN 启动类添加到名为 `Startup.cs` 的 API。 右键单击项目，选择“添加”和“新建项”，并搜索“OWIN”。

![添加 OWIN 启动类](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

打开 `Startup.cs` 文件并将文件内容替换为以下代码：

```C#
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

### <a name="step-15-protect-identity-api-class"></a>步骤 1.5：保护标识 API 类
打开 Controllers\IdentityController.cs，将 `[Authorize]` 标记添加到控制器类。 `[Authorize]` 标记将控制器的访问权限限制给符合授权要求的用户。

![将 [Authorize] 标记添加到控制器](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>步骤 2：发布到 Azure
若要发布项目，请在“解决方案资源管理器”中右键单击“Contoso.AADB2C.API”项目，选择“发布”。

## <a name="step-3-add-the-restful-services-app-id--app-secret-to-azure-ad-b2c"></a>步骤 3：将 RESTful 服务应用 ID 和应用机密添加到 Azure AD B2C
RESTful 服务受客户端 ID（用户名）和客户端机密的保护后，需将凭据存储在 Azure AD B2C 租户中。 自定义策略在调用 RESTful 服务时会提供凭据。  

### <a name="step-31-add-restful-services-client-id"></a>步骤 3.1：添加 RESTful 服务客户端 ID
1.  转到 Azure AD B2C 租户，然后选择“B2C 设置” > “标识体验框架”
2.  选择“策略密钥”，查看租户中的可用密钥。
3.  单击“+添加”。
4.  对于“选项”，使用“手动”。
5.  使用 `B2cRestClientId` 作为“名称”。  
    可能会自动添加前缀 `B2C_1A_`。
6.  在“机密”框中，输入前面定义的应用 ID
7.  对于“密钥用法”，请使用“机密”。
8.  单击“创建” 
9.  确认已创建密钥 `B2C_1A_B2cRestClientId`。

### <a name="step-32-add-restful-services-client-secret"></a>步骤 3.2：添加 RESTful 服务客户端机密
1.  转到 Azure AD B2C 租户，然后选择“B2C 设置” > “标识体验框架”
2.  选择“策略密钥”，查看租户中的可用密钥。
3.  单击“+添加”。
4.  对于“选项”，使用“手动”。
5.  使用 `B2cRestClientSecret` 作为“名称”。  
    可能会自动添加前缀 `B2C_1A_`。
6.  在“机密”框中，输入前面定义的应用机密
7.  对于“密钥用法”，请使用“机密”。
8.  单击“创建” 
9.  确认已创建密钥 `B2C_1A_B2cRestClientSecret`。

## <a name="step-4-change-the-technicalprofile-to-support-basic-authentication-in-your-extension-policy"></a>步骤 4：更改 `TechnicalProfile` 以支持扩展策略中的基本身份验证
1.  从工作目录打开扩展策略文件 (TrustFrameworkExtensions.xml)。
2.  找到包含 `Id="REST-API-SignUp"` 的 `<TechnicalProfile>` 节点
3.  找到 `<Metadata>` 元素
4.  将 `AuthenticationType` 更改为 `Basic`
```xml
<Item Key="AuthenticationType">Basic</Item>
```
5.  紧靠在 `<Metadata>` 元素右括号的后面添加以下 XML 片段：  

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```
添加 XML 片段后，`TechnicalProfile` 应如下所示：

![添加基本身份验证 XML 元素](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>步骤 5：将策略上传到租户

1.  在 [Azure 门户](https://portal.azure.com)中，切换到 [Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)，单击“Azure AD B2C”。
2.  选择“标识体验框架”。
3.  单击“所有策略”。
4.  选择“上传策略”
5.  选中“覆盖策略(若存在)”框。
6.  上传 TrustFrameworkExtensions.xml，并确保它能够通过验证

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>步骤 6：使用“立即运行”测试自定义策略
1.  打开“Azure AD B2C 设置”并转到“标识体验框架”。

    >[!NOTE]
    >
    >    “立即运行”需要在租户中至少预先注册一个应用程序。 
    >    在 Azure AD B2C [入门](active-directory-b2c-get-started.md)或[应用程序注册](active-directory-b2c-app-registration.md)文章中了解如何注册应用程序。

2.  打开上传的信赖方 (RP) 自定义策略“B2C_1A_signup_signin”。 选择“立即运行”。
3.  尝试在“名”字段中输入“Test”，B2C 会在页面顶部显示错误消息

    ![测试标识 API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4.  尝试在“名”字段中输入名称（不要输入“test”。 B2C 会注册该用户，然后将 loyaltyNumber 发送到应用程序。 请注意本示例中此 JWT 中的编号。

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

## <a name="next-steps"></a>后续步骤
* [使用客户端证书保护 RESTful API](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

## <a name="optional-download-the-complete-policy-files-and-code"></a>[可选] 下载完整的策略文件和代码
* 建议在完成自定义策略入门演练后，使用自己的自定义策略文件而不是这些示例文件来生成方案。  [参考示例策略文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic)
* 可在此处下载完整代码：[Visual Studio 解决方案参考示例](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API)