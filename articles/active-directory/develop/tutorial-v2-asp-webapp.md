---
title: 将登录添加到 Microsoft 标识平台 ASP.NET Web 应用
titleSuffix: Microsoft identity platform
description: 使用基于传统 Web 浏览器的应用程序和 OpenID Connect 标准，在 ASP.NET 解决方案中实现 Microsoft 登录
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: e33f52d5c1f9c06a5acbae5c66b051ca82ef14c0
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126646"
---
# <a name="add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>向 ASP.NET Web 应用添加 Microsoft 登录功能

本指南演示如何使用基于传统 Web 浏览器的应用程序和 OpenID Connect，通过 ASP.NET MVC 解决方案实现 Microsoft 登录。

在本指南完成时，你的应用程序将能够接受个人帐户（例如 outlook.com、live.com 等）的登录。 此外，来自任何与 Microsoft 标识平台集成的公司或组织的工作和学校帐户也能够登录到你的应用。

> 本指南需要 Microsoft Visual Studio 2019。  尚未安装？  [免费下载 Visual Studio 2019](https://www.visualstudio.com/downloads/)。

>[!NOTE]
> 如果你不熟悉 Microsoft 标识平台，我们建议你从[将 Microsoft 标识平台登录添加到 ASP.NET Web 应用](quickstart-v2-aspnet-webapp.md)开始。

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>本指南生成的示例应用的工作原理

![显示本教程生成的示例应用的工作原理](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

所创建的示例应用程序基于这样的场景：使用浏览器访问 ASP.NET 网站，该网站提示用户通过登录按钮进行身份验证。 在此方案中，呈现网页的大部分工作在服务器端完成。

## <a name="libraries"></a>库

本指南使用以下库：

|库|说明|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|让应用程序可使用 OpenIdConnect 进行身份验证的中间件|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|允许应用程序使用 Cookie 维持用户会话的中间件|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|允许基于 OWIN 的应用程序使用 ASP.NET 请求管道在 Internet Information Services (IIS) 上运行|

## <a name="set-up-your-project"></a>设置项目

本部分介绍如何使用 OpenID Connect 通过 OWIN 中间件在 ASP.NET 项目上安装和配置身份验证管道。

> 想要改为下载此示例的 Visual Studio 项目？ [下载项目](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)并跳到[注册应用程序](#register-your-application)，在执行前配置代码示例。

### <a name="create-your-aspnet-project"></a>创建 ASP.NET 项目

1. 在 Visual Studio 中：转到“文件” > “新建” > “项目”。   
2. 在 Visual C#\Web 下，选择“ASP.NET Web 应用程序(.NET Framework)”   。
3. 为应用程序命名，并单击“确定”  。
4. 选择“空”并选中添加“MVC”引用的复选框   。

## <a name="add-authentication-components"></a>添加身份验证组件

1. 在 Visual Studio 中：转到“工具”   > “Nuget 包管理器”   > “包管理器控制台”  。
2. 在包管理器控制台窗口中键入以下命令，添加 *OWIN 中间件 NuGet 包*：

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>关于这些库
> 这些库通过基于 Cookie 的身份验证使用 OpenID Connect 启用单一登录 (SSO)。 完成身份验证后，代表用户的令牌会发送到应用程序，OWIN 中间件会创建会话 Cookie。 浏览器随后对后续请求使用此 Cookie，这样一来，用户就无需重新键入密码，也不需要任何其他验证。
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>配置身份验证管道

以下步骤用于创建 OWIN 中间件 Startup 类，以配置 OpenID Connect 身份验证。 IIS 进程启动时，会自动执行此类。

> [!TIP]
> 如果项目的根文件夹中没有 `Startup.cs` 文件，请执行以下操作：
> 1. 右键单击项目的根文件夹，然后选择“添加” > “新建项” > “OWIN 启动类”。   <br/>
> 2. 将其命名为 **Startup.cs**。
>
>> 确保选择的类是 OWIN Startup 类，而不是标准 C# 类。 验证是否可以看到命名空间上的 [assembly:OwinStartup(typeof({NameSpace}.Startup))]。

1. 向 Startup.cs 添加 *OWIN* 和 *Microsoft.IdentityModel* 引用：

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
        // The Client ID is used by the application to uniquely identify itself to Microsoft identity platform.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Microsoft identity platform endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
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
                        ValidateIssuer = false // This is a simplification
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

> [!NOTE]
> 在本快速入门中，设置 `ValidateIssuer = false` 是一种简化操作。 在实际应用程序中，必须验证颁发者。
> 查看示例，了解如何执行该操作。

<!--start-collapse-->
> ### <a name="more-information"></a>详细信息
> 在 *OpenIDConnectAuthenticationOptions* 中提供的参数将充当应用程序与 Microsoft 标识平台通信时使用的坐标。 OpenID Connect 中间件会在后台使用 Cookie，因此，还必须设置 Cookie 身份验证，如以上代码所示。 *ValidateIssuer* 值告知 OpenIdConnect 不要限制某个特定组织的访问权限。
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>添加控制器来处理登录和注销请求

若要创建新控制器来公开登录和注销方法，请执行以下步骤：

1.  右键单击“控制器”文件夹，并选择“添加” > “控制器”。   
2.  选择“MVC (.NET 版本)控制器 - 空”  。
3.  选择 **添加** 。
4.  将其命名为 **HomeController**，然后选择“添加”。 
5.  向该类添加 OWIN 引用：

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. 通过代码启动身份验证质询，添加以下两个方法来处理控制器登录和注销：

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

## <a name="create-the-apps-home-page-for-user-sign-in"></a>创建应用的用户登录主页

在 Visual Studio 中，创建新视图来添加登录按钮并在身份验证后显示用户信息：

1.  右键单击“视图/主页”文件夹，然后选择“添加视图”   。
2.  将新视图命名为 **Index**。
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
> ### <a name="more-information"></a>详细信息
> 此页以 SVG 形式添加登录按钮，背景为黑色：<br/>![Microsoft 登录](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> 对于多个登录按钮，请转到[品牌准则](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "品牌准则")。
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>添加控制器来显示用户声明
此控制器演示如何使用 `[Authorize]` 属性来保护控制器。 此属性只允许通过身份验证的用户，从而限制对控制器的访问。 以下代码使用该属性来显示作为登录的一部分被检索的用户声明：

1.  右键单击“控制器”文件夹，并选择“添加” > “控制器”。   
2.  选择“MVC {version} 控制器 - 空”  。
3.  选择 **添加** 。
4.  将其命名为“ClaimsController”  。
5.  将控制器类的代码替换为以下代码。 这会将 `[Authorize]` 属性添加到该类：

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
> ### <a name="more-information"></a>详细信息
> 因为使用 `[Authorize]` 属性，仅当用户通过身份验证后，才执行此控制器的所有方法。 如果用户未通过身份验证，并尝试访问控制器，OWIN 将启动身份验证质询，并强制用户进行身份验证。 以上代码查看用户的 ID 令牌中包含的特定用户属性的声明列表。 这些属性包括用户的完整姓名和用户名，以及全局用户标识符使用者。 它还包含租户 ID，表示用户的组织的 ID  。 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>创建视图来显示用户的声明

在 Visual Studio 中创建新视图，以在网页上显示用户的声明：

1.  右键单击“视图/声明”文件夹，然后选择“添加视图”   。
2.  将新视图命名为 **Index**。
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

## <a name="register-your-application"></a>注册应用程序

若要注册应用程序并将应用程序注册信息添加到解决方案，有两个选项：

### <a name="option-1-express-mode"></a>选项 1：快速模式

若要快速注册应用程序，请执行以下步骤：

1. 转到新的 [Azure 门户 - 应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)窗格。
1. 输入应用程序的名称并选择“注册”  。
1. 遵照说明下载内容，并单击一下自动配置新应用程序。

### <a name="option-2-advanced-mode"></a>选项 2：高级模式

若要手动注册应用程序并将应用的注册信息添加到解决方案，请执行以下步骤：

1. 打开 Visual Studio，然后：
   1. 在解决方案资源管理器中，选择项目并查看“属性”窗口（如果看不到“属性”窗口，请按 F4）。
   1. 将“已启用 SSL”更改为 `True`。
   1. 在 Visual Studio 中右键单击该项目，然后选择“属性”和“Web”选项卡   。在“服务器”部分，将“项目 URL”设置更改为“SSL URL”    。
   1. 复制 SSL URL。 需在下一步将此 URL 添加到注册门户的重定向 URL 列表。<br/><br/>![项目属性](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 如果你的帐户有权访问多个租户，请在右上角选择该帐户，并将门户会话设置为所需的 Azure AD 租户。
1. 转到面向开发人员的 Microsoft 标识平台的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页。
1. 选择“新注册”。 
1. “注册应用程序”页出现后，请输入应用程序的注册信息： 
   1. 在“名称”  部分输入一个会显示给应用用户的有意义的应用程序名称，例如 **ASPNET-Tutorial**。
   1. 将在步骤 1 中从 Visual Studio 复制的 SSL URL（例如 `https://localhost:44368/`）添加到“回复 URL”中，然后选择“注册”。  
1. 选择“身份验证”菜单，在“隐式授权”下选择“ID 令牌”，然后选择“保存”。    
1. 在根文件夹中的 web.config 文件内的 `configuration\appSettings` 节下添加以下内容：

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. 将 `ClientId` 替换为刚注册的应用程序 ID。
1. 将 `redirectUri` 替换为项目的 SSL URL。

## <a name="test-your-code"></a>测试代码

若要在 Visual Studio 中测试应用程序，请按 F5 运行项目。 浏览器随即打开到 http://<span></span>localhost:{port} 位置，然后显示“使用 Microsoft 登录”按钮  。 选择该按钮，启动登录过程。

准备好运行测试后，使用 Azure AD 帐户（工作或学校帐户）或个人 Microsoft 帐户（<span>live.</span>com 或 <span>outlook.</span>com）登录。

![Microsoft 登录](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![登录 Microsoft 帐户](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

<!--start-collapse-->
> ###  <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Microsoft 标识平台终结点中的权限和许可
>  与 Microsoft 标识平台集成的应用程序遵循的授权模型可让用户和管理员控制数据的访问方式。 在用户对 Microsoft 标识平台进行身份验证以访问此应用程序后，系统会提示他们许可应用程序请求的权限（“查看你的基本个人资料”和“保留你已授权访问的数据的访问权限”）。 接受这些权限后，用户将转到应用程序结果。 但是，如果出现以下情况之一，则系统可能会向用户提示“需要管理员许可”页： 
>  > - 应用程序开发人员添加了任何需要“管理员许可”的附加权限。 
>  > - 或者，在配置的租户（“企业应用程序”->“用户设置”）中，用户无法许可代表他们访问公司数据的应用。 
>
> 有关详细信息，请参阅 [Microsoft 标识平台终结点中的权限和许可](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)。
<!--end-collapse-->

#### <a name="view-application-results"></a>查看应用程序结果

登录后，用户将重定向到网站主页。 该主页是 Microsoft 应用程序注册门户上应用程序注册信息中指定的 HTTPS URL。 主页包括欢迎消息“你好，\<用户>”、一个注销链接和一个用于查看用户声明的链接。  用户声明链接连接到你之前创建的“声明”控制器。

### <a name="view-the-users-claims"></a>查看用户的声明

若要查看用户声明，请选择该链接以浏览到控制器视图（仅经过身份验证的用户可访问该视图）。

#### <a name="view-the-claims-results"></a>查看声明结果

浏览到控制器视图后，应当会显示包含用户基本属性的表格：

|properties |值 |说明 |
|---|---|---|
|**名称** |用户全名 | 用户的名字和姓氏
|**用户名** |user<span>@domain.com</span> | 用于标识用户的用户名|
|**主题** |主题 |唯一标识 Web 上用户的字符串|
|**租户 ID** |Guid | 唯一表示用户的 Azure AD 组织的 **guid**|

此外，还应当显示包含身份验证请求中所有声明的表格。 有关详细信息，请参阅 [ID 令牌中的声明列表](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)。

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>测试对具有 Authorize 属性的方法的访问（可选）

若要作为由 `Authorize` 属性保护的控制器的匿名用户来测试访问，请执行以下步骤：

1. 选择注销用户的链接并完成注销过程。
2. 在浏览器中，键入 http://<span></span>localhost:{port}/claims 来访问由 `Authorize` 属性保护的控制器。

#### <a name="expected-results-after-access-to-a-protected-controller"></a>访问受保护控制器后的预期结果

系统会提示进行身份验证来使用受保护的控制器视图。

## <a name="advanced-options"></a>高级选项

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>保护整个网站
若要保护整个网站，请在 **Global.asax** 文件中向 `Application_Start` 方法中的 `GlobalFilters` 筛选器添加 `AuthorizeAttribute` 属性：

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>限制谁可以登录到应用程序

默认情况下，当生成本指南创建的应用程序时，应用程序将接受使用个人帐户（包括 outlook.com、live.com 和其他帐户）以及与 Microsoft 标识平台集成的任何公司或组织的工作和学校帐户进行登录。 这是针对 SaaS 应用程序推荐使用的选项。

为了限制应用程序的用户登录访问权限，提供了以下多个选项。

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>选项 1：限制某个组织的 Active Directory 实例的用户登录应用程序（单租户）

此选项经常用于 *LOB 应用程序*：如果希望应用程序仅接受属于特定 Azure AD 实例的帐户（包括该示例的来宾帐户）进行登录，请按照下列步骤操作  ：

1. 在 web.config 文件中，将 `Tenant` 参数的值从 `Common` 更改为组织的租户名称，例如 `contoso.onmicrosoft.com`。
2. 在 [OWIN Startup 类](#configure-the-authentication-pipeline)中，将 `ValidateIssuer` 参数设置为 `true`。

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>选项 2：仅允许特定组织列表中的用户访问

可以将登录访问权限仅限于允许组织列表中的 Azure AD 组织中的这些用户帐户：
1. 在 [OWIN Startup 类](#configure-the-authentication-pipeline)中，将 `ValidateIssuer` 参数设置为 `true`。
2. 将 `ValidIssuers` 参数的值设置为允许的组织的列表。

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>选项 3：使用自定义方法来验证颁发者

可通过 IssuerValidator 参数实现自定义方法来验证颁发者  。 有关如何使用此参数的详细信息，请参阅 [TokenValidationParameters 类](/previous-versions/visualstudio/dn464192(v=vs.114))。

## <a name="next-steps"></a>后续步骤

了解 Web 应用如何调用 Web API。

### <a name="learn-how-to-create-the-application-used-in-this-quickstart-guide"></a>了解如何创建本快速入门指南中使用的应用程序

详细了解使用 Microsoft 标识平台调用 Web API 的 Web 应用：

> [!div class="nextstepaction"]
> [调用 Web API 的 Web 应用](scenario-web-app-sign-user-overview.md)

了解如何生成调用 Microsoft Graph 的 Web 应用：

> [!div class="nextstepaction"]
> [Microsoft Graph ASP.NET 教程](https://docs.microsoft.com/graph/tutorials/aspnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

帮助我们改进 Microsoft 标识平台。 填写包括两个问题的调查，将你的想法告诉我们：

> [!div class="nextstepaction"]
> [Microsoft 标识平台调查](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
