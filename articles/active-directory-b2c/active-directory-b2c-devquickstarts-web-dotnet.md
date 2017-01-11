---
title: Azure Active Directory B2C | Microsoft Docs
description: "如何生成使用 Azure Active Directory B2C 进行登录、注册和配置文件管理的 Web 应用程序。"
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 0b1c6ede-de0a-4c32-92b3-5c813dc26804
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: a977cb509fb64d7c986e2e0f7e2b5e4e3e45dec0
ms.openlocfilehash: a0e03bda81d26dbc28706b45ee974359337573f0


---
# <a name="azure-ad-b2c-build-a-net-web-app"></a>Azure AD B2C：生成 .NET Web 应用
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

通过使用 Azure Active Directory (Azure AD) B2C，只需几个简短的步骤即可将强大的自助服务标识管理功能添加到 Web 应用。 本文将讨论如何创建包含用户注册、登录和配置文件管理的 .NET 模型-视图-控制器 (MVC) Web 应用。 该应用将支持使用用户名或电子邮件以及使用社交帐户（ Facebook 和 Google）进行注册和登录。

## <a name="get-an-azure-ad-b2c-directory"></a>获取 Azure AD B2C 目录
只有在创建目录或租户之后，才可使用 Azure AD B2C。 目录是所有用户、应用、组等对象的容器。  如果没有容器，请先 [创建 B2C 目录](active-directory-b2c-get-started.md) ，然后继续执行本指南中的步骤。

## <a name="create-an-application"></a>创建应用程序
接下来，需要在 B2C 目录中创建应用。 此应用为 Azure AD 提供所需的 Azure AD 信息，使之能够与应用安全通信。 若要创建应用，请遵循 [这些说明](active-directory-b2c-app-registration.md)。  请务必：

* 在应用程序中包含 **Web 应用/Web API**。
* 输入 `https://localhost:44316/` 作为**重定向URI**。 它是此代码示例的默认 URL。
* 复制分配给应用的**应用程序 ID**。  稍后需要用到此值。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>创建策略
在 Azure AD B2C 中，每个用户体验由 [策略](active-directory-b2c-reference-policies.md)定义。 本代码示例包含三个标识体验：注册、登录和编辑配置文件。 需要按 [策略参考文章](active-directory-b2c-reference-policies.md#create-a-sign-up-policy)中所述，为每个类型创建一个策略。

> [!NOTE]
> Azure AD B2C 还支持组合的注册或登录策略，但并未在本教程中对此进行说明。  注册或登录策略会在[此同等教程](active-directory-b2c-devquickstarts-web-dotnet-susi.md)中进行说明。
>
>

创建三个策略时，请务必：

* 在标识提供者边栏选项卡中，选择“用户 ID 注册”或“电子邮件注册”。
* 在注册策略中，选择“显示名称”和其他注册属性。
* 在每个策略中，选择“显示名称”声明作为应用程序声明。 也可以选择其他声明。
* 创建每个策略后，请复制策略的 **名称** 。 稍后将需要这些策略名称。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

创建三个策略后，可以开始构建应用。  

## <a name="download-the-code-and-configure-authentication"></a>下载代码和配置身份验证
本示例的代码[保留在 GitHub 上](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet)。 若要根据说明构建示例，可以[下载 .zip 文件格式的骨干项目](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip)。 也可以克隆骨干项目：

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

完成的示例也[作为 .zip 文件提供](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip)，或者放在同一存储库的 `complete` 分支中。

下载示例代码后，打开 Visual Studio .sln 文件开始处理。

应用通过发送身份验证消息与 Azure AD B2C 进行通信，该消息指定要作为 HTTP 请求的一部分进行执行的策略。 对于 .NET Web 应用程序，可以使用 Microsoft 的 OWIN 中间件来发送 OpenID Connect 身份验证请求、执行策略、管理用户会话等。

首先，使用 Visual Studio 包管理器控制台将 OWIN 中间件 NuGet 包添加到项目若要开始，请使用 Package Manager Console 将 OWIN 中间件 NuGet 包添加到项目。

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

接下来，打开位于项目根目录中的 `web.config` 文件，并在 `<appSettings>` 节中输入应用的配置值，替换现有值。

```
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
...
```

[!INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

接下来，将 OWIN 启动类添加到名为 `Startup.cs` 的项目。 右键单击项目，选择“添加”和“添加项”，然后搜索“OWIN”。 **请确保将类声明更改为 `public partial class Startup`**。 我们在另一个文件中实现了此类的一部分。 当你的应用程序启动时，该 OWIN 中间件将调用 `Configuration(...)` 方法。 在此方法中，调用 `ConfigureAuth(...)`为应用设置身份验证。

```C#
// Startup.cs

public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

打开文件 `App_Start\Startup.Auth.cs` 并实现 `ConfigureAuth(...)` 方法。  在 `OpenIdConnectAuthenticationOptions` 中提供的参数充当应用程序与 Azure AD 通信时使用的坐标。 还需要设置 Cookie 身份验证。 OpenID Connect 中间件使用 Cookie 来维护用户会话等等。

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

    // B2C policy identifiers
    public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        // Configure OpenID Connect middleware for each policy
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignUpPolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(ProfilePolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignInPolicyId));
    }

    // Used for avoiding yellow-screen-of-death
    private Task AuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
    {
        notification.HandleResponse();
        if (notification.Exception.Message == "access_denied")
        {
            notification.Response.Redirect("/");
        }
        else
        {
            notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
        }

        return Task.FromResult(0);
    }

    private OpenIdConnectAuthenticationOptions CreateOptionsFromPolicy(string policy)
    {
        return new OpenIdConnectAuthenticationOptions
        {
            // For each policy, give OWIN the policy-specific metadata address, and
            // set the authentication type to the id of the policy
            MetadataAddress = String.Format(aadInstance, tenant, policy),
            AuthenticationType = policy,

            // These are standard OpenID Connect parameters, with values pulled from web.config
            ClientId = clientId,
            RedirectUri = redirectUri,
            PostLogoutRedirectUri = redirectUri,
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = AuthenticationFailed,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {
                NameClaimType = "name",
            },
        };
    }
}
```

## <a name="send-authentication-requests-to-azure-ad"></a>向 Azure AD 发送身份验证请求
现在，应用已正确配置为使用 OpenID Connect 身份验证协议来与 Azure AD B2C 进行通信。  OWIN 已处理有关创建身份验证消息、验证 Azure AD 提供的令牌以及保留用户会话的细节。  剩下的就是启动每个用户的流。

当用户在 Web 应用中选择“注册”、“登录”或“编辑配置文件”时，将在 `Controllers\AccountController.cs` 中调用相关操作。 在每种情况下，都可以使用内置 OWIN 方法来触发正确的策略：

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by specifying the policy id as the AuthenticationType
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties () { RedirectUri = "/" }, Startup.SignInPolicyId);
    }
}

public void SignUp()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.SignUpPolicyId);
    }
}


public void Profile()
{
    if (Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.ProfilePolicyId);
    }
}
```

如果用户未登录，则还可以使用控制器中需要执行特定策略的 `[Authorize]` 标记。 打开 `Controllers\HomeController.cs`，然后将 `[Authorize]` 标记添加到声明控制器。  调用 Authorize 标记时，OWIN 将选择配置为最后一个执行的策略。

```C#
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a policy if the user is not already signed in the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

也可以使用 OWIN 从应用注销用户。 在 `Controllers\AccountController.cs` 中：  

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

## <a name="display-user-information"></a>显示用户信息
当使用 OpenID Connect 验证用户时，Azure AD 会将 ID 令牌返回到包含**声明**的应用。 这些是有关用户的断言。 可以使用声明来个性化应用。  

打开 `Controllers\HomeController.cs` 文件。 可以通过 `ClaimsPrincipal.Current` 安全主体对象访问控制器中的用户声明。

```C#
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

## <a name="run-the-sample-app"></a>运行示例应用
最后，可以生成并运行应用。 使用电子邮件地址或用户名注册应用。 注销，然后以同一用户身份重新登录。 编辑该用户的配置文件。 注销并以其他用户身份注册。 请注意，“声明”选项卡上显示的信息对应于在策略上配置的信息。

## <a name="add-social-idps"></a>添加社交 IDP
目前，该应用仅支持用户使用**本地帐户**进行注册和登录。 这些帐户是存储在 B2C 目录中使用用户名和密码的帐户。 通过使用 Azure AD B2C，无需更改任何代码即可添加对其他**身份提供者** (IDP) 的支持。

要向应用添加社交 IDP，请按照这些文章中的详细说明开始操作。 对于要支持的每个 IDP，需要在该系统中注册一个应用程序并获取一个客户端 ID。

* [将 Facebook 设置为 IDP](active-directory-b2c-setup-fb-app.md)
* [将 Google 设置为 IDP](active-directory-b2c-setup-goog-app.md)
* [将 Amazon 设置为 IDP](active-directory-b2c-setup-amzn-app.md)
* [将 LinkedIn 设置为 IDP](active-directory-b2c-setup-li-app.md)

将身份提供者添加到 B2C 目录后，需要编辑你的三个策略以包含新的 IDP，如[策略参考文章](active-directory-b2c-reference-policies.md)中所述。 保存策略后，再次运行应用。  应该会在每个标识体验中看到新增的 IDP 作为登录和注册选项。

可以试验策略并观察其对示例应用的影响。 添加或删除 IDP、操纵应用程序声明或更改注册属性。 试验持续进行，直到能够了解策略、身份验证请求和 OWIN 如何联系在一起时为止。

[以 .zip 文件提供](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip)完整示例（不包括配置值）以供参考。 也可以从 GitHub 克隆它：

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->



<!--HONumber=Dec16_HO5-->


