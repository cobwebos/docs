---
title: Azure Active Directory B2C 中的身份验证、注册、密码重置 | Microsoft Docs
description: 如何生成使用 Azure Active Directory B2C 进行注册/登录、配置文件编辑和密码重置的 Web 应用程序。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/17/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ec106b46097f9a35b9e41e08de4c18339f1b28f0
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34710400"
---
# <a name="create-an-aspnet-web-app-with-azure-active-directory-b2c-sign-up-sign-in-profile-edit-and-password-reset"></a>使用 Azure Active Directory B2C 注册、登录、配置文件编辑和密码重置创建 ASP.NET Web 应用

本教程演示如何：

> [!div class="checklist"]
> * 向 Web 应用添加 Azure AD B2C 标识功能
> * 在 Azure AD B2C 目录中注册 Web 应用
> * 创建 Web 应用的用户注册/登录、配置文件编辑和密码重置策略

## <a name="prerequisites"></a>先决条件

- 必须将 B2C 租户连接到 Azure 帐户。 可以[在此](https://azure.microsoft.com/)创建一个免费的 Azure 帐户。
- 需要 [Microsoft Visual Studio](https://www.visualstudio.com/)或类似程序，才能查看和修改示例代码。

## <a name="create-an-azure-ad-b2c-directory"></a>创建 Azure AD B2C 目录

只有在创建目录或租户之后，才可使用 Azure AD B2C。 目录是所有用户、应用、组等对象的容器。 如果还没有目录，请先创建 B2C 目录，然后继续执行本指南中的步骤。

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

> [!NOTE]
> 
> 需要将 B2C 租户连接到 Azure 订阅。 选择“创建”，再选择“将现有的 Azure AD B2C 租户链接到我的 Azure 订阅”选项，然后在“Azure AD B2C 租户”下拉列表中，选择要关联的租户。

## <a name="create-and-register-an-application"></a>创建并注册应用程序

接下来，需要在 B2C 目录中创建并注册应用。 这将提供 Azure AD B2C 所需信息，使之能够与应用安全通信。 

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

完成后，将在应用程序设置中具有一个 API 和一个本机应用程序。

## <a name="create-policies-on-your-b2c-tenant"></a>在 B2C 租户上创建策略

在 Azure AD B2C 中，每个用户体验由 [策略](active-directory-b2c-reference-policies.md)定义。 本代码示例包含三个标识体验：注册与登录、配置文件编辑和密码重置。  需要按 [策略参考文章](active-directory-b2c-reference-policies.md)中所述，为每个类型创建一个策略。 对于每个策略，确保选择“显示名称属性或声明”，并复制策略的名称以供将来使用。

### <a name="add-your-identity-providers"></a>添加标识提供者

在设置中，选择“标识提供者”，然后选择用户名注册或电子邮件注册。

### <a name="create-a-sign-up-and-sign-in-policy"></a>创建注册或登录策略

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

### <a name="create-a-profile-editing-policy"></a>创建配置文件编辑策略

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

### <a name="create-a-password-reset-policy"></a>创建密码重置策略

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

创建策略后，可以开始构建应用。

## <a name="download-the-sample-code"></a>下载示例代码

本教程的代码在 [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) 上维护。 可运行以下命令来克隆示例：

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

下载示例代码后，打开 Visual Studio .sln 文件开始处理。 解决方案文件包含两个项目：`TaskWebApp` 和 `TaskService`。 `TaskWebApp` 是用户交互的 MVC Web 应用程序。 `TaskService` 是应用的后端 Web API，存储每个用户的待办事项列表。 本文仅讨论 `TaskWebApp` 应用程序。 若要了解如何使用 Azure AD B2C 构建 `TaskService`，请参阅 [.NET Web 应用教程](active-directory-b2c-devquickstarts-api-dotnet.md)。

## <a name="update-code-to-use-your-tenant-and-policies"></a>更新代码以使用租户和策略

我们的示例配置为使用演示租户的策略和客户端 ID。 若要将其连接到自己的租户，需要打开 `TaskWebApp` 项目中的 `web.config`，然后替换以下值：

* 将 `ida:Tenant` 的值替换为租户名称
* 将 `ida:ClientId` 的值替换为 Web 应用的应用程序 ID
* 将 `ida:ClientSecret` 的值替换为 Web 应用机密密钥
* 将 `ida:SignUpSignInPolicyId` 的值替换为“注册或登录”策略名称
* 将 `ida:EditProfilePolicyId` 的值替换为“编辑配置文件”策略名称
* 将 `ida:ResetPasswordPolicyId` 的值替换为“重置密码”策略名称

## <a name="launch-the-app"></a>启动应用
从 Visual Studio 内部启动应用。 导航到“待办事项列表”选项卡，注意 URl 为：https://login.microsoftonline.com/*YourTenantName*/oauth2/v2.0/authorize?p=*YourSignUpPolicyName*&client_id=*YourclientID*.....

使用电子邮件地址或用户名注册应用。 注销，然后重新登录并编辑配置文件或重置密码。 注销并以其他用户身份登录。 

## <a name="add-social-idps"></a>添加社交 IDP

目前，应用仅支持用户使用“本地帐户”注册和登录；帐户使用用户名和密码存储在 B2C 目录中。 通过使用 Azure AD B2C，无需更改任何代码即可添加对其他**身份提供者** (IDP) 的支持。

要向应用添加社交 IDP，请按照这些文章中的详细说明开始操作。 对于要支持的每个 IDP，需要在该系统中注册一个应用程序并获取一个客户端 ID。

* [将 Facebook 设置为 IDP](active-directory-b2c-setup-fb-app.md)
* [将 Google 设置为 IDP](active-directory-b2c-setup-goog-app.md)
* [将 Amazon 设置为 IDP](active-directory-b2c-setup-amzn-app.md)
* [将 LinkedIn 设置为 IDP](active-directory-b2c-setup-li-app.md)

将标识提供者添加到 B2C 目录后，编辑三个策略中的每一个以包含新的 IDP，如[策略参考文章](active-directory-b2c-reference-policies.md)中所述。 保存策略后，再次运行应用。  应该会在每个标识体验中看到新增的 IDP 作为登录和注册选项。

可以试验策略并观察其对示例应用的影响。 添加或删除 IDP、操纵应用程序声明或更改注册属性。 试验持续进行，直到能够了解策略、身份验证请求和 OWIN 如何联系在一起时为止。

## <a name="sample-code-walkthrough"></a>示例代码演练
以下部分说明如何配置示例应用程序代码。 可以将它用作今后应用开发的指南。

### <a name="add-authentication-support"></a>添加身份验证支持

现在可以配置应用程序以使用 Azure AD B2C。 应用程序与 Azure AD B2C 通信，发送 OpenID Connect 身份验证请求。 请求规定应用程序想要通过指定的策略执行的用户体验。 可以使用 Microsoft 的 OWIN 库来发送这些请求、执行策略、管理用户会话等。

#### <a name="install-owin"></a>安装 OWIN

首先，使用 Visual Studio 包管理器控制台将 OWIN 中间件 NuGet 包添加到项目要开始，请使用 Package Manager Console 将 OWIN 中间件 NuGet 包添加到项目。

```Console
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

#### <a name="add-an-owin-startup-class"></a>添加 OWIN 启动类

将 OWIN 启动类添加到名为 `Startup.cs` 的 API。  右键单击项目，选择“添加”和“新建项”，并搜索“OWIN”。 当应用程序启动时，该 OWIN 中间件将调用 `Configuration(…)` 方法。

在本示例中，我们已将类声明更改为 `public partial class Startup`，并实现了 `App_Start\Startup.Auth.cs` 中的类的另一部分。 在 `Configuration` 方法中，我们添加了对 `Startup.Auth.cs` 中定义的 `ConfigureAuth` 的调用。 修改后，`Startup.cs` 如下所示：

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

#### <a name="configure-the-authentication-middleware"></a>配置身份验证中间件

打开文件 `App_Start\Startup.Auth.cs` 并实现 `ConfigureAuth(...)` 方法。 在 `OpenIdConnectAuthenticationOptions` 中提供的参数充当应用程序与 Azure AD B2C 通信时使用的坐标。 如果未指定某些参数，将使用默认值。 例如，我们不指定 `ResponseType` 在示例中，因此默认值 `code id_token` 将使用在 Azure AD B2C 到每个传出请求。

还需要设置 Cookie 身份验证。 OpenID Connect 中间件使用 Cookie 来维护用户会话等等。

```CSharp
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // Initialize variables ...

    // Configure the OWIN middleware
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseCookieAuthentication(new CookieAuthenticationOptions());
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseOpenIdConnectAuthentication(
            new OpenIdConnectAuthenticationOptions
            {
                // Generate the metadata address using the tenant and policy information
                MetadataAddress = String.Format(AadInstance, Tenant, DefaultPolicy),

                // These are standard OpenID Connect parameters, with values pulled from web.config
                ClientId = ClientId,
                RedirectUri = RedirectUri,
                PostLogoutRedirectUri = RedirectUri,

                // Specify the callbacks for each type of notifications
                Notifications = new OpenIdConnectAuthenticationNotifications
                {
                    RedirectToIdentityProvider = OnRedirectToIdentityProvider,
                    AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    AuthenticationFailed = OnAuthenticationFailed,
                },

                // Specify the claims to validate
                TokenValidationParameters = new TokenValidationParameters
                {
                    NameClaimType = "name"
                },

                // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
                Scope = $"openid profile offline_access {ReadTasksScope} {WriteTasksScope}"
            }
        );
    }

    // Implement the "Notification" methods...
}
```

在 `OpenIdConnectAuthenticationOptions` 更高版本，我们定义一组特定的通知所接收的 OpenID Connect 中间件的回调函数。 这些行为定义使用 `OpenIdConnectAuthenticationNotifications` 对象，并存储到 `Notifications` 变量。 在示例中，我们定义根据事件的三个不同的回调。

### <a name="using-different-policies"></a>使用不同的策略

`RedirectToIdentityProvider` 每当请求时，Azure AD B2C 就会触发通知。 回调函数中 `OnRedirectToIdentityProvider`，可以将其签入传出呼叫如果我们想要使用不同的策略。 为了进行密码重置或编辑配置文件，需要使用相应的策略，如密码重置而不是默认的“注册或登录”策略的策略。

在我们示例中，当用户想要重置密码或编辑配置文件中，我们将添加我们希望到 OWIN 上下文使用的策略。 可通过执行以下操作实现：

```CSharp
    // Let the middleware know you are trying to use the edit profile policy
    HttpContext.GetOwinContext().Set("Policy", EditProfilePolicyId);
```

并且可以实现回调函数 `OnRedirectToIdentityProvider` 通过执行以下操作：

```CSharp
/*
*  On each call to Azure AD B2C, check if a policy (e.g. the profile edit or password reset policy) has been specified in the OWIN context.
*  If so, use that policy when making the call. Also, don't request a code (since it won't be needed).
*/
private Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    var policy = notification.OwinContext.Get<string>("Policy");

    if (!string.IsNullOrEmpty(policy) && !policy.Equals(DefaultPolicy))
    {
        notification.ProtocolMessage.Scope = OpenIdConnectScopes.OpenId;
        notification.ProtocolMessage.ResponseType = OpenIdConnectResponseTypes.IdToken;
        notification.ProtocolMessage.IssuerAddress = notification.ProtocolMessage.IssuerAddress.Replace(DefaultPolicy, policy);
    }

    return Task.FromResult(0);
}
```

### <a name="handling-authorization-codes"></a>处理授权代码

`AuthorizationCodeReceived` 通知触发时收到授权代码。 OpenID Connect 中间件不支持访问令牌交换代码。 可以手动交换中的回调函数的令牌的代码。 有关详细信息，请查看在[文档](active-directory-b2c-devquickstarts-web-api-dotnet.md)说明如何。

### <a name="handling-errors"></a>处理错误

`AuthenticationFailed` 通知触发身份验证失败时。 在回调方法中，可以根据需要处理错误。 不过，应添加错误代码 `AADB2C90118` 的检查。 在执行“注册或登录”策略期间，用户有机会选择“忘记密码?”链接。 在此情况下，Azure AD B2C 将错误代码发送到应用，该错误代码指示应用应使用密码重置策略进行请求。

```CSharp
/*
* Catch any failures received by the authentication middleware and handle appropriately
*/
private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    // Handle the error code that Azure AD B2C throws when trying to reset a password from the login page
    // because password reset is not supported by a "sign-up or sign-in policy"
    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```

### <a name="send-authentication-requests-to-azure-ad"></a>向 Azure AD 发送身份验证请求

现在，应用已正确配置为使用 OpenID Connect 身份验证协议来与 Azure AD B2C 进行通信。 OWIN 管理有关创建身份验证消息、验证 Azure AD B2C 提供的令牌以及保留用户会话的详细信息。 剩下的就是启动每个用户的流。

当用户在 Web 应用中选择“注册/登录”、“编辑配置文件”或“重置密码”时，会在 `Controllers\AccountController.cs` 中调用相关操作。

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign up or sign in
*/
public void SignUpSignIn()
{
    // Use the default policy to process the sign up / sign in flow
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge();
        return;
    }

    Response.Redirect("/");
}

/*
*  Called when requesting to edit a profile
*/
public void EditProfile()
{
    if (Request.IsAuthenticated)
    {
        // Let the middleware know you are trying to use the edit profile policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
        HttpContext.GetOwinContext().Set("Policy", Startup.EditProfilePolicyId);

        // Set the page to redirect to after editing the profile
        var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
        HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

        return;
    }

    Response.Redirect("/");

}

/*
*  Called when requesting to reset a password
*/
public void ResetPassword()
{
    // Let the middleware know you are trying to use the reset password policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
    HttpContext.GetOwinContext().Set("Policy", Startup.ResetPasswordPolicyId);

    // Set the page to redirect to after changing passwords
    var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
    HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

    return;
}
```

也可以使用 OWIN 从应用注销用户。 在 `Controllers\AccountController.cs` 中：

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign out
*/
public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

除了显式调用策略之外，还可以在控制器中使用 `[Authorize]` 标记，如果用户未登录，该标记将执行策略。 打开 `Controllers\HomeController.cs`，并将 `[Authorize]` 标记添加到声明控制器。  命中 `[Authorize]` 标记时，OWIN 选择配置的最后一个策略。

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

### <a name="display-user-information"></a>显示用户信息

当使用 OpenID Connect 验证用户时，Azure AD B2C 会将 ID 令牌返回到包含**声明**的应用。 这些是有关用户的断言。 可以使用声明来个性化应用。

打开 `Controllers\HomeController.cs` 文件。 可以通过 `ClaimsPrincipal.Current` 安全主体对象访问控制器中的用户声明。

```CSharp
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

可以用同样的方式访问应用程序所收到的任何声明。  可在“声明”页面上查看应用收到的所有声明列表。
