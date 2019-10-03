---
title: 授权代理和如何启用它们 |Microsoft
description: 了解 Microsoft 身份验证库（MSAL）允许 Android 应用使用的不同授权代理，以及如何启用它们。
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
ms.date: 09/05/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7daf40d6c6e552d6b76e424359f57f031641039
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679810"
---
# <a name="authorization-agents-android"></a>授权代理（Android）

本文介绍 Microsoft 身份验证库（MSAL）允许你的应用使用的不同授权代理，以及如何启用它们。

为授权代理选择特定策略是可选的，它表示应用可以自定义的其他功能。 大多数应用将使用 MSAL 默认值（请参阅[了解 ANDROID MSAL 配置文件](msal-configuration.md)以查看不同的默认值）。

MSAL 支持使用 `WebView` 或系统浏览器的授权。  下图显示了如何使用 `WebView` 或使用 CustomTabs 或不带 CustomTabs 的系统浏览器：

![MSAL 登录示例](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>单一登录含义

默认情况下，与 MSAL 集成的应用程序使用系统浏览器的自定义选项卡进行授权。 与 Webview 不同的是，自定义选项卡使用默认系统浏览器共享 cookie jar，从而能够使用与自定义选项卡集成的 web 或其他本机应用进行更少的登录。

如果应用程序使用 @no__t 0 策略，但未将 Microsoft Authenticator 或公司门户支持集成到其应用中，则用户将不会在设备上或在本机应用和 web 应用之间提供单一登录（SSO）体验。

如果应用程序将 MSAL 与 Microsoft Authenticator 或公司门户支持一起使用，则用户可以在应用程序之间具有 SSO 体验，前提是该用户具有其中一个应用的有效登录。

## <a name="webview"></a>WebView

若要使用应用内 Web 视图，请在传递到 MSAL 的应用配置 JSON 中添加以下行：

```json
"authorization_user_agent" : "WEBVIEW"
```

使用应用内 `WebView` 时，用户可以直接登录到应用。 令牌保存在应用的沙箱内，不能在应用的 cookie jar 之外使用。 因此，用户不能跨应用程序提供 SSO 体验，除非应用程序与验证器或公司门户集成。

但 `WebView` 提供了为登录 UI 自定义外观的功能。 有关如何执行此自定义的详细信息，请参阅[Android webview](https://developer.android.com/reference/android/webkit/WebView) 。

## <a name="default-browser-plus-custom-tabs"></a>默认浏览器和自定义选项卡

默认情况下，MSAL 使用浏览器和[自定义选项卡](https://developer.chrome.com/multidevice/android/customtabs)策略。 可以通过在自定义配置文件中使用以下 JSON 配置，显式指示此策略，以防止将来版本中的更改 `DEFAULT`：

```json
"authorization_user_agent" : "BROWSER"
```

使用此方法通过设备的浏览器提供 SSO 体验。 MSAL 使用共享 cookie jar，这允许其他本机应用或 web 应用通过使用 MSAL 设置的持久会话 cookie 在设备上实现 SSO。

## <a name="browser-selection-heuristic"></a>浏览器选择试探法

由于 MSAL 无法指定要在每个大范围的 Android 手机上使用的确切浏览器包，因此 MSAL 实现了浏览器选择试探法，用于尝试提供最佳的跨设备 SSO。

MSAL 检索设备上安装的浏览器的完整列表，以选择要使用的浏览器。 列表按包管理器返回的顺序，后者间接反映用户的首选项。 例如，默认浏览器（如果已设置）是列表中的第一项。 将选择列表中的_第一个_浏览器，而不考虑它是否支持自定义选项卡。 如果浏览器支持自定义选项卡，MSAL 将启动自定义选项卡。自定义选项卡的外观和感觉更接近应用内 `WebView` 并允许基本 UI 自定义。 有关详细信息，请参阅[Android 中的自定义选项卡](https://developer.chrome.com/multidevice/android/customtabs)。

如果设备上没有浏览器包，MSAL 将使用应用内 `WebView`。

浏览器列表中浏览器的顺序取决于操作系统。 它按从最优先到最少的顺序排列。 如果设备默认设置未更改，则应为每个登录启动相同的浏览器，以确保 SSO 体验。

> [!NOTE]
> 如果将另一个浏览器设置为默认浏览器，MSAL 不再优先于 Chrome。 例如，在具有 Chrome 和预安装的另一个浏览器的设备上，MSAL 将使用用户已设置为默认的浏览器。

### <a name="tested-browsers"></a>测试的浏览器

已对以下浏览器进行了测试，以查看它们是否正确地重定向到配置文件中指定的 `"redirect_uri"`：

| | 内置浏览器 | Chrome | Opera  | Microsoft Edge | UC 浏览器 | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| 结点4（API 17） | 穿过 | 穿过 |不适用 |不适用 |不适用 |不适用 |
| Samsung S7 （API 25） | 穿过 | 穿过 | 穿过 | 穿过 | 失败 |穿过 |
| Huawei （API 26） |pass * * | 穿过 | 失败 | 穿过 | 穿过 |穿过 |
| Vivo （API 26） |穿过|穿过|穿过|穿过|穿过|失败|
| 象素2（API 26） |穿过 | 穿过 | 穿过 | 穿过 | 失败 |穿过 |
| Oppo | 穿过 | 不适用 * * * |不适用  |不适用 |不适用 | 不适用|
| OnePlus （API 25） |穿过 | 穿过 | 穿过 | 穿过 | 失败 |穿过 |
| 结点（API 28） |穿过 | 穿过 | 穿过 | 穿过 | 失败 |穿过 |
|MI | 穿过 | 穿过 | 穿过 | 穿过 | 失败 |穿过 |

\* Samsung 的内置浏览器是 Samsung Internet。  
\* * Huawei 的内置浏览器是 Huawei 浏览器。  
默认浏览器无法在 Oppo 设备设置内进行更改。
