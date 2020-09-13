---
title: 获取用于调用 Web API 的令牌（桌面应用）| Azure
titleSuffix: Microsoft identity platform
description: 了解如何生成调用 Web API 的桌面应用来获取应用的令牌
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: ab6842fe6787b9e1a61b3c25fabb6c64c2597b9a
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032803"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>用于调用 Web API 的桌面应用：获取令牌

生成公共客户端应用程序的实例后，你将使用它来获取一个令牌，然后使用该令牌调用 Web API。

## <a name="recommended-pattern"></a>推荐的模式

Web API 由其 `scopes` 定义。 无论在应用程序中提供哪种体验，要使用的模式都是：

- 通过调用 `AcquireTokenSilent` 系统性地尝试从令牌缓存中获取令牌。
- 如果此调用失败，则使用所需的 `AcquireToken` 流（此处由 `AcquireTokenXX` 表示）。

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>在 MSAL.NET 中

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>在适用于 iOS 和 macOS 的 MSAL 中

Objective-C：

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Swift：

```swift
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

        if (nsError.domain == MSALErrorDomain &&
            nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
        }
        return
    }
}
```
---

下面详细说明了在桌面应用程序中获取令牌的各种方法。

## <a name="acquire-a-token-interactively"></a>以交互方式获取令牌

下面的示例展示了以交互方式获取令牌，以便使用 Microsoft Graph 读取用户配置文件的最少代码。

# <a name="net"></a>[.NET](#tab/dotnet)
### <a name="in-msalnet"></a>在 MSAL.NET 中

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="mandatory-parameters"></a>必需参数

`AcquireTokenInteractive` 只有一个必需的参数 ``scopes``，其中包含一个定义需要令牌的范围的字符串枚举。 如果令牌用于 Microsoft Graph，则可以在“权限”一节中每个 Microsoft Graph API 的 API 参考中找到所需范围。 例如，若要[列出用户的联系人](/graph/api/user-list-contacts)，必须使用范围“User.Read”、“Contacts.Read”。 有关详细信息，请参阅 [Microsoft Graph 权限参考](/graph/permissions-reference)。

在 Android 上，还需要按如下所示使用 `.WithParentActivityOrWindow` 指定父活动，以便在交互后令牌返回到该父活动。 如果未指定父活动，则调用 `.ExecuteAsync()` 时会引发异常。

### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET 中的特定可选参数

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

UI 非常重要，因为它是交互式的。 `AcquireTokenInteractive` 提供一个特定的可选参数，该参数可为支持它的平台指定父 UI。 在桌面应用程序中使用时，`.WithParentActivityOrWindow` 根据具体的平台采用不同的类型。 或者，如果您不想控制登录对话框在屏幕上的显示位置，则可以省略可选的父窗口参数以创建窗口。 这适用于基于命令行的应用程序，该应用程序用于将调用传递到任何其他后端服务，无需任何 windows 用户交互。 

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

备注：

- 在 .NET Standard 中，预期的 `object` 是 `Activity`（在 Android 上）、`UIViewController`（在 iOS 上）、`NSWindow`（在 MAC 上）和 `IWin32Window` 或 `IntPr`（在 Windows 上）。
- 在 Windows 上，必须从 UI 线程调用 `AcquireTokenInteractive`，以便嵌入性浏览器获取正确的 UI 同步上下文。 不从 UI 线程调用可能会导致无法正常输送消息和 UI 出现死锁的情况。 在尚未进入 UI 线程的情况下，从 UI 线程调用 Microsoft 身份验证库 (MSAL) 的方法之一是使用 WPF 上的 `Dispatcher`。
- 使用 WPF 时，若要从 WPF 控件获取一个窗口，可以使用 `WindowInteropHelper.Handle` 类。 然后从 WPF 控件 (`this`) 发出调用：

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` 用于通过指定提示来控制与用户的交互。

![显示“提示”结构中的字段的图像。 这些常量值通过定义由 WithPrompt() 方法显示的提示的类型来控制与用户的交互。](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

类定义以下常量：

- ``SelectAccount`` 强制 STS 显示帐户选择对话框，其中包含用户已建立会话的帐户。 当应用程序开发人员想要让用户在不同的标识之间选择时，此选项非常有用。 此选项驱动 MSAL 将 ``prompt=select_account`` 发送到标识提供者。 此选项为默认值。 它能够很好地根据可用的信息（例如帐户和用户会话的存在性）提供尽量最佳的体验。 不要对其进行更改，除非你有充分的理由。
- 应用程序开发人员可以使用 ``Consent`` 强制要求向用户显示许可提示，即使以前已经授予了许可。 在这种情况下，MSAL 会将 `prompt=consent` 发送到标识提供者。 此选项可用于某些注重安全的应用程序，其中的组织监管机制要求每次使用该应用程序时，都要向用户显示许可对话框。
- 应用程序开发人员可以使用 ``ForceLogin`` 来让服务向用户显示凭据提示，即使可能不需要这种用户提示。 如果获取令牌失败，可以使用此选项让用户重新登录。 在这种情况下，MSAL 会将 `prompt=login` 发送到标识提供者。 有时，此选项会在某些注重安全的应用程序中使用，其中的组织监管机制要求用户每次在访问应用程序的特定部分时重新登录。
- ``Never``（仅适用于 .NET 4.5 和 WinRT）不会提示用户，而是尝试使用隐藏的嵌入式 Web 视图中存储的 Cookie。 有关详细信息，请参阅“MSAL.NET 中的 Web 视图”。 使用此选项可能会失败。 在这种情况下，`AcquireTokenInteractive` 会引发异常来告知需要 UI 交互。 需要使用另一个 `Prompt` 参数。
- ``NoPrompt`` 不会向标识提供者发送任何提示。 此选项仅适用于 Azure Active Directory (Azure AD) B2C 编辑配置文件策略。 有关详细信息，请参阅 [Azure AD B2C 细节](https://aka.ms/msal-net-b2c-specificities)。

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

此修饰符在高级方案中使用，在该方案中你希望用户提前许可多个资源，并且不想使用增量许可，这种许可通常与 MSAL.NET/Microsoft 标识平台配合使用。 有关详细信息，请参阅[让用户提前许可多个资源](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)。

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Web UI 是一种调用浏览器的机制。 此机制可以是专用的 UI WebBrowser 控件，也可以是委托打开浏览器的方法。
MSAL 为大多数平台提供 Web UI 实现，但有时，你可能需要自行托管浏览器：

- MSAL 未明确涵盖的平台，例如 Blazor、Unity 和桌面上的 Mono。
- 你希望对应用程序进行 UI 测试，并使用可以与 Selenium 配合使用的自动化浏览器。
- 浏览器和运行 MSAL 的应用位于不同的进程中。

##### <a name="at-a-glance"></a>速览

为了实现这一点，请为 MSAL 提供需要显示在所选浏览器中的 `start Url`，方便最终用户输入用户名等项。
身份验证完成后，应用需将 `end Url`（其中包含 Azure AD 提供的代码）传回给 MSAL。
`end Url` 的宿主始终为 `redirectUri`。 若要截获 `end Url`，请执行以下操作之一：

- 监视浏览器重定向，直至进入 `redirect Url`。
- 让浏览器重定向到你监视的 URL。

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi 是一个扩展点

`WithCustomWebUi` 是一个扩展点，可用于在公共客户端应用程序中提供你自己的 UI。 还可以让用户通过标识提供者的 /Authorize 终结点，并让其登录和许可。 然后，MSAL.NET 可以兑换身份验证代码并获取令牌。 例如，在 Visual Studio 中使用该参数可让电子应用程序（例如 Visual Studio 反馈）提供 Web 交互，并让 MSAL.NET 完成大部分工作。 如果要提供 UI 自动化，也可以使用它。 在公共客户端应用程序中，MSAL.NET 使用代码交换的证明密钥 (PKCE) 标准来确保遵守规则。 只有 MSAL.NET 才能兑换代码。 有关详细信息，请参阅 [RFC 7636 - OAuth 公共客户端的代码交换证明密钥](https://tools.ietf.org/html/rfc7636)。

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>使用 WithCustomWebUi

若要使用 `.WithCustomWebUI`，请执行以下步骤。

  1. 实现 `ICustomWebUi` 接口。 有关详细信息，请参阅[此网站](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)。 实现一个 `AcquireAuthorizationCodeAsync` 方法，并接受 MSAL.NET 计算的授权代码 URL。 然后，让用户完成与标识提供者的交互，并返回该 URL，标识提供者会结合授权代码一起使用该 URL 回调你的实现。 如果遇到问题，实现应引发 `MsalExtensionException` 异常，以便与 MSAL 进行良好的配合。
  2. 在 `AcquireTokenInteractive` 调用中，使用 `.WithCustomUI()` 修饰符传递自定义 Web UI 的实例。

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>测试自动化中 ICustomWebUi 的实现示例：SeleniumWebUI

MSAL.NET 团队已重新编写 UI 测试，以使用此扩展性机制。 如果有兴趣，请查看 MSAL.NET 源代码中的 [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) 类。

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>通过 SystemWebViewOptions 提供极佳体验

在 MSAL.NET 4.1 [`SystemWebViewOptions`](/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet) 中，可以指定：

- 在系统 Web 浏览器中出现登录或许可错误时，要转到的 URI (`BrowserRedirectError`) 或需要显示的 HTML 片段 (`HtmlMessageError`)。
- 在成功完成登录或许可时，要转到的 URI (`BrowserRedirectSuccess`) 或需要显示的 HTML 片段 (`HtmlMessageSuccess`)。
- 启动系统浏览器所需运行的操作。 可以通过设置 `OpenBrowserAsync` 委托来提供自己的实现。 此类还为两个浏览器提供默认的实现：`OpenWithEdgeBrowserAsync` 和 `OpenWithChromeEdgeBrowserAsync`，分别对应于 Microsoft Edge 和 [Chromium 上的 Microsoft Edge](https://www.windowscentral.com/faq-edge-chromium)。

若要使用此结构，请编写如下示例所示的内容：

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>其他可选参数

若要详细了解 `AcquireTokenInteractive` 的所有其他可选参数，请参阅 [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)。

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python 不直接提供以交互方式获取令牌的方法。 它要求应用程序在其实现用户交互流时发送授权请求，以获取授权代码。 然后，可以将此代码传递到 `acquire_token_by_authorization_code` 方法以获取令牌。

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])

```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>在适用于 iOS 和 macOS 的 MSAL 中

Objective-C：

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

Swift：

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```
---

## <a name="integrated-windows-authentication"></a>Windows 集成身份验证

若要使域用户登录到已加入域或已加入 Azure AD 的计算机，请使用 Windows 集成身份验证 (IWA)。

### <a name="constraints"></a>约束

- Windows 集成身份验证仅适用于“联合+”用户，即，在 Active Directory 中创建的、由 Azure AD 支持的用户。 直接在 Azure AD 中创建的但不是由 Active Directory 支持的用户（称为“托管用户”）不能使用此身份验证流。 此项限制不影响用户名和密码流。
- IWA 适用于针对 .NET Framework、.NET Core 和通用 Windows 平台 (UWP) 等平台编写的应用。
- IWA 不会绕过[多重身份验证 (MFA)](../authentication/concept-mfa-howitworks.md)。 如果已配置 MFA，则在需要 MFA 质询的情况下，IWA 可能失败，因为 MFA 需要用户交互。
  > [!NOTE]
  > 这一点较为棘手。 IWA 是非交互式的，但 MFA 需要用户交互。 标识提供者何时请求执行 MFA 并不由你控制，而是由租户管理员控制。 根据观察，在以下情况下需要 MFA：当你从不同国家/地区登录时；未通过 VPN 连接到公司网络时；有时甚至通过 VPN 连接也需要 MFA。 规则并不确定。 Azure AD 使用 AI 来持续判断是否需要执行 MFA。 如果 IWA 失败，则回退到用户提示，例如交互式身份验证或设备代码流。

- 在 `PublicClientApplicationBuilder` 中传入的颁发机构需要：
  - 租户化（采用 `https://login.microsoftonline.com/{tenant}/` 格式，其中，`tenant` 是表示租户 ID 或者与该租户关联的域的 GUID）。
  - 对于任何工作和学校帐户：`https://login.microsoftonline.com/organizations/`。
  - 不支持 Microsoft 个人帐户。 不能使用 /common 或 /consumers 租户。

- 因为集成 Windows 身份验证是一个无提示流：
  - 应用程序的用户必须已事先许可使用该应用程序。
  - 或者，租户管理员必须已事先许可租户中的所有用户使用该应用程序。
  - 换句话说：
    - 开发人员已在 Azure 门户中自行选择“授予”按钮。
    - 或者，租户管理员已在应用程序注册的“API 权限”选项卡中选择“授予/撤销 {租户域} 的管理员许可”按钮。  有关详细信息，请参阅 [添加访问 WEB API 的权限](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)。
    - 或者，你已提供某种方式让用户许可应用程序。 有关详细信息，请参阅[请求单个用户的许可](./v2-permissions-and-consent.md#requesting-individual-user-consent)。
    - 或者，你已提供某种方式让租户管理员许可应用程序。 有关详细信息，请参阅[管理员许可](./v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)。

- 已针对 .NET Desktop、.NET Core 和 UWP 应用启用此流。

有关许可的详细信息，请参阅 [Microsoft 标识平台的权限和许可](./v2-permissions-and-consent.md)。

### <a name="learn-how-to-use-it"></a>了解其用法

# <a name="net"></a>[.NET](#tab/dotnet)

在 MSAL.NET 中，需要使用：

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

通常只需要一个参数 (`scopes`)。 根据 Windows 管理员设置策略的方式，有可能不允许 Windows 计算机上的应用程序查找已登录的用户。 在这种情况下，请使用另一个方法 `.WithUsername()`，并以 UPN 格式（例如 `joe@contoso.com`）传入已登录用户的用户名。 在 .NET Core 上，只有采用用户名的重载可用，因为 .NET Core 平台无法请求用于登录 OS 的用户名。

下面的示例展示了最新的情况，并说明了可获取的异常类型以及缓解措施。

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

有关 AcquireTokenByIntegratedWindowsAuthentication 上可能的修饰符列表，请参阅 [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)。

# <a name="java"></a>[Java](#tab/java)

此代码摘录自 [MSAL Java 开发示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)。

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python 中尚不支持此流。

# <a name="macos"></a>[macOS](#tab/macOS)

此流不适用于 macOS。

---

## <a name="username-and-password"></a>用户名和密码

还可以通过提供用户名和密码获取令牌。 此流存在限制，因此不建议使用，但仍有一些用例需要用到它。

### <a name="this-flow-isnt-recommended"></a>不建议使用此流

不建议使用此流，因为要求用户提供其密码的应用程序是不安全的。 有关详细信息，请参阅[如何解决不断增多的密码问题？](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。 在已加入 Windows 域的计算机上以无提示方式获取令牌的首选流程是[集成 Windows 身份验证](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication)。 你还可以使用[设备代码流](https://aka.ms/msal-net-device-code-flow)。

> [!NOTE]
> 在某些情况下，使用用户名和密码非常有用，例如 DevOps 方案。 但是，如果你想在自行提供 UI 的交互式方案中使用用户名和密码，建议打消这个念头。 使用用户名和密码意味着会丧失许多功能：
>
> - 新式标识的核心原则。 密码可能被窃取和重放，因为共享机密可能会被截获。 此流与无密码登录是不兼容的。
> - 需要执行 MFA 的用户将无法登录，因为没有交互。
> - 用户无法执行单一登录 (SSO)。

### <a name="constraints"></a>约束

同样存在以下约束：

- 用户名和密码流与条件访问和多重身份验证不兼容。 因此，如果应用在租户管理员要求进行多重身份验证的 Azure AD 租户中运行，则无法使用此流。 许多组织都要求进行多重身份验证。
- 它仅适用工作和学校帐户（而不适用于 MSA）。
- 可在 .NET Desktop 和 .NET Core 中使用该流，但不能在 UWP 中使用。

### <a name="b2c-specifics"></a>B2C 特性

有关详细信息，请参阅[用于 B2C 的资源所有者密码凭据 (ROPC)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c)。

### <a name="use-it"></a>用法

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication` 包含方法 `AcquireTokenByUsernamePassword`。

以下示例演示了一个简化的用例。

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

以下示例演示了最新的用例，并解释了可能出现的各种异常及其缓解措施。

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

有关可应用到 `AcquireTokenByUsernamePassword` 的所有修饰符的详细信息，请参阅 [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)。

# <a name="java"></a>[Java](#tab/java)

以下代码摘录自 [MSAL Java 开发示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)。

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

此代码摘录自 [MSAL Python 开发示例](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)。

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

# <a name="macos"></a>[macOS](#tab/macOS)

适用于 macOS 的 MSAL 不支持此流。

---

## <a name="command-line-tool-without-a-web-browser"></a>命令行工具（不使用 Web 浏览器）

### <a name="device-code-flow"></a>设备代码流

如果你正在编写一个不包含 Web 控件的命令行工具，并且无法或者不想要使用前面所述的流，则需要使用设备代码流。

使用 Azure AD 的交互式身份验证需要 Web 浏览器。 有关详细信息，请参阅 [Web 浏览器的用法](https://aka.ms/msal-net-uses-web-browser)。 为了对不提供 Web 浏览器的设备或操作系统上的用户进行身份验证，设备代码流可让用户使用另一台设备（例如某台计算机或手机）以交互方式登录。 通过使用设备代码流，应用程序将通过为这些设备或操作系统设计的由两个步骤组成的过程获取令牌。 此类应用程序的例子包括 iOT 上运行的应用程序或命令行工具 (CLI)。 其思路是：

1. 每当需要用户身份验证时，应用就会为用户提供一个代码。 系统要求用户使用另一台设备（例如，已连接到 Internet 的智能手机）转到某个 URL（例如 `https://microsoft.com/devicelogin`）。 然后系统会提示用户输入代码。 完成此操作后，网页将引导用户进行常规的身份验证操作，其中包括同意提示和多重身份验证（如有必要）。

2. 身份验证成功后，命令行应用通过后端通道接收所需的令牌，并使用它们来执行所需的 Web API 调用。

### <a name="use-it"></a>用法

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication` 包含名为 `AcquireTokenWithDeviceCode` 的方法。

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

该方法用作参数：

- 要请求其访问令牌的 `scopes`。
- 接收 `DeviceCodeResult` 的回调。

  ![DeviceCodeResult 属性](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

以下示例代码演示了最新的用例，并解释了可能出现的各种异常及其缓解措施。

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] Scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }
    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
# <a name="java"></a>[Java](#tab/java)

此代码摘录自 [MSAL Java 开发示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)。

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

此代码摘录自 [MSAL Python 开发示例](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)。

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

# <a name="macos"></a>[macOS](#tab/macOS)

此流不适用于 macOS。

---

## <a name="file-based-token-cache"></a>基于文件的令牌缓存

在 MSAL.NET 中，默认会提供内存中令牌缓存。

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>在 Windows 桌面应用和 Web 应用或 Web API 中可自定义序列化

对于 .NET Framework 和 .NET Core，如果你不执行任何额外的操作，则内存中令牌缓存的持续时间与应用程序的持续时间相同。 若要了解为何不提供现成的序列化，请回顾一下，MSAL .NET Desktop 或 .NET Core 应用程序不仅可能是控制台或 Windows 应用程序（有权访问文件系统），而且还可能是 Web 应用程序或 Web API。 这些 Web 应用和 Web API 可能会使用一些特定的缓存机制，例如数据库、分布式缓存和 Redis 缓存。 若要在 .NET 桌面或 .NET Core 中使用持久的令牌缓存应用程序，需要自定义序列化。

令牌缓存序列化中涉及的类和接口包括以下类型：

- ``ITokenCache``，定义用于订阅令牌缓存序列化请求的事件，以及用于序列化或反序列化采用各种格式的缓存的方法（ADAL v3.0、MSAL 2.x 和 MSAL 3.x = ADAL v5.0）。
- ``TokenCacheCallback`` 是传递给事件的回调，可让你处理序列化。 将结合 ``TokenCacheNotificationArgs`` 类型的参数调用它们。
- ``TokenCacheNotificationArgs`` 仅提供应用程序的 ``ClientId``，是对该令牌适用的用户的引用。

  ![令牌缓存序列化示意图](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET 将为你创建令牌缓存，当你调用应用程序的 `UserTokenCache` 和 `AppTokenCache` 属性时，它会提供 `IToken` 缓存。 你不应自行实现接口。 实现自定义令牌缓存序列化时，你的责任是：
>
> - 对 `BeforeAccess` 和 `AfterAccess` 事件（或其异步对应事件）做出反应。 `BeforeAccess` 委托负责反序列化缓存。 `AfterAccess` 委托负责序列化缓存。
> - 需要知道，其中的一部分事件存储或加载 Blob，这些 Blob 将通过事件参数传递到所需的任何存储。

所用的策略会有所不同，具体取决于是针对公共客户端应用程序（例如桌面）还是机密客户端应用程序（例如 Web 应用、Web API 或守护程序应用）编写令牌缓存序列化。

从 MSAL v2.x 开始，可以使用多个选项。 所做的选择取决于是否既要以 MSAL.NET 格式序列化缓存（在 MSAL 中通用的统一格式缓存），又要跨平台序列化缓存。 或者，你可能还想要支持 ADAL v3 的[传统](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)令牌缓存序列化。

[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) 示例部分说明了如何自定义令牌缓存序列化，以在 ADAL.NET 3.x、ADAL.NET 5.x 与 MSAL.NET 之间共享 SSO 状态。

### <a name="simple-token-cache-serialization-msal-only"></a>简单令牌缓存序列化（仅限 MSAL）

下面是适用于桌面应用程序的令牌缓存的自定义序列化的简单实现示例。 此处，用户令牌缓存是应用程序所在的同一文件夹中的某个文件。

生成应用程序后，通过调用 ``TokenCacheHelper.EnableSerialization()`` 并传递应用程序 `UserTokenCache` 来启用序列化。

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

此帮助程序类类似于以下代码片段：

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

[Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) 开源库中提供了适用于公共客户端应用程序（适用于 Windows、Mac 和 Linux 上运行的桌面应用程序）的基于产品质量令牌缓存文件的序列化程序预览。 可以通过以下 NuGet 包将此程序包含在应用程序中：[Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/)。

> [!NOTE]
> 免责声明：Microsoft.Identity.Client.Extensions.Msal 库是基于 MSAL.NET 的扩展。 这些库中的类在将来可能会按原样或在进行重大更改后被纳入 MSAL.NET。

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>双令牌缓存序列化（MSAL 统一缓存和 ADAL v3）

你可能想要使用统一缓存格式实现令牌缓存序列化。 此格式在 ADAL.NET 4.x 和 MSAL.NET 2.x，以及同一个平台上的同一代或更旧的其他 MSAL 中通用。 可从以下代码中找到灵感：

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

这一次，帮助程序类类似于以下代码：

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [从桌面应用调用 Web API](scenario-desktop-call-api.md)
