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
ms.openlocfilehash: 6675d67299091325fcc3e12572a906716bf5b88d
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132420"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>方案：调用 web Api 的移动应用程序

了解如何构建调用 web Api 的移动应用程序。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>入门

创建你的第一个移动应用程序并试用快速入门。

> [!div class="nextstepaction"]
> [快速入门：从 Android 应用获取令牌并调用 Microsoft Graph API](./quickstart-v2-android.md)
>
> [快速入门：从 iOS 应用获取令牌并调用 Microsoft Graph API](./quickstart-v2-ios.md)
>
> [快速入门：从 Xamarin iOS 和 Android 应用获取令牌并调用 Microsoft Graph API](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>概述

个性化的无缝用户体验是移动应用程序所必需的。  Microsoft 标识平台使移动开发人员能够创建适用于 iOS 和 Android 用户的体验。 您的应用程序可以 Azure Active Directory （Azure AD）用户、个人 Microsoft 帐户用户和 Azure AD B2C 用户登录。 它还可以获取令牌，以代表用户调用 web API。 为了实现这些流，我们将使用 Microsoft 身份验证库（MSAL）。 MSAL 实现行业标准[oauth 2.0 授权代码流](v2-oauth2-auth-code-flow.md)。

![守护程序应用](./media/scenarios/mobile-app.svg)

移动应用的注意事项：

- **用户体验是关键**：允许用户在要求登录之前查看应用的值。 仅请求所需的权限。
- **支持所有用户配置**：许多移动业务用户必须遵循条件访问策略和设备合规性策略。 请确保支持这些关键方案。
- **实现单一登录（SSO）** ：通过使用 MSAL 和 Microsoft 标识平台，你可以通过设备的浏览器或 Microsoft Authenticator （Android 上 Intune 公司门户）启用单一登录。

## <a name="specifics"></a>细节

在 Microsoft 标识平台上构建移动应用时，请注意以下事项：

- 用户首次登录时可能需要某些用户交互，具体取决于平台。 例如，iOS 要求应用在第一次使用 SSO 时显示用户交互，Microsoft Authenticator （在 Android 上 Intune 公司门户）。
- 在 iOS 和 Android 上，MSAL 可能使用外部浏览器来登录用户。 外部浏览器可能会显示在应用的顶部。 你可以自定义配置以改用应用内 Webview。
- 切勿在移动应用程序中使用机密。 在这些应用程序中，所有用户都可以访问机密。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用注册](scenario-mobile-app-registration.md)
