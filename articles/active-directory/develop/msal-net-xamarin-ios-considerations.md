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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91258053"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>将 Xamarin iOS 与 MSAL.NET 配合使用时的注意事项

在 Xamarin iOS 上使用适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 时，应该：

- 重写并实现 `AppDelegate` 中的 `OpenUrl` 函数。
- 启用密钥链组。
- 启用令牌缓存共享。
- 启用密钥链访问。
- 了解有关 iOS 12 和 iOS 13 以及身份验证的已知问题。

## <a name="implement-openurl"></a>实现 OpenUrl

重写 `FormsApplicationDelegate` 派生类的 `OpenUrl` 方法，并调用 `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`。 下面是一个示例：

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

另外，请执行以下任务：

* 定义重定向 URI 方案。
* 获取所需的权限，使应用能够调用另一个应用。
* 为重定向 URI 采用特定形式。
* 在 Azure 门户中[注册重定向 URI](quickstart-register-app.md#add-a-redirect-uri)。

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
> `KeychainSecurityGroup` 属性已弃用。 改用 `iOSKeychainSecurityGroup` 属性。 使用 `iOSKeychainSecurityGroup` 时，不要求有 `TeamId` 前缀。

### <a name="use-microsoft-authenticator"></a>使用 Microsoft Authenticator

应用程序可以使用 Microsoft Authenticator（充当中介）来启用：

- **SSO**：启用 SSO 时，用户无需登录到每个应用程序。
- **设备标识**：使用设备标识通过访问设备证书进行身份验证。 当设备加入工作区时，将在设备上创建此证书。 如果租户管理员启用了与设备相关的条件访问，则应用程序将准备就绪。
- **应用程序标识验证**：应用程序在调用中介时会传递其重定向 URL。 中介将验证重定向 URL。

有关如何启用中介的详细信息，请参阅[在 Xamarin iOS 和 Android 应用程序中使用 Microsoft Authenticator 或 Microsoft Intune 公司门户](msal-net-use-brokers-with-xamarin-apps.md)。

## <a name="known-issues-with-ios-12-and-authentication"></a>iOS 12 和身份验证的已知问题

Microsoft 已发布[安全公告](https://github.com/aspnet/AspNetCore/issues/4647)，其中提供了有关 iOS 12 与某些身份验证类型之间的不兼容性的信息。 这种不兼容性会中断社交、WSFed 和 OIDC 登录。该安全公告有助于了解如何删除应用程序中的 ASP.NET 安全限制，以使这些应用程序与 iOS 12 兼容。

在 Xamarin iOS 上开发 MSAL.NET 应用程序的过程中，尝试从 iOS 12 登录到网站时，你可能会看到无限循环。 此类行为类似于 GitHub 上的这一 ADAL 问题：[尝试从 iOS 12 登录到网站时出现无限循环 #1329](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)。

此外，你还可能会看到 iOS 12 Safari 中发生 ASP.NET Core OIDC 身份验证中断。 有关详细信息，请参阅此 [WebKit 问题](https://bugs.webkit.org/show_bug.cgi?id=188165)。

## <a name="known-issues-with-ios-13-and-authentication"></a>iOS 13 和身份验证方面的已知问题

如果你的应用需要条件访问或证书身份验证支持，请让你的应用能够与 Microsoft Authenticator 代理应用通信。 然后，MSAL 会负责处理你的应用程序与 Microsoft Authenticator 之间的请求和响应。

在 iOS 13 上，Apple 删除了应用程序在通过自定义 URL 方案从外部应用程序接收响应时读取源应用程序的功能，进行了一项重大的 API 更改。

Apple 的 [UIApplicationOpenURLOptionsSourceApplicationKey](https://developer.apple.com/documentation/uikit/uiapplicationopenurloptionssourceapplicationkey?language=objc) 状态文档：

> 如果请求源自属于你的团队的另一个应用，UIKit 会将此键的值设置为该应用的 ID。如果源应用的团队标识符不同于当前应用的团队标识符，则该键的值为 nil。

此更改对 MSAL 有重大影响，因为它依赖于 `UIApplication.SharedApplication.OpenUrl` 来验证 MSAL 和 Microsoft Authenticator 应用之间的通信。

另外，在 iOS 13 上，开发人员在使用 `ASWebAuthenticationSession` 时需要提供呈现控制器。

如果你使用 Xcode 11 进行生成，并且使用 iOS 代理或者 `ASWebAuthenticationSession`，你的应用就会受影响。

在这些情况下，请使用 [MSAL.NET 4.4.0+](https://www.nuget.org/packages/Microsoft.Identity.Client/)，以便能够成功进行身份验证。

### <a name="additional-requirements"></a>其他需求

- 使用最新的 MSAL 库时，请确保设备上安装了 Microsoft Authenticator 6.3.19 或更高版本。
- 更新到 MSAL.NET 4.4.0 或更高版本时，请更新 info.plist 文件中的 `LSApplicationQueriesSchemes` 并添加 `msauthv3`：

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

    对于在支持 iOS 13 的设备上检测是否存在最新 Microsoft Authenticator 应用程序而言，将 `msauthv3` 添加到 info.plist 是必要的。

## <a name="report-an-issue"></a>报告问题

如果你有问题，或者需要报告在 MSAL.NET 中发现的问题，请在 GitHub 上的 [AzureAD/microsoft-authentication-library-for-dotnet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues) 存储库中创建问题。

## <a name="next-steps"></a>后续步骤

有关 Xamarin iOS 属性的信息，请参阅以下示例的 README.md 文件的[特定于 iOS 的注意事项](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations)段落：

示例 | 平台 | 说明
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、通用 Windows 平台 (UWP) | 一个简单的 Xamarin Forms 应用，演示如何通过 Azure AD 2.0 终结点使用 MSAL 对 Microsoft 个人帐户和 Azure AD 进行身份验证。 该应用还演示如何使用生成的令牌来访问 Microsoft Graph。

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
