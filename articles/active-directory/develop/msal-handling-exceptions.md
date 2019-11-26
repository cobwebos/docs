---
title: 错误和异常 (MSAL)
titleSuffix: Microsoft identity platform
description: 了解如何处理 MSAL 应用程序中的错误和异常、条件性访问和声明挑战。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/22/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 753296596982279a14ff2775b0e129048dbe8369
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482072"
---
# <a name="handle-msal-exceptions-and-errors"></a>处理 MSAL 异常和错误

本文概述不同类型的错误，并提供有关处理常见登录错误的建议。

## <a name="msal-error-handling-basics"></a>MSAL 错误处理基础知识

Microsoft 身份验证库（MSAL）中的异常面向应用程序开发人员进行故障排除--而不是向最终用户显示。 异常消息未经本地化。

处理异常和错误时，可以使用异常类型本身和错误代码来区分不同的异常。  有关错误代码的列表，请参阅[身份验证和授权错误代码](reference-aadsts-error-codes.md)。

在登录体验期间，你可能会遇到有关同意、条件性访问（MFA、设备管理、基于位置的限制）、令牌颁发和兑换以及用户属性的错误。

请参阅以下部分，该部分与你使用的语言相匹配，以获取有关应用的错误处理的更多详细信息。

## <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

处理 .NET 异常时，可以使用异常类型本身和 `ErrorCode` 成员来区分异常。 `ErrorCode` 值是 [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet) 类型的常量。

也可以查看 [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet)、[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) 和 [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) 的字段。

如果引发了 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)，请尝试查看[身份验证和授权错误代码](reference-aadsts-error-codes.md)来确定该代码是否列在其中。

### <a name="common-net-exceptions"></a>常见的 .NET 异常

下面是可能引发的常见异常和一些可能的缓解措施：  

| 异常 | 错误代码 | 缓解措施|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001：用户或管理员尚未同意使用 ID 为 "{appId}"、名为 "{appName}" 的应用程序。 针对此用户和资源发送交互式授权请求。| 需要先获取用户的许可。 如果未使用 .NET Core（它没有任何 Web UI），请调用 `AcquireTokeninteractive`（仅一次）。 如果你使用 .NET Core 或者不希望执行 `AcquireTokenInteractive`，则用户可以导航到某个 URL 来提供许可： https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read。 若要调用 `AcquireTokenInteractive`，请使用 `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079：用户需要使用多重身份验证（MFA）。| 无缓解措施。 如果为租户配置了 MFA 并且 Azure Active Directory (AAD) 决定强制实施 MFA，则需要回退到 `AcquireTokenInteractive` 或 `AcquireTokenByDeviceCode` 等交互式流。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010： */common*或 */consumers*终结点不支持授予类型。 请使用 */organizations* 或特定于租户的终结点。 使用了 */common*。| 根据 Azure AD 发出的消息中所述，颁发机构需要使用一个租户或 */organizations*。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002：请求正文必须包含以下参数： `client_secret or client_assertion`。| 如果应用程序未注册为 Azure AD 中的公共客户端应用程序，则可能会引发此异常。 在 Azure 门户中编辑应用程序的清单，并将 `allowPublicClient` 设置为 `true`。 |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`：无法识别已登录的用户| 库无法查询当前的 Windows 已登录用户，或者此用户未加入 AD 或 AAD（不支持已加入工作区的用户）。 缓解1：对于 UWP，请检查应用程序是否具有以下功能：企业身份验证、专用网络（客户端和服务器）、用户帐户信息。 缓解2：实现自己的逻辑来提取用户名（例如 john@contoso.com），并使用用户名中的 `AcquireTokenByIntegratedWindowsAuth` 形式。|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| 此方法依赖于 Active Directory (AD) 公开的协议。 如果在 Azure Active Directory 中创建了一个用户但该用户不受 AD 的支持（“托管”用户），则此方法将会失败。 在 AD 中创建的且并受 AAD 支持的用户（“联合”用户）可以受益于这种非交互式身份验证方法。 缓解：使用交互式身份验证。|

### `MsalUiRequiredException`

调用 `AcquireTokenSilent()` 时，从 MSAL.NET 返回的一个常见状态代码是 `MsalError.InvalidGrantError`。 此状态代码表示应用程序应再次调用身份验证库，但要在交互模式下调用（对公共客户端应用程序使用 AcquireTokenInteractive 或 AcquireTokenByDeviceCodeFlow，并在 Web 应用中执行质询）。 这是因为，只有在完成额外的用户交互之后，才能颁发身份验证令牌。

在大多数情况下，`AcquireTokenSilent` 失败的原因是令牌缓存中没有与请求匹配的令牌。 访问令牌将在 1 小时后过期，`AcquireTokenSilent` 会尝试基于刷新令牌获取新令牌（在 OAuth2 中，这称为“刷新令牌”流）。 此流也可能出于各种原因（例如，租户管理员配置了更严格的登录策略）而失败。 

交互的目的是让用户采取某种措施。 其中的某些条件可让用户轻松解决（例如，单击一下鼠标接受使用条款），但某些条件无法使用当前配置来解决（例如，相关的计算机需要连接到特定的企业网络）。 有些条件可帮助用户设置多重身份验证，或者在其设备上安装 Microsoft Authenticator。

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` 分类枚举

MSAL 公开一个 `Classification` 字段，读取该字段可以提供更好的用户体验，例如，通知用户其密码已过期，或者他们需要许可使用某些资源。 支持的值是 `UiRequiredExceptionClassification` 枚举的一部分：

| 分类    | 含义           | 建议的处理方式 |
|-------------------|-------------------|----------------------|
| BasicAction | 在交互式身份验证流期间，可通过用户交互来解决条件。 | 调用 AcquireTokenInteractively()。 |
| AdditionalAction | 在交互式身份验证流以外，可以通过与系统进行附加的补救交互来解决条件。 | 调用 AcquireTokenInteractively() 以显示一条解释补救措施的消息。 如果用户不太可能完成补救措施，调用方应用程序可以选择隐藏需要 additional_action 的流。 |
| MessageOnly      | 目前无法解决条件。 启动交互式身份验证流会显示一条解释该条件的消息。 | 调用 AcquireTokenInteractively() 以显示一条解释该条件的消息。 用户阅读该消息并关闭窗口后，AcquireTokenInteractively() 将返回 UserCanceled 错误。 如果该消息不太可能会为用户带来帮助，调用方应用程序可以选择隐藏导致 message_only 的流。|
| ConsentRequired  | 用户许可缺失或已撤销。 | 调用 AcquireTokenInteractively()，让用户提供许可。 |
| UserPasswordExpired | 用户的密码已过期。 | 调用 AcquireTokenInteractively()，使用户能够重置其密码。 |
| PromptNeverFailed| 已结合参数 prompt=never 调用交互式身份验证，这会强制 MSAL 依赖于浏览器 Cookie，且不显示浏览器。 此操作已失败。 | 调用 AcquireTokenInteractively() 但不指定 Prompt.None |
| AcquireTokenSilentFailed | MSAL SDK 没有足够的信息，无法从缓存中提取令牌。 原因可能是缓存中没有令牌，或找不到帐户。 错误消息中提供了更多详细信息。  | 调用 AcquireTokenInteractively()。 |
| 无    | 不提供更多详细信息。 在交互式身份验证流期间，可通过用户交互来解决条件。 | 调用 AcquireTokenInteractively()。 |

## <a name="net-code-example"></a>.NET 代码示例

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

MSAL.js 提供用于抽象化和分类各种常见错误的错误对象。 它还提供用于访问具体错误详细信息的接口，例如，方便适当处理错误的错误消息。

### <a name="error-object"></a>Error 对象

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

通过扩展 error 类可以访问以下属性：
- `AuthError.message`：与 `errorMessage`相同。
- `AuthError.stack`：引发的错误的堆栈跟踪。

### <a name="error-types"></a>错误类型

可用的错误类型如下：

- `AuthError`： MSAL 库的基本错误类，还用于意外错误。

- `ClientAuthError`： Error 类，表示客户端身份验证的问题。 来自库的大多数错误都是 ClientAuthError。 这些错误的原因包括：正在进行登录时调用某个登录方法、用户取消了登录，等等。

- `ClientConfigurationError`： Error 类，扩展在给定用户配置参数格式不正确或缺失时发出请求之前引发的 `ClientAuthError`。

- `ServerError`： Error 类表示身份验证服务器发送的错误字符串。 这些错误可能包括：请求格式或参数无效，或者阻止服务器对用户进行身份验证或授权的任何其他错误。

- `InteractionRequiredAuthError`： Error 类，扩展 `ServerError` 以表示服务器错误，这需要交互式调用。 如果用户必须与服务器交互才能提供凭据或许可身份验证/授权，则 `acquireTokenSilent` 会引发此错误。 错误代码包括 `"interaction_required"`、`"login_required"` 和 `"consent_required"`。

若要使用重定向方法（`loginRedirect`、`acquireTokenRedirect`）在身份验证流中处理错误，需要按如下所示注册回调，重定向后，系统会使用 `handleRedirectCallback()` 结合成功或失败状态调用该回调：

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

弹出体验的方法（`loginPopup`、`acquireTokenPopup`）会返回约定，因此你可以使用约定模式（.then 和 .catch）来处理这些错误，如下所示：

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>需要交互的错误

尝试使用非交互式方法获取令牌（例如 `acquireTokenSilent`），但 MSAL 无法以静默方式执行该操作时，将返回错误。

可能的原因包括：

- 需要登录
- 需要许可
- 需要经历多重身份验证体验。

补救措施是调用 `acquireTokenPopup` 或 `acquireTokenRedirect` 等交互式方法：

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

## <a name="pythontabpython"></a>[Python](#tab/python)

在 Python 的 MSAL 中，大多数错误都作为 API 调用的返回值传达。 此错误表示为一个字典，其中包含来自 Microsoft 标识平台的 JSON 响应。

* 成功的响应包含 `"access_token"` 键。 响应的格式由 OAuth2 协议定义。 有关详细信息，请参阅[5.1 成功响应](https://tools.ietf.org/html/rfc6749#section-5.1)
* 错误响应包含 `"error"`，通常 `"error_description"`。 响应的格式由 OAuth2 协议定义。 有关详细信息，请参阅[5.2 错误响应](https://tools.ietf.org/html/rfc6749#section-5.2)

返回错误时，`"error_description"` 项包含用户可读的消息;这通常包含 Microsoft 标识平台错误代码。 有关各个错误代码的详细信息，请参阅[身份验证和授权错误代码](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes)。

在 Python 的 MSAL 中，异常非常罕见，因为大多数错误都是通过返回错误值处理的。 仅当尝试使用库时出现问题时（例如 API 参数格式不正确时），才会引发 `ValueError` 异常。

## <a name="javatabjava"></a>[Java](#tab/java)

在 Java MSAL 中，有三种类型的异常： `MsalClientException`、`MsalServiceException`和 `MsalInteractionRequiredException`;所有这些都继承自 `MsalException`。

- 当库或设备的本地错误发生错误时，将引发 `MsalClientException`。
- 当安全令牌服务（STS）返回错误响应或发生另一个网络错误时，将引发 `MsalServiceException`。
- 如果要求身份验证成功，则会引发 `MsalInteractionRequiredException`。

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` 公开在 STS 的请求中返回的 HTTP 标头。 通过 `MsalServiceException.headers()` 访问

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

`InvalidGrantError`调用 `AcquireTokenSilently()` 时，从 MSAL for Java 返回的常见状态代码之一。 这意味着，需要进行其他用户交互，然后才能颁发身份验证令牌。 应用程序应再次调用身份验证库，但在交互模式下，通过向公共客户端应用程序发送 `AuthorizationCodeParameters` 或 `DeviceCodeParameters` 来实现。

大多数情况下，`AcquireTokenSilently` 失败，是因为令牌缓存没有与请求匹配的令牌。 访问令牌在一小时后过期，`AcquireTokenSilently` 将尝试基于刷新令牌获取一个新令牌。 在 OAuth2 中，这是刷新令牌流。 此流也可能会出于各种原因（例如租户管理员配置更严格的登录策略时）而失败。

导致此错误的一些情况非常适合用户解决。 例如，他们可能需要接受使用条款。 也许无法使用当前配置来完成请求，因为计算机需要连接到特定的公司网络。

MSAL 公开一个 `reason` 字段，可用于提供更好的用户体验。 例如，"`reason`" 字段可能会告诉用户其密码已过期，或者他们需要提供使用某些资源的许可。 支持的值是 `InteractionRequiredExceptionReason` 枚举的一部分：

| Reason | 含义 | 建议的处理 |
|---------|-----------|-----------------------------|
| `BasicAction` | 在交互身份验证流期间，用户交互可以解决条件 | 用交互式参数调用 `acquireToken` |
| `AdditionalAction` | 与交互式身份验证流之外的系统进行更多的补救交互时，可以解决此问题。 | 使用交互式参数调用 `acquireToken`，以显示一条消息，说明要采取的补救措施。 如果用户不太可能完成补救措施，则调用应用程序可能会选择隐藏需要其他操作的流。 |
| `MessageOnly` | 目前无法解决条件。 启动交互身份验证流，以显示一条说明条件的消息。 | 使用交互式参数调用 `acquireToken`，以显示一条说明条件的消息。 `acquireToken` 将在用户读取消息并关闭窗口后返回 `UserCanceled` 错误。 如果用户不太可能从消息中获益，应用程序可以选择隐藏导致消息的流。 |
| `ConsentRequired`| 用户许可缺失或已撤销。 |通过交互式参数调用 `acquireToken`，以便用户可以获得同意。 |
| `UserPasswordExpired` | 用户的密码已过期。 | 通过交互式参数调用 `acquireToken`，以便用户可以重置其密码 |
| `None` |  提供了更多详细信息。 在交互身份验证流期间，用户交互可能会解决此问题。 | 用交互式参数调用 `acquireToken` |

### <a name="code-example"></a>代码示例

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

## <a name="iosmacostabiosmacos"></a>[iOS/macOS](#tab/iosmacos)

[MSALError 枚举](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)中列出了 MSAL for IOS 和 macOS 错误的完整列表。

MSAL 生成的所有错误将连同 `MSALErrorDomain` 域一起返回。

对于系统错误，MSAL 将从系统 API 返回原始 `NSError`。 例如，如果令牌获取因未建立网络连接而失败，则 MSAL 将返回错误，同时返回 `NSURLErrorDomain` 域和 `NSURLErrorNotConnectedToInternet` 代码。

建议至少在客户端处理以下两个 MSAL 错误：

- `MSALErrorInteractionRequired`：用户必须执行交互式请求。 有许多状况可能会导致此错误，例如，身份验证会话过期，或需要满足额外的身份验证要求。 调用 MSAL 交互式令牌获取 API 可予以恢复。 

- `MSALErrorServerDeclinedScopes`：已拒绝部分或全部作用域。 确定是仅使用授予的范围继续，还是停止登录过程。

> [!NOTE]
> 请只将 `MSALInternalError` 枚举用于参考和调试。 请勿尝试在运行时自动处理这些错误。 如果应用遇到属于 `MSALInternalError` 类别的任何错误，可以显示一条面向用户的常规消息来解释发生了什么情况。

例如，`MSALInternalErrorBrokerResponseNotReceived` 表示用户未完成身份验证并已手动返回到应用。 在这种情况下，应用应该显示一条常规错误消息来指出身份验证未完成，并建议他们尝试再次进行身份验证。

以下 Objective-C 示例代码演示了处理某些常见错误状况的最佳做法：

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

## <a name="conditional-access-and-claims-challenges"></a>条件访问和声明质询

以无提示方式获取令牌时，如果你尝试访问的 API 需要[条件访问声明质询](conditional-access-dev-guide.md)（例如 MFA 策略），则应用程序可能会收到错误。

处理此错误的模式是使用 MSAL 以交互方式获取令牌。 以交互方式获取令牌会提示用户，并使他们能够满足所需的条件访问策略。

在某些情况下调用需要条件访问的 API 时，API 返回的错误中可能会包含声明质询。 例如，如果条件性访问策略要拥有托管设备（Intune），则该错误将类似于[AADSTS53000：需要对设备进行管理才能访问此资源](reference-aadsts-error-codes.md)或类似的内容。 在这种情况下，可以在令牌获取调用中传递声明，使系统提示用户，以满足相应的策略。

### <a name="net"></a>.NET

从 MSAL.NET 调用需要条件访问的 API 时，应用程序需要处理声明质询异常。 此错误将显示为 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)，其中的 [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) 属性不为空。

若要处理声明质询，需要使用 `.WithClaim()` 类的 `PublicClientApplicationBuilder` 方法。

### <a name="javascript"></a>JavaScript

使用 MSAL.js 以无提示方式获取令牌时（使用 `acquireTokenSilent`），如果你尝试访问的 API 需要[条件访问声明质询](conditional-access-dev-guide.md)（例如 MFA 策略），则应用程序可能会收到错误。

处理此错误的模式是发出交互式调用（例如 `acquireTokenPopup` 或 `acquireTokenRedirect`）以获取 MSAL.js 中的令牌，如以下示例所示：

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    if (error instanceof InteractionRequiredAuthError) {
        // Extract claims from error message
        accessTokenRequest.claimsRequest = extractClaims(error.errorMessage);
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
    }
});
```

以交互方式获取令牌会提示用户，并使他们能够满足所需的条件访问策略。

调用需要条件访问的 API 时，API 返回的错误中可能会包含声明质询。 在这种情况下，可将错误中返回的声明传递到 `claimsRequest` 类的 `AuthenticationParameters.ts` 字段，以符合相应的策略。 

有关更多详细信息，请参阅[请求其他声明](active-directory-optional-claims.md)。

### <a name="msal-for-ios-and-macos"></a>适用于 iOS 和 MacOS 的 MSAL

适用于 iOS 和 macOS 的 MSAL 使你可以在交互式和无提示令牌采集方案中请求特定声明。

若要请求自定义声明，请在 `MSALSilentTokenParameters` 或 `MSALInteractiveTokenParameters`中指定 `claimsRequest`。

有关详细信息，请参阅[使用 MSAL For iOS 和 MacOS 请求自定义声明](request-custom-claims.md)。

## <a name="retrying-after-errors-and-exceptions"></a>出现错误和异常后重试

在调用 MSAL 时，应实现自己的重试策略。 MSAL 对 AAD 服务发出 HTTP 调用，在此过程中偶尔会发生失败，例如，由于网络故障或服务器过载。  

### <a name="http-error-codes-500-600"></a>HTTP 错误代码 500-600

对于 HTTP 错误代码为 500-600 的错误，MSAL.NET 实现一个简单的重试一次机制。

### <a name="http-429"></a>HTTP 429

如果过多的请求导致服务令牌服务器 (STS) 过载，`Retry-After` 响应字段中将返回 HTTP 错误 429，并提示可在多长时间后重试。

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) 以 `System.Net.Http.Headers.HttpResponseHeaders` 属性的形式公开 `namedHeaders`。 可以使用错误代码中的附加信息来提高应用程序的可靠性。 对于前面所述的场景，可以使用 `RetryAfterproperty`（类型为 `RetryConditionHeaderValue`）并计算重试时间。

下面是使用客户端凭据流的守护程序应用程序示例。 可以根据用于获取令牌的方法改编此示例。

```csharp
do
{
    retry = false;
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                           .ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (ex.ErrorCode == "temporarily_unavailable")
         {
             RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
             if (retryAfter.Delta.HasValue)
             {
                 delay = retryAfter.Delta;
             }
             else if (retryAfter.Date.HasValue)
             {
                 delay = retryAfter.Date.Value.Offset;
             }
         }
    }
    …
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```
