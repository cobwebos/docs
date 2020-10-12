---
title: 自定义浏览器和 Web 视图 (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: 了解如何自定义用于登录用户的 MSAL iOS/macOS 浏览器体验。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: a8486ec87b5198231a33b1dab382ba457c8c8066
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85478121"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>如何：自定义 iOS/macOS 的浏览器和 WebView

完成交互式身份验证需要使用 Web 浏览器。 在 iOS 和 macOS 10.15+ 上，Microsoft 身份验证库 (MSAL) 默认使用系统 Web 浏览器（可能显示在应用的顶部）执行交互式身份验证，以将用户登录。 使用系统浏览器的好处是可与其他应用程序和 Web 应用程序共享单一登录 (SSO) 状态。

可以通过将配置自定义为其他用于显示 Web 内容的选项来更改体验，例如：

仅对 iOS 而言：

- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

对于 iOS 和 macOS：

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc)。

MSAL for macOS 仅在较早的 OS 版本上支持 `WKWebView`。 仅在 macOS 10.15 及更高版本上支持 `ASWebAuthenticationSession`。 

## <a name="system-browsers"></a>系统浏览器

对于 iOS，`ASWebAuthenticationSession`、`SFAuthenticationSession` 和 `SFSafariViewController` 被视为系统浏览器。 对于 macOS，只有 `ASWebAuthenticationSession` 可用。 一般情况下，系统浏览器将与 Safari 浏览器应用程序共享 Cookie 和其他网站数据。

默认情况下，MSAL 会动态检测 iOS 版本，并选择适用于该版本的建议系统浏览器。 在 iOS 12+ 上，系统浏览器为 `ASWebAuthenticationSession`。 

### <a name="default-configuration-for-ios"></a>iOS 的默认配置

| 版本 | Web 浏览器 |
|:-------------:|:-------------:|
| iOS 12+ | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

### <a name="default-configuration-for-macos"></a>macOS 的默认配置

| 版本 | Web 浏览器 |
|:-------------:|:-------------:|
| macOS 10.15+ | ASWebAuthenticationSession |
| 其他版本 | WKWebView |

开发人员还可为 MSAL 应用选择不同的系统浏览器：

- `SFAuthenticationSession` 是 `ASWebAuthenticationSession` 在 iOS 11 中的版本。
- `SFSafariViewController` 更通用，提供用于浏览 Web 的接口，并且还可用于登录。 在 iOS 9 和10中，Cookie 和其他网站数据将与 Safari 共享 -- 但 iOS 11 和更高版本中不提供这种共享。

## <a name="in-app-browser"></a>应用中浏览器

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) 是显示 Web 内容的应用中浏览器。 它不与其他 **WKWebView** 实例或 Safari 浏览器共享 Cookie 或网站数据。 WKWebView 是同时适用于 iOS 和 macOS 的跨平台浏览器。

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Cookie 共享和单一登录 (SSO) 的含义

所用浏览器共享 Cookie 的方式会影响 SSO 体验。 下表汇总了每个浏览器的 SSO 体验。

| 技术    | 浏览器类型  | iOS 可用性 | macOS 可用性 | 共享 Cookie 和其他数据  | MSAL 可用性 | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | 系统 | iOS12 和更高版本 | macOS 10.15 和更高版本 | 是 | iOS 和 macOS 10.15+ | 具有 Safari 实例
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | 系统 | iOS11 和更高版本 | 不适用 | 是 | 仅限 iOS |  具有 Safari 实例
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | 系统 | iOS11 和更高版本 | 空值 | 否 | 仅限 iOS | 否**
| **SFSafariViewController** | 系统 | iOS10 | 空值 | 是 | 仅限 iOS |  具有 Safari 实例
| **WKWebView**  | 应用中 | iOS8 和更高版本 | macOS 10.10 和更高版本 | 否 | iOS 和 macOS | 否**

** 要正常进行 SSO，需要在应用之间共享令牌。 这需要使用令牌缓存或中介应用程序，例如适用于 iOS 的 Microsoft Authenticator。

## <a name="change-the-default-browser-for-the-request"></a>更改请求的默认浏览器

可以根据 UX 要求使用应用中浏览器或特定的系统浏览器，只需在 `MSALWebviewParameters` 中更改以下属性即可：

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>按交互式请求进行更改

可以通过更改 `MSALInteractiveTokenParameters.webviewParameters.webviewType` 属性，然后将其传递给 `acquireTokenWithParameters:completionBlock:` API，将每个请求配置为替代默认浏览器。

此外，MSAL 还支持通过设置 `MSALInteractiveTokenParameters.webviewParameters.customWebView` 属性来传入自定义 `WKWebView`。

例如：

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(authPresentationViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

如果使用自定义 webview，则会使用通知来指示显示的 Web 内容的状态，例如：

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### <a name="options"></a>选项

MSAL 支持的所有 Web 浏览器类型都在 [MSALWebviewType 枚举](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)中声明

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
    /**
     For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession or SFAuthenticationSession).
     For older versions, with AuthenticationSession not being available, uses SafariViewController.
     For macOS 10.15 and above uses ASWebAuthenticationSession
     For older macOS versions uses WKWebView
     */
    MSALWebviewTypeDefault,
    
    /** Use ASWebAuthenticationSession where available.
     On older iOS versions uses SFAuthenticationSession
     Doesn't allow any other webview type, so if either of these are not present, fails the request*/
    MSALWebviewTypeAuthenticationSession,
    
#if TARGET_OS_IPHONE
    
    /** Use SFSafariViewController for all versions. */
    MSALWebviewTypeSafariViewController,
    
#endif
    /** Use WKWebView */
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>后续步骤

详细了解[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)
