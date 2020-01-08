---
title: ADAL 到 MSAL 的迁移指南（MSAL iOS/macOS） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解适用于 iOS/macOS 的 MSAL 与 ObjectiveC 的 Azure AD 身份验证库之间的差异（ADAL）。ObjC）以及如何迁移到适用于 iOS/macOS 的 MSAL。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: oldalton
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f35243e29755c42dbe8e3a696f2718ee3d10178c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424428"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>将应用程序迁移到适用于 iOS 和 macOS 的 MSAL

创建 Azure Active Directory 身份验证库（[ADAL 目标-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)）是为了通过 v2.0 终结点与 Azure Active Directory 帐户一起工作。

适用于 iOS 和 macOS 的 Microsoft 身份验证库（MSAL）构建用于通过 Microsoft 标识平台处理所有 Microsoft 标识，例如 Azure Active Directory （Azure AD）帐户、个人 Microsoft 帐户和 Azure AD B2C 帐户（正式Azure AD v2.0 终结点）。

Microsoft 标识平台与 Azure Active Directory 1.0 版有一些重要差异。 本文重点介绍这些差异，并提供有关将应用从 ADAL 迁移到 MSAL 的指导。

## <a name="adal-and-msal-app-capability-differences"></a>ADAL 和 MSAL 应用功能差异

### <a name="who-can-sign-in"></a>谁可以登录

* ADAL 仅支持工作和学校帐户，也称为 Azure AD 帐户。
* MSAL 支持个人 Microsoft 帐户（MSA 帐户），例如 Hotmail.com、Outlook.com 和 Live.com。
* MSAL 支持工作和学校帐户，并 Azure AD B2C 帐户。

### <a name="standards-compliance"></a>标准符合性

* Microsoft 标识平台终结点遵循 OAuth 2.0 和 OpenId Connect 标准。

### <a name="incremental-and-dynamic-consent"></a>增量同意和动态同意

* Azure Active Directory 1.0 版终结点要求在应用程序注册过程中提前声明所有权限。 这意味着这些权限是静态的。
* Microsoft 标识平台允许您动态请求权限。 应用程序可以根据需要请求权限，并在应用程序需要时请求更多权限。

有关 Azure Active Directory 1.0 和 Microsoft 标识平台之间的差异的详细信息，请参阅[为什么要更新到 microsoft 标识平台（v2.0）？](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)。

## <a name="adal-and-msal-library-differences"></a>ADAL 和 MSAL 库差异

MSAL 公共 API 反映 Azure AD v1.0 和 Microsoft 标识平台之间的一些主要差异。

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicClientApplication 而不是 ADAuthenticationContext

`ADAuthenticationContext` 是 ADAL 应用创建的第一个对象。 它表示 ADAL 的实例化。 应用为每个 Azure Active Directory 云和租户（颁发机构）组合创建 `ADAuthenticationContext` 的新实例。 相同的 `ADAuthenticationContext` 可用于获取多个公共客户端应用程序的令牌。

在 MSAL 中，主要交互是通过在[OAuth 2.0 公共客户端](https://tools.ietf.org/html/rfc6749#section-2.1)之后建模的 `MSALPublicClientApplication` 对象进行的。 一个 `MSALPublicClientApplication` 实例可用于与多个 AAD 云和租户进行交互，而无需为每个颁发机构创建新的实例。 对于大多数应用程序，一个 `MSALPublicClientApplication` 实例就够了。

### <a name="scopes-instead-of-resources"></a>范围而非资源

在 ADAL 中，应用程序必须提供*资源*标识符（如 `https://graph.microsoft.com`），以便从 Azure Active Directory v2.0 终结点获取令牌。 资源可以在应用程序清单中定义多个可理解的作用域或 oAuth2Permissions。 这允许客户端应用在应用注册期间预定义的一组特定范围内请求该资源的令牌。

在 MSAL （而不是单个资源标识符）中，应用程序为每个请求提供了一组范围。 作用域是资源标识符，后跟以资源/权限形式的权限名称。 例如： `https://graph.microsoft.com/user.read`

在 MSAL 中提供范围的方法有两种：

* 提供您的应用程序所需的所有权限的列表。 例如： 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    在这种情况下，应用请求 `directory.read` 和 `directory.write` 权限。 如果用户尚未同意此应用程序，则会要求用户同意这些权限。 应用程序还可能会收到用户已同意应用程序的其他权限。 系统仅会提示用户同意新权限或尚未授予的权限。

* `/.default` 范围。

这是每个应用程序的内置作用域。 它指的是在注册应用程序时配置的权限的静态列表。 其行为与 `resource`的行为类似。 这在迁移时非常有用，可确保维护一组类似的作用域和用户体验。

若要使用 `/.default` 范围，请将 `/.default` 追加到资源标识符。 例如：`https://graph.microsoft.com/.default`。 如果资源以斜杠（`/`）结尾，则仍应追加 `/.default`，包括前导正斜杠，导致其中包含双正斜杠（`//`）。

可在[此处](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope)阅读有关使用 "/.default" 范围的详细信息

### <a name="supporting-different-webview-types--browsers"></a>& 浏览器支持不同的 Web 视图类型

ADAL 仅支持适用于 iOS 的 UIWebView/WKWebView 和 macOS 的 Web 视图。 MSAL for iOS 支持在请求授权代码时显示 web 内容的更多选项，不再支持 `UIWebView`;这可以改善用户体验和安全性。

默认情况下，iOS 上的 MSAL 使用[ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)，这是 Web 组件 Apple 建议在 iOS 12 + 设备上进行身份验证。 它通过应用程序和 Safari 浏览器之间的 cookie 共享来提供单一登录（SSO）权益。

您可以选择使用不同的 web 组件，具体取决于应用程序要求和所需的最终用户体验。 有关更多选项，请参阅[支持的 web 视图类型](customize-webviews.md)。

从 ADAL 迁移到 MSAL 时，`WKWebView` 提供与 iOS 和 macOS 上的 ADAL 最相似的用户体验。 建议迁移到 iOS `ASWebAuthenticationSession` （如有可能）。 对于 macOS，建议使用 `WKWebView`。

### <a name="account-management-api-differences"></a>帐户管理 API 差异

调用 ADAL 方法 `acquireToken()` 或 `acquireTokenSilent()`时，会收到一个 `ADUserInformation` 对象，其中包含表示正在进行身份验证的帐户的 `id_token` 中的声明列表。 此外，`ADUserInformation` 根据 `upn` 声明返回 `userId`。 初始交互式令牌采集完成后，ADAL 要求开发人员提供所有静态调用中的 `userId`。

ADAL 不提供用于检索已知用户标识的 API。 它依赖于应用程序来保存和管理这些帐户。

MSAL 提供了一组 Api 来列出所有已知的 MSAL 帐户，而无需获取令牌。

与 ADAL 类似，MSAL 返回的帐户信息包含来自 `id_token`的声明列表。 它属于 `MSALResult` 对象中的 `MSALAccount` 对象。

MSAL 提供了一组 Api 来删除帐户，从而使该应用无法访问已删除的帐户。 删除帐户后，以后的令牌获取调用会提示用户执行交互式令牌采集。 帐户删除仅适用于启动它的客户端应用程序，并且不会从设备或系统浏览器上运行的其他应用中删除该帐户。 这可以确保即使在注销单个应用后，用户仍在设备上仍有 SSO 体验。

此外，MSAL 还会返回一个帐户标识符，该标识符可用于稍后在以后自动请求令牌。 但是，帐户标识符（可通过 `MSALAccount` 对象中的 `identifier` 属性访问）无法显示，因此你不能采用它所处的格式，也不应尝试对其进行解释或分析。

### <a name="migrating-the-account-cache"></a>迁移帐户缓存

从 ADAL 迁移时，应用通常会存储 ADAL 的 `userId`，这并不具有 MSAL 所需的 `identifier`。 作为一次性迁移步骤，应用可通过以下 API 使用 ADAL 的 userId 查询 MSAL 帐户：

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

此 API 读取 MSAL 和 ADAL 的缓存，以通过 ADAL userId （UPN）查找帐户。

如果找到该帐户，开发人员应使用该帐户执行无提示令牌获取。 第一个无提示令牌获取将有效地升级帐户，开发人员将在 MSAL 结果中获取 MSAL 兼容帐户标识符（`identifier`）。 之后，只应使用以下 API 将 `identifier` 用于帐户查找：

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

尽管可以继续对 MSAL 中的所有操作使用 ADAL 的 `userId`，因为 `userId` 基于 UPN，所以有多种限制会导致用户体验不佳。 例如，如果 UPN 更改，用户必须再次登录。 建议所有应用使用不可显示的帐户 `identifier` 所有操作。

详细了解[缓存状态迁移](sso-between-adal-msal-apps-macos-ios.md)。

### <a name="token-acquisition-changes"></a>令牌获取更改

MSAL 引入了一些令牌获取调用更改：

* 与 ADAL 一样，`acquireTokenSilent` 始终导致发出无提示请求。
* 与 ADAL 不同，`acquireToken` 始终通过 web 视图或 Microsoft Authenticator 应用来导致用户可操作的 UI。 系统可能会提示用户输入其凭据，具体取决于 web 视图/Microsoft Authenticator 中的 SSO 状态。
* 在 ADAL 中，与 `AD_PROMPT_AUTO` `acquireToken` 首先尝试以无提示方式获取标记，并且仅当缄默请求失败时才显示 UI。 在 MSAL 中，可通过首先调用 `acquireTokenSilent` 并仅在无提示获取失败时调用 `acquireToken` 来实现此逻辑。 这允许开发人员在开始交互式令牌采集之前自定义用户体验。

### <a name="error-handling-differences"></a>错误处理差异

MSAL 在你的应用程序可以处理的错误与需要用户干预的错误之间提供了更高的清晰度。 开发人员必须处理的错误数量有限：

* `MSALErrorInteractionRequired`：用户必须执行交互式请求。 这可能是由于各种原因导致的，例如身份验证会话过期、条件性访问策略已更改、刷新令牌已过期或已被吊销、缓存中没有有效的令牌，等等。
* `MSALErrorServerDeclinedScopes`：请求未完全完成并且某些范围未被授予访问权限。 这可能是因为用户拒绝了一个或多个作用域的同意。

处理[`MSALError` 列表](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)中的所有其他错误是可选的。 您可以使用这些错误中的信息来改善用户体验。

有关 MSAL 错误处理的详细信息，请参阅[使用 MSAL 处理异常和错误](msal-handling-exceptions.md)。

### <a name="broker-support"></a>Broker 支持

从版本0.3.0 开始，MSAL 使用 Microsoft Authenticator 应用为中转身份验证提供支持。 Microsoft Authenticator 还支持条件访问方案。 条件访问方案的示例包括要求用户通过 Intune 注册设备或使用 AAD 注册以获取令牌的设备符合性策略。 和移动应用管理（MAM）条件访问策略，在应用可以获取令牌之前需要符合性证明。

为应用程序启用代理：

1. 为应用程序注册 broker 兼容的重定向 URI 格式。 与 broker 兼容的重定向 URI 格式为 `msauth.<app.bundle.id>://auth`。 将 `<app.bundle.id>` 替换为应用程序的捆绑 ID。 如果要从 ADAL 迁移，且应用程序已支持 broker，则无需执行任何额外操作。 你以前的重定向 URI 与 MSAL 完全兼容，因此你可以跳到步骤3。

2. 将应用程序的重定向 URI 方案添加到 info.plist 文件。 对于默认的 MSAL 重定向 URI，格式为 `msauth.<app.bundle.id>`。 例如：

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. 将以下方案添加到应用的 info.plist 下的 LSApplicationQueriesSchemes：

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

### <a name="business-to-business-b2b"></a>企业到企业（B2B）

在 ADAL 中，为应用请求令牌的每个租户创建单独 `ADAuthenticationContext` 的实例。 这在 MSAL 中不再是必需的。 在 MSAL 中，可以创建 `MSALPublicClientApplication` 的单个实例，并通过为 acquireToken 和 acquireTokenSilent 调用指定不同的颁发机构来将其用于任何 AAD 云和组织。

## <a name="sso-in-partnership-with-other-sdks"></a>与其他 Sdk 合作的 SSO

适用于 iOS 的 MSAL 可以通过包含以下 Sdk 的统一缓存来实现 SSO：

- ADAL 目标-C 2.7. x +
- MSAL.NET for Xamarin 2.4. x +
- ADAL.NET for Xamarin 4.4 +

SSO 是通过 iOS 密钥链共享实现的，并且仅在从同一 Apple 开发人员帐户发布的应用之间可用。

SSO 到 iOS 密钥链共享是唯一的无提示 SSO 类型。

在 macOS 上，MSAL 可以通过适用于 iOS、基于 macOS 的应用程序和基于 ADAL 目标-C 的应用程序的其他 MSAL 实现 SSO。

IOS 上的 MSAL 还支持其他两种类型的 SSO：

* SSO 到 web 浏览器。 适用于 iOS 的 MSAL 支持 `ASWebAuthenticationSession`，该应用程序通过在设备上的其他应用与特定 Safari 浏览器之间共享的 cookie 提供 SSO。
* 通过身份验证代理的 SSO。 在 iOS 设备上，Microsoft Authenticator 充当身份验证代理。 它可以遵循条件性访问策略，例如需要相容设备，并为已注册的设备提供 SSO。 默认情况下，从版本0.3.0 开始的 MSAL Sdk 支持 broker。

## <a name="intune-mam-sdk"></a>Intune MAM SDK

[INTUNE MAM SDK](https://docs.microsoft.com/intune/app-sdk-get-started)支持 MSAL for iOS，版本[11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2)

## <a name="msal-and-adal-in-the-same-app"></a>同一应用中的 MSAL 和 ADAL

ADAL 版本2.7.0 及更高版本无法与同一个应用程序中的 MSAL 共存。 主要原因是由于共享子模块通用代码。 由于目标 C 不支持命名空间，因此，如果将 ADAL 和 MSAL 框架添加到应用程序中，则会有相同类的两个实例。 不保证在运行时选取哪一项。 如果两个 Sdk 使用相同版本的冲突类，则应用可能仍可正常工作。 但是，如果它是不同的版本，你的应用程序可能会遇到难以诊断的意外故障。

不支持在同一个生产应用程序中运行 ADAL 和 MSAL。 但是，如果只是测试用户并将其从 ADAL 目标-C 迁移到 MSAL for iOS 和 macOS，则可以继续使用[Adal 目标-c 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10)。 它是在同一个应用程序中使用 MSAL 的唯一版本。 此 ADAL 版本不会有新的功能更新，因此应该仅用于迁移和测试目的。 应用不应依赖于 ADAL 和 MSAL 共存长期。

同一应用程序中不支持 ADAL 和 MSAL 共存。
完全支持多个应用程序之间的 ADAL 和 MSAL 共存。

## <a name="practical-migration-steps"></a>实用的迁移步骤

### <a name="app-registration-migration"></a>应用注册迁移

无需更改现有的 AAD 应用程序即可切换到 MSAL 并启用 AAD 帐户。 但是，如果基于 ADAL 的应用程序不支持中转身份验证，则需要为应用程序注册新的重定向 URI，然后才能切换到 MSAL。

重定向 URI 的格式应为： `msauth.<app.bundle.id>://auth`。 将 `<app.bundle.id>` 替换为应用程序的捆绑 ID。 指定[Azure 门户](https://aka.ms/MobileAppReg)中的重定向 URI。

仅适用于 iOS，若要支持基于证书的身份验证，需要在应用程序中注册额外的重定向 URI，并且 Azure 门户采用以下格式： `msauth://code/<broker-redirect-uri-in-url-encoded-form>`。 例如： `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

建议所有应用都注册两个重定向 Uri。

如果要添加对增量许可的支持，请在 " **api 权限**" 选项卡下，选择你的应用配置为请求访问的 api 和权限。

如果要从 ADAL 迁移并希望同时支持 AAD 和 MSA 帐户，则需要更新现有应用程序注册以支持这两者。 我们不建议更新现有的生产应用程序以立即支持 AAD 和 MSA。 相反，请创建另一个客户端 ID，该 ID 同时支持 AAD 和 MSA 用于测试，并在验证所有方案都正常工作后，更新现有应用。

### <a name="add-msal-to-your-app"></a>将 MSAL 添加到应用

可以使用首选的包管理工具将 MSAL SDK 添加到应用。 请参阅[此处的详细说明](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation)。

### <a name="update-your-apps-infoplist-file"></a>更新应用的 info.plist 文件

仅适用于 iOS，将应用程序的重定向 URI 方案添加到 info.plist 文件。 对于 ADAL broker 兼容的应用，它应该已经存在。 默认的 MSAL 重定向 URI 方案将采用以下格式： `msauth.<app.bundle.id>`。  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

将以下方案添加到 `LSApplicationQueriesSchemes`下的应用 info.plist。

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>更新 AppDelegate 代码

仅适用于 iOS，将以下内容添加到 AppDelegate 文件：

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

**如果使用的是 Xcode 11**，应改为将 MSAL 回拨放入 `SceneDelegate` 文件中。
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

这允许 MSAL 处理来自 broker 和 web 组件的响应。
这在 ADAL 中不是必需的，因为它会自动 "swizzled" 应用委托方法。 手动添加此方法更少出错，并为应用程序提供更多控制。

### <a name="enable-token-caching"></a>启用令牌缓存

默认情况下，MSAL 会在 iOS 或 macOS 密钥链中缓存应用的令牌。 

若要启用令牌缓存，请执行以下操作：
1. 确保应用程序已正确签名
2. 转到 Xcode 项目设置 >“功能”选项卡 > “启用密钥链共享”
3. 单击 " **+** "，然后输入以下**密钥链 Groups**条目： 3. 对于 iOS，请输入 `com.microsoft.adalcache` macOS 输入 `com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>创建 MSALPublicClientApplication 并切换到其 acquireToken 和 acquireTokeSilent 调用

你可以使用以下代码创建 `MSALPublicClientApplication`：

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

然后调用帐户管理 API 来查看缓存中是否存在任何帐户：

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



如果找到帐户，请调用 MSAL `acquireTokenSilent` API：

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
