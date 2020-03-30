---
title: 获取用于调用 Web API 的令牌（移动应用）| Azure
titleSuffix: Microsoft identity platform
description: 了解如何构建用于调用 Web API 的移动应用。 （获取应用的令牌。
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 9427235f47a31da75426559a4285634ab2837577
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132448"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>获取调用 Web API 的移动应用的令牌

在应用可以调用受保护的 Web API 之前，它需要访问令牌。 本文将引导您完成使用 Microsoft 身份验证库 （MSAL） 获取令牌的过程。

## <a name="define-a-scope"></a>定义范围

请求令牌时，需定义作用域。 作用域决定了应用能够访问哪些数据。  

定义作用域的最简单方法是将所需的 Web API`App ID URI`与作用域`.default`相结合。 此定义告诉 Microsoft 标识平台，你的应用需要在门户中设置的所有作用域。

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>获取令牌

### <a name="acquire-tokens-via-msal"></a>通过 MSAL 获取令牌

MSAL 允许应用以无提示方式和交互方式获取令牌。 调用`AcquireTokenSilent()`或`AcquireTokenInteractive()`时，MSAL 返回请求作用域的访问令牌。 正确的模式是发出静默请求，然后回退到交互式请求。

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});    

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

首先尝试以无提示方式获取令牌：

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";
    
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];
    
MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {
        
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        // Access token to call the Web API
        NSString *accessToken = result.accessToken;
    }
        
    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```
 
```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"
        
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
            
    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier
            
    // Access token to call the Web API
    let accessToken = authResult.accessToken
}
```

如果 MSAL`MSALErrorInteractionRequired`返回 ，则尝试以交互方式获取令牌：

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithParentViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
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

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(parentViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in
                
    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }
                
    // Get access token from result
    let accessToken = authResult.accessToken
})
```

适用于 iOS 和 macOS 的 MSAL 支持各种修改器以交互或静默方式获取令牌：
* [获取令牌的常见参数](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [获取交互式令牌的参数](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [获取静默令牌的参数](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

下面的示例显示了以交互方式获取令牌的最小代码。 该示例使用 Microsoft 图形读取用户的配置文件。

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

#### <a name="mandatory-parameters-in-msalnet"></a>MSAL.NET 中的必需参数

`AcquireTokenInteractive`只有一个必需参数： `scopes`. 参数`scopes`枚举定义需要令牌的范围的字符串。 如果令牌用于 Microsoft 图形，则可以在每个 Microsoft 图形 API 的 API 引用中找到所需的作用域。 在引用中，转到"权限"部分。 

例如，要[列出用户的联系人](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)，请使用"用户.阅读"，"联系人.阅读"的范围。 有关详细信息，请参阅 [Microsoft Graph 权限参考](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)。

在 Android 上，您可以使用 创建应用时指定父活动`PublicClientApplicationBuilder`。 如果当时未指定父活动，则稍后可以使用以下部分中操作来指定它`.WithParentActivityOrWindow`。 如果指定父活动，则令牌在交互后将返回该父活动。 如果不指定它，`.ExecuteAsync()`则调用将引发异常。

#### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET 中特定的可选参数

以下各节解释MSAL.NET中的可选参数。 

##### <a name="withprompt"></a>WithPrompt

参数`WithPrompt()`通过指定提示控制与用户的交互性。

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

该类定义以下常量：

- `SelectAccount`强制安全令牌服务 （STS） 显示帐户选择对话框。 该对话框包含用户具有会话的帐户。 如果要让用户在不同的标识中选择，则可以使用此选项。 此选项会驱动 MSAL 向标识提供者发送 `prompt=select_account`。 
    
    常`SelectAccount`量是默认值，它根据可用信息有效地提供了最佳体验。 可用信息可能包括帐户、用户会话的状态等。 除非有充分理由更改此默认值，否则不要更改此默认值。
- `Consent`使您能够提示用户同意，即使之前已授予同意。 在这种情况下，MSAL 会将 `prompt=consent` 发送到标识提供者。 

    您可能希望在以安全为中心的应用程序中`Consent`使用常量，其中组织治理要求用户每次使用该应用程序时都看到同意对话框。
- `ForceLogin`使服务能够提示用户输入凭据，即使不需要提示。 

    如果令牌获取失败，并且您希望让用户重新登录，则此选项非常有用。 在这种情况下，MSAL 会将 `prompt=login` 发送到标识提供者。 您可能希望在以安全为中心的应用程序中使用此选项，其中组织治理要求用户每次访问应用程序的特定部分时登录。
- `Never`仅适用于 .NET 4.5 和 Windows 运行时 （WinRT）。 此常量不会提示用户，但它将尝试使用存储在隐藏嵌入 Web 视图中的 Cookie。 有关详细信息，请参阅将[Web 浏览器与MSAL.NET](https://docs.microsoft.com/azure/active-directory/develop/msal-net-web-browsers)一起使用。

    如果此选项失败，则`AcquireTokenInteractive`引发异常以通知您需要 UI 交互。 然后，您需要使用另一`Prompt`个参数。
- `NoPrompt`不会向标识提供程序发送提示。 

    此选项仅适用于 Azure 活动目录 B2C 中的编辑配置文件策略。 有关详细信息，请参阅[B2C 详细信息](https://aka.ms/msal-net-b2c-specificities)。

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

在高级`WithExtraScopeToConsent`方案中使用修改器，您希望用户向多个资源提供预先同意。 当不想使用增量同意时，可以使用此修改器，增量同意通常用于MSAL.NET或 Microsoft 标识平台 2.0。 有关详细信息，请参阅[让用户提前许可多个资源](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)。

下面是代码示例： 

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>其他可选参数

要了解 的其他可选参数，`AcquireTokenInteractive`请参阅[收购令牌交互式参数生成器 的参考文档](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)。

### <a name="acquire-tokens-via-the-protocol"></a>通过协议获取令牌

我们不建议直接使用该协议获取令牌。 如果这样做，则应用将不支持某些涉及单一登录 （SSO）、设备管理和条件访问的方案。

当您使用该协议获取移动应用的令牌时，请发出两个请求： 

* 获取授权代码。
* 将代码交换为令牌。

#### <a name="get-an-authorization-code"></a>获取授权代码

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>获取访问权限并刷新令牌

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [调用 Web API](scenario-mobile-call-api.md)
