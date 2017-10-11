---
title: "Azure AD v2 ASP.NET Web 服务器获取已启动 – 使用 |Microsoft 文档"
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
ms.openlocfilehash: 3b7d29e48c91f40e8782a5e32a52998b815fe331
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>添加一个控制器来处理在登录和注销请求

此步骤演示如何创建新的控制器以公开在登录和注销方法。

1.  右键单击`Controllers`文件夹，然后选择`Add` > `Controller`
2.  选择`MVC (.NET version) Controller – Empty`。
3.  单击*添加*
4.  将其命名为`HomeController`单击*添加*
5.  添加*OWIN*对该类的引用：

```csharp
using Microsoft.Owin.Security;
using Microsoft.Owin.Security.Cookies;
using Microsoft.Owin.Security.OpenIdConnect;
```
<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
启动身份验证质询通过代码你的控制器中添加以下处理登录和注销的两个方法：
</li>
</ol>

```csharp
/// <summary>
/// Send an OpenID Connect sign-in request.
/// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
/// </summary>
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
    HttpContext.GetOwinContext().Authentication.SignOut(
            OpenIdConnectAuthenticationDefaults.AuthenticationType,
            CookieAuthenticationDefaults.AuthenticationType);
}
```

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>创建应用程序的主页上，以在用户通过登录按钮进行签名

在 Visual Studio 中，创建新视图，以添加在登录按钮和显示身份验证后的用户信息：

1.  右键单击`Views\Home`文件夹，然后选择`Add View`
2.  将其命名为`Index`。
3.  将以下 HTML，其中包括登录按钮，添加到该文件：

```html
<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <title>Sign-In with Microsoft Guide</title>
</head>
<body>
@if (!Request.IsAuthenticated)
{
    <!-- If the user is not authenticated, display the sign-in button -->
    <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
        <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
        <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
        <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
        <rect x="150" y="129" width="122" height="122" fill="#F35325" />
        <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
        <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
        <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
        <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
        </svg>
    </a>
}
else
{
    <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
    <br /><br />
    @Html.ActionLink("See Your Claims", "Index", "Claims")
    <br /><br />
    @Html.ActionLink("Sign out", "SignOut", "Home")
}
@if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
{
    <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
}
</body>
</html>
```
<!--start-collapse-->
### <a name="more-information"></a>详细信息
> 此页可将登录按钮添加带有黑色背景的 SVG 格式：<br/>![使用 Microsoft 登录](media/active-directory-serversidewebapp-aspnetwebappowin-use/aspnetsigninbuttonsample.png)<br/> 对于多个登录按钮，请转到[本页](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Branding guidelines")。
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>添加一个控制器来显示用户的声明
此控制器演示的使用`[Authorize]`属性以保护控制器。 通过仅允许经过身份验证的用户，此特性将访问限制到的控制器。 下面的代码使用的属性显示为登录的一部分中检索到的用户声明。

1.  右键单击`Controllers`文件夹：`Add` > `Controller`
2.  选择`MVC {version} Controller – Empty`。
3.  单击*添加*
4.  将其命名为`ClaimsController`
5.  将控制器类的代码替换为下面的代码这会将添加`[Authorize]`到类属性：

```csharp
[Authorize]
public class ClaimsController : Controller
{
    /// <summary>
    /// Add user's claims to viewbag
    /// </summary>
    /// <returns></returns>
    public ActionResult Index()
    {
        var claimsPrincipalCurrent = System.Security.Claims.ClaimsPrincipal.Current;
        //You get the user’s first and last name below:
        ViewBag.Name = claimsPrincipalCurrent.FindFirst("name").Value;

        // The 'preferred_username' claim can be used for showing the username
        ViewBag.Username = claimsPrincipalCurrent.FindFirst("preferred_username").Value;

        // The subject claim can be used to uniquely identify the user across the web
        ViewBag.Subject = claimsPrincipalCurrent.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier).Value;

        // TenantId is the unique Tenant Id - which represents an organization in Azure AD
        ViewBag.TenantId = claimsPrincipalCurrent.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;

        return View();
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>详细信息
> 因为使用了`[Authorize]`可以仅执行属性，此控制器的所有方法，当用户进行身份验证。 如果用户未经过身份验证，并尝试访问控制器，OWIN 将启动身份验证质询，并强制用户进行身份验证。 上面的代码查看的声明集合`ClaimsPrincipal.Current`中的用户的令牌中包含的特定用户属性的实例。 这些属性包括用户的完整名称和用户名，以及全局用户标识符使用者。 它还包含*租户 ID*，它表示用户的组织的 ID。 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>创建视图来显示用户的声明

在 Visual Studio 中，创建新视图，以在网页上显示用户的声明：

1.  右键单击`Views\Claims`文件夹和：`Add View`
2.  将其命名为`Index`。
3.  将以下 HTML 添加到文件：

```html
<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <title>Sign-In with Microsoft Sample</title>
    <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
</head>
<body style="padding:50px">
    <h3>Main Claims:</h3>
    <table class="table table-striped table-bordered table-hover">
        <tr><td>Name</td><td>@ViewBag.Name</td></tr>
        <tr><td>Username</td><td>@ViewBag.Username</td></tr>
        <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
        <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
    </table>
    <br />
    <h3>All Claims:</h3>
    <table class="table table-striped table-bordered table-hover table-condensed">
    @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
    {
        <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
    }
</table>
    <br />
    <br />
    @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
</body>
</html>
```
