---
title: 配置密钥链
titleSuffix: Microsoft identity platform
description: 了解如何配置密钥链，使应用可以在密钥链中缓存令牌。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d94bf7ffe955c9ec9ee2a2e7f7c4dbaaa28df270
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085861"
---
# <a name="configure-keychain"></a>配置密钥链

当[适用于 iOS 和 macOS 的 Microsoft 身份验证库](msal-overview.md)（MSAL）在用户中签名或刷新令牌时，它会尝试缓存密钥链中的令牌。 使用密钥链中的缓存令牌，MSAL 可以在同一 Apple 开发人员分发的多个应用之间提供静默单一登录（SSO）。 SSO 是通过密钥链访问组功能实现的。 有关详细信息，请参阅 Apple 的[密钥链项文档](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)。

本文介绍如何配置应用权利，以便 MSAL 可以将缓存的令牌写入 iOS 和 macOS 密钥链。

## <a name="default-keychain-access-group"></a>默认密钥链访问组

### <a name="ios"></a>iOS

默认情况下，MSAL on iOS 使用 `com.microsoft.adalcache` 访问组。 这是 MSAL 和 Azure AD 身份验证库（ADAL） Sdk 使用的共享访问组，可确保同一发布者的多个应用之间的最佳单一登录（SSO）体验。

在 iOS 上的 "**项目 > 设置**" 下的 "XCode" 中，将 "`com.microsoft.adalcache` 密钥链组添加到你的应用的**权利 > ** **密钥链共享**"

### <a name="macos"></a>macOS

默认情况下，macOS 上的 MSAL 使用 `com.microsoft.identity.universalstorage` 访问组。

由于 macOS 密钥链限制，MSAL 的 `access group` 不会直接转换为 KSecAttrAccessGroup 10.14 及更早版本上的密钥链访问组属性（参见[macOS](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)）。 但是，它的行为与 SSO 的行为类似，这是为了确保同一 Apple 开发人员分发的多个应用程序可以具有无提示 SSO。

在 macOS 10.15 后（macOS Catalina），MSAL 使用密钥链访问组属性来实现无提示 SSO，与 iOS 类似。

## <a name="custom-keychain-access-group"></a>自定义密钥链访问组

如果要使用不同的密钥链访问组，请在创建 `MSALPublicClientApplication`之前传递自定义 `MSALPublicClientApplicationConfig` 组，如下所示：

# <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

```objc
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                                                            redirectUri:@"your-redirect-uri"
                                                                                              authority:nil];
    
config.cacheConfig.keychainSharingGroup = @"custom-group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:nil];
    
// Now call acquiretoken. 
// Tokens will be saved into the "custom-group" access group
// and only shared with other applications declaring the same access group
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: nil)
config.cacheConfig.keychainSharingGroup = "custom-group"
        
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```

---

## <a name="disable-keychain-sharing"></a>禁用密钥链共享

如果你不想在多个应用之间共享 SSO 状态或使用任何密钥链访问组，请通过将应用程序捆绑 ID 作为你的 keychainGroup 传递来禁用密钥链共享：

# <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>34018错误（无法将项目设置为密钥链）

错误-34018 通常表示未正确配置密钥链。 确保在 MSAL 中配置的密钥链访问组与 "权利" 中配置的访问组匹配。

## <a name="ensure-your-application-is-properly-signed"></a>确保应用程序已正确签名

在 macOS 上，应用程序无需由开发人员签署即可执行。 尽管 MSAL 的大部分功能都将继续工作，但 SSO 到密钥链的访问要求对应用程序进行签名。 如果遇到多个密钥链提示，请确保应用程序的签名有效。

## <a name="next-steps"></a>后续步骤

了解有关密钥链 access 组的详细信息，请参阅 Apple[共享访问密钥链中的项](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)。
