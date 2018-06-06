---
title: include 文件
description: include 文件
services: active-directory
author: andretms
ms.service: active-directory
ms.topic: include
ms.date: 05/08/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 5d3af1800e18e3686e69d4a25131c68d3bdc805b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33949441"
---
## <a name="set-up-your-project"></a>设置项目

本部分介绍使用 OpenID Connect 通过 OWIN 中间件在 ASP.NET 项目上安装和配置身份验证管道的步骤。 

> 想要改为下载此示例的 Visual Studio 项目？ [下载项目](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)并跳到[配置步骤](#register-your-application)，在执行前配置代码示例。

### <a name="create-your-aspnet-project"></a>创建 ASP.NET 项目

1. 在 Visual Studio 中：`File` > `New` > `Project`
2. 在 *Visual C#\Web* 下，选择 `ASP.NET Web Application (.NET Framework)`。
3. 命名应用程序，并单击“确定”
4. 选择 `Empty` 并选中复选框，添加 `MVC` 引用

## <a name="add-authentication-components"></a>添加身份验证组件

1. 在 Visual Studio 中：`Tools` > `Nuget Package Manager` > `Package Manager Console`
2. 在包管理器控制台窗口中键入以下命令，添加 *OWIN 中间件 NuGet 包*：

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>关于这些库
>上述库通过基于 Cookie 的身份验证使用 OpenID Connect 启用单一登录 (SSO)。 完成身份验证后，代表用户的令牌会发送到应用程序，OWIN 中间件会创建会话 Cookie。 浏览器随后对后续请求使用此 Cookie，这样一来，用户就无需重新键入密码，也不需要任何其他验证。
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>配置身份验证管道
下面的步骤用于创建 OWIN 中间件 Startup 类，以配置 OpenID Connect 身份验证。 IIS 进程启动时，会自动执行此类。

> [!TIP]
> 如果项目的根文件夹中没有 `Startup.cs` 文件，请执行以下操作：
> 1. 右键单击项目的根文件夹：> `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. 将其命名为 `Startup.cs`
>
>> 确保选择的类是 OWIN Startup 类，而不是标准 C# 类。 通过检查是否在命名空间上看到 `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` 来进行确认。

1. 向 `Startup.cs` 添加 *OWIN* 和 *Microsoft.IdentityModel* 引用：

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. 使用以下代码替换 Startup 类：

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
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter 
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false
                    },
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
> ### <a name="more-information"></a>更多信息
> 在 *OpenIDConnectAuthenticationOptions* 中提供的参数将充当应用程序与 Azure AD 通信时使用的坐标。 OpenID Connect 中间件会在后台使用 Cookie，因此，还需要设置 Cookie 身份验证，如以上代码所示。 *ValidateIssuer* 值告知 OpenIdConnect 不要限制某个特定组织的访问权限。
<!--end-collapse-->

