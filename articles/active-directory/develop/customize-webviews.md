---
title: 自定义浏览器和 Webview |Microsoft 标识平台
description: 了解如何自定义 MSAL for iOS 和 macOS 用来登录用户的浏览器体验
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1b7417de8de6fb063de18fe670ef474a3b486d0
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269072"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>如何：自定义 iOS/macOS 的浏览器和 Webview

Web 浏览器是交互式身份验证所必需的。 在 iOS 上，默认情况下，Microsoft 身份验证库（MSAL）使用系统 web 浏览器（可能会显示在应用的顶部）进行交互式身份验证，以便登录用户。 使用系统浏览器具有很大的优势，即与其他应用程序和 web 应用程序共享单一登录（SSO）状态。

你可以通过将配置自定义为其他用于显示 web 内容的选项来更改体验，例如：

仅适用于 iOS：

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

对于 iOS 和 macOS：

- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc)。

MacOS 的 MSAL 仅支持`WKWebView`。

## <a name="system-browsers"></a>系统浏览器

对于 iOS， `ASWebAuthenticationSession` `SFAuthenticationSession`、和`SFSafariViewController`被视为系统浏览器。 通常，系统浏览器在 Safari 浏览器应用程序中共享 cookie 和其他网站数据。

默认情况下，MSAL 将动态检测 iOS 版本，并选择该版本上提供的建议系统浏览器。 在 iOS 12 + 上，它`ASWebAuthenticationSession`将是。 

| Version | Web 浏览器 |
|:-------------:|:-------------:|
| iOS 12 + | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

开发人员还可以为 MSAL 应用选择其他系统浏览器：

- `SFAuthenticationSession`是的`ASWebAuthenticationSession`iOS 11 版本。
- `SFSafariViewController`更通用，并提供用于浏览 web 的界面，还可用于登录。 在 iOS 9 和10中，cookie 和其他网站数据与 Safari 共享--而不是在 iOS 11 和更高版本中。

## <a name="in-app-browser"></a>应用内浏览器

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview)是一个应用程序内浏览器，用于显示 web 内容。 它不与其他**WKWebView**的实例或 Safari 浏览器共享 cookie 或网站数据。 WKWebView 是适用于 iOS 和 macOS 的跨平台浏览器。

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Cookie 共享和单一登录（SSO）的影响

你使用的浏览器会影响 SSO 体验，因为其共享 cookie 的方式。 下表总结了每个浏览器的 SSO 体验。

| 技术    | 浏览器类型  | iOS 可用性 | macOS 可用性 | 共享 cookie 和其他数据  | MSAL 可用性 | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | 系统 | iOS12 及更高 | macOS 10.15 及更高 | 是 | 仅限 iOS | w/Safari 实例
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | 系统 | iOS11 及更高 | 不可用 | 是 | 仅限 iOS |  w/Safari 实例
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | 系统 | iOS11 及更高 | 不可用 | 否 | 仅限 iOS | 否 * *
| **SFSafariViewController** | 系统 | iOS10 | 不可用 | 是 | 仅限 iOS |  w/Safari 实例
| **WKWebView**  | 应用内 | iOS8 及更高 | macOS 10.10 及更高 | 否 | iOS 和 macOS | 否 * *

\* * 要使 SSO 正常工作，需要在应用之间共享标记。 这需要令牌缓存或代理应用程序，如适用于 iOS 的 Microsoft Authenticator。

## <a name="change-the-default-browser-for-the-request"></a>更改请求的默认浏览器

你可以根据用户的 UX 要求使用应用内浏览器或特定系统浏览器，方法是在中`MSALWebviewParameters`更改以下属性：

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>按交互式请求更改

可以通过在将`MSALInteractiveTokenParameters.webviewParameters.webviewType`属性传递`acquireTokenWithParameters:completionBlock:`给 API 之前更改属性，将每个请求配置为覆盖默认浏览器。

此外，MSAL 还支持通过`WKWebView` `MSALInteractiveTokenParameters.webviewParameters.customWebView`设置属性来传入自定义。

例如：

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithParentViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(parentViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

如果使用自定义 web 视图，将使用通知来指示要显示的 web 内容的状态，例如：

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

所有 MSAL 支持的 web 浏览器类型都在[MSALWebviewType 枚举](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)中声明

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
#if TARGET_OS_IPHONE
    // For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession
    // or SFAuthenticationSession).
    // For older versions, with AuthenticationSession not being available, uses
    // SafariViewController.
    MSALWebviewTypeDefault,
    
    // Use SFAuthenticationSession/ASWebAuthenticationSession
    MSALWebviewTypeAuthenticationSession,
    
    // Use SFSafariViewController for all versions.
    MSALWebviewTypeSafariViewController,
    
#endif
    // Use WKWebView
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>后续步骤

详细了解[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)
