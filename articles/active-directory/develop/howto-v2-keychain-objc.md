---
title: 配置密钥链
titleSuffix: Microsoft identity platform
description: 了解如何配置密钥链，使应用能够在密钥链中缓存令牌。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 06e197a6e445c7dc1179be696318905f2132ee36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85477730"
---
# <a name="configure-keychain"></a>配置密钥链

如果[适用于 iOS 和 macOS 的 Microsoft 身份验证库](msal-overview.md) (MSAL) 要将用户登录或刷新令牌，它会尝试在密钥链中缓存令牌。 通过在密钥链中缓存令牌，MSAL 可以在同一家 Apple 开发商分发的多个应用之间提供静默单一登录 (SSO)。 SSO 是通过密钥链访问组功能实现的。 有关详细信息，请参阅 Apple 的[密钥链项文档](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)。

本文介绍如何配置应用权利，使 MSAL 能够将缓存的令牌写入 iOS 和 macOS 密钥链。

## <a name="default-keychain-access-group"></a>默认的密钥链访问组

### <a name="ios"></a>iOS

iOS 上的 MSAL 默认使用 `com.microsoft.adalcache` 访问组。 它是 MSAL 和 Azure AD 身份验证库 (ADAL) SDK 使用的共享访问组，可确保在同一家发行商的多个应用之间提供最佳单一登录 (SSO) 体验。

在 iOS 上，请在 XCode 中的“Project settings”（项目设置） > “Capabilities”（功能） > “Keychain sharing”（密钥链共享）下，将 `com.microsoft.adalcache` 密钥链组添加到应用的权利中

### <a name="macos"></a>macOS

macOS 上的 MSAL 默认使用 `com.microsoft.identity.universalstorage` 访问组。

由于 macOS 密钥链的限制，在 macOS 10.14 和更低版本上，MSAL 的 `access group` 不会直接转换为密钥链访问组属性（请参阅 [kSecAttrAccessGroup](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)）。 但是，从 SSO 的角度讲，MSAL 的行为类似，可确保同一家 Apple 开发商分发的多个应用程序提供静默 SSO。

在 macOS 10.15 和更高版本 (macOS Catalina) 中，与 iOS 类似，MSAL 使用密钥链访问组属性来实现静默 SSO。

## <a name="custom-keychain-access-group"></a>自定义的密钥链访问组

若要使用不同的密钥链访问组，可以在创建 `MSALPublicClientApplicationConfig` 时传递自定义组，然后再创建 `MSALPublicClientApplication`，如下所示：

# <a name="objective-c"></a>[Objective-C](#tab/objc)

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

# <a name="swift"></a>[Swift](#tab/swift)

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

如果你不想要在多个应用之间共享 SSO 状态，或不想使用任何密钥链访问组，请通过传递应用程序捆绑 ID 作为 keychainGroup，来禁用密钥链共享：

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>处理 -34018 错误（无法将项设置为密钥链）

错误 -34018 通常意味着未正确配置密钥链。 确保 MSAL 中配置的密钥链访问组与权利中配置的访问组匹配。

## <a name="ensure-your-application-is-properly-signed"></a>确保应用程序已正确签名

在 macOS 上，应用程序无需开发人员签名即可执行。 尽管 MSAL 的大部分功能可以继续工作，但通过密钥链访问实现的 SSO 要求为应用程序签名。 如果多次遇到密钥链提示，请确保应用程序的签名有效。

## <a name="next-steps"></a>后续步骤

在 Apple 的 [Sharing Access to Keychain Items Among a Collection of Apps](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)（在一系列应用之间共享对密钥链项的访问权限）一文中详细了解密钥链访问组。
