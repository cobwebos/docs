---
title: ADAL 到 MSAL 的迁移指南（MSAL iOS/macOS） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解适用于 iOS/macOS 的 MSAL 与 ObjectiveC 的 Azure AD 身份验证库之间的差异（ADAL）。ObjC）以及如何迁移到适用于 iOS/macOS 的 MSAL。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 6050bdc8c2600998b9804b04b62102e74612719f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "77085171"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>将应用程序迁移到适用于 iOS 和 macOS 的 MSAL

创建的 Azure Active Directory 身份验证库 ([ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)) 通过 v1.0 终结点使用 Azure Active Directory 帐户。

适用于 iOS 和 macOS 的 Microsoft 身份验证库（MSAL）构建用于通过 Microsoft 标识平台（正式为 Azure AD v2.0 终结点）处理所有 Microsoft 标识，例如 Azure Active Directory （Azure AD）帐户、个人 Microsoft 帐户和 Azure AD B2C 帐户。

Microsoft 标识平台与 Azure Active Directory v1.0 之前存在一些重要差异。 本文重点描述这些差异，并提供有关将应用从 ADAL 迁移到 MSAL 的指导。

## <a name="adal-and-msal-app-capability-differences"></a>ADAL 与 MSAL 应用功能的差异

### <a name="who-can-sign-in"></a>谁可以登录

* ADAL 仅支持工作和学校帐户（也称为 Azure AD 帐户）。
* MSAL 支持个人 Microsoft 帐户（MSA 帐户），例如 Hotmail.com、Outlook.com 和 Live.com。
* MSAL 支持工作和学校帐户，并支持 Azure AD B2C 帐户。

### <a name="standards-compliance"></a>标准合规性

* Microsoft 标识平台终结点遵从 OAuth 2.0 和 OpenId Connect 标准。

### <a name="incremental-and-dynamic-consent"></a>增量许可和动态许可

* Azure Active Directory v1.0 终结点要求在应用程序注册过程中提前声明所有权限。 这意味着，这些权限是静态的。
* Microsoft 标识平台允许动态请求权限。 应用只能根据需求请求权限，并在需求提高时请求更多的权限。

有关 Azure Active Directory v1.0 与 Microsoft 标识平台之间的差异的详细信息，请参阅[为何要更新到 Microsoft 标识平台 (v2.0)？](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)。

## <a name="adal-and-msal-library-differences"></a>ADAL 与 MSAL 库的差异

MSAL 公共 API 反映 Azure AD v1.0 与 Microsoft 标识平台之间的一些重要差异。

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicClientApplication 而不是 ADAuthenticationContext

`ADAuthenticationContext` 是 ADAL 应用创建的第一个对象。 它表示 ADAL 的实例化。 应用为 Azure Active Directory 云和租户（颁发机构）的每个组合创建 `ADAuthenticationContext` 的新实例。 可以使用同一个 `ADAuthenticationContext` 获取多个公共客户端应用程序的令牌。

在 MSAL 中，主要交互是通过 `MSALPublicClientApplication` 对象进行的，该对象在 [OAuth 2.0 公共客户端](https://tools.ietf.org/html/rfc6749#section-2.1)之后建模。 可以使用一个 `MSALPublicClientApplication` 实例来与多个 AAD 云和租户交互，而无需为每个颁发机构创建新的实例。 对于大多数应用而言，一个 `MSALPublicClientApplication` 实例便已足够。

### <a name="scopes-instead-of-resources"></a>范围而不是资源

在 ADAL 中，应用必须提供资源标识符（例如 `https://graph.microsoft.com`）才能从 Azure Active Directory v1.0 终结点获取令牌。** 资源可以在应用清单中定义它可以识别的多个范围或 oAuth2Permissions。 这样，客户端应用便可以根据应用注册期间预定义的一组特定范围请求该资源的令牌。

在 MSAL 中，应用不是提供单个资源标识符，而是为每个请求提供一组范围。 范围是资源标识符后接“资源/权限”格式的权限名称。 例如，`https://graph.microsoft.com/user.read`

在 MSAL 中可通过两种方式提供范围：

* 提供应用所需的所有权限的列表。 例如： 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    在本例中，应用将请求 `directory.read` 和 `directory.write` 权限。 如果用户以前未为此应用许可这些权限，则系统会要求他们许可这些权限。 应用程序还可能会收到用户已许可的其他权限。 系统只会提示用户许可新的权限或尚未授予的权限。

* `/.default` 范围。

这是每个应用程序的内置范围。 它引用注册应用程序时配置的权限的静态列表。 其行为类似于 `resource`。 此行为在迁移时非常有用，可确保维持一组类似的范围和用户体验。

若要使用 `/.default` 范围，请将 `/.default` 追加到资源标识符。 例如：`https://graph.microsoft.com/.default`。 如果资源以斜杠 (`/`) 结尾，则仍应追加 `/.default`，包括前导正斜杠，也就是说，范围中应包含两个正斜杠 (`//`)。

可在[此处](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope)阅读有关使用“/.default”范围的详细信息

### <a name="supporting-different-webview-types--browsers"></a>支持不同的 WebView 类型和浏览器

ADAL 仅支持适用于 iOS 的 UIWebView/WKWebView，以及适用于 macOS 的 WebView。 适用于 iOS 的 MSAL 支持在请求授权代码时使用更多选项来显示 Web 内容，但不再支持 `UIWebView`；这可以改善用户体验和安全性。

默认情况下，iOS 上的 MSAL 使用 [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)，Apple 建议使用此 Web 组件在 iOS 12+ 设备上进行身份验证。 此组件通过应用与 Safari 浏览器之间的 Cookie 共享来提供单一登录 (SSO) 的优势。

可以根据应用要求和所需的最终用户体验，选择使用不同的 Web 组件。 有关更多选项，请参阅[支持的 Web 视图类型](customize-webviews.md)。

从 ADAL 迁移到 MSAL 时，`WKWebView` 将提供与 iOS 和 macOS 上的 ADAL 非常类似的用户体验。 我们建议在可能的情况下迁移到 iOS 上的 `ASWebAuthenticationSession`。 对于 macOS，我们建议使用 `WKWebView`。

### <a name="account-management-api-differences"></a>帐户管理 API 的差异

调用 ADAL 方法 `acquireToken()` 或 `acquireTokenSilent()` 时，会收到一个 `ADUserInformation` 对象，其中包含 `id_token`（表示正在进行身份验证的帐户）中的声明列表。 此外，`ADUserInformation` 基于 `upn` 声明返回 `userId`。 获取初始交互令牌后，ADAL 要求开发人员在所有静默调用中提供 `userId`。

ADAL 不提供用于检索已知用户标识的 API。 它依赖应用来保存和管理这些帐户。

MSAL 提供一组 API 用于列出 MSAL 已知的所有帐户，而无需获取令牌。

类似于 ADAL，MSAL 返回帐户信息，其中包含 `id_token` 中的声明列表。 该列表是 `MSALResult` 对象中的 `MSALAccount` 对象的一部分。

MSAL 提供一组 API 用于删除帐户，使应用无法访问已删除的帐户。 删除帐户后，以后的令牌获取调用会提示用户执行交互式令牌获取操作。 帐户删除操作仅适用于启动该操作的客户端应用程序，不会从设备上运行的其他应用或者从系统浏览器中删除该帐户。 这可以确保用户即使在从单个应用注销后，也仍可以在该设备上获得 SSO 体验。

此外，MSAL 还会返回一个帐户标识符，以后可以使用该标识符以静默方式请求令牌。 但是，帐户标识符（可通过 `MSALAccount` 对象中的 `identifier` 属性访问）无法显示，因此你不能假设它采用哪种格式，也不能尝试对它进行解释或分析。

### <a name="migrating-the-account-cache"></a>迁移帐户缓存

从 ADAL 迁移时，应用通常会存储 ADAL 的 `userId`，其中并不包含 MSAL 所需的 `identifier`。 作为一次性的迁移步骤，应用可通过以下 API 使用 ADAL 的 userId 查询 MSAL 帐户：

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

此 API 读取 MSAL 和 ADAL 的缓存，以按 ADAL userId (UPN) 查找帐户。

如果找到该帐户，开发人员应使用该帐户执行静默令牌获取操作。 第一个静默令牌获取操作将有效地升级帐户，开发人员将在 MSAL 结果中获取 MSAL 兼容的帐户标识符 (`identifier`)。 然后，只能通过以下 API 使用 `identifier` 进行帐户查找：

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

尽管可以继续将 ADAL 的 `userId` 用于 MSAL 中的所有操作，但由于 `userId` 基于 UPN，需要遵守的多种限制会导致用户体验不佳。 例如，如果 UPN 更改，则用户必须再次登录。 建议所有应用使用不可显示的帐户 `identifier` 来执行所有操作。

详细了解[缓存状态迁移](sso-between-adal-msal-apps-macos-ios.md)。

### <a name="token-acquisition-changes"></a>令牌获取更改

MSAL 引入了一些令牌获取调用更改：

* 与 ADAL 相同，`acquireTokenSilent` 始终生成静默请求。
* 与 ADAL 不同，`acquireToken` 始终通过 Web 视图或 Microsoft Authenticator 应用来生成用户可操作的 UI。 根据 Web 视图/Microsoft Authenticator 中的 SSO 状态，系统可能会提示用户输入其凭据。
* 在 ADAL 中，包含 `AD_PROMPT_AUTO` 的 `acquireToken` 首先尝试静默令牌获取操作，仅当静默请求失败时才显示 UI。 在 MSAL 中，可以通过先调用 `acquireTokenSilent`，并仅在静默获取失败时才调用 `acquireToken`，来实现此逻辑。 这样，开发人员就可以启动交互式令牌获取之前自定义用户体验。

### <a name="error-handling-differences"></a>错误处理的差异

MSAL 更明确地区分应用可以处理的错误，以及需要用户干预的错误。 开发人员必须处理的错误数有限：

* `MSALErrorInteractionRequired`：用户必须执行交互式请求。 这可能是由于各种原因导致的，例如身份验证会话过期、条件性访问策略已更改、刷新令牌已过期或已被吊销、缓存中没有有效的令牌，等等。
* `MSALErrorServerDeclinedScopes`：请求未完全完成并且某些范围未被授予访问权限。 此错误的可能原因是用户拒绝许可一个或多个范围的权限。

处理[ `MSALError` 列表](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)中的所有其他错误是可选的。 可以使用这些错误中的信息来改善用户体验。

有关 MSAL 错误处理的详细信息，请参阅[使用 MSAL 处理异常和错误](msal-handling-exceptions.md)。

### <a name="broker-support"></a>中介支持

从版本 0.3.0 开始，MSAL 使用 Microsoft Authenticator 应用为中介身份验证提供支持。 Microsoft Authenticator 还支持条件访问方案。 条件访问方案的示例包括要求用户通过 Intune 注册设备或使用 AAD 注册以获取令牌的设备符合性策略。 和移动应用管理（MAM）条件访问策略，在应用可以获取令牌之前需要符合性证明。

若要为应用程序启用中介：

1. 为应用程序注册中介兼容的重定向 URI 格式。 中介兼容的重定向 URI 格式为 `msauth.<app.bundle.id>://auth`。 将 `<app.bundle.id>` 替换为应用程序的捆绑 ID。 如果要从 ADAL 迁移，而应用程序已支持中介，则无需执行任何额外的操作。 以前的重定向 URI 与 MSAL 完全兼容，因此你可以跳到步骤 3。

2. 将应用程序的重定向 URI 方案添加到 info.plist 文件。 对于默认的 MSAL 重定向 URI，格式为 `msauth.<app.bundle.id>`。 例如：

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. 将以下方案添加到应用的 Info.plist 中的 LSApplicationQueriesSchemes 下：

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. 将以下内容添加到 AppDelegate 文件以处理回调：目标-C：
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift：
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>企业到企业 (B2B)

在 ADAL 中， `ADAuthenticationContext` 为应用请求令牌的每个租户创建单独的实例。 这在 MSAL 中不再是必需的。 在 MSAL 中，可以创建的单个实例 `MSALPublicClientApplication` ，并通过为 acquireToken 和 acquireTokenSilent 调用指定不同的颁发机构来将其用于任何 AAD 云和组织。

## <a name="sso-in-partnership-with-other-sdks"></a>在与其他 SDK 的合作方案中实现 SSO

适用于 iOS 的 MSAL 可以通过包含以下 SDK 的统一缓存来实现 SSO：

- ADAL Objective-C 2.7.x+
- MSAL.NET for Xamarin 2.4.x+
- ADAL.NET for Xamarin 4.4.x+

SSO 是通过 iOS 密钥链共享实现的，只能在通过同一 Apple 开发人员帐户发布的应用之间使用。

通过 iOS 密钥链共享实现的 SSO 是唯一的静默 SSO 类型。

在 macOS 上，MSAL 可以在其他基于“适用于 iOS 和 macOS 的 MSAL”的应用程序以及基于 ADAL Objective-C 的应用程序中实现 SSO。

iOS 上的 MSAL 还支持其他两种类型的 SSO：

* 通过 Web 浏览器实现的 SSO。 适用于 iOS 的 MSAL 支持 `ASWebAuthenticationSession`，它通过设备上的其他应用与具体 Safari 浏览器之间共享的 Cookie 提供 SSO。
* 通过身份验证中介实现的 SSO。 在 iOS 设备上，Microsoft Authenticator 充当身份验证中介。 它可以遵循条件性访问策略，例如需要相容设备，并为已注册的设备提供 SSO。 MSAL SDK 从版本 0.3.0 开始默认支持中介。

## <a name="intune-mam-sdk"></a>Intune MAM SDK

[Intune MAM SDK](https://docs.microsoft.com/intune/app-sdk-get-started) 从版本 [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2) 开始支持适用于 iOS 的 MSAL

## <a name="msal-and-adal-in-the-same-app"></a>同一应用中的 MSAL 和 ADAL

在同一个应用程序中，ADAL 2.7.0 和更高版本不能与 MSAL 共存。 主要原因在于共享的子模块通用代码。 由于 Objective-C 不支持命名空间，因此，如果同时将 ADAL 和 MSAL 框架添加到应用程序，则同一个类会有两个实例。 无法保证在运行时选取哪个实例。 如果两个 SDK 使用有冲突的类的同一版本，应用仍可能正常运行。 但是，如果使用不同的版本，应用可能会遇到难以诊断的意外崩溃。

不支持在同一生产应用程序中同时运行 ADAL 和 MSAL。 但是，如果只是进行测试，或者将用户从 ADAL Objective-C 迁移到适用于 iOS 和 macOS 的 MSAL，可以继续使用 [ADAL Objective-C 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10)。 只有版本 2.6.10 能够与同一应用程序中的 MSAL 一起运行。 此 ADAL 版本今后不会有新的功能更新，因此，只能将它用于迁移和测试目的。 应用不应长期依赖于 ADAL 和 MSAL 的共存。

不支持 ADAL 和 MSAL 在同一应用程序中共存。
完全支持 ADAL 和 MSAL 在多个应用程序之间共存。

## <a name="practical-migration-steps"></a>可行的迁移步骤

### <a name="app-registration-migration"></a>应用注册迁移

无需更改现有的 AAD 应用程序即可切换到 MSAL 并启用 AAD 帐户。 但是，如果基于 ADAL 的应用程序不支持中介身份验证，则需要先为应用程序注册新的重定向 URI，然后才能切换到 MSAL。

重定向 URI 应采用以下格式：`msauth.<app.bundle.id>://auth`。 将 `<app.bundle.id>` 替换为应用程序的捆绑 ID。 在 [Azure 门户](https://aka.ms/MobileAppReg)中指定重定向 URI。

（仅适用于 iOS）若要支持基于证书的身份验证，需要在应用程序和 Azure 门户中，使用以下格式额外注册一个重定向 URI：`msauth://code/<broker-redirect-uri-in-url-encoded-form>`。 例如，`msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

建议所有应用注册这两个重定向 URI。

若要添加对增量许可的支持，请在“API 权限”选项卡下的应用注册中，选择应用配置为请求访问的 API 和权限。****

如果从 ADAL 迁移并希望同时支持 AAD 和 MSA 帐户，则需要更新现有的应用程序注册才能支持这两种帐户。 我们目前不建议更新现有的生产应用来支持 AAD 和 MSA。 应该创建支持 AAD 和 MSA 的另一个客户端 ID 用于测试，在确认所有方案正常后，再更新现有应用。

### <a name="add-msal-to-your-app"></a>将 MSAL 添加到应用

可以使用首选的包管理工具将 MSAL SDK 添加到应用。 请参阅[此处的详细说明](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation)。

### <a name="update-your-apps-infoplist-file"></a>更新应用的 Info.plist 文件

（仅适用于 iOS）将应用程序的重定向 URI 方案添加到 info.plist 文件。 对于 ADAL 中介兼容的应用，该方案应已存在。 默认的 MSAL 重定向 URI 方案采用以下格式：`msauth.<app.bundle.id>`。  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

将以下方案添加到应用的 Info.plist 中的 `LSApplicationQueriesSchemes` 下。

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>更新 AppDelegate 代码

（仅适用于 iOS）将以下内容添加到 AppDelegate.m 文件：

Objective-C：

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift：

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

**如果使用的是 Xcode 11**，应改为将 MSAL 回调放入 `SceneDelegate` 文件。
如果支持兼容旧版 iOS 的 UISceneDelegate 和 UIApplicationDelegate，则需将 MSAL 回叫置于这两个文件中。

Objective-C：

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift：

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

这样，MSAL 就可以处理来自中介和 Web 组件的响应。
在 ADAL 中不一定要这样做，因为它会自动“重排”应用委托方法。 手动添加此回调更不容易出错，并可为应用程序提供更高的控制度。

### <a name="enable-token-caching"></a>启用令牌缓存

默认情况下，MSAL 会在 iOS 或 macOS 密钥链中缓存应用的令牌。 

若要启用令牌缓存，请执行以下操作：
1. 确保应用程序已正确签名
2. 转到 Xcode 项目设置 >“功能”选项卡 > “启用密钥链共享”
3. 单击 **+** 并输入以下“密钥链组”条目：3.a 对于 iOS，输入 `com.microsoft.adalcache` 3.b 对于 macOS，输入 `com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>创建 MSALPublicClientApplication 并切换到其 acquireToken 和 acquireTokeSilent 调用

可使用以下代码创建 `MSALPublicClientApplication`：

Objective-C：

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Swift：

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

然后调用帐户管理 API 来确定缓存中是否存在任何帐户：

Objective-C：

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Swift：

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



或读取所有帐户：

Objective-C：

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Swift：

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



如果找到了帐户，请调用 MSAL `acquireTokenSilent` API：

Objective-C：

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Swift：

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>后续步骤

详细了解[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)
