---
title: ADAL 应用与 MSAL 应用 (iOS/macOS) 之间的 SSO - Microsoft 标识平台 | Azure
description: ''
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 7a8a1667ba1ca2a99c053c6941e3ba778299fd53
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80880744"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>如何：macOS 和 iOS 上的 ADAL 与 MSAL 应用之间的 SSO

适用于 iOS 的 Microsoft 身份验证库 (MSAL) 可与 [ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) 共享应用程序之间的 SSO 状态。 你可以按照自己的步调将应用迁移到 MSAL，确保用户仍可受益于跨应用 SSO - 即使混用基于 ADAL 和基于 MSAL 的应用。

如果你正在寻找有关使用 MSAL SDK 在应用之间设置 SSO 的指导，请参阅[多个应用之间的静默 SSO](single-sign-on-macos-ios.md#silent-sso-between-apps)。 本文重点介绍 ADAL 与 MSAL 之间的 SSO。

实现 SSO 的具体步骤取决于所用的 ADAL 版本。

## <a name="adal-27x"></a>ADAL 2.7.x

本部分介绍 MSAL 与 ADAL 2.7.x 之间的 SSO 差异

### <a name="cache-format"></a>缓存格式

ADAL 2.7.x 可以读取 MSAL 缓存格式。 对于版本 ADAL 2.7.x 的跨应用 SSO，无需执行任何特殊操作。 但是，需要注意这两个库支持的帐户标识符的差异。

### <a name="account-identifier-differences"></a>帐户标识符差异

MSAL 和 ADAL 使用不同的帐户标识符。 ADAL 使用 UPN 作为主要帐户标识符。 MSAL 使用一个不可显示的帐户标识符，该标识符基于 AAD 帐户的对象 ID 和租户 ID，以及其他帐户类型的 `sub` 声明。

在 MSAL 结果中收到 `MSALAccount` 对象时，其 `identifier` 属性会包含一个帐户标识符。 应用程序应使用此标识符发出后续的静默请求。

除 `identifier` 以外，`MSALAccount` 对象还包含名为 `username` 的可显示标识符。 该标识符将转换为 ADAL 中的 `userId`。 `username` 不被视为唯一标识符且随时可能会更改，因此，只应将其用于 ADAL 的后向兼容方案。 MSAL 支持使用 `username` 或 `identifier` 的缓存查询，建议按 `identifier` 进行查询。

下表汇总了 ADAL 与 MSAL 之间的帐户标识符差异：

| 帐户标识符                | MSAL                                                         | ADAL 2.7.x      | 旧版 ADAL（ADAL 2.7.x 以前的版本） |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| 可显示的标识符            | `username`                                                   | `userId`        | `userId`                       |
| 不可显示的唯一标识符 | `identifier`                                                 | `homeAccountId` | 不适用                            |
| 没有任何已知的帐户 ID               | 通过 `allAccounts:` 中的 `MSALPublicClientApplication` API 查询所有帐户 | 空值             | 不适用                            |

这是提供这些标识符的 `MSALAccount` 接口：

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>从 MSAL 到 ADAL 的 SSO

如果你有一个 MSAL 应用和一个 ADAL 应用，并且用户是首次登录到基于 MSAL 的应用，则你可以通过保存 `MSALAccount` 对象中的 `username` 并将其作为 `userId` 传递给基于 ADAL 的应用，在 ADAL 应用中实现 SSO。 然后，ADAL 可以使用 `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` API 以静默方式查找帐户信息。

### <a name="sso-from-adal-to-msal"></a>从 ADAL 到 MSAL 的 SSO

如果你有一个 MSAL 应用和一个 ADAL 应用，并且用户是首次登录到基于 ADAL 的应用，则你可以使用 ADAL 用户标识符在 MSAL 中执行帐户查找。 此方法在从 ADAL 迁移到 MSAL 时也适用。

#### <a name="adals-homeaccountid"></a>ADAL 的 homeAccountId

ADAL 2.7.x 通过此属性在结果中返回 `ADUserInformation` 对象中的 `homeAccountId`：

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

ADAL 中的 `homeAccountId` 等效于 MSAL 中的 `identifier`。 可以保存此标识符，以便在 MSAL 中使用它来通过 `accountForIdentifier:error:` API 执行帐户查找。

#### <a name="adals-userid"></a>ADAL 的 `userId`

如果 `homeAccountId` 不可用，或者只有可显示的标识符，则可以使用 ADAL 的 `userId` 在 MSAL 中执行帐户查找。

在 MSAL 中，首先按 `username` 或 `identifier` 查找帐户。 如果有 `identifier`，请始终使用它执行查询，仅将 `username` 用作应变手段。 如果找到帐户，请在 `acquireTokenSilent` 调用中使用该帐户。

Objective-C：

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift：

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



MSAL 支持的帐户查找 API：

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2.x-2.6.6

本部分介绍 MSAL 与 ADAL 2.x-2.6.6 之间的 SSO 差异

旧版 ADAL 原生并不支持 MSAL 缓存格式。 但是，为了确保从 ADAL 平稳迁移到 MSAL，MSAL 可以读取旧版 ADAL 缓存格式，而不会再次提示输入用户凭据。

由于 `homeAccountId` 在旧版 ADAL 中不可用，因此需要使用 `username` 查找帐户：

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

例如：

Objective-C：


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift：

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



或者，可以读取所有帐户，这样也会读取 ADAL 中的帐户信息：

Objective-C：

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift：

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>后续步骤

详细了解[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)
