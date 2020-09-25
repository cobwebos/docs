---
title: Xamarin iOS 注意事项 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: 了解将 Xamarin iOS 与适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 配合使用时的注意事项。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 70ab4a151fe73b59663fd8fa16170b2e507c2511
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258053"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>将 Xamarin iOS 与 MSAL.NET 配合使用时的注意事项

在 Xamarin iOS 上使用适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 时，应该：

- 重写并实现 `AppDelegate` 中的 `OpenUrl` 函数。
- 启用密钥链组。
- 启用令牌缓存共享。
- 启用密钥链访问。
- 了解 iOS 12 和 iOS 13 及身份验证的已知问题。

## <a name="implement-openurl"></a>实现 OpenUrl

重写 `FormsApplicationDelegate` 派生类的 `OpenUrl` 方法，并调用 `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`。 下面是一个示例：

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

同时，执行以下任务：

* 定义重定向 URI 方案。
* 获取所需的权限，使应用能够调用另一个应用。
* 具有特定形式的重定向 URI。
* 在 Azure 门户中[注册重定向 URI](quickstart-register-app.md#add-a-redirect-uri) 。

### <a name="enable-keychain-access"></a>启用密钥链访问

若要启用密钥链访问，请确保应用程序具有密钥链访问组。 创建应用程序时，可以使用 `WithIosKeychainSecurityGroup()` API 来设置密钥链访问组。

若要从缓存和单一登录 (SSO) 中获益，请在所有应用程序中将密钥链访问组设置为相同的值。

此设置示例使用 MSAL 4.x：

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

此外，在 `Entitlements.plist` 文件中启用密钥链访问。 使用以下访问组或你自己的访问组。

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

使用 `WithIosKeychainSecurityGroup()` API 时，MSAL 会自动将安全组追加到应用程序团队 ID 的末尾 (`AppIdentifierPrefix`)。  MSAL 之所以添加安全组，是因为当你在 Xcode 中生成应用程序时，它会执行相同的操作。 正因如此，`Entitlements.plist` 文件中的权利需要在密钥链访问组的前面包含 `$(AppIdentifierPrefix)`。

有关详细信息，请参阅 [iOS 权利文档](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)。

### <a name="enable-token-cache-sharing-across-ios-applications"></a>启用 iOS 应用程序之间的令牌缓存共享

从 MSAL 2.x 开始，可以指定一个密钥链访问组，用于在多个应用程序之间保留令牌缓存。 此设置可让你在使用相同密钥链访问组的多个应用程序之间共享令牌缓存。 可以在 [ADAL.NET](https://aka.ms/adal-net) 应用程序、MSAL.NET Xamarin ios 应用程序和 [objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) 或 [MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)开发的本机 iOS 应用程序之间共享令牌缓存。

通过共享令牌缓存，可以在使用相同密钥链访问组的所有应用程序之间实现单一登录 (SSO)。

若要启用此缓存共享，请在共享同一缓存的所有应用程序中，使用 `WithIosKeychainSecurityGroup()` 方法将密钥链访问组设置为相同的值。 本文中的第一个代码示例演示了如何使用该方法。

本文前面已提到，每次使用 `WithIosKeychainSecurityGroup()` API 时，MSAL 都会添加 `$(AppIdentifierPrefix)`。 MSAL 之所以添加此元素，是因为团队 ID `AppIdentifierPrefix` 确保只有同一发布者构建的应用程序可以共享密钥链访问。

> [!NOTE]
> `KeychainSecurityGroup` 属性已弃用。 改用 `iOSKeychainSecurityGroup` 属性。 `TeamId`使用时不需要前缀 `iOSKeychainSecurityGroup` 。

### <a name="use-microsoft-authenticator"></a>使用 Microsoft Authenticator

应用程序可以使用 Microsoft Authenticator 作为 broker 来实现以下操作：

- **Sso**：启用 sso 后，用户无需登录到每个应用程序。
- **设备标识**：通过访问设备证书，使用设备标识进行身份验证。 当设备加入工作区时，将在设备上创建此证书。 如果租户管理员启用了与设备相关的条件性访问，你的应用程序将准备就绪。
- **应用程序标识验证**：应用程序在调用中介时会传递其重定向 URL。 Broker 验证重定向 URL。

有关如何启用代理的详细信息，请参阅 [在 Xamarin iOS 和 Android 应用程序上使用 Microsoft Authenticator 或 Microsoft Intune 公司门户](msal-net-use-brokers-with-xamarin-apps.md)。

## <a name="known-issues-with-ios-12-and-authentication"></a>iOS 12 和身份验证的已知问题

Microsoft 发布了有关 iOS 12 和某些类型的身份验证之间的不兼容性的 [安全建议](https://github.com/aspnet/AspNetCore/issues/4647) 。 不兼容性中断社交、WSFed 和 OIDC 登录。此安全建议可帮助你了解如何从应用程序中删除 ASP.NET 安全限制，使其与 iOS 12 兼容。

当你在 Xamarin iOS 上开发 MSAL.NET 应用程序时，当你尝试从 iOS 12 登录到网站时，可能会看到一个无限循环。 此类行为与 GitHub 上的此 ADAL 问题类似： [尝试从 iOS 12 #1329 登录到网站时出现无限循环](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)。

还可能会在 iOS 12 Safari ASP.NET Core OIDC authentication 中看到中断。 有关详细信息，请参阅此 [WebKit 问题](https://bugs.webkit.org/show_bug.cgi?id=188165)。

## <a name="known-issues-with-ios-13-and-authentication"></a>IOS 13 和身份验证的已知问题

如果你的应用需要条件访问或证书身份验证支持，则允许你的应用与 Microsoft Authenticator broker 应用通信。 然后，MSAL 负责处理应用程序和 Microsoft Authenticator 之间的请求和响应。

在 iOS 13 上，Apple 通过自定义 URL 方案在从外部应用程序接收响应时删除应用程序读取源应用程序的能力，从而进行了重大的 API 更改。

Apple 的 [UIApplicationOpenURLOptionsSourceApplicationKey](https://developer.apple.com/documentation/uikit/uiapplicationopenurloptionssourceapplicationkey?language=objc) 状态文档：

> *如果请求源自属于你的团队的其他应用，则 UIKit 会将此密钥的值设置为该应用的 ID。如果源应用的团队标识符不同于当前应用的团队标识符，则该项的值为 nil。*

此更改会对 MSAL 中断，因为它依赖 `UIApplication.SharedApplication.OpenUrl` 于验证 MSAL 和 Microsoft Authenticator 应用程序之间的通信。

此外，在 iOS 13 上，开发人员需要在使用时提供表示控制器 `ASWebAuthenticationSession` 。

如果是使用 Xcode 11 构建的，并且使用的是 iOS broker 或，则应用会受到影响 `ASWebAuthenticationSession` 。

在这种情况下，请使用 [MSAL.NET 4.4.0 +](https://www.nuget.org/packages/Microsoft.Identity.Client/) 启用成功的身份验证。

### <a name="additional-requirements"></a>其他需求

- 使用最新的 MSAL 库时，请确保设备上已安装 **Microsoft Authenticator 版本 6.3.19 +** 。
- 更新到 MSAL.NET 4.4.0 + 时，请更新 `LSApplicationQueriesSchemes` *info.plist* 文件中的，并添加 `msauthv3` ：

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

    `msauthv3`要添加到*info.plist*中，必须在支持 iOS 13 的设备上检测最新的 Microsoft Authenticator 应用程序是否存在。

## <a name="report-an-issue"></a>报告问题

如果你有疑问或想要报告你在 MSAL.NET 中发现的问题，请在 GitHub 上的 [AzureAD/dotnet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues) 存储库中打开一个问题。

## <a name="next-steps"></a>后续步骤

有关 Xamarin iOS 属性的信息，请参阅以下示例的 README.md 文件的 [特定于 iOS 的注意事项](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) 段落：

示例 | 平台 | 说明
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、通用 Windows 平台 (UWP)  | 一个简单的 Xamarin Forms 应用，演示如何通过 Azure AD 2.0 终结点使用 MSAL 对 Microsoft 个人帐户和 Azure AD 进行身份验证。 该应用还演示了如何使用生成的令牌来访问 Microsoft Graph。

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
