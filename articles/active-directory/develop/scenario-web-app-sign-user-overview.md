---
title: 用于登录用户的 Web 应用（概述）- Microsoft 标识平台
description: 了解如何构建用于登录用户的 Web 应用（概述）
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b898a93b87811fa5139e148a3273d7051af851b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056291"
---
# <a name="scenario-web-app-that-signs-in-users"></a>场景：用于登录用户的 Web 应用

了解生成一个可以通过 Microsoft 标识平台登录用户的 Web 应用所需的一切。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>入门

如果需要创建第一个可移植 (ASP.NET Core) Web 应用来登录用户，请按以下快速入门操作：

> [!div class="nextstepaction"]
> [快速入门：用于登录用户的 ASP.NET Core Web 应用](quickstart-v2-aspnet-core-webapp.md)

如果首选继续使用 ASP.NET，请尝试以下教程：

> [!div class="nextstepaction"]
> [快速入门：用于登录用户的 ASP.NET Web 应用](quickstart-v2-aspnet-webapp.md)

## <a name="overview"></a>概述

向 Web 应用添加身份验证，使之可以登录用户。 添加身份验证以后，Web 应用即可访问受限制的配置文件信息，并可执行其他操作，例如自定义你提供给其用户的体验。 Web 应用可对 Web 浏览器中的用户进行身份验证。 在此方案中，Web 应用程序指示用户的浏览器让用户登录到 Azure AD 中。 Azure AD 通过用户的浏览器返回一个登录响应，该响应在一个安全令牌中包含了关于用户的声明。 登录用户会利用 [Open ID Connect](./v2-protocols-oidc.md) 标准协议本身，该协议已通过使用中间件[库](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps)简化。

![Web 应用用于登录用户](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

在第二阶段，也可让应用程序代表已登录用户调用 Web API。 此下一阶段是另一方案，详见 [Web 应用调用 Web API](scenario-web-app-call-api-overview.md)

> [!NOTE]
> 向 Web 应用添加登录就是保护 Web 应用并验证用户令牌，这正是**中间件**库所做的。 此方案尚不需要 Microsoft 身份验证库 (MSAL)，后者就是获取令牌来调用受保护的 API。 身份验证库仅在后续方案中引入，此时 Web 应用需要调用 Web API。

## <a name="specifics"></a>详情

- 在应用程序注册期间，需提供一个回复 URI，或多个回复 URI（如果将应用部署到多个位置）。 在某些情况下（ASP.NET/ASP.NET Core），需要启用 ID 令牌。 最后需设置注销 URI，方便应用程序响应用户注销。
- 在应用程序代码中，需提供方便 Web 应用向其委托登录的机构。 可能需要自定义令牌验证（尤其是在 ISV 方案中）。
- Web 应用程序支持任何帐户类型。 有关详细信息，请参阅[支持的帐户类型](v2-supported-account-types.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用注册](scenario-web-app-sign-user-app-registration.md)
