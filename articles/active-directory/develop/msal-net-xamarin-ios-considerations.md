---
title: Xamarin iOS 注意事项 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: 了解有关将 Xamarin iOS 与 Microsoft 身份验证库一起使用 .NET （MSAL.NET） 的注意事项。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262706"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>将 Xamarin iOS 与MSAL.NET
在 Xamarin iOS 上使用 .NET （MSAL.NET） 的 Microsoft 身份验证库时，您应该： 

- 重写和`OpenUrl`实现 中的`AppDelegate`函数。
- 启用钥匙串组。
- 启用令牌缓存共享。
- 启用钥匙串访问。
- 了解 iOS 12 和身份验证的已知问题。

## <a name="implement-openurl"></a>实现 OpenUrl

重写`OpenUrl``FormsApplicationDelegate`派生类和调用`AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`的方法。 下面是一个示例：

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

还要执行以下任务： 
* 定义 URL 方案。
* 需要应用调用其他应用的权限。
* 具有重定向 URL 的特定窗体。
* 在[Azure 门户](https://portal.azure.com)中注册重定向 URL。

### <a name="enable-keychain-access"></a>启用密钥链访问

要启用钥匙串访问，请确保应用程序具有钥匙串访问组。 使用 API 创建应用程序时，`WithIosKeychainSecurityGroup()`可以设置钥匙串访问组。

要从缓存和单一登录 （SSO） 中受益，请在所有应用程序中将钥匙串访问组设置为相同的值。

此设置的此示例使用 MSAL 4.x：
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

还启用`Entitlements.plist`文件中的钥匙串访问。 使用以下访问组或您自己的访问组。

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

使用 API`WithIosKeychainSecurityGroup()`时，MSAL 会自动将安全组追加到应用程序*的团队 ID* （）`AppIdentifierPrefix`的末尾。 MSAL 会添加安全组，因为当您在 Xcode 中构建应用程序时，它将执行相同的操作。 这就是为什么`Entitlements.plist`文件中的权利需要在钥匙串访问组之前包含`$(AppIdentifierPrefix)`。

有关详细信息，请参阅[iOS 权利文档](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)。 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>启用 iOS 应用程序之间的令牌缓存共享

从 MSAL 2.x 开始，可以指定一个钥匙串访问组，以在多个应用程序中保留令牌缓存。 此设置使您能够在具有相同钥匙串访问组的多个应用程序之间共享令牌缓存。 您可以在[ADAL.NET](https://aka.ms/adal-net)应用程序、MSAL.NET Xamarin.iOS 应用程序以及[ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc)或[MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)中开发的本机 iOS 应用程序中共享令牌现金。

通过共享令牌缓存，允许在使用同一钥匙串访问组的所有应用程序之间单一登录 （SSO）。

要启用此缓存共享，请使用`WithIosKeychainSecurityGroup()`方法将钥匙串访问组设置为共享同一缓存的所有应用程序中的相同值。 本文的第一个代码示例演示如何使用 方法。

在本文前面，您了解到，每当使用 API`$(AppIdentifierPrefix)`时，`WithIosKeychainSecurityGroup()`您都会添加 MSAL。 MSAL 添加此元素，因为团队`AppIdentifierPrefix`ID 可确保只有同一发布者制作的应用程序才能共享钥匙串访问权限。

> [!NOTE]
> `KeychainSecurityGroup` 属性已弃用。
> 
> 从 MSAL 2.x 开始，开发人员在使用`TeamId``KeychainSecurityGroup`该属性时被迫包括前缀。 但是，从 MSAL 2.7.x 开始，当您`iOSKeychainSecurityGroup`使用新属性时，MSAL`TeamId`在运行时解析前缀。 使用此属性时，不要在值中包括`TeamId`前缀。 前缀不是必需的。
>
> 由于属性`KeychainSecurityGroup`已过时，请使用 属性`iOSKeychainSecurityGroup`。

### <a name="use-microsoft-authenticator"></a>使用 Microsoft Authenticator

您的应用程序可以使用 Microsoft 身份验证器作为代理来启用：

- **SSO：** 启用 SSO 时，用户无需登录到每个应用程序。
- **设备标识**：使用设备标识通过访问设备证书进行身份验证。 此证书在设备加入工作区时在设备上创建。 如果租户管理员启用与设备相关的条件访问，则应用程序将准备就绪。
- **应用程序标识验证**：当应用程序调用代理时，它将传递其重定向 URL。 代理验证重定向 URL。

有关如何启用代理的详细信息，请参阅[在 Xamarin iOS 和 Android 应用程序上使用微软身份验证器或微软 Intune 公司门户](msal-net-use-brokers-with-xamarin-apps.md)。

## <a name="known-issues-with-ios-12-and-authentication"></a>iOS 12 和身份验证的已知问题
Microsoft 发布了有关 iOS 12 与某些类型的身份验证不兼容[的安全警告](https://github.com/aspnet/AspNetCore/issues/4647)。 不兼容会破坏社交、WSFed 和 OIDC 登录。安全咨询可帮助开发人员了解如何从应用程序中删除ASP.NET安全限制，使其与 iOS 12 兼容。  

当您在 Xamarin iOS 上开发MSAL.NET应用程序时，当您尝试从 iOS 12 登录网站时，您可能会看到无限循环。 此行为与此[ADAL 问题](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)类似。 

您可能还会看到 ASP.NET使用 iOS 12 Safari 进行核心 OIDC 身份验证的中断。 有关详细信息，请参阅此[WebKit 问题](https://bugs.webkit.org/show_bug.cgi?id=188165)。

## <a name="next-steps"></a>后续步骤

有关 Xamarin iOS 属性的信息，请参阅以下示例README.md文件中特定于[iOS](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations)的注意事项段落：

示例 | Platform | 描述
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS， 安卓， 通用 Windows 平台 （UWP） | 一个简单的 Xamarin 窗体应用，演示如何使用 MSAL 通过 Azure AD 2.0 终结点对 Microsoft 个人帐户和 Azure AD 进行身份验证。 该应用程序还演示如何使用生成的令牌访问 Microsoft 图形。

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->