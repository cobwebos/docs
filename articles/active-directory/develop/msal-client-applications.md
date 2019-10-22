---
title: 客户端应用程序（Microsoft 身份验证库） |Microsoft
description: 在 Microsoft 身份验证库（MSAL）中了解公用客户端和机密客户端应用程序。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c854cc34a1ea50f37428cfc18146618d516de7d
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "69532974"
---
# <a name="public-client-and-confidential-client-applications"></a>公共客户端和机密客户端应用程序
Microsoft 身份验证库（MSAL）定义两种类型的客户端：公用客户端和机密客户端。 这两种客户端类型可以通过安全地使用授权服务器进行身份验证并维护其客户端凭据的机密性来区分。 相反，Azure AD 身份验证库（ADAL）使用称为 "*身份验证上下文*" （连接到 Azure AD）。

- **机密客户端应用程序**是在服务器上运行的应用程序（web 应用程序、web API 应用程序，甚至是服务/守护程序应用）。 它们被视为难以访问，出于这一原因，可以保留应用程序机密。 机密客户端可以保存配置时间机密。 客户端的每个实例都有不同的配置（包括客户端 ID 和客户端密码）。 这些值对于最终用户来说很难提取。 Web 应用是最常见的机密客户端。 客户端 ID 是通过 web 浏览器公开的，但机密只在后通道中传递，而永远不会直接公开。

    机密客户端应用： <BR>
    ![Web 应用 ](media/msal-client-applications/web-app.png) ![Web API ](media/msal-client-applications/web-api.png) ![Daemon/service ](media/msal-client-applications/daemon-service.png)

- **公用客户端应用程序**是在设备或台式计算机上或在 web 浏览器中运行的应用程序。 它们不能安全地保存应用程序机密，因此它们只代表用户访问 Web Api。 （它们仅支持公共客户端流。）公共客户端无法保存配置时间机密，因此它们没有客户端密码。

    公共客户端应用： <BR>
    ![Desktop 应用 ](media/msal-client-applications/desktop-app.png) ![Browserless API ](media/msal-client-applications/browserless-app.png) ![Mobile 应用 ](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> 在 MSAL 中，不会分离公共和机密的客户端应用。  MSAL 将客户端应用代表作为基于用户代理的应用，在用户代理（如 web 浏览器）中执行客户端代码的公用客户端。 这些客户端不存储机密，因为浏览器上下文可公开访问。

## <a name="comparing-the-client-types"></a>比较客户端类型
公共客户端与机密客户端应用程序之间的一些相似性和差异如下：

- 这两种应用程序都维护一个用户令牌缓存，并可无提示地获取令牌（当令牌已在令牌缓存中时）。 机密客户端应用程序还具有应用程序本身的令牌的应用令牌缓存。
- 这两种类型的应用都管理用户帐户，并且可以从用户令牌缓存获取帐户，从其标识符获取帐户，或删除帐户。
- 公用客户端应用有四种方法可获取令牌（四个身份验证流）。 机密客户端应用程序提供了三种方法来获取令牌（以及计算标识提供者授权终结点的 URL 的一种方法）。 有关详细信息，请参阅[获取令牌](msal-acquire-cache-tokens.md)。

如果已使用 ADAL，你可能会注意到，与 ADAL 的身份验证上下文不同，在 MSAL 中，客户端 ID （也称为*应用程序 id*或*应用 id*）在应用程序构造时传递一次。 当应用程序获取令牌时，无需再次传递它。 这适用于公共和机密客户端应用。 机密客户端应用程序的构造函数也会传递客户端凭据：它们与标识提供者共享的机密。

## <a name="next-steps"></a>后续步骤
学习内容：
- [客户端应用程序配置选项](msal-client-application-configuration.md)
- [使用 MSAL.NET 实例化客户端应用程序](msal-net-initializing-client-applications.md)
- [使用 MSAL 实例化客户端应用程序](msal-js-initializing-client-applications.md)
