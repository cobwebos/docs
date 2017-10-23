---
title: "Azure AD v2.0 .NET Web 应用登录入门 | Microsoft Docs"
description: "如何构建一个使用个人 Microsoft 帐户和工作或学校帐户来登录用户的 .NET MVC Web 应用。"
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: c8b97ac6-0a06-4367-81b6-7d1d98152b14
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: ba5bdf7daba6086b70aec54ebe25d4445fa708c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="add-sign-in-to-an-net-mvc-web-app"></a>将登录凭据添加到 .NET MVC Web 应用
通过 v2.0 终结点，可以快速地将身份验证添加 Web 应用，同时支持个人 Microsoft 帐户以及工作或学校帐户。  在 ASP.NET Web 应用中，可以使用随附在 .NET Framework 4.5 中的 Microsoft OWIN 中间件来完成此操作。

> [!NOTE]
> v2.0 终结点并不支持所有 Azure Active Directory 方案和功能。  若要确定是否应使用 v2.0 终结点，请阅读 [v2.0 限制](active-directory-v2-limitations.md)。
>
>

 此处，我们将构建一个可以使用 OWIN 来将用户登录、显示有关用户的某些信息以及将用户从应用中注销的 Web 应用。

## <a name="download"></a>下载
本教程的代码 [在 GitHub 上](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet)维护。  若要遵照该代码，可以[下载 .zip 格式的应用骨架](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip)，或克隆该骨架：

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

本教程末尾也提供完成的应用。

## <a name="register-an-app"></a>注册应用程序
在 [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) 中创建新应用，或遵循以下[详细步骤](active-directory-v2-app-registration.md)。  请确保：

* 复制分配给应用的**应用程序 ID**，稍后将要用到。
* 为应用添加 **Web** 平台。
* 输入正确的**重定向 URI**。 重定向 URI 向 Azure AD 指示身份验证响应应定向到的位置，本教程的默认值为 `https://localhost:44326/`。

## <a name="install--configure-owin-authentication"></a>安装并配置 OWIN 身份验证
在这里，我们要将 OWIN 中间件配置为使用 OpenID Connect 身份验证协议。  OWIN 用于发出登录和注销请求、管理用户的会话、获取有关用户的信息，等等。

1. 首先，打开位于项目根目录中的 `web.config` 文件，并在 `<appSettings>` 节中输入应用的配置值。

  * `ida:ClientId` 是在注册门户中为应用分配的**应用程序 ID**。
  * `ida:RedirectUri` 是在门户中输入的**重定向 URI**。

2. 接下来，使用包管理器控制台将 OWIN 中间件 NuGet 包添加到项目。

        ```
        PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
        PM> Install-Package Microsoft.Owin.Security.Cookies
        PM> Install-Package Microsoft.Owin.Host.SystemWeb
        ```  

3. 将称为 `Startup.cs` 的“OWIN 启动类”添加到项目。右键单击项目，选择“添加” --> “新建项”，并搜索“OWIN”。  当应用程序启动时，该 OWIN 中间件将调用 `Configuration(...)` 方法。
4. 将类声明更改为 `public partial class Startup` - 我们已在另一个文件中实现了此类的一部分。  在 `Configuration(...)` 方法中，调用 ConfigureAuth(...) 以设置 Web 应用的身份验证。  

        ```C#
        [assembly: OwinStartup(typeof(Startup))]
        
        namespace TodoList_WebApp
        {
            public partial class Startup
            {
                public void Configuration(IAppBuilder app)
                {
                    ConfigureAuth(app);
                }
            }
        }
        ```

5. 打开文件 `App_Start\Startup.Auth.cs` 并实现 `ConfigureAuth(...)` 方法。  在 `OpenIdConnectAuthenticationOptions` 中提供的参数将充当应用程序与 Azure AD 通信时使用的坐标。  还需要设置 Cookie 身份验证 - OpenID Connect 中间件会在幕后使用 Cookie。

        ```C#
        public void ConfigureAuth(IAppBuilder app)
                     {
                             app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);
        
                             app.UseCookieAuthentication(new CookieAuthenticationOptions());
        
                             app.UseOpenIdConnectAuthentication(
                                     new OpenIdConnectAuthenticationOptions
                                     {
                                             // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
                                             // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                                             // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.
        
                                             ClientId = clientId,
                                             Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
                                             RedirectUri = redirectUri,
                                             Scope = "openid email profile",
                                             ResponseType = "id_token",
                                             PostLogoutRedirectUri = redirectUri,
                                             TokenValidationParameters = new TokenValidationParameters
                                             {
                                                     ValidateIssuer = false,
                                             },
                                             Notifications = new OpenIdConnectAuthenticationNotifications
                                             {
                                                     AuthenticationFailed = OnAuthenticationFailed,
                                             }
                                     });
                     }
        ```

## <a name="send-authentication-requests"></a>发送身份验证请求
现在，应用程序已正确配置为使用 OpenID Connect 身份验证协议来与 v2.0 终结点通信。  OWIN 会处理有关创建身份验证消息、验证 Azure AD 提供的令牌以及保留用户会话的所有繁琐细节。  要做的一切就是提供某种方式让用户登录和注销。

- 可以在控制器中使用授权标记，要求用户在访问特定页面之前登录。  打开 `Controllers\HomeController.cs`，然后将 `[Authorize]` 标记添加到 About 控制器。
        
        ```C#
        [Authorize]
        public ActionResult About()
        {
          ...
        ```

- 还可以使用 OWIN 直接从代码内部发出身份验证请求。  打开 `Controllers\AccountController.cs`。  在 SignIn() 和 SignOut() 操作中，分别发出 OpenID Connect 质询和注销请求。

        ```C#
        public void SignIn()
        {
            // Send an OpenID Connect sign-in request.
            if (!Request.IsAuthenticated)
            {
                HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
            }
        }
        
        // BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
        public void SignOut()
        {
            // Send an OpenID Connect sign-out request.
            HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
            Response.Redirect("/");
        }
        ```

- 现在，请打开 `Views\Shared\_LoginPartial.cshtml`。  将在其中向用户显示应用程序的登录和注销链接，用户名会在视图中列显。

        ```HTML
        @if (Request.IsAuthenticated)
        {
            <text>
                <ul class="nav navbar-nav navbar-right">
                    <li class="navbar-text">
        
                        @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@
        
                        Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
                    </li>
                    <li>
                        @Html.ActionLink("Sign out", "SignOut", "Account")
                    </li>
                </ul>
            </text>
        }
        else
        {
            <ul class="nav navbar-nav navbar-right">
                <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
            </ul>
        }
        ```

## <a name="display-user-information"></a>显示用户信息
使用 OpenID Connect 对用户进行身份验证时，v2.0 终结点将向应用返回 id_token，其中包含有关用户的声明或断言。  可以使用这些声明来个性化应用程序：

- 打开 `Controllers\HomeController.cs` 文件。  可以通过 `ClaimsPrincipal.Current` 安全主体对象访问控制器中的用户声明。

        ```C#
        [Authorize]
        public ActionResult About()
        {
            ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;
        
            // The object ID claim will only be emitted for work or school accounts at this time.
            Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
            ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;
        
            // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
            ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;
        
            // The subject or nameidentifier claim can be used to uniquely identify the user
            ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        
            return View();
        }
        ```

## <a name="run"></a>运行
最后，生成并运行应用程序！   使用个人 Microsoft 帐户或者工作或学校帐户登录，随后你会看到该用户的标识已出现在顶部导航栏中。  Web 应用现在使用行业标准的协议进行保护，可以使用个人和工作/学校帐户来验证用户。

若要参考，请在[此处查看 .zip 格式的](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip)完整示例（不含配置值），也可从 GitHub 克隆该示例：

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## <a name="next-steps"></a>后续步骤
现在，可以转到更高级的主题。  可能想要尝试：

[使用 v2.0 终结点保护 Web API >>](active-directory-devquickstarts-webapi-dotnet.md)

有关更多资源，请查看：

* [v2.0 开发人员指南 >>](active-directory-appmodel-v2-overview.md)
* [StackOverflow“azure-active-directory”标记 >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>获取关于我们产品的安全更新
建议发生安全事件时获取相关通知，方法是访问 [此页](https://technet.microsoft.com/security/dd252948) 并订阅“安全公告通知”。
