---
title: IOS 和 macOS 上的 ADAL 和 MSAL 应用之间的 SSO-Microsoft 标识平台
description: ''
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 218e659452779b6372541c3abec908878493f5d2
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268916"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>如何：MacOS 和 iOS 上的 ADAL 和 MSAL 应用之间的 SSO

适用于 iOS 的 Microsoft 身份验证库（MSAL）可以在应用程序之间与[ADAL 目标-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)共享 SSO 状态。 你可以按自己的节奏将你的应用迁移到 MSAL，确保你的用户仍可从跨应用 SSO （即使混合使用 ADAL 和基于 MSAL 的应用）中受益。

如果正在查找使用 MSAL SDK 在应用之间设置 SSO 的指南，请参阅[多个应用之间的无提示 sso](single-sign-on-macos-ios.md#silent-sso-between-apps)。 本文重点介绍 ADAL 和 MSAL 之间的 SSO。

实现 SSO 的具体情况取决于所使用的 ADAL 版本。

## <a name="adal-27x"></a>ADAL 2.7. x

本部分介绍 MSAL 与 ADAL 2.7. x 之间的 SSO 差异

### <a name="cache-format"></a>缓存格式

ADAL 2.7. x 可以读取 MSAL 缓存格式。 不需要对版本为 ADAL 2.7. x 的跨应用 SSO 执行任何特殊操作。 但是，您需要了解这两个库支持的帐户标识符之间的差异。

### <a name="account-identifier-differences"></a>帐户标识符差异

MSAL 和 ADAL 使用不同的帐户标识符。 ADAL 使用 UPN 作为其主帐户标识符。 MSAL 使用一个不可显示的帐户标识符，该标识符基于对象 ID 和 AAD 帐户`sub`的租户 ID 以及其他帐户类型的声明。

当你在 MSAL `MSALAccount`结果中收到对象时，它将`identifier`在属性中包含一个帐户标识符。 应用程序应将此标识符用于后续的无提示请求。

除外`identifier`， `MSALAccount`对象还包含名`username`为的可显示标识符。 这在 ADAL `userId`中转换为。 `username`不会被视为唯一的标识符，可以随时更改，因此它只应用于 ADAL 的后向兼容方案。 MSAL 支持使用`username`或`identifier`（建议使用`identifier`进行查询）的缓存查询。

下表汇总了 ADAL 与 MSAL 之间的帐户标识符差异：

| 帐户标识符                | MSAL                                                         | ADAL 2.7. x      | 旧 ADAL （ADAL 2.7. x 之前） |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| 可显示标识符            | `username`                                                   | `userId`        | `userId`                       |
| 唯一不可显示的标识符 | `identifier`                                                 | `homeAccountId` | 不可用                            |
| 不知道帐户 id               | 通过`allAccounts:` API 在中查询所有帐户`MSALPublicClientApplication` | 不可用             | 不可用                            |

这是提供`MSALAccount`这些标识符的接口：

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

如果你有一个 MSAL 应用和一个 ADAL 应用，并且用户第一次登录到基于 MSAL 的应用，则可以通过`username` `MSALAccount`从对象保存并将其作为`userId`传递到基于 ADAL 的应用程序来获取 adal 应用中的 SSO。 然后，ADAL 可以通过`acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` API 以无提示方式查找帐户信息。

### <a name="sso-from-adal-to-msal"></a>从 ADAL 到 MSAL 的 SSO

如果你有一个 MSAL 应用和一个 ADAL 应用，并且用户首次登录到基于 ADAL 的应用，则可以使用 ADAL 用户标识符在 MSAL 中查找帐户。 从 ADAL 迁移到 MSAL 时也适用。

#### <a name="adals-homeaccountid"></a>ADAL 的 homeAccountId

ADAL 2.7. x 通过此`homeAccountId`属性返回`ADUserInformation`结果中的对象中的：

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId`在 ADAL 中，与 MSAL `identifier`中的等效。 可以保存此标识符，以便在 MSAL 中使用`accountForIdentifier:error:` API 进行帐户查找。

#### <a name="adals-userid"></a>ADAL`userId`

如果`homeAccountId`不可用，或者只有可显示的标识符，则可以使用 ADAL 的`userId`查找 MSAL 中的帐户。

在 MSAL 中，首先查找或`username` `identifier`的帐户。 始终使用`identifier`进行查询，并仅将`username`其用作回退。 如果找到帐户，请使用 acquireTokenSilent 调用中的帐户。

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

反应

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



MSAL 支持的帐户查找 Api：

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occured trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occured trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2.x-2.6。6

本部分介绍 MSAL 与 ADAL 2.x-2.6.6 之间的 SSO 差异。

旧的 ADAL 版本不支持 MSAL 缓存格式。 但是，若要确保从 ADAL 平稳迁移到 MSAL，MSAL 可以读取较旧的 ADAL 缓存格式，而不会再次提示用户凭据。

由于`homeAccountId`较旧的 ADAL 版本中不可用，因此你需要`username`使用查找帐户：

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

反应

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



或者，你可以读取所有帐户，这些帐户也将从 ADAL 读取帐户信息：

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

反应

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
