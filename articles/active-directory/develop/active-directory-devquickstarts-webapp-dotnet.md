---
title: "Azure AD .NET Web 应用入门 | Microsoft Docs"
description: "生成一个与 Azure AD 集成以支持登录的 .NET MVC Web 应用。"
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: e15a41a4-dc5d-4c90-b3fe-5dc33b9a1e96
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 43ba592b6294a9a75a20dacd81953a77c241b89f
ms.contentlocale: zh-cn
ms.lasthandoff: 03/18/2017


---
# <a name="aspnet-web-app-sign-in-and-sign-out-with-azure-ad"></a>使用 Azure AD 进行 ASP.NET Web 应用登录和注销
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

使用 Azure Active Directory (Azure AD)，只需编写几行代码、提供单一登录和注销，就能简单外包 Web 应用的标识管理。 通过使用 Microsoft 适用于 .NET 的开放 Web 界面 (OWIN) 中间件将用户登录到 ASP.NET Web 应用或从 ASP.NET Web 应用注销。 .NET Framework 4.5 中包含社区驱动 OWIN 中间件。 本文将演示如何使用 OWIN 执行以下操作：

* 使用 Azure AD 作为标识提供者将用户登录到 Web 应用。
* 显示某些用户信息。
* 将用户从应用中注销。

## <a name="before-you-get-started"></a>准备工作
* 下载[应用框架](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip)或下载[已完成的示例](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip)。
* 你还需要一个用于注册应用的 Azure AD 租户。 如果你还没有 Azure AD 租户，请[了解如何获取租户](active-directory-howto-tenant.md)。

准备好后，请按照以下 4 个部分中的步骤操作。

## <a name="step-1-register-the-new-app-with-azure-ad"></a>步骤 1：向 Azure AD 注册新的应用
若要设置应用对用户进行身份验证，请先执行以下操作，在租户中注册用户：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在顶部栏中，单击你的帐户名。 在“目录”列表下选择要注册应用的 Active Directory 租户。
3. 在左窗格中，单击“更多服务”，然后选择“Azure Active Directory”。
4. 单击“应用注册”，然后选择“添加”。
5. 根据提示创建一个新 **Web 应用程序和/或 WebAPI**。
  * **名称** - 向用户描述应用。
  * **登录 URL** 是应用的基本 URL。 框架的默认 URL 是 https://localhost:44320/。
6. 完成注册后，Azure AD 将为应用分配唯一的应用程序 ID。 请复制应用页中的值，以便在后续部分中使用。
7. 从应用程序的“设置” -> “属性”页中，更新应用 ID URI。 **应用 ID URI** 是应用的唯一标识符。 命名约定为 `https://<tenant-domain>/<app-name>`（例如 `https://contoso.onmicrosoft.com/my-first-aad-app`）。

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>步骤 2：将应用设置为使用 OWIN 身份验证管道
在此步骤中，将 OWIN 中间件配置为使用 OpenID Connect 身份验证协议。 使用 OWIN 发出登录和注销请求、管理用户会话以及获取用户信息等。

1. 若要开始，请使用 Package Manager Console 将 OWIN 中间件 NuGet 包添加到项目。

     ```
     PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
     PM> Install-Package Microsoft.Owin.Security.Cookies
     PM> Install-Package Microsoft.Owin.Host.SystemWeb
     ```

2. 将称为 `Startup.cs` 的 OWIN 启动类添加到项目。右键单击项目，依次选择“添加”、“新建项”，然后搜索“OWIN”。 该应用启动时，OWIN 中间件会调用 **Configuration(...)** 方法。
3. 将类声明更改为 `public partial class Startup`。 我们已在另一个文件中实现了此类的一部分。 在 **Configuration(...)** 方法中，调用 **ConfgureAuth(...)** 以设置应用的身份验证。  

     ```C#
     public partial class Startup
     {
         public void Configuration(IAppBuilder app)
         {
             ConfigureAuth(app);
         }
     }
     ```

4. 打开 App_Start\Startup.Auth.cs 文件，然后实现 **ConfigureAuth(...)** 方法。 在 OpenIDConnectAuthenticationOptions 中提供的参数将充当应用与 Azure AD 通信时使用的坐标。 你还需要设置 Cookie 身份验证，因为 OpenID Connect 中间件将在后台使用 Cookie。

     ```C#
     public void ConfigureAuth(IAppBuilder app)
     {
         app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

         app.UseCookieAuthentication(new CookieAuthenticationOptions());

         app.UseOpenIdConnectAuthentication(
             new OpenIdConnectAuthenticationOptions
             {
                 ClientId = clientId,
                 Authority = authority,
                 PostLogoutRedirectUri = postLogoutRedirectUri,
                 Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = context =>
                        {
                            context.HandleResponse();
                            context.Response.Redirect("/Error?message=" + context.Exception.Message);
                            return Task.FromResult(0);
                        }
                    }
             });
     }
     ```

5. 打开位于项目根目录中的 web.config 文件，然后在 `<appSettings>` 节中输入配置值。
  * `ida:ClientId`：在“第 1 步：向 Azure AD 注册新应用”中从 Azure 门户复制的 GUID。
  * `ida:Tenant`：Azure AD 租户的名称，例如 contoso.onmicrosoft.com。
  * `ida:PostLogoutRedirectUri`：在成功完成注销请求后，告知 Azure AD 要将用户重定向到哪个位置的指示器。

## <a name="step-3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>步骤 3：使用 OWIN 向 Azure AD 发出登录和注销请求
现在，应用已正确配置为使用 OpenID Connect 身份验证协议来与 Azure AD 进行通信。 OWIN 已处理有关创建身份验证消息、验证 Azure AD 提供的令牌以及保留用户会话的细节。 你要做的一切就是提供某种方式让用户登录和注销。

1. 可以在控制器中使用授权标记，要求用户在访问特定页面之前登录。 为此，请打开 Controllers\HomeController.cs，然后将 `[Authorize]` 标记添加到 About 控制器。

     ```C#
     [Authorize]
     public ActionResult About()
     {
       ...
     ```

2. 还可以使用 OWIN 直接从代码内部发出身份验证请求。 为此，请打开 Controllers\AccountController.cs。 然后，在 SignIn() 和 SignOut() 操作中，发出 OpenID Connect 质询并注销请求。

     ```C#
     public void SignIn()
     {
         // Send an OpenID Connect sign-in request.
         if (!Request.IsAuthenticated)
         {
             HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
         }
     }
     public void SignOut()
     {
         // Send an OpenID Connect sign-out request.
         HttpContext.GetOwinContext().Authentication.SignOut(
              OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
     }
     ```

3. 打开 Views\Shared\_LoginPartial.cshtml 以向用户显示应用登录和注销链接，并在视图中打印用户名称。

    ```HTML
    @if (Request.IsAuthenticated)
    {
     <text>
         <ul class="nav navbar-nav navbar-right">
             <li class="navbar-text">
                 Hello, @User.Identity.Name!
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

## <a name="step-4-display-user-information"></a>步骤 4：显示用户信息
使用 OpenID Connect 对用户进行身份验证时，Azure AD 将向应用返回 id_token，其中包含有关用户的“声明”或断言。 可以通过执行以下操作，使用这些声明对应用进行个性化设置：

1. 打开 Controllers\HomeController.cs 文件。 可以通过 `ClaimsPrincipal.Current` 安全主体对象访问控制器中的用户声明。

 ```C#
 public ActionResult About()
 {
     ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
     ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
     ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
     ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
     ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

     return View();
 }
 ```

2. 构建并运行应用程序。 如果尚未使用 .onmicrosoft.com 域在租户中创建一个新的用户，则现在可以执行此操作。 方法如下：

  a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 以该用户的身份登录，然后注意该用户的标识在顶部栏中的显示方式。

  b.保留“数据库类型”设置，即设置为“共享”。 注销，然后以租户中其他用户的身份重新登录。

  c. 如果你有浓厚的兴趣，可以注册并运行此应用的另一个实例（使用其自身的 clientId），然后观察单一登录的运作方式。

## <a name="next-steps"></a>后续步骤
有关参考，请参阅[已完成示例](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip)（无需配置值）。

现在，你可以转到更高级的主题。 例如，请尝试[使用 Azure AD 保护 Web API](active-directory-devquickstarts-webapi-dotnet.md)。

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

