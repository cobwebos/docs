---
title: Xamarin iOS 注意事项（适用于 .NET 的 Microsoft 身份验证库）
titleSuffix: Microsoft identity platform
description: 了解将 Xamarin iOS 与适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）一起使用时的特定注意事项。
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
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64524960e584907b1e761a36f8ceb1461a7771c7
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802604"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>针对 MSAL.NET 的 Xamarin 特定于 iOS 的注意事项
在 Xamarin iOS 上，使用 MSAL.NET 时，必须考虑几个注意事项

- [IOS 12 和身份验证的已知问题](#known-issues-with-ios-12-and-authentication)
- [重写和实现 `AppDelegate`中的 `OpenUrl` 函数](#implement-openurl)
- [启用密钥链组](#enable-keychain-access)
- [启用令牌缓存共享](#enable-token-cache-sharing-across-ios-applications)
- [启用密钥链访问](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>IOS 12 和身份验证的已知问题
Microsoft 发布了一个[安全建议](https://github.com/aspnet/AspNetCore/issues/4647)，以提供有关 iOS12 与某些类型的身份验证之间的不兼容性的信息。 不兼容性中断社会、WSFed 和 OIDC 登录。 此咨询还提供了有关开发人员可以执行哪些操作来删除 ASP.NET 添加到其应用程序的当前安全限制以与 iOS12 兼容的指导。  

在 Xamarin iOS 上开发 MSAL.NET 应用程序时，尝试从 iOS 12 登录到网站时可能会出现无限循环（类似于此[ADAL 问题](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)）。 

如此[WebKit 问题](https://bugs.webkit.org/show_bug.cgi?id=188165)中所述，使用 IOS 12 SAFARI ASP.NET Core OIDC authentication 可能会出现中断。

## <a name="implement-openurl"></a>实现 OpenUrl

首先，需要重写 `FormsApplicationDelegate` 派生类的 `OpenUrl` 方法，并调用 `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`。

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

你还需要定义 URL 方案，要求你的应用程序调用其他应用程序，具有特定形式的重定向 URL，并在[Azure 门户](https://portal.azure.com)中注册此重定向 url。

### <a name="enable-keychain-access"></a>启用密钥链访问

若要启用密钥链访问，应用程序必须具有密钥链访问组。
创建应用程序时，可以使用 `WithIosKeychainSecurityGroup()` api 设置密钥链访问组，如下所示：

若要启用单一登录，需要在所有应用程序中将 `PublicClientApplication.iOSKeychainSecurityGroup` 属性设置为相同的值。

使用 MSAL v3. x 的一个示例是：
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

应将 info.plist 更新为类似于以下 XML 片段：

此更改是*在*`Entitlements.plist` 文件中使用以下访问组或你自己的访问权限来启用密钥链访问的补充：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.msalrocks</string>
  </array>
</dict>
</plist>
```

使用 MSAL v4. x 的一个示例是：

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

使用 `WithIosKeychainSecurityGroup()` api 时，MSAL 会自动将安全组附加到应用程序的 "团队 ID" （AppIdentifierPrefix）的末尾，因为当你使用 xcode 构建应用程序时，它将执行相同的操作。 [有关更多详细信息，请参阅 iOS 权利文档](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)。 这就是为什么需要在 info.plist 中的密钥链 access 组之前将权利更新为包括 $ （AppIdentifierPrefix）的原因。

### <a name="enable-token-cache-sharing-across-ios-applications"></a>在 iOS 应用程序上启用令牌缓存共享

在 MSAL 2.x 中，可以指定要用于在多个应用程序中保存令牌缓存的密钥链访问组。 此设置使你可以在具有相同密钥链访问组的多个应用程序之间共享令牌缓存，包括使用[ADAL.NET](https://aka.ms/adal-net)、MSAL.NET Xamarin 应用程序开发的应用程序，以及使用[开发的本机 iOS 应用程序Objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc)或[MSAL. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)）。

共享令牌缓存允许使用同一密钥链访问组的所有应用程序之间进行单一登录。

若要启用此缓存共享，需要在共享同一缓存的所有应用程序中将使用 "WithIosKeychainSecurityGroup （）" 方法设置为相同的值，如上述示例中所示。

如前所述，在使用 `WithIosKeychainSecurityGroup()` api 时，MSAL 添加了 $ （AppIdentifierPrefix）。 这是因为 AppIdentifierPrefix 或 "团队 ID" 用于确保只有同一发布者所做的应用程序可以共享密钥链访问权限。

> [!NOTE]
> **`KeychainSecurityGroup` 属性已弃用。**
> 
> 以前，在 MSAL 2.x 中，开发人员在使用 `KeychainSecurityGroup` 属性时被强制加入 TeamId 前缀。
>
>  在 MSAL 2.7. x 中，使用新的 `iOSKeychainSecurityGroup` 属性时，MSAL 将在运行时解析 TeamId 前缀。 使用此属性时，值不应包含 TeamId 前缀。
>  使用 new `iOSKeychainSecurityGroup` 属性，该属性不需要提供 TeamId，因为之前的 `KeychainSecurityGroup` 属性现已过时。

### <a name="use-microsoft-authenticator"></a>使用 Microsoft Authenticator

应用程序可以使用 Microsoft Authenticator （broker）启用以下操作：

- 单一登录（SSO）。 用户无需登录到每个应用程序。
- 设备标识。 通过访问设备证书，该证书是在加入工作区时在设备上创建的。 如果租户管理员启用了与设备相关的条件性访问，你的应用程序将准备就绪。
- 应用程序标识验证。 当应用程序调用代理时，它会传递其重定向 url，并且 broker 将对其进行验证。

有关如何启用代理的详细信息，请参阅[在 Xamarin iOS 和 Android 应用程序上使用 Microsoft Authenticator 或 Microsoft Intune 公司门户](msal-net-use-brokers-with-xamarin-apps.md)。

### <a name="sample-illustrating-xamarin-ios-specific-properties"></a>阐释 Xamarin iOS 特定属性的示例

以下示例的 readme.md 文件的[IOS 特定注意事项](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations)段落中提供了更多详细信息：

示例 | 平台 | 描述
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | 一个简单的 Xamarin Forms 应用，展示如何通过 Azure AD v2.0 终结点使用 MSAL 对 MSA 和 Azure AD 进行身份验证，并使用生成的令牌访问 Microsoft Graph。

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
