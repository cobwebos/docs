---
title: 可将用户登录的 Web 应用（登录）- Microsoft 标识平台
description: 了解如何生成可将用户登录的 Web 应用（登录）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: be7801515355452306cd5e7afa709a0681c7c314
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562199"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>可将用户登录的 Web 应用 - 登录

了解如何在可将用户登录的 Web 应用的代码中添加登录。

## <a name="sign-in"></a>登录

只需使用前一篇文章[应用的代码配置](scenario-web-app-sign-user-app-configuration.md)中的代码就能实现注销。在用户登录到你的应用后，你可能希望他们能够注销。ASP.NET Core 将为你处理注销。

## <a name="what-sign-out-involves"></a>注销涉及的操作

从 Web 应用注销不仅仅涉及到从 Web 应用的状态中删除有关已登录帐户的信息。
Web 应用还必须将用户重定向到 Microsoft 标识平台`logout`终结点才能注销。当 Web 应用将用户重定向到 `logout` 终结点时，此终结点将从浏览器中清除用户的会话。 如果你的应用程序未进入`logout`终结点, 则用户将重新向你的应用程序重新进行身份验证, 而无需再次输入其凭据, 因为他们将与 Microsoft 标识平台终结点建立有效的单一登录会话。

若要了解详细信息, 请参阅 Microsoft 标识平台中的[发送注销请求](v2-protocols-oidc.md#send-a-sign-out-request)部分[和 OpenID connect 协议](v2-protocols-oidc.md)概念文档。

## <a name="application-registration"></a>应用程序注册

在应用程序注册期间，已注册一个**注销后的 URI**。 在本教程中，你已在“身份验证”页中“高级设置”部分的“注销 URL”字段中注册了 `https://localhost:44321/signout-oidc`。 有关详细信息，请参阅[注册 webApp 应用](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

## <a name="aspnet-core-code"></a>ASP.NET Core 代码

### <a name="signout-button"></a>注销按钮

注销按钮在 `Views\Shared\_LoginPartial.cshtml` 中公开，仅当存在已完成身份验证的帐户（即，用户事先已登录）时才会显示。

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

### <a name="signout-action-of-the-accountcontroller"></a>`AccountController` 的 `Signout()` 操作

在 Web 应用中按下“注销”按钮会触发 `Account` 控制器上的 `SignOut` 操作。 在以前的 ASP.NET Core 模板版本中，`Account` 控制器嵌入在 Web 应用中，但现在不再是这样，它现在是 ASP.NET Core 框架本身的一部分。 

可以从 ASP.NET Core 存储库的 [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs) 中获取 `AccountController` 的代码。 帐户控制：

- 将 OpenID 重定向 URI 设置为 `/Account/SignedOut`，以便在 Azure AD 执行注销后回调控制器
- 调用 `Signout()`，让 OpenIdConnect 中间件联系 Microsoft 标识平台 `logout` 终结点，这会：

  - 从浏览器中清除会话 Cookie；
  - 最后回调**注销 URL**，默认情况下，这会显示已注销视图页 [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml)（也作为 ASP.NET Core 的一部分提供）。

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>截获对 `logout` 终结点的调用

ASP.NET Core OpenIdConnect 中间件提供名为 `OnRedirectToIdentityProviderForSignOut` 的 OpenIdConnect 事件，可让应用截获对 Microsoft 标识平台 `logout` 终结点的调用。 Web 应用使用这种方法来尝试避免在用户注销时向其显示选择帐户对话框。这种截获是在 `Microsoft.Identity.Web` 可重用库的 `AddAzureAdV2Authentication` 中完成的。 请参阅 [StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

```CSharp
public static IServiceCollection AddAzureAdV2Authentication(this IServiceCollection services,
                                                            IConfiguration configuration)
{
    ...
    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
        ...
        options.Authority = options.Authority + "/v2.0/";
        ...
        // Attempt to avoid displaying the select account dialog when signing out
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            var user = context.HttpContext.User;
            context.ProtocolMessage.LoginHint = user.GetLoginHint();
            context.ProtocolMessage.DomainHint = user.GetDomainHint();
            await Task.FromResult(0);
        };
    }
}
```

## <a name="aspnet-code"></a>ASP.NET 代码

在 ASP.NET 中，注销是从控制器（例如 AccountController）中的 SignOut() 方法触发的。 此方法不是 ASP.NET 框架的一部分（与在 ASP.NET Core 中相反），并且不使用异步函数，正因如此，它会：

- 发送 OpenId 注销质询
- 清除缓存
- 重定向到所需的页面

```CSharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 MsalAppBuilder.ClearUserTokenCache();
 Response.Redirect("/");
}
```

## <a name="protocol"></a>Protocol

如果你不想要使用 ASP.NET Core 或 ASP.NET，可以查看 [Open ID Connect](./v2-protocols-oidc.md) 提供的协议文档。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转移到生产环境](scenario-web-app-sign-user-production.md)
