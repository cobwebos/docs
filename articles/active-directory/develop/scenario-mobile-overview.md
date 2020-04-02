---
title: 构建用于调用 Web API 的移动应用 | Azure
titleSuffix: Microsoft identity platform | Azure
description: 了解如何构建调用 Web API 的移动应用（概述）
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
ms.openlocfilehash: acd44298e401aabaef03f5ddd84f37f32a3d8bcd
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546094"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>方案：调用 Web API 的移动应用程序

了解如何构建用于调用 Web API 的移动应用。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>入门

创建第一个移动应用程序并尝试快速入门。

> [!div class="nextstepaction"]
> [快速入门：获取令牌并从 Android 应用调用 Microsoft 图形 API](./quickstart-v2-android.md)
>
> [快速入门：获取令牌并从 iOS 应用调用 Microsoft 图形 API](./quickstart-v2-ios.md)
>
> [快速入门：获取令牌，并从 Xamarin iOS 和 Android 应用程序调用 Microsoft 图形 API](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>概述

个性化无缝用户体验对于移动应用很重要。  移动开发人员可以通过 Microsoft 标识平台为 iOS 和 Android 用户创建该体验。 您的应用程序可以登录 Azure 活动目录 （Azure AD） 用户、个人 Microsoft 帐户用户和 Azure AD B2C 用户。 它还可以获取令牌来代表他们调用 Web API。 为了实现这些流，我们将使用 Microsoft 身份验证库 （MSAL）。 MSAL 实现行业标准[OAuth2.0 授权代码流](v2-oauth2-auth-code-flow.md)。

![守护程序应用](./media/scenarios/mobile-app.svg)

移动应用的注意事项：

- **用户体验是关键**：允许用户在请求登录之前查看应用的价值。 仅请求所需的权限。
- **支持所有用户配置**：许多移动业务用户必须遵守条件访问策略和设备合规性策略。 请务必支持这些关键方案。
- **实现单一登录 （SSO）：** 通过使用 MSAL 和 Microsoft 标识平台，您可以通过设备的浏览器或 Microsoft 身份验证器（Android 上的 Intune 公司门户）启用单一登录。
- **实现共享设备模式**：使应用程序能够在共享设备方案中使用，例如医院、制造、零售和财务。 [阅读有关支持共享设备模式的信息](msal-shared-devices.md)。

## <a name="specifics"></a>详情

在 Microsoft 标识平台上构建移动应用时，请记住以下注意事项：

- 根据平台的不同，用户首次登录时可能需要一些用户交互。 例如，iOS 要求应用在首次通过 Microsoft 身份验证器（Android 上的 Intune 公司门户）首次使用 SSO 时显示用户交互。
- 在 iOS 和 Android 上，MSAL 可能使用外部浏览器登录用户。 外部浏览器可能显示在应用顶部。
- 不要在移动应用程序中使用机密。 在这些应用程序中，所有用户都可以访问机密。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用注册](scenario-mobile-app-registration.md)
