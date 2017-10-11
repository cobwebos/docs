---
title: "Azure AD v2 ASP.NET Web 服务器获取已启动 – 设置 |Microsoft 文档"
description: "与传统 web 基于浏览器应用程序使用 OpenID Connect 标准在 ASP.NET 解决方案上实现 Microsoft 登录"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: ebf54f5a203adb7f0e5b0c47dcc07595e269e218
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
## <a name="set-up-your-project"></a>设置你的项目

本部分显示的步骤来安装和配置在 ASP.NET 项目使用 OpenID Connect OWIN 中间件通过身份验证管道。 

> 希望改为下载此示例的 Visual Studio 项目？ [下载的项目](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)并跳到[配置步骤](#create-an-application-express)配置在执行之前的代码示例。

<!--start-collapse-->
> ### <a name="create-your-aspnet-project"></a>创建 ASP.NET 项目

> 1. 在 Visual Studio 中：`File` > `New` > `Project`<br/>
> 2. 下*Visual C# \Web*，选择`ASP.NET Web Application (.NET Framework)`。
> 3. 将你的应用程序，然后单击*确定*
> 4. 选择`Empty`和选中复选框可添加`MVC`引用
<!--end-collapse-->

## <a name="add-authentication-components"></a>添加身份验证组件

1. 在 Visual Studio 中：`Tools` > `Nuget Package Manager` > `Package Manager Console`
2. 添加*OWIN 中间件 NuGet 包*下在 Package Manager Console 窗口中键入以下命令：

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>有关这些库

>上面的库启用单一登录 (SSO) 通过基于 cookie 的身份验证使用 OpenID Connect。 完成身份验证并代表用户令牌发送到你的应用程序之后，OWIN 中间件创建会话 cookie。 浏览器然后在后续请求使用此 cookie 使用户无需重新键入密码，并且需要任何其他验证。
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>配置身份验证管道
下面的步骤用于创建 OWIN 中间件 Startup 类，以配置 OpenID Connect 身份验证。 IIS 进程启动时，将自动执行此类。

> 如果你的项目不具有`Startup.cs`的根文件夹中的文件：<br/>
> 1. 右键单击项目的根文件夹： >`Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. 将其命名为`Startup.cs`

> 请确保选定的类是 OWIN Startup 类而不是标准 C# 类。 通过检查如果你看到确认这`[assembly: OwinStartup(typeof({NameSpace}.Startup))]`上面命名空间。


1. 添加*OWIN*和*Microsoft.IdentityModel*引用`Startup.cs`:

```csharp
using Microsoft.Owin;
using Owin;
using Microsoft.IdentityModel.Protocols;
using Microsoft.Owin.Security;
using Microsoft.Owin.Security.Cookies;
using Microsoft.Owin.Security.OpenIdConnect;
using Microsoft.Owin.Security.Notifications;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Startup 类替换为以下代码：
</li>
</ol>

```csharp
public class Startup
{        
    // The Client ID is used by the application to uniquely identify itself to Azure AD.
    string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

    // RedirectUri is the URL where the user will be redirected to after they sign in.
    string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

    // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
    static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

    // Authority is the URL for authority, composed by Azure Active Directory v2 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
    string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

    /// <summary>
    /// Configure OWIN to use OpenIdConnect 
    /// </summary>
    /// <param name="app"></param>
    public void Configuration(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
            new OpenIdConnectAuthenticationOptions
            {
                // Sets the ClientId, authority, RedirectUri as obtained from web.config
                ClientId = clientId,
                Authority = authority,
                RedirectUri = redirectUri,
                // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                PostLogoutRedirectUri = redirectUri,
                Scope = OpenIdConnectScopes.OpenIdProfile,
                // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                ResponseType = OpenIdConnectResponseTypes.IdToken,
                // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter 
                TokenValidationParameters = new System.IdentityModel.Tokens.TokenValidationParameters() { ValidateIssuer = false },
                // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                Notifications = new OpenIdConnectAuthenticationNotifications
                {
                    AuthenticationFailed = OnAuthenticationFailed
                }
            }
        );
    }

    /// <summary>
    /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
    /// </summary>
    /// <param name="context"></param>
    /// <returns></returns>
    private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
    {
        context.HandleResponse();
        context.Response.Redirect("/?errormessage=" + context.Exception.Message);
        return Task.FromResult(0);
    }
}

```
<!--start-collapse-->
> ### <a name="more-information"></a>详细信息

> 在中提供的参数*OpenIDConnectAuthenticationOptions*充当应用程序可以与 Azure AD 通信的坐标。 由于 OpenID Connect 中间件在后台使用 cookie，还需要将 cookie 身份验证设置为上面所示的代码。 *并且 ValidateIssuer*值告知 OpenIdConnect 不到一个特定的组织中限制访问。
<!--end-collapse-->

