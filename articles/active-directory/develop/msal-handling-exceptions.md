---
title: 错误和异常（MSAL） |Microsoft 标识平台
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
ms.date: 09/08/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 635793d18bf33752a2672788bb632571743410b7
ms.sourcegitcommit: 387da88b8262368c1b67fffea58fe881308db1c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2019
ms.locfileid: "71982770"
---
# <a name="handle-msal-exceptions-and-errors"></a>处理 MSAL 异常和错误

本文概述不同类型的错误，并提供有关处理常见登录错误的建议。

## <a name="msal-error-handling-basics"></a>MSAL 错误处理基础知识

Microsoft 身份验证库 (MSAL) 中的异常旨在帮助应用开发人员进行故障排除，而不会向最终用户显示。 异常消息未经本地化。

处理异常和错误时，可以使用异常类型本身和错误代码来区分不同的异常。  有关错误代码的列表，请参阅[身份验证和授权错误代码](reference-aadsts-error-codes.md)。

在无提示或交互式令牌获取过程中，应用可能会在登录体验过程中遇到错误，例如有关同意、条件性访问（MFA、设备管理、基于位置的限制）、令牌颁发和兑换以及用户的错误属性.

## <a name="msal-for-ios-and-macos-errors"></a>MSAL for iOS 和 macOS 错误

完整的错误列表在[MSALError 枚举](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)中列出。

在 @no__t 域中返回所有 MSAL 生成的错误。 

对于系统错误，MSAL 从系统 API 返回原始 @no__t 0。 例如，如果令牌获取由于缺少网络连接而失败，则 MSAL 将返回 @no__t 为 @no__t 域和为1的错误。

建议在客户端上至少处理以下两个 MSAL 错误：

- `MSALErrorInteractionRequired`：用户必须执行交互式请求。 很多情况下可能会导致此错误，如身份验证会话过期或需要额外的身份验证要求。 调用 MSAL 交互式令牌获取 API 进行恢复。 

- `MSALErrorServerDeclinedScopes`：部分或全部作用域已被拒绝。 决定是仅继续接受的作用域还是停止登录过程。

> [!NOTE]
> @No__t-0 枚举应仅用于引用和调试。 不要尝试在运行时自动处理这些错误。 如果您的应用程序遇到 `MSALInternalError` 下的任何错误，您可能需要显示一条面向用户的一般消息，说明发生了什么情况。

例如，`MSALInternalErrorBrokerResponseNotReceived` 表示用户未完成身份验证并手动返回到应用。 在这种情况下，你的应用程序应显示一般性错误消息，说明身份验证未完成，并建议他们尝试再次进行身份验证。

以下目标为 C 的示例代码演示了处理一些常见错误情况的最佳实践：

Objective-C
```ObjC
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

Swift
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

## <a name="net-exceptions"></a>.NET 异常

处理异常时，可以使用异常类型本身和 `ErrorCode` 成员来区分不同的异常。 @no__t 值为[MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet)类型的常量。

还可以查看[MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet)、 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)和[MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet)字段。

如果引发[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) ，请尝试[身份验证和授权错误代码](reference-aadsts-error-codes.md)，以查看代码是否列在此处。

### <a name="common-exceptions"></a>常见异常

下面是可能会引发的常见异常以及一些可能的缓解措施：  

| 异常 | 错误代码 | 缓解|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001：用户或管理员尚未许可使用名为“{appName}”、ID 为“{appId}”的应用程序。 针对此用户和资源发送交互式授权请求。| 需要先获取用户的许可。 如果未使用 .NET Core （没有任何 Web UI），请调用（仅一次） `AcquireTokeninteractive`。 如果你使用的是 .NET core 或不想执行 `AcquireTokenInteractive`，则用户可以导航到 URL 以获得许可： https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read 。 调用 `AcquireTokenInteractive`： `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079：用户需要使用多重身份验证（MFA）。| 没有缓解措施。 如果为你的租户配置了 MFA 并且 Azure Active Directory （AAD）决定强制执行，则需要回退到交互式流，如 `AcquireTokenInteractive` 或 `AcquireTokenByDeviceCode`。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010：不支持通过 */common*或 */consumers*终结点的授予类型。 请使用 */organizations* 或特定于租户的终结点。 使用了 */common*。| 根据 Azure AD 发出的消息中所述，颁发机构需要使用一个租户或 */organizations*。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002：请求正文必须包含以下参数： `client_secret or client_assertion`。| 如果你的应用程序未在 Azure AD 中注册为公用客户端应用程序，则会引发此异常。 在 Azure 门户中，编辑应用程序的清单并将 @no__t 设置为 `true`。 |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`：无法识别已登录的用户| 库无法查询当前的 Windows 登录用户或此用户未加入 AD 或 AAD （不支持工作位置加入的用户）。 缓解措施 1：在 UWP 中，检查应用程序是否具有以下功能：企业身份验证、专用网络（客户端和服务器）、用户帐户信息。 缓解措施 2：实现自己的逻辑以提取用户名（例如 john@contoso.com），并使用 `AcquireTokenByIntegratedWindowsAuth` 表单来提取用户名。|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| 此方法依赖于 Active Directory (AD) 公开的协议。 如果在 Azure Active Directory 中创建了一个用户但该用户不受 AD 的支持（“托管”用户），则此方法将会失败。 在 AD 中创建的且并受 AAD 支持的用户（“联合”用户）可以受益于这种非交互式身份验证方法。 缓解措施：使用交互式身份验证。|

### `MsalUiRequiredException`

调用 `AcquireTokenSilent()` @no__t 时，从 MSAL.NET 返回的常见状态代码之一。 此状态代码表示应用程序应再次调用身份验证库，但在交互模式（AcquireTokenInteractive 或 AcquireTokenByDeviceCodeFlow，适用于公用客户端应用程序，并在 Web 应用中执行质询）。 这是因为在颁发身份验证令牌之前，需要进行其他用户交互。

大多数情况下，`AcquireTokenSilent` 失败时，是因为令牌缓存没有与请求匹配的令牌。 访问令牌将在1小时后过期，`AcquireTokenSilent` 将尝试基于刷新令牌获取新的令牌（在 OAuth2 中，这是 "刷新令牌" 流）。 此流也可能因多种原因而失败，例如，如果租户管理员配置了更严格的登录策略。 

交互的目标是让用户执行操作。 其中的某些条件可以让用户轻松地解决问题（例如，只需一次单击即可接受使用条款），某些条件无法使用当前配置进行解析（例如，有问题的计算机需要连接到特定的企业网络）。 某些帮助用户设置多重身份验证，或者在其设备上安装 Microsoft Authenticator。

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` 分类枚举

MSAL 公开一个 `Classification` 字段，你可以读取该字段以提供更好的用户体验，例如，通知用户其密码已过期，或者他们需要提供使用某些资源的许可。 支持的值是 `UiRequiredExceptionClassification` 枚举的一部分：

| 分类    | 含义           | 建议的处理 |
|-------------------|-------------------|----------------------|
| BasicAction | 交互身份验证流期间，用户交互可以解决条件。 | 调用 AcquireTokenInteractively （）。 |
| AdditionalAction | 在交互身份验证流之外，与系统进行的其他补救交互可以解决条件。 | 调用 AcquireTokenInteractively （）以显示一条说明补救措施的消息。 如果用户不太可能完成补救操作，调用应用程序可能会选择隐藏需要 additional_action 的流。 |
| MessageOnly      | 此时无法解决条件。 启动交互身份验证流将显示一条消息，说明该情况。 | 调用 AcquireTokenInteractively （）以显示一条说明条件的消息。 用户读取该消息并关闭窗口后，AcquireTokenInteractively （）将返回 UserCanceled 错误。 如果用户不太可能从消息中获益，则调用应用程序可能会选择隐藏导致 message_only 的流。|
| ConsentRequired  | 用户同意丢失或已被吊销。 | 为用户调用 AcquireTokenInteractively （）以授予许可。 |
| UserPasswordExpired | 用户的密码已过期。 | 调用 AcquireTokenInteractively （），以使用户可以重置其密码。 |
| PromptNeverFailed| 在参数 prompt = never 的情况下调用交互身份验证，强制 MSAL 依赖浏览器 cookie，而不是显示浏览器。 此失败。 | 在不提示的情况下调用 AcquireTokenInteractively （）。 None |
| AcquireTokenSilentFailed | MSAL SDK 没有足够的信息，无法从缓存中获取令牌。 这可能是因为缓存中没有任何令牌或找不到帐户。 错误消息包含更多详细信息。  | 调用 AcquireTokenInteractively （）。 |
| 无    | 未提供更多详细信息。 交互身份验证流期间，用户交互可能会解决此问题。 | 调用 AcquireTokenInteractively （）。 |

## <a name="code-example"></a>代码示例

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
   res = await application.AcquireTokenInteractive(scopes)
                          .ExecuteAsync();
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


## <a name="javascript-errors"></a>JavaScript 错误

MSAL 提供了一些错误对象，这些对象可对不同类型的常见错误进行抽象和分类。 它还提供了用于访问错误的特定详细信息的接口，例如错误消息处理适当的错误消息。

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
* **AuthError.message：** 与 errorMessage 相同。
* **AuthError.stack：** 引发的错误的堆栈跟踪。 允许跟踪错误来源位置。

### <a name="error-types"></a>错误类型

可用的错误类型如下：

- `AuthError`：MSAL.js 库的基本 error 类，也可用于意外错误。

- `ClientAuthError`：Error 类，表示客户端身份验证的问题。 来自库的大多数错误都是 ClientAuthError。 这些错误是因为当登录名已在进行时调用 login 方法引起的，用户取消了登录名等。

- `ClientConfigurationError`：Error 类，扩展在给定用户配置参数格式不正确或缺失时发出请求之前引发 `ClientAuthError`。

- `ServerError`：Error 类表示身份验证服务器发送的错误字符串。 这些错误可能包括：请求格式或参数无效，或者阻止服务器对用户进行身份验证或授权的任何其他错误。

- `InteractionRequiredAuthError`：错误类，扩展 `ServerError` 以表示服务器错误，这需要交互式调用。 如果用户需要与服务器交互以提供凭据或同意身份验证/授权，则 @no__t 引发此错误。 错误代码包括 `"interaction_required"`、`"login_required"` 和 `"consent_required"`。

对于使用重定向方法（`loginRedirect`，`acquireTokenRedirect`）的身份验证流中的错误处理，需要注册在使用 `handleRedirectCallback()` 方法进行重定向后使用成功或失败调用的回调：

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

### <a name="interaction-required-errors"></a>需要交互，个错误

当你尝试使用非交互式方法获取令牌（如 `acquireTokenSilent`）时将返回错误，但 MSAL 无法以无提示方式执行此操作。

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

## <a name="conditional-access-and-claims-challenges"></a>条件访问和声明质询

以静默方式获取令牌时，应用程序可能会在你尝试访问的 API 所需的[条件性访问声明质询](conditional-access-dev-guide.md)（如 MFA 策略）时收到错误。

处理此错误的模式是使用 MSAL 以交互方式获取令牌。 以交互方式获取令牌会提示用户，并使他们能够满足所需的条件访问策略。

在某些情况下调用需要条件访问的 API 时，API 返回的错误中可能会包含声明质询。 例如，如果条件访问策略要求使用托管设备 (Intune)，则错误将类似于 [AADSTS53000:需要管理你的设备才能访问此资源](reference-aadsts-error-codes.md)。 在这种情况下，可以在令牌获取调用中传递声明，使系统提示用户，以满足相应的策略。

### <a name="net"></a>.NET

从 MSAL.NET 调用需要条件访问的 API 时，应用程序需要处理声明质询异常。 此错误将显示为 [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)，其中的 [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) 属性不为空。

若要处理声明质询，需要使用 @no__t 类的 `.WithClaim()` 方法。

### <a name="javascript"></a>JavaScript

当使用 MSAL 以静默方式（使用 `acquireTokenSilent`）获取令牌时，应用程序可能会在你尝试访问的 API 所需的[条件性访问声明质询](conditional-access-dev-guide.md)（如 MFA 策略）时收到错误。

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

调用需要条件访问的 API 时，API 返回的错误中可能会包含声明质询。 在这种情况下，可以将错误中返回的声明传递给 @no__t 类的 `claimsRequest` 字段以满足适当的策略。 

有关更多详细信息，请参阅[请求其他声明](active-directory-optional-claims.md)。

### <a name="msal-for-ios-and-macos"></a>适用于 iOS 和 MacOS 的 MSAL

适用于 iOS 和 macOS 的 MSAL 使你可以在交互式和无提示令牌采集方案中请求特定声明。

若要请求自定义声明，请在 `MSALSilentTokenParameters` 或 `MSALInteractiveTokenParameters` 中指定 `claimsRequest`。

有关详细信息，请参阅[使用 MSAL For iOS 和 MacOS 请求自定义声明](request-custom-claims.md)。

## <a name="retrying-after-errors-and-exceptions"></a>出现错误和异常后重试

在调用 MSAL 时，应实现自己的重试策略。 MSAL 对 AAD 服务发出 HTTP 调用，偶尔发生的故障可能发生，例如，网络可能会关闭或服务器过载。  

### <a name="http-error-codes-500-600"></a>HTTP 错误代码 500-600

对于 HTTP 错误代码为 500-600 的错误，MSAL.NET 实现一个简单的重试一次机制。

### <a name="http-429"></a>HTTP 429

如果服务令牌服务器（STS）的请求过多，将返回 HTTP 错误429，并提示你可以在 `Retry-After` 响应字段中重试的时间。

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)图面 @no__t 为 `namedHeaders` 的属性。 可以使用错误代码中的其他信息来提高应用程序的可靠性。 在这种情况下，你可以使用 `RetryAfterproperty` （类型 `RetryConditionHeaderValue`）并在重试时进行计算。

下面是使用客户端凭据流的守护程序应用程序的示例。 可以将此修改为任何用于获取令牌的方法。

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
