---
title: 构建调用 web Api 的移动应用 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何构建调用 web Api 的移动应用程序（概述）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0a33b5bb4fc7220a9cf66f40e9805d3fde9fccef
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702039"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>方案：调用 web Api 的移动应用程序

了解如何构建调用 web Api 的移动应用程序。

## <a name="prerequisites"></a>必备组件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>入门

创建你的第一个移动应用程序并试用快速入门。

> [!div class="nextstepaction"]
> [快速入门：从 Android 应用获取令牌并调用 Microsoft Graph API](./quickstart-v2-android.md)
>
> [快速入门：从 iOS 应用获取令牌并调用 Microsoft Graph API](./quickstart-v2-ios.md)
>
> [快速入门：从 Xamarin iOS & Android 应用获取令牌并调用 Microsoft Graph API](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>概述

个性化的无缝用户体验是移动应用程序所必需的。  Microsoft 标识平台使移动开发人员能够创建适用于 iOS 和 Android 用户的体验。 你的应用程序可以登录 Azure Active Directory （Azure AD）用户、个人 Microsoft 帐户用户和 Azure AD B2C 用户，并获取令牌以代表他们调用 web API。 为了实现这些流，我们将使用 Microsoft 身份验证库（MSAL），它可实现行业标准[oauth 2.0 授权代码流](v2-oauth2-auth-code-flow.md)。

![守护程序应用](./media/scenarios/mobile-app.svg)

移动应用的注意事项：

- **用户体验是关键**的：允许用户在请求登录之前查看应用的值，并仅请求所需的权限。
- **支持所有用户配置**：许多移动业务用户处于条件访问和设备符合性策略下。 请确保支持这些关键方案。
- **实现单一登录（SSO）** ： MSAL 和 Microsoft 标识平台通过设备的浏览器或 Microsoft Authenticator （Android 上的 Intune 公司门户）简化单一登录。

## <a name="specifics"></a>细节

在 Microsoft 标识平台上构建移动应用时，请记住以下注意事项：

- 用户首次登录时可能需要某些用户交互，具体取决于平台。 例如，在第一次使用 SSO 时，iOS 要求应用显示用户交互 Microsoft Authenticator （Android 上 Intune 公司门户）。
- 在 iOS 和 Android 上，MSAL 可能使用外部浏览器（可能出现在应用的顶部）来登录用户。 你可以自定义配置以改用应用内 Webview。
- 切勿在移动应用程序中使用机密。 所有用户都可以访问它。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用注册](scenario-mobile-app-registration.md)
