---
title: 客户端应用程序（Microsoft 身份验证库）| Azure
description: 了解 Microsoft 身份验证库 (MSAL) 中的公共客户端和机密客户端应用程序。
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
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532974"
---
# <a name="public-client-and-confidential-client-applications"></a>公共客户端和机密客户端应用程序
Microsoft 身份验证库 (MSAL) 定义两种类型的客户端：公共客户端和机密客户端。 这两种客户端的区分方式是，它们能否在授权服务器上安全地完成身份验证，以及能否维持客户端凭据的保密性。 相比之下，Azure AD 身份验证库 (ADAL) 使用所谓的“身份验证上下文”（与 Azure AD 建立的连接）。

- **机密客户端应用程序**是在服务器上运行的应用（Web 应用、Web API 应用，甚至服务/守护程序应用）。 它们被认为很难访问，因此能够保守应用程序的机密。 机密客户端可以保存配置时机密。 客户端的每个实例采用不同的配置（包括客户端 ID 和客户端机密）。 最终用户很难提取这些值。 Web 应用是最常见的机密客户端。 客户端 ID 通过 Web 浏览器公开，但机密仅在传回通道中传递，永远不会直接公开。

    机密客户端应用 <BR>
    ![Web 应用](media/msal-client-applications/web-app.png) ![Web API](media/msal-client-applications/web-api.png) ![守护程序/服务](media/msal-client-applications/daemon-service.png)

- **公共客户端应用程序**是在设备、台式计算机或 Web 浏览器中运行的应用。 我们并不确信这些应用程序能够安全保守应用程序的机密，因此，它们只是代表用户访问 Web API。 （它们仅支持公共客户端流。）公共客户端无法保存配置时机密，因此它们没有客户端机密。

    公共客户端应用 <BR>
    ![桌面应用](media/msal-client-applications/desktop-app.png) ![无浏览器 API](media/msal-client-applications/browserless-app.png) ![移动应用](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> 在 MSAL.js 中，公共和机密客户端应用没有区分开来。  MSAL.js 以基于用户代理的应用形式表示客户端应用，其中，在用户代理中执行客户端代码的公共客户端类似于 Web 浏览器。 这些客户端不存储机密，因为浏览器上下文可公开访问。

## <a name="comparing-the-client-types"></a>客户端类型的比较
下面是公共客户端与机密客户端之间的一些相似之处和差别：

- 这两种应用都会维护用户令牌缓存，并可以静默方式获取令牌（如果该令牌已在令牌缓存中）。 机密客户端应用还为应用本身的令牌提供应用令牌缓存。
- 这两种应用都会管理用户帐户，并可以从用户令牌缓存中获取帐户、从其标识符中获取帐户，或删除帐户。
- 公共客户端应用提供四种方式来获取令牌（四种身份验证流）。 机密客户端应用提供三种方式来获取令牌（以及一种用于计算标识提供者授权终结点 URL 的方式）。 有关详细信息，请参阅[获取令牌](msal-acquire-cache-tokens.md)。

如果你已使用 ADAL，可能会注意到，与 ADAL 的身份验证上下文不同，在 MSAL 中，客户端 ID（也称为“应用程序 ID”或“应用 ID”）只会在构造应用程序时传递一次。 当应用获取令牌时，不再需要传递此 ID。 公共和机密客户端应用都是如此。 还会为机密客户端应用的构造函数传递客户端凭据：这些应用与标识提供者共享的机密。

## <a name="next-steps"></a>后续步骤
学习内容：
- [客户端应用程序配置选项](msal-client-application-configuration.md)
- [使用 MSAL.NET 实例化客户端应用程序](msal-net-initializing-client-applications.md)
- [使用 MSAL.js 实例化客户端应用程序](msal-js-initializing-client-applications.md)
