---
title: 调用 Web API 的移动应用 - 概述 | Microsoft 标识平台
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfc5e12bf1633c32a61e03c7df6d6c0320263d6e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325317"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>场景：用于调用 Web API 的移动应用程序

了解构建调用 Web API 的移动应用需要知道的一切内容。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>入门

创建第一个移动应用程序并尝试快速入门！

> [!div class="nextstepaction"]
> [快速入门：获取令牌并从 Android 应用中调用 Microsoft Graph API](./quickstart-v2-android.md)
>
> [快速入门：获取令牌并从 iOS 应用中调用 Microsoft Graph API](./quickstart-v2-ios.md)
>
> [快速入门：获取令牌并从 Xamarin iOS 和 Android 应用中调用 Microsoft Graph API](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>概述

个性化无缝用户体验对于移动应用很重要。  移动开发人员可以通过 Microsoft 标识平台为 iOS 和 Android 用户创建该体验。 你的应用程序可以登录 Azure Active Directory (Azure AD) 用户、个人 Microsoft 帐户用户和 Azure AD B2C 用户, 并获取令牌以代表他们调用 web API。 若要实现这些流，我们将使用 Microsoft 身份验证库 (MSAL)，后者用于实现行业标准 [OAuth2.0 授权代码流](v2-oauth2-auth-code-flow.md)。

![守护程序应用](./media/scenarios/mobile-app.svg)

移动应用的注意事项：

- **关键在于用户体验**：在要求用户登录之前，让用户了解应用的价值，并且只请求所需的权限。
- **支持所有用户配置**：许多移动业务用户处于条件访问和设备符合性策略下。 请务必支持这些关键方案。
- **实现单一登录 (SSO)** ：有了 MSAL 和 Microsoft 标识平台，即可通过设备的浏览器或 Microsoft Authenticator（以及 Android 上的 Intune 公司门户）轻松地进行单一登录。

## <a name="specifics"></a>详情

在 Microsoft 标识平台上生成移动应用时，请牢记以下注意事项：

- 第一次进行用户登录时，可能需要完成某些用户交互，具体取决于平台。 例如，在首次通过 Microsoft Authenticator（以及 Android 上的 Intune 公司门户）使用 SSO 时，iOS 会要求应用显示用户交互。
- 在 iOS 和 Android 上，MSAL 可能使用外部浏览器（可能显示在应用的顶端）来登录用户。 可以自定义配置，改用应用内 WebView。
- 不要在移动应用程序中使用机密。 所有用户均可访问该机密。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用注册](scenario-mobile-app-registration.md)
