---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 98bb86be1e1d0dccb5a76b91489e664ee4a30765
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943583"
---
## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>添加控制器来处理登录和注销请求

此步骤演示如何创建新控制器来公开登录和注销方法。

1.  右键单击 `Controllers` 文件夹，并选择`Add` > `Controller`
2.  选择 `MVC (.NET version) Controller – Empty`。
3.  单击“添加”
4.  将其命名为 `HomeController`，然后单击“添加”
5.  向该类添加 OWIN 引用：

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```
    
6. 通过代码启动身份验证质询，添加下面两个方法来处理控制器登录和注销：
    
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

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>创建应用的主页，通过登录按钮来登录用户

在 Visual Studio 中，创建新视图来添加登录按钮并在身份验证后显示用户信息：

1.  右键单击 `Views\Home` 文件夹，并选择`Add View`
2.  将它命名为 `Index`。
3.  向文件添加以下 HTML，其中包括登录按钮：

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
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
> ### <a name="more-information"></a>更多信息
> 此页以 SVG 形式添加登录按钮，背景为黑色：<br/>![Microsoft 登录](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> 对于多个登录按钮，请转到[本页](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "品牌准则")。
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>添加控制器来显示用户声明
此控制器演示如何使用 `[Authorize]` 属性来保护控制器。 此属性只允许通过身份验证的用户，从而限制对控制器的访问。 下面的代码使用该属性来显示作为登录的一部分被检索的用户声明。

1.  右键单击 `Controllers` 文件夹，并选择`Add` > `Controller`
2.  选择 `MVC {version} Controller – Empty`。
3.  单击“添加”
4.  将其命名为 `ClaimsController`
5.  将控制器类的代码替换为下面的代码，这将 `[Authorize]` 属性添加到类：

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
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;
    
            //You get the user’s first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;
    
            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;
    
            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;
    
            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;
    
            return View();
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>更多信息
> 因为使用 `[Authorize]` 属性，仅当用户通过身份验证后，才执行此控制器的所有方法。 如果用户未通过身份验证，并尝试访问控制器，OWIN 将启动身份验证质询，并强制用户进行身份验证。 上面的代码查看用户的 ID 令牌中包含的特定用户属性的声明列表。 这些属性包括用户的完整姓名和用户名，以及全局用户标识符使用者。 它还包含租户 ID，表示用户的组织的 ID。 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>创建视图来显示用户的声明

在 Visual Studio 中创建新视图，以在网页上显示用户的声明：

1.  右键单击 `Views\Claims` 文件夹，并选择`Add View`
2.  将它命名为 `Index`。
3.  将以下 HTML 添加到文件：

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
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
