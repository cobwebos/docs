---
title: 编写可使用户登录/注销用户的 Web 应用 - Microsoft 标识平台 | Azure
description: 了解如何生成可使用户登录/注销用户的 Web 应用
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: df02c7d2ace6c58d86f4044607eca386f1790e1d
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734308"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>用于登录用户的 Web 应用：登录和注销

了解如何在可将用户登录的 Web 应用的代码中添加登录。 然后了解如何让用户注销。

## <a name="sign-in"></a>登录

登录由两个部分组成：

- HTML 页中的登录按钮
- 控制器 code-behind 中的登录操作

### <a name="sign-in-button"></a>登录按钮

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core 中，对于 Microsoft 标识平台应用程序，"**登录**" 按钮在`Views\Shared\_LoginPartial.cshtml`中公开（对于 MVC 应用） `Pages\Shared\_LoginPartial.cshtm`或（适用于 Razor 应用）。 仅当用户未经过身份验证时才会显示。 也就是说，当用户尚未登录或注销时，会显示该用户。相反，当用户已登录时，将显示 "**注销**" 按钮。 请注意，帐户控制器是在 MicrosoftIdentity NuGet**包**中的名为 " **MicrosoftIdentity** " 的区域中定义的。

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET MVC 中，注销按钮在 `Views\Shared\_LoginPartial.cshtml` 中公开。 仅当存在已完成身份验证的帐户时，才会显示此按钮。 即，仅当用户事先已登录时才显示。

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

# <a name="java"></a>[Java](#tab/java)

在 Java 快速入门中，登录按钮位于 [main/resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/resources/templates/index.html) 文件中。

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

# <a name="python"></a>[Python](#tab/python)

Python 快速入门中没有登录按钮。 进入 Web 应用的根目录时，code-behind 会自动提示用户登录。 请参阅 [app.py#L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18)。

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>控制器的 `SignIn` 操作

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET 中，在 Web 应用中选择“登录”按钮会触发 `AccountController` 控制器上的 `SignIn` 操作。  在以前的 ASP.NET Core 模板版本中，`Account` 控制器嵌入在 Web 应用中。 这并不是这样，因为控制器现在是**Microsoft 的 web.config**包的一部分。 有关详细信息，请参阅[AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) 。

此控制器还处理 Azure AD B2C 应用程序。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，注销是从控制器（例如 [AccountController.cs#L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)）中的 `SignOut()` 方法触发的。 此方法不是 ASP.NET 框架的一部分（与 ASP.NET Core 中的情况相反）。 它在提议重定向 URI 后发送 OpenID 登录质询。

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

在 Java 中，注销是通过直接调用 Microsoft 标识平台 `logout` 终结点并提供 `post_logout_redirect_uri` 值来处理的。 有关详细信息，请参阅 [AuthPageController.java#L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)。

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

# <a name="python"></a>[Python](#tab/python)

与其他平台不同，MSAL Python 将负责处理让用户从登录页登录的过程。 请参阅 [app.py#L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28)。

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

`_build_msal_app()` 方法在 [app.py#L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) 中定义，如下所示：

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

在用户登录到你的应用后，你希望他们能够注销。

## <a name="sign-out"></a>注销

从 Web 应用注销不仅仅涉及到从 Web 应用的状态中删除有关已登录帐户的信息。
该 Web 应用还必须将用户重定向到 Microsoft 标识平台 `logout` 终结点才能注销。

当 Web 应用将用户重定向到 `logout` 终结点时，此终结点将从浏览器中清除用户的会话。 如果应用尚未进入 `logout` 终结点，则用户不需要再次输入凭据就能重新通过应用的身份验证。 原因是他们与 Microsoft 标识平台终结点之间建立了有效的单一登录会话。

有关详细信息，请参阅 [Microsoft 标识平台和 OpenID Connect 协议](v2-protocols-oidc.md)文档中的[发送注销请求](v2-protocols-oidc.md#send-a-sign-out-request)部分。

### <a name="application-registration"></a>应用程序注册

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在应用程序注册期间，需要注册一个注销后的 URI。 在本教程中，你已在“身份验证”页上“高级设置”部分的“注销 URL”字段中注册了 `https://localhost:44321/signout-oidc`。    有关详细信息，请参阅[注册 webApp 应用](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在应用程序注册期间，需要注册一个注销后的 URI。 在本教程中，你已在“身份验证”页上“高级设置”部分的“注销 URL”字段中注册了 `https://localhost:44308/Account/EndSession`。    有关详细信息，请参阅[注册 webApp 应用](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)。

# <a name="java"></a>[Java](#tab/java)

在应用程序注册期间，需要注册一个注销后的 URI。 在本教程中，你已在“身份验证”页上“高级设置”部分的“注销 URL”字段中注册了 `http://localhost:8080/msal4jsample/sign_out`。   

# <a name="python"></a>[Python](#tab/python)

在应用程序注册过程中，无需注册额外的注销 URL。 将在应用的主 URL 上回调应用。

---

### <a name="sign-out-button"></a>注销按钮

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET 中，在 web 应用中选择 "**注销**" 按钮会`SignOut`触发`AccountController`控制器上的操作（请参阅下文）

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET MVC 中，注销按钮在 `Views\Shared\_LoginPartial.cshtml` 中公开。 仅当存在已完成身份验证的帐户时，才会显示此按钮。 即，仅当用户事先已登录时才显示。

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

# <a name="java"></a>[Java](#tab/java)

在 Java 快速入门中，注销按钮位于 main/resources/templates/auth_page.html 文件中。

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="python"></a>[Python](#tab/python)

在 Python 快速入门中，注销按钮位于 [templates/index.html#L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) 文件中。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python web app</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>控制器的 `SignOut` 操作

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

在以前的 ASP.NET Core 模板版本中，`Account` 控制器嵌入在 Web 应用中。 这并不是这样，因为控制器现在是**Microsoft 的 web.config**包的一部分。 有关详细信息，请参阅[AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) 。

- 将 OpenID 重定向 URI 设置为 `/Account/SignedOut`，以便在 Azure AD 完成注销后回调控制器。
- 调用 `Signout()`，让 OpenID Connect 中间件联系 Microsoft 标识平台 `logout` 终结点。 然后，终结点将会：

  - 从浏览器中清除会话 Cookie。
  - 回调注销 URL。 默认情况下，注销 URL 会显示已注销视图页 [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml)。 此页还作为 MIcrosoft 的一部分提供。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，注销是从控制器（例如 [AccountController.cs#L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)）中的 `SignOut()` 方法触发的。 与 ASP.NET Core 中的情况相反，此方法不是 ASP.NET 框架的一部分。 该方法：

- 发送 OpenID 注销质询。
- 清除缓存。
- 重定向到所需的页面。

```csharp
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

# <a name="java"></a>[Java](#tab/java)

在 Java 中，注销是通过直接调用 Microsoft 标识平台 `logout` 终结点并提供 `post_logout_redirect_uri` 值来处理的。 有关详细信息，请参阅 [AuthPageController.java#L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)。

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

# <a name="python"></a>[Python](#tab/python)

将用户注销的代码位于 [app.py#L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48) 中。

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

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core OpenID Connect 中间件提供名为 `OnRedirectToIdentityProviderForSignOut` 的 OpenID Connect 事件，可让应用截获对 Microsoft 标识平台 `logout` 终结点的调用。 此方法由 Microsoft 身份验证（这会在 web 应用调用 web api 的情况下清除帐户）

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，需委托中间件执行注销，并清除会话 Cookie：

```csharp
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

# <a name="java"></a>[Java](#tab/java)

在 Java 快速入门中，注销后的重定向 URI 只显示 index.html 页。

# <a name="python"></a>[Python](#tab/python)

在 Python 快速入门中，注销后的重定向 URI 只显示 index.html 页。

---

## <a name="protocol"></a>协议

若要了解有关注销的详细信息，请阅读 [OpenID Connect](./v2-protocols-oidc.md) 提供的协议文档。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转移到生产环境](scenario-web-app-sign-user-production.md)
