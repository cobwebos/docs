---
title: Xamarin iOS 注意事项（MSAL.NET） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解将 Xamarin iOS 与适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）配合使用时的注意事项。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76e614b605cd07cd5dc454824dd204447f806907
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132479"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>将 Xamarin iOS 与 MSAL.NET 配合使用时的注意事项
在 Xamarin iOS 上使用适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）时，应执行以下操作： 

- 重写和实现 `AppDelegate`中的 `OpenUrl` 函数。
- 启用密钥链组。
- 启用令牌缓存共享。
- 启用密钥链访问。
- 了解 iOS 12 和身份验证的已知问题。

## <a name="implement-openurl"></a>实现 OpenUrl

重写 `FormsApplicationDelegate` 派生类的 `OpenUrl` 方法，并调用 `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`。 下面是一个示例：

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

同时执行以下任务： 
* 定义 URL 方案。
* 需要应用程序调用其他应用程序的权限。
* 具有特定形式的重定向 URL。
* 在[Azure 门户](https://portal.azure.com)中注册重定向 URL。

### <a name="enable-keychain-access"></a>启用密钥链访问

若要启用密钥链访问，请确保你的应用程序具有密钥链访问组。 使用 `WithIosKeychainSecurityGroup()` API 创建应用程序时，可以设置密钥链访问组。

若要从缓存和单一登录（SSO）中获益，请在所有应用程序中将 "密钥链" 访问组设置为相同的值。

此安装示例使用 MSAL 4.x：
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

同时，在 `Entitlements.plist` 文件中启用密钥链访问。 使用以下访问组或您自己的访问组。

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

使用 `WithIosKeychainSecurityGroup()` API 时，MSAL 会自动在应用程序的*团队 ID* （`AppIdentifierPrefix`）的末尾追加安全组。 MSAL 添加安全组，因为在 Xcode 中生成应用程序时，它将执行相同的操作。 这就是 `Entitlements.plist` 文件中的权利需要在密钥链访问组之前包含 `$(AppIdentifierPrefix)` 的原因。

有关详细信息，请参阅[iOS 权利文档](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)。 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>在 iOS 应用程序上启用令牌缓存共享

从 MSAL 2.x 开始，可以指定密钥链访问组，以在多个应用程序中持久保存令牌缓存。 此设置使你可以在具有相同密钥链访问组的多个应用程序之间共享令牌缓存。 可以在[ADAL.NET](https://aka.ms/adal-net)应用程序、MSAL.NET Xamarin ios 应用程序和[objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc)或[MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)开发的本机 iOS 应用程序之间共享令牌现金。

通过共享令牌缓存，可以在使用相同密钥链访问组的所有应用程序中允许单一登录（SSO）。

若要启用此缓存共享，请使用 `WithIosKeychainSecurityGroup()` 方法将密钥链访问组设置为共享同一缓存的所有应用程序中的相同值。 本文中的第一个代码示例演示如何使用方法。

本文前面介绍了在每次使用 `WithIosKeychainSecurityGroup()` API 时，MSAL 会添加 `$(AppIdentifierPrefix)`。 MSAL 将添加此元素，因为团队 ID `AppIdentifierPrefix` 确保只有同一发布者所做的应用程序才能共享密钥链访问权限。

> [!NOTE]
> `KeychainSecurityGroup` 属性已弃用。
> 
> 从 MSAL 2.x 开始，当开发人员使用 `KeychainSecurityGroup` 属性时，会强制将 `TeamId` 前缀包括在内。 但从 MSAL 2.7. x 开始，当你使用新的 `iOSKeychainSecurityGroup` 属性时，MSAL 会在运行时解析 `TeamId` 前缀。 使用此属性时，请不要在值中包含 `TeamId` 前缀。 前缀不是必需的。
>
> 由于 `KeychainSecurityGroup` 属性已过时，请使用 `iOSKeychainSecurityGroup` 属性。

### <a name="use-microsoft-authenticator"></a>使用 Microsoft Authenticator

应用程序可以使用 Microsoft Authenticator 作为 broker 来实现以下操作：

- **Sso**：启用 sso 后，用户无需登录到每个应用程序。
- **设备标识**：通过访问设备证书，使用设备标识进行身份验证。 当设备加入工作区时，会在设备上创建此证书。 如果租户管理员启用了与设备相关的条件性访问，你的应用程序将准备就绪。
- **应用程序标识验证**：当应用程序调用代理时，它会传递其重定向 URL。 Broker 验证重定向 URL。

有关如何启用代理的详细信息，请参阅[在 Xamarin iOS 和 Android 应用程序上使用 Microsoft Authenticator 或 Microsoft Intune 公司门户](msal-net-use-brokers-with-xamarin-apps.md)。

## <a name="known-issues-with-ios-12-and-authentication"></a>IOS 12 和身份验证的已知问题
Microsoft 发布了有关 iOS 12 和某些类型的身份验证之间的不兼容性的[安全建议](https://github.com/aspnet/AspNetCore/issues/4647)。 不兼容性中断社交、WSFed 和 OIDC 登录。安全公告可帮助开发人员了解如何从应用程序中删除 ASP.NET 安全限制，使其与 iOS 12 兼容。  

当你在 Xamarin iOS 上开发 MSAL.NET 应用程序时，当你尝试从 iOS 12 登录到网站时，可能会看到一个无限循环。 此行为与此[ADAL 问题](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)类似。 

还可能会在 iOS 12 Safari ASP.NET Core OIDC authentication 中看到中断。 有关详细信息，请参阅此[WebKit 问题](https://bugs.webkit.org/show_bug.cgi?id=188165)。

## <a name="next-steps"></a>后续步骤

有关 Xamarin iOS 属性的信息，请参阅以下示例的 README.md 文件的[特定于 iOS 的注意事项](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations)段落：

示例 | 平台 | 说明
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、通用 Windows 平台（UWP） | 一个简单的 Xamarin Forms 应用，演示如何通过 Azure AD 2.0 终结点使用 MSAL 对 Microsoft 个人帐户和 Azure AD 进行身份验证。 该应用还演示了如何使用生成的令牌来访问 Microsoft Graph。

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->