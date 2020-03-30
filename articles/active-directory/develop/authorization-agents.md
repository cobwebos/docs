---
title: 授权代理及其启用方式 | Azure
description: 了解 Microsoft 身份验证库 (MSAL) 允许 Android 应用使用的不同授权代理，以及如何启用这些代理。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2019
ms.author: marsma
ms.reviewer: shoatman, brianmel, hahamil
ms.custom: aaddev
ms.openlocfilehash: 4f1b3fc5b60069cfa47d437e4341ded141204418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085330"
---
# <a name="authorization-agents-android"></a>授权代理 (Android)

本文介绍 Microsoft 身份验证库 (MSAL) 允许应用使用的不同授权代理，以及如何启用这些代理。

为授权代理选择特定的策略属于可选操作，这是应用可以自定义的附加功能。 大多数应用使用 MSAL 默认设置（有关各项默认设置，请参阅[了解 Android MSAL 配置文件](msal-configuration.md)）。

MSAL 支持使用 `WebView` 或系统浏览器授权。  下图显示了使用 `WebView` 或使用包含或不包含自定义标签页的系统浏览器进行授权的大致形式：

![MSAL 登录示例](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>单一登录的含义

默认情况下，与 MSAL 集成的应用程序使用系统浏览器的自定义标签页进行授权。 与 WebView 不同，自定义标签页与默认系统浏览器共享 Cookie jar，可以减少与自定义标签页集成的 Web 应用或其他本机应用中的登录次数。

如果应用程序使用 `WebView` 策略但未在应用中集成 Microsoft Authenticator 或公司门户支持，则用户在整个设备上或者在本机应用与 Web 应用之间无法获得单一登录 (SSO) 体验。

如果应用程序使用支持 Microsoft Authenticator 或公司门户的 MSAL，并且用户在某个应用中具有有效的登录名，则用户可以获得跨应用程序的 SSO 体验。

## <a name="webview"></a>Web 视图

若要使用应用中 WebView，请在传递给 MSAL 的应用配置 JSON 中添加以下行：

```json
"authorization_user_agent" : "WEBVIEW"
```

使用应用中 `WebView` 时，用户可以直接登录到应用。 令牌保留在应用的沙盒内部，不能在应用 Cookie jar 的外部使用。 因此，除非应用与 Authenticator 或公司门户集成，否则用户无法获得跨应用程序的 SSO 体验。

但是，`WebView` 确实提供用于自定义登录 UI 外观的功能。 有关如何进行这种自定义的详细信息，请参阅 [Android WebView](https://developer.android.com/reference/android/webkit/WebView)。

## <a name="default-browser-plus-custom-tabs"></a>默认浏览器加上自定义标签页

MSAL 默认使用浏览器和[自定义标签页](https://developer.chrome.com/multidevice/android/customtabs)策略。 可以在自定义配置文件中使用以下 JSON 配置显式指定此策略，以防将来的版本对 `DEFAULT` 做出更改：

```json
"authorization_user_agent" : "BROWSER"
```

使用此方法可通过设备的浏览器提供 SSO 体验。 MSAL 使用共享的 Cookie jar，使其他本机应用或 Web 应用能够使用 MSAL 设置的持久会话 Cookie 在设备上实现 SSO。

## <a name="browser-selection-heuristic"></a>浏览器选择试探法

由于 MSAL 无法指定可在众多 Android 手机上使用的确切浏览器包，因此 MSAL 实施浏览器选择试探法，以尝试提供最佳的跨设备 SSO。

MSAL 将检索设备上安装的浏览器的完整列表，以选择要使用的浏览器。 该列表由包管理器按顺序（间接反映了用户的偏好）返回。 例如，如果已设置默认浏览器，则它是列表中的第一个条目。 始终会选择列表中的第一个浏览器，无论它是否支持自定义标签页。__ 如果浏览器支持自定义选项卡，MSAL 将启动自定义选项卡。 自定义选项卡的外观和感觉更接近应用`WebView`内，并允许基本的 UI 自定义。 有关详细信息，请参阅 [Android 中的自定义标签页](https://developer.chrome.com/multidevice/android/customtabs)。

如果设备上没有浏览器包，MSAL 将使用应用中 `WebView`。

浏览器列表中的浏览器顺序取决于操作系统。 该列表按从高到低的优先顺序排列。 如果设备默认设置未更改，则每次登录时应启动同一浏览器，以确保提供 SSO 体验。

> [!NOTE]
> 如果将另一个浏览器设置为默认浏览器，MSAL 将不再优先使用 Chrome。 例如，在同时预装有 Chrome 和另一浏览器的设备上，MSAL 将使用用户设置的默认浏览器。

### <a name="tested-browsers"></a>已测试的浏览器

我们已对以下浏览器进行测试，以确定它们是否可以正确重定向到配置文件中指定的 `"redirect_uri"`：

| | 内置浏览器 | Chrome | Opera  | Microsoft Edge | UC 浏览器 | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | 通过 | 通过 |不适用 |不适用 |不适用 |不适用 |
| Samsung S7 (API 25) | 通过* | 通过 | 通过 | 通过 | 失败 |通过 |
| Huawei (API 26) |通过** | 通过 | 失败 | 通过 | 通过 |通过 |
| Vivo (API 26) |通过|通过|通过|通过|通过|失败|
| Pixel 2 (API 26) |通过 | 通过 | 通过 | 通过 | 失败 |通过 |
| Oppo | 通过 | 不适用*** |不适用  |不适用 |不适用 | 不适用|
| OnePlus (API 25) |通过 | 通过 | 通过 | 通过 | 失败 |通过 |
| Nexus (API 28) |通过 | 通过 | 通过 | 通过 | 失败 |通过 |
|MI | 通过 | 通过 | 通过 | 通过 | 失败 |通过 |

*Samsung 的内置浏览器为 Samsung Internet。  
**Huawei 的内置浏览器为 Huawei Browser。  
***无法在 Oppo 设备设置中更改默认浏览器。
