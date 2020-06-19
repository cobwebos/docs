---
title: 获取用于调用 Web API 的令牌（桌面应用） | Azure
titleSuffix: Microsoft identity platform
description: 了解如何构建调用 Web API 以获取应用的令牌的桌面应用
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c439c118e242f3561593aa0c8fe9a88b3b07a4a9
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83771836"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>调用 Web API 的桌面应用：获取令牌

生成公共客户端应用程序的实例后，你将使用它来获取令牌，然后使用该令牌调用 Web API。

## <a name="recommended-pattern"></a>推荐的模式

Web API 由其 `scopes` 定义。 无论你在应用程序中提供何种体验，都将使用以下模式：

- 通过调用 `AcquireTokenSilent`，系统性地尝试从令牌缓存获取令牌。
- 如果此调用失败，则使用想要使用的 `AcquireToken` 流，在此处用 `AcquireTokenXX` 表示。

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

下面是在桌面应用程序中获取令牌的各种方法。

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

`AcquireTokenInteractive` 仅具有一个必需参数 ``scopes``，该参数包含字符串的枚举，这些字符串定义需要令牌的范围。 如果令牌用于 Microsoft Graph，则可以在“权限”一节中每个 Microsoft Graph API 的 API 参考中找到所需范围。 例如，若要[列出用户的联系人](https://docs.microsoft.com/graph/api/user-list-contacts)，必须使用范围“User. Read”、“Contacts.Read”。 有关详细信息，请参阅 [Microsoft Graph 权限参考](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)。

在 Android 上，你还需要使用 `.WithParentActivityOrWindow` 指定父活动（如下所示），以便在交互后将令牌返回到该父活动。 如果不指定它，在调用 `.ExecuteAsync()` 时将引发异常。

### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET 中的特定可选参数

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

UI 非常重要，因为它是交互式的。 `AcquireTokenInteractive` 具有一个特定的可选参数，该参数可为支持它的平台指定父 UI。 在桌面应用程序中使用时，`.WithParentActivityOrWindow` 具有不同的类型，具体取决于平台。

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

- 在 .NET Standard 中，预期的 `object` 在 Android 上为 `Activity`，在 iOS 上为 `UIViewController`，在 MAC 上为 `NSWindow`，在 Windows 上为 `IWin32Window` 或 `IntPr`。
- 在 Windows 上，必须从 UI 线程调用 `AcquireTokenInteractive`，以便嵌入性浏览器获取正确的 UI 同步上下文。 不从 UI 线程调用可能会导致消息无法正确抽取并导致 UI 出现死锁情况。 如果你不在 UI 线程上，从 UI 线程调用 Microsoft 身份验证库 (MSAL) 的一种方法是在 WPF 上使用 `Dispatcher`。
- 如果你使用的是 WPF，若要从 WPF 控件获取窗口，你可以使用 `WindowInteropHelper.Handle` 类。 然后，调用来自 WPF 控件 (`this`)：

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` 用于通过指定提示来控制与用户的交互。

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

类定义以下常量：

- ``SelectAccount`` 强制 STS 显示帐户选择对话框，该对话框包含用户具有会话的帐户。 当应用程序开发人员希望允许用户在不同标识之间进行选择时，此选项很有用。 此选项驱动 MSAL 将 ``prompt=select_account`` 发送到标识提供者。 此选项为默认值。 它可以很好地根据可用的信息（例如用户的会话帐户以及会话是否存在）提供最佳体验。 不要对其进行更改，除非你有充分的理由。
- 应用程序开发人员可使用 ``Consent`` 强制提示用户许可，即使之前已许可。 在这种情况下，MSAL 会将 `prompt=consent` 发送到标识提供者。 此选项可用于某些重视安全性的应用程序，在这些应用程序中，组织治理要求在每次使用应用程序时，都向用户显示许可对话框。
- ``ForceLogin`` 通过服务提示用户提供凭据，即使此用户提示可能并无必要。 如果获取令牌失败，此选项对让用户再次登录很有帮助。 在这种情况下，MSAL 会将 `prompt=login` 发送到标识提供者。 有时，它用于重视安全性的应用程序，在这些应用程序中，组织治理要求用户在每次访问应用程序的特定部分时需重新登录。
- ``Never``（仅适用于 .NET 4.5 和 WinRT）不会提示用户，而是尝试使用隐藏的嵌入式 Web 视图中存储的 cookie。 有关详细信息，请参阅 MSAL.NET 中的 Web 视图。 使用此选项可能会失败。 在这种情况下，`AcquireTokenInteractive` 将引发异常，通知需要 UI 交互。 你需要使用另一个 `Prompt` 参数。
- ``NoPrompt`` 不会向标识提供者发送任何提示。 此选项仅适用于 Azure Active Directory (Azure AD) B2C 编辑配置文件策略。 有关详细信息，请参阅 [Azure AD B2C 特性](https://aka.ms/msal-net-b2c-specificities)。

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

如果希望用户提前预先许可多个资源，并且不希望使用通常用于 MSAL.NET/ Microsoft 标识平台的增量许可，则可以使用此修饰符。 有关详细信息，请参阅[让用户提前许可多个资源](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)。

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Web UI 是用于调用浏览器的机制。 此机制可以是专用的 UI WebBrowser 控件，也可以是委托打开浏览器的方法。
MSAL 为大多数平台提供 Web UI 实现，但在某些情况下，你可能想要自行托管浏览器：

- MSAL 未明确覆盖的平台，例如 Blazor、Unity 和桌面上的 Mono。
- 需要对应用程序进行 UI 测试，并使用可与 Selenium 搭配使用的自动浏览器。
- 浏览器和运行 MSAL 的应用位于单独的进程中。

##### <a name="at-a-glance"></a>速览

若要实现此目的，需要提供 MSAL `start Url`，这需要在所选的浏览器中显示，以便最终用户可以输入用户名等项目。
身份验证完成后，应用需要回传 MSAL `end Url`，其中包含 Azure AD 提供的代码。
`redirectUri` 的主机始终为 `end Url`。 若要截获 `end Url`，请执行以下操作之一：

- 监视浏览器重定向，直到 `redirect Url` 命中。
- 让浏览器重定向到你所监视的 URL。

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi 是一个扩展点

`WithCustomWebUi` 是一个扩展点，可用于在公共客户端应用程序中提供自己的 UI。 你还可以让用户通过标识提供者的 /Authorize 终结点，并让他们登录并进行许可。 然后，MSAL.NET 可以兑换身份验证代码并获取令牌。 例如，它在 Visual Studio 中用于使电子应用程序（例如 Visual Studio 反馈）提供 Web 交互，但会将大部分工作留给 MSAL.NET 来完成。 如果要提供 UI 自动化，也可以使用它。 在公共客户端应用程序中，MSAL.NET 使用代码交换 (PKCE) 标准的证明密钥来确保遵守安全性。 只有 MSAL.NET 才能兑换代码。 有关详细信息，请参阅 [RFC 7636 - OAuth 公共客户端用于代码交换的证明密钥](https://tools.ietf.org/html/rfc7636)。

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>使用 WithCustomWebUi

若要使用 `.WithCustomWebUI`，请执行以下步骤。

  1. 实现 `ICustomWebUi` 接口。 有关详细信息，请参阅[此网站](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)。 实现一个 `AcquireAuthorizationCodeAsync` 方法，并接受由 MSAL.NET 计算的授权代码 URL。 然后，让用户与身份提供者进行交互，并返回 URL（身份提供者将通过此 URL 调用你的实现）以及授权代码。 如果遇到问题，实现应引发 `MsalExtensionException` 异常，以便与 MSAL 进行良好的配合。
  2. 在 `AcquireTokenInteractive` 调用中，使用传递自定义 Web UI 实例的 `.WithCustomUI()` 修饰符。

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>测试自动化中 ICustomWebUi 的实现示例：SeleniumWebUI

MSAL.NET 团队已重写 UI 测试，以便使用此扩展性机制。 如果你感兴趣，请查看 MSAL.NET 源代码中的 [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) 类。

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>使用 SystemWebViewOptions 提供出色的体验

从 MSAL.NET 4.1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet) 中，你可以指定：

- 系统 Web 浏览器中出现登录或许可错误时要转到的 URI (`BrowserRedirectError`) 或要显示的 HTML 片段 (`HtmlMessageError`)。
- 登录或许可成功时要转到的 URI (`BrowserRedirectSuccess`) 或要显示的 HTML 片段 (`HtmlMessageSuccess`)。
- 需要运行以启动系统浏览器的操作。 可以通过设置 `OpenBrowserAsync` 委托来提供自己的实现。 该类还为两种浏览器提供了默认实现：用于 Microsoft Edge 的 `OpenWithEdgeBrowserAsync` 和用于[基于 Chromium 的 Microsoft Edge](https://www.windowscentral.com/faq-edge-chromium) 的 `OpenWithChromeEdgeBrowserAsync`。

若要使用此结构，请编写类似于以下示例的代码：

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

若要详细了解用于 `AcquireTokenInteractive` 的其他可选参数，请参阅 [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)。

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

MSAL Python 不会直接提供交互式获取令牌方法。 相反，它要求应用程序在其用户交互流实现中发送授权请求，以获取授权代码。 然后，可以将此代码传递到 `acquire_token_by_authorization_code` 方法以获取令牌。

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

若要在域中或加入 Azure AD 的计算机上登录域用户，请使用集成 Windows 身份验证 (IWA)。

### <a name="constraints"></a>约束

- 集成 Windows 身份验证仅可用于“联合 +”用户，即在 Active Directory 中创建并受 Azure AD 支持的用户。 直接在 Azure AD 中创建但不受 Active Directory 支持的用户（称为“托管”用户）不能使用此身份验证流。 此限制不会影响用户名和密码流。
- IWA 适用于针对 .NET Framework、.NET Core 和通用 Windows 平台 (UWP) 等平台编写的应用。
- IWA 不会绕过[多重身份验证 (MFA)](../authentication/concept-mfa-howitworks.md)。 如果已配置 MFA，则在需要 MFA 质询的情况下，IWA 可能失败，因为 MFA 需要用户交互。
  > [!NOTE]
  > 这一点较为棘手。 IWA 是非交互式的，但 MFA 需要用户交互。 标识提供者何时请求执行 MFA 并不由你控制，而是由租户管理员控制。 根据观察，在以下情况下需要 MFA：当你从不同国家/地区登录时；未通过 VPN 连接到公司网络时；有时甚至通过 VPN 连接也需要 MFA。 规则并不确定。 Azure AD 使用 AI 以持续了解是否需要 MFA。 如果 IWA 失败，则回退到用户提示（如交互式身份验证或设备代码流）。

- 传入的 `PublicClientApplicationBuilder` 的授权机构需要为：
  - 格式为 `https://login.microsoftonline.com/{tenant}/` 的租户，其中 `tenant` 为表示租户 ID 的 GUID 或与租户关联的域。
  - 对于任何工作和学校帐户：`https://login.microsoftonline.com/organizations/`。
  - 不支持 Microsoft 个人帐户。 不能使用 /common 或 /consumers 租户。

- 因为集成 Windows 身份验证是一个无提示流：
  - 应用程序的用户必须事先同意使用该应用程序。
  - 或者，租户管理员必须事先同意租户中的所有用户使用该应用程序。
  - 换句话说：
    - 作为开发人员的你在 Azure 门户中为你自己选择了“授权”按钮。
    - 或者，租户管理员在该应用程序注册页的“API 权限”选项卡上选择了“为 {租户域} 授权/撤消管理员同意” 。 有关详细信息，请参阅 [添加访问 Web API 的权限](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis)。
    - 或者，你为用户提供了一种同意使用应用程序的方式。 有关详细信息，请参阅[请求单个用户同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent)。
    - 或者，你为租户管理员提供了一种同意使用应用程序的方式。 有关详细信息，请参阅[管理员同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant)。

- 将面向 .NET 桌面、.NET Core 和 UWP 应用启用此流。

有关同意的详细信息，请参阅 [Microsoft 标识平台权限和同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)。

### <a name="learn-how-to-use-it"></a>了解其用法

# <a name="net"></a>[.NET](#tab/dotnet)

在 MSAL.NET 中，需要使用：

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

通常只需要一个参数 (`scopes`)。 Windows 计算机上的应用程序可能无法查找已登录的用户，具体取决于 Windows 管理员设置策略的方式。 在这种情况下，请使用第二种方法，即 `.WithUsername()`，并以 UPN 格式传入已登录用户的用户名，例如 `joe@contoso.com`。 在 .NET Core 中，仅可使用携带用户名的重载，因为 .NET Core 平台无法向 OS 请求用户名。

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

还可以通过提供用户名和密码获取令牌。 此流受到限制，不建议使用它，但在某些用例中需要用到它。

### <a name="this-flow-isnt-recommended"></a>不建议使用此流

不建议使用此流，因为让应用程序要求用户输入密码不安全。 有关详细信息，请参阅[如何解决日益增长的密码问题？](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。 在已加入 Windows 域的计算机上以无提示方式获取令牌的首选流程是[集成 Windows 身份验证](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication)。 你还可以使用[设备代码流](https://aka.ms/msal-net-device-code-flow)。

> [!NOTE]
> 在某些情况下，使用用户名和密码非常有用，例如 DevOps 方案。 但是，如果你想在自行提供 UI 的交互式方案中使用用户名和密码，建议打消这个念头。 使用用户名和密码，你需要放弃很多东西：
>
> - 新式标识的核心原则。 由于可以截取共享机密，密码可能会被网络钓鱼和重播。 它与无密码不兼容。
> - 需要进行 MFA 的用户无法登录，因为没有交互。
> - 用户不能进行单一登录 (SSO)。

### <a name="constraints"></a>约束

同样存在以下约束：

- 用户名和密码流与条件访问和多重身份验证不兼容。 因此，如果应用在租户管理员要求进行多重身份验证的 Azure AD 租户中运行，则无法使用此流。 许多组织都要求进行多重身份验证。
- 它仅适用于工作和学校帐户（而不适用于 MSA）。
- 流在 .NET 桌面和 .NET Core 中可用，但在 UWP 上不可用。

### <a name="b2c-specifics"></a>B2C 特性

有关详细信息，请参阅[资源所有者密码凭据 (ROPC) 与 B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c)。

### <a name="use-it"></a>使用方式

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication` 包含 `AcquireTokenByUsernamePassword` 方法。

下面的示例展示了一个简化的案例。

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

下面的示例展示了最新的情况，并说明了可获取的异常类型以及缓解措施。

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

有关可应用于 `AcquireTokenByUsernamePassword` 的所有修饰符的详细信息，请参阅 [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)。

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

## <a name="command-line-tool-without-a-web-browser"></a>无需 Web 浏览器的命令行工具

### <a name="device-code-flow"></a>设备代码流

如果你正在编写的命令行工具没有 Web 控件，并且你不能或不想使用以前的流，则需要使用设备代码流。

使用 Azure AD 进行交互式身份验证需要 Web 浏览器。 有关详细信息，请参阅 [Web 浏览器的使用](https://aka.ms/msal-net-uses-web-browser)。 为了在不提供 Web 浏览器的设备或操作系统上对用户进行身份验证，设备代码流允许用户使用其他设备（如计算机或移动电话）以交互方式登录。 通过使用设备代码流，应用程序通过专为这些设备或 OS 设计的两步流程获取令牌。 此类应用程序的示例是在 iOT 或命令行工具 (CLI) 上运行的应用程序。 其思路是：

1. 每当需要进行用户身份验证时，应用便为用户提供代码。 要求用户使用其他设备（如连接 Internet 的智能手机）来访问 URL，例如 `https://microsoft.com/devicelogin`。 然后系统会提示用户输入代码。 完成此操作后，网页将引导用户进行常规的身份验证操作，其中包括同意提示和多重身份验证（如有必要）。

2. 身份验证成功后，命令行应用通过后端通道接收所需的令牌，并使用它们来执行所需的 Web API 调用。

### <a name="use-it"></a>使用方式

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication` 包含一个名为 `AcquireTokenWithDeviceCode` 的方法。

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

该方法用作参数：

- 要为其请求访问令牌的 `scopes`。
- 接收 `DeviceCodeResult` 的回叫。

  ![DeviceCodeResult 属性](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

下面的示例代码展示了最新的案例，并说明了可获取的异常类型以及缓解措施。

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

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>可在 Windows 桌面应用和 Web 应用或 Web API 中自定义序列化

对于 .NET Framework 和 .NET Core，如果不执行任何其他操作，则内存中令牌缓存将在应用程序运行期间持续存在。 若要了解不提供可立即使用的序列化的原因，请记住，MSAL .NET 桌面或 .NET Core 应用程序可以是控制台或 Windows 应用程序（有权访问文件系统），也可以是 Web 应用程序或 Web API。 这些 Web 应用和 Web API 可能使用某些特定的缓存机制，例如数据库、分布式缓存和 Redis 缓存。 若要在 .NET 桌面或 .NET Core 中使用永久性令牌缓存应用程序，则需要自定义序列化。

令牌缓存序列化中涉及的类和接口包括以下类型：

- ``ITokenCache``，定义用于订阅令牌缓存序列化请求的事件，以及用于以各种格式（ADAL v3.0，MSAL 2.x 和 MSAL 3.x = ADAL v5.0）对缓存进行序列化或反序列化的方法。
- ``TokenCacheCallback`` 是传递给事件的回调，可让你处理序列化。 将结合 ``TokenCacheNotificationArgs`` 类型的参数调用它们。
- ``TokenCacheNotificationArgs`` 仅为令牌可用的用户提供应用程序 ``ClientId`` 以及参考。

  ![令牌缓存序列化关系图](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET 将为你创建令牌缓存，当你调用应用程序的 `UserTokenCache` 和 `AppTokenCache` 属性时，它会提供 `IToken` 缓存。 你不应自行实现接口。 实现自定义令牌缓存序列化时，你的责任是：
>
> - 对 `BeforeAccess` 和 `AfterAccess` 事件，或它们的异步对应项做出反应。 `BeforeAccess` 委托负责反序列化缓存。 `AfterAccess` 委托负责序列化缓存。
> - 需了解其中的一部分事件存储或加载 Blob，这些 Blob 通过事件参数传递到所需的任何存储。

所用的策略会有所不同，具体取决于是针对公共客户端应用程序（例如桌面）还是机密客户端应用程序（例如 Web 应用或 Web API、守护程序应用）编写令牌缓存序列化。

从 MSAL v2. x 开始，你有多种选择。 你的选择取决于是否只想将缓存序列化为 MSAL.NET 格式，这是一种统一格式的缓存，在 MSAL 和平台之间都很常用。 或者，你可能还希望支持 ADAL v3 的[旧式](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)令牌缓存序列化。

示例 [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) 中的一部分说明了如何自定义令牌缓存序列化，以在 ADAL.NET 3.x、ADAL.NET 5.x 与 MSAL.NET 之间共享 SSO 状态。

### <a name="simple-token-cache-serialization-msal-only"></a>简单令牌缓存序列化（仅限 MSAL）

下面是为桌面应用程序自定义令牌缓存序列化的简单实现示例。 其中，用户令牌缓存所在的文件与应用程序位于同一文件夹中。

构建应用程序之后，可以通过调用 ``TokenCacheHelper.EnableSerialization()`` 并传递应用程序 `UserTokenCache` 来启用序列化。

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

[Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) 开放源代码库中提供了适用于公共客户端应用程序（适用于 Windows、Mac 和 Linux 上运行的桌面应用程序）的基于产品质量令牌缓存文件的序列化程序预览。 可以通过以下 Nuget 包将此程序包含在应用程序中：[Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/)。

> [!NOTE]
> 免责声明：Microsoft.Identity.Client.Extensions.Msal 库是对 MSAL.NET 的扩展。 这些库中的类在将来可能会按原样或在进行重大更改后被纳入 MSAL.NET。

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>双重令牌缓存序列化（MSAL 统一缓存 + ADAL v3）

你可能想要采用统一缓存格式实现令牌缓存序列化。 此格式是 ADAL.NET 4.x 和 MSAL.NET 2.x，以及相同平台上同一代或更低版本的其他 MSAL 通用的。 通过以下代码获得灵感：

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
