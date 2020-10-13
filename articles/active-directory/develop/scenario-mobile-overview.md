---
title: 构建用于调用 Web API 的移动应用 | Azure
titleSuffix: Microsoft identity platform | Azure
description: 了解如何构建调用 Web API 的移动应用（概述）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 70ddc2191cc06a1bede87adc5940e0f7dea63384
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91257531"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>方案：用于调用 Web API 的移动应用程序

了解如何构建用于调用 Web API 的移动应用。

## <a name="getting-started"></a>入门

如果尚未创建第一个应用程序，请先完成快速入门：

- [快速入门：获取令牌并从 Android 应用中调用 Microsoft Graph API](./quickstart-v2-android.md)
- [快速入门：获取令牌并从 iOS 应用中调用 Microsoft Graph API](./quickstart-v2-ios.md)
- [快速入门：从 Xamarin iOS 和 Android 应用 (GitHub 获取令牌并调用 MICROSOFT GRAPH API](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)) 

## <a name="overview"></a>概述

个性化无缝用户体验对于移动应用很重要。  移动开发人员可以通过 Microsoft 标识平台为 iOS 和 Android 用户创建该体验。 应用程序可以 Azure Active Directory (Azure AD) 用户、个人 Microsoft 帐户用户和 Azure AD B2C 用户进行登录。 它还可以获取令牌，代表这些用户来调用 Web API。 为了实现这些流，我们将使用 Microsoft 身份验证库 (MSAL)。 MSAL 用于实现行业标准 [OAuth2.0 授权代码流](v2-oauth2-auth-code-flow.md)。

![守护程序应用](./media/scenarios/mobile-app.svg)

移动应用的注意事项：

- **关键在于用户体验**：在要求用户登录之前，让用户了解应用的价值。 只请求所需的权限。
- **支持所有用户配置**：许多移动业务用户必须遵循条件访问策略和设备合规性策略。 请务必支持这些关键方案。
- **实现单一登录 (SSO)** ：使用 MSAL 和 Microsoft 标识平台即可通过设备的浏览器或 Microsoft Authenticator（以及 Android 上的 Intune 公司门户）进行单一登录。
- **实现共享设备模式**：使应用程序可以在共享设备场景（例如医院、制造、零售和金融）中使用。 [阅读有关支持共享设备模式的详细信息](msal-shared-devices.md)。

## <a name="specifics"></a>详情

在 Microsoft 标识平台上生成移动应用时，请牢记以下注意事项：

- 该用户第一次登录时，可能需要完成某些用户交互，具体取决于平台。 例如，在首次通过 Microsoft Authenticator（以及 Android 上的 Intune 公司门户）使用 SSO 时，iOS 会要求应用显示用户交互。
- 在 iOS 和 Android 上，MSAL 可以使用外部浏览器来登录用户。 外部浏览器可能显示在应用顶端。
- 不要在移动应用程序中使用机密。 在这些应用程序中，所有用户都可以访问机密。

## <a name="recommended-reading"></a>推荐阅读内容

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用注册](scenario-mobile-app-registration.md)
