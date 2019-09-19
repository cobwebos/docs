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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ed61a09ffc76b4813dcb97330d3a1a436aa16eb
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086461"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>可将用户登录的 Web 应用 - 登录

了解如何在可将用户登录的 Web 应用的代码中添加登录。

## <a name="sign-in"></a>登录

在之前的[应用程序代码配置](scenario-web-app-sign-user-app-configuration.md)中查看的代码是实现登录所需的全部代码。
在用户登录到你的应用后，你可能希望他们能够注销。ASP.NET Core 将为你处理注销。

## <a name="what-sign-out-involves"></a>注销涉及的操作

从 Web 应用注销不仅仅涉及到从 Web 应用的状态中删除有关已登录帐户的信息。
该 Web 应用还必须将用户重定向到 Microsoft 标识平台 `logout` 终结点才能注销。当 Web 应用将用户重定向到 `logout` 终结点时，此终结点将从浏览器中清除用户的会话。 如果应用未转到 `logout` 终结点，则用户不需要再次输入凭据就能重新通过应用的身份验证，因为他们与 Microsoft 标识平台终结点之间存在有效的单一登录会话。

有关详细信息，请参阅 [Microsoft 标识平台和 OpenID Connect 协议](v2-protocols-oidc.md)概念文档中的[发送注销请求](v2-protocols-oidc.md#send-a-sign-out-request)部分。

### <a name="application-registration"></a>应用程序注册

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在应用程序注册期间，已注册一个**注销后的 URI**。 在本教程中，你已在“身份验证”页中“高级设置”部分的“注销 URL”字段中注册了 `https://localhost:44321/signout-oidc`。 有关详细信息，请参阅[注册 webApp 应用](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在应用程序注册期间，已注册一个**注销后的 URI**。 在本教程中，你已在“身份验证”页中“高级设置”部分的“注销 URL”字段中注册了 `https://localhost:44308/Account/EndSession`。 有关详细信息，请参阅[注册 webApp 应用](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)

# <a name="javatabjava"></a>[Java](#tab/java)

在应用程序注册过程中，将注册一个**注销后的 URI**。 在本教程中，你已在“身份验证”页中“高级设置”部分的“注销 URL”字段中注册了 `http://localhost:8080/msal4jsample/`。

# <a name="pythontabpython"></a>[Python](#tab/python)

在应用程序注册过程中，无需注册注销 URL。 该示例不实现全局注销

---

### <a name="sign-out-button"></a>"注销" 按钮

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core 中，将在中`Views\Shared\_LoginPartial.cshtml`显示 "注销" 按钮，并仅在存在已验证的帐户时显示（即用户以前已登录）。

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

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET MVC 中，将在中`Views\Shared\_LoginPartial.cshtml`公开 "注销" 按钮，并且仅在存在已验证的帐户时显示（即用户以前已登录的情况）。

```html
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

# <a name="javatabjava"></a>[Java](#tab/java)

在 Java 快速入门中，"注销" 按钮位于 main/resources/templates/auth_page.html 文件中。

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="pythontabpython"></a>[Python](#tab/python)

在 Python 快速入门中，"注销" 按钮位于[templates/display.html](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/templates/display.html#L18-L20)文件中。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Acquire Token Result </title>
</head>
<body>
    {% if cond  %}
        <p1><b>Your information</b> </p1>
        <table>
        {% for key, value in auth_result.items() %}
           <tr>
                <th> {{ key }} </th>
                <td> {{ value }} </td>
           </tr>
        {% endfor %}
        </table>
        <form action="/logout" >
            <input type="submit" value=" Logout"/>
        </form>
    {% else %}
        <p1><b> {{auth_result}} </b> </p1>
        <form action="/authenticate" >
            <input type="submit" value=" Sign-in"/>
        </form>
    {% endif %}
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`Signout()`控制器的操作

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET 中，按 web 应用上的 "**注销**" 按钮会触发`SignOut` `AccountController`控制器上的操作。 在以前版本的 ASP.NET core 模板中， `Account`控制器已嵌入到 web 应用中，但不再是这种情况，因为它现在是 ASP.NET Core 框架自身的一部分。

可以从 ASP.NET Core 存储库的 [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs) 中获取 `AccountController` 的代码。 帐户控制：

- 将 OpenID 重定向 URI 设置`/Account/SignedOut`为，以便在 Azure AD 执行注销时，回调控制器
- 调用 `Signout()`，让 OpenIdConnect 中间件联系 Microsoft 标识平台 `logout` 终结点，这会：

  - 从浏览器中清除会话 Cookie；
  - 最后调用**注销 URL**，默认情况下，该 URL 将显示 "注销视图" 页[SignedOut](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml)作为 ASP.NET Core 的一部分提供。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，从控制器上的`SignOut()`方法（例如 AccountController）触发注销。 此方法不是 ASP.NET 框架的一部分（与 ASP.NET Core 中发生的情况相反）。 它不使用`async`，这就是为什么：

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
 Response.Redirect("/");
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

在 Java 中，通过直接调用 Microsoft 标识平台注销终结点并提供 post_logout_redirect_uri 来处理注销。

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
@app.route("/logout")
def logout():
    return flask.redirect(flask.url_for('index'))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>截获对 `logout` 终结点的调用

注销后的 URI 使应用程序能够参与到全局注销。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core OpenIdConnect 中间件提供名为 `OnRedirectToIdentityProviderForSignOut` 的 OpenIdConnect 事件，可让应用截获对 Microsoft 标识平台 `logout` 终结点的调用。 有关如何订阅此事件（清除令牌缓存）的示例，请参阅[WebAppServiceCollectionExtensions/.cs # L151-L156。](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156)

```CSharp
               // Handling the global sign-out
                options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
                {
                    // Forget about the signed-in user
                };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，你将委托给中间件以执行注销，并清除会话 cookie：

```CSharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

在 Java 快速入门中，发布注销重定向 URI 只显示 "索引" 页。 

# <a name="pythontabpython"></a>[Python](#tab/python)

在 Python 快速入门中，发布注销重定向 URI 只显示 "索引" 页。

---

## <a name="protocol"></a>Protocol

如果要了解有关注销的详细信息，请阅读[打开 ID Connect](./v2-protocols-oidc.md)中提供的协议文档。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转移到生产环境](scenario-web-app-sign-user-production.md)
