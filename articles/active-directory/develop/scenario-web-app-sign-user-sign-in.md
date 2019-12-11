---
title: 编写用于登录用户的 web 应用-Microsoft 标识平台 |Microsoft
description: 了解如何构建登录用户的 web 应用（登录）
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8d7d5737a8332416a225154709ab7d66e447764
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74961975"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>登录用户的 Web 应用：登录和注销

了解如何在 web 应用程序中为登录用户的代码添加登录。 然后，了解如何让用户注销。

## <a name="sign-in"></a>登录

登录由两部分组成：

- HTML 页上的 "登录" 按钮
- 控制器的代码隐藏中的登录操作

### <a name="sign-in-button"></a>"登录" 按钮

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core 中，`Views\Shared\_LoginPartial.cshtml`中公开了 "登录" 按钮。 仅当没有经过身份验证的帐户时，才会显示该帐户。 也就是说，当用户尚未登录或注销时，会显示该用户。

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET MVC 中，`Views\Shared\_LoginPartial.cshtml`中公开了 "注销" 按钮。 仅当有已经过身份验证的帐户时，才会显示该帐户。 也就是说，当用户以前登录时，将显示它。

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

在 Java 快速入门中，"登录" 按钮位于[main/resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html)文件中。

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 快速入门中没有 "登录" 按钮。 代码隐藏在到达 web 应用程序的根时，会自动提示用户进行登录。 请参阅[py # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18)。

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>控制器 `SignIn` 操作

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET 中，选择 web 应用中的 "**登录**" 按钮会触发 `AccountController` 控制器上的 `SignIn` 操作。 在以前版本的 ASP.NET core 模板中，`Account` 控制器已嵌入到 web 应用中。 这并不是因为控制器现在是 ASP.NET Core 框架的一部分。

`AccountController` 的代码可从[AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)中的 ASP.NET Core 存储库获取。 帐户控制通过重定向到 Microsoft 标识平台终结点来质询用户。 有关详细信息，请参阅 ASP.NET Core 中提供的[登录](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31)方法。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，从控制器上的 `SignOut()` 方法（例如， [AccountController # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)）触发注销。 此方法不是 ASP.NET 框架的一部分（与 ASP.NET Core 中发生的情况相反）。 建议在建议重定向 URI 后发送 OpenID 登录质询。

```CSharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

在 Java 中，通过直接调用 Microsoft 标识平台 `logout` 终结点并提供 `post_logout_redirect_uri` 值来处理注销。 有关详细信息，请参阅[AuthPageController # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)。

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="pythontabpython"></a>[Python](#tab/python)

与其他平台不同，MSAL Python 会让用户从登录页登录。 请参阅[py # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28)。

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

`_build_msal_app()` 方法是在[py # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88)中定义的，如下所示：

```Python
def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

用户登录到你的应用后，你将需要启用它们。

## <a name="sign-out"></a>注销

从 web 应用注销只涉及从 web 应用的状态中删除有关登录帐户的信息。
Web 应用还必须将用户重定向到 Microsoft 标识平台 `logout` 终结点才能注销。 

当你的 web 应用将用户重定向到 `logout` 终结点时，此终结点将从浏览器中清除用户的会话。 如果你的应用未进入 `logout` 终结点，则用户将重新对你的应用进行身份验证，而无需再次输入其凭据。 原因是它们将与 Microsoft 标识平台终结点建立有效的单一登录会话。

若要了解详细信息，请参阅 Microsoft 标识平台中的[发送注销请求](v2-protocols-oidc.md#send-a-sign-out-request)部分[和 OpenID connect 协议](v2-protocols-oidc.md)文档。

### <a name="application-registration"></a>应用程序注册

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在应用程序注册过程中，你将注册注销后的 URI。 在本教程中，你已在 "**身份验证**" 页上的 "**高级设置**" 部分的 "**注销 URL** " 字段中注册 `https://localhost:44321/signout-oidc`。 有关详细信息，请参阅[注册 webApp 应用](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在应用程序注册过程中，你将注册注销后的 URI。 在本教程中，你已在 "**身份验证**" 页上的 "**高级设置**" 部分的 "**注销 URL** " 字段中注册 `https://localhost:44308/Account/EndSession`。 有关详细信息，请参阅[注册 webApp 应用](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)。

# <a name="javatabjava"></a>[Java](#tab/java)

在应用程序注册过程中，你将注册注销后的 URI。 在本教程中，你已在 "**身份验证**" 页上的 "**高级设置**" 部分的 "**注销 URL** " 字段中注册 `http://localhost:8080/msal4jsample/sign_out`。

# <a name="pythontabpython"></a>[Python](#tab/python)

在应用程序注册过程中，无需注册额外的注销 URL。 应用程序将在其主 URL 上回调。

---

### <a name="sign-out-button"></a>"注销" 按钮

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core 中，`Views\Shared\_LoginPartial.cshtml`中显示 "注销" 按钮。 仅当有已经过身份验证的帐户时，才会显示该帐户。 也就是说，当用户以前登录时，将显示它。

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

在 ASP.NET MVC 中，`Views\Shared\_LoginPartial.cshtml`中公开了 "注销" 按钮。 仅当有已经过身份验证的帐户时，才会显示该帐户。 也就是说，当用户以前登录时，将显示它。

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

在 Python 快速入门中，"注销" 按钮位于[templates/index .html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10)文件中。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python Web App</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>控制器 `SignOut` 操作

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET 中，选择 web 应用中的 "**注销**" 按钮会触发 `AccountController` 控制器上的 `SignOut` 操作。 在以前版本的 ASP.NET Core 模板中，`Account` 控制器已嵌入到 web 应用中。 这并不是因为控制器现在是 ASP.NET Core 框架的一部分。

在[AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)中的 ASP.NET core 存储库中提供了 `AccountController` 的代码。 帐户控制：

- 将 OpenID 重定向 URI 设置为 `/Account/SignedOut`，以便当 Azure AD 完成注销时，回调该控制器。
- 调用 `Signout()`，这允许 OpenID Connect 中间件与 Microsoft 标识平台 `logout` 终结点联系。 然后终结点：

  - 从浏览器中清除会话 cookie。
  - 回调注销 URL。 默认情况下，注销 URL 显示注销视图页面[SignedOut](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml)。 此页还作为 ASP.NET Core 的一部分提供。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，从控制器上的 `SignOut()` 方法（例如， [AccountController # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)）触发注销。 此方法不是 ASP.NET 框架的一部分，这与 ASP.NET Core 中发生的情况相反。 以便

- 发送 OpenID 注销质询。
- 清除缓存。
- 重定向到所需的页。

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

在 Java 中，通过直接调用 Microsoft 标识平台 `logout` 终结点并提供 `post_logout_redirect_uri` 值来处理注销。 有关详细信息，请参阅[AuthPageController # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)。

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

注销用户的代码位于[py # L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48)中。

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>截获对 `logout` 终结点的调用

注销后的 URI 使应用程序能够参与全局注销。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core OpenID Connect 中间件使你的应用能够通过提供名为 `OnRedirectToIdentityProviderForSignOut`的 OpenID Connect 事件来截获对 Microsoft 标识平台 `logout` 终结点的调用。 有关如何订阅此事件（清除令牌缓存）的示例，请参阅 WebAppServiceCollectionExtensions [/.cs # L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156)。

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

在 Java 快速入门中，注销后的重定向 URI 只显示 "索引" 页。

# <a name="pythontabpython"></a>[Python](#tab/python)

在 Python 快速入门中，注销后的重定向 URI 只显示 "索引" 页。

---

## <a name="protocol"></a>协议

如果要了解有关注销的详细信息，请阅读[OPEN ID Connect](./v2-protocols-oidc.md)中提供的协议文档。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转到生产](scenario-web-app-sign-user-production.md)
