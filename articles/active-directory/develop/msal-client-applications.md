---
title: 客户端应用程序 （Microsoft 身份验证库） |Azure
description: 了解有关公共客户端和机密客户端应用程序中 Microsoft 身份验证库 (MSAL)。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d09436b9a2ac38e7b07a51f01d65769ed19d08e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66430824"
---
# <a name="public-client-and-confidential-client-applications"></a>公共客户端和机密客户端应用程序
Microsoft 身份验证库 (MSAL) 定义了两种类型的客户端： 公共客户端和机密客户端。 通过使用授权服务器的安全地进行身份验证和维护其凭据机密性的客户端能力来区分两个客户端类型。 与此相反，Azure AD 身份验证库 (ADAL) 使用的名称是什么*身份验证上下文*（这是连接到 Azure AD）。

- **机密客户端应用程序**是在服务器 （web 应用、 Web API 应用或甚至服务/守护程序应用） 运行的应用。 它们是被认为很难访问，并因此能够保持应用程序密码。 机密客户端可以保存配置时机密。 客户端的每个实例都有不同的配置 （包括客户端 ID 和客户端机密）。 这些值是很难使最终用户可提取。 Web 应用是最常见的机密客户端。 通过 web 浏览器中，公开的客户端 ID，但仅在返回通道中传递并永远不会直接公开机密。

    机密客户端应用程序： <BR>
    ![Web 应用](media/msal-client-applications/web-app.png) ![Web API](media/msal-client-applications/web-api.png) ![守护程序/服务](media/msal-client-applications/daemon-service.png)

- **公共客户端应用程序**是设备或桌面计算机上或在 web 浏览器中运行的应用。 它们不受信任，可以安全地保存应用程序机密，以便它们仅代表用户访问 Web Api。 （它们支持仅公共客户端流）。公共客户端不能保存配置时的机密，因此它们不存在客户端机密。

    公共客户端应用程序： <BR>
    ![桌面应用程序](media/msal-client-applications/desktop-app.png) ![Browserless API](media/msal-client-applications/browserless-app.png) ![移动应用](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> MSAL.js，在公共和机密客户端应用程序没有区别。  MSAL.js 表示为用户基于代理的应用程序，如 web 浏览器用户代理中执行客户端代码的公共客户端的客户端应用程序。 由于浏览器上下文可公开访问，这些客户端不存储机密。

## <a name="comparing-the-client-types"></a>比较客户端类型
下面是一些相似之处和公共客户端与机密客户端之间的差异的应用：

- 这两种类型的应用程序维护用户令牌缓存，并可以获取令牌以无提示方式 （如果该令牌已在令牌缓存)。 机密客户端应用程序还具有适用于应用本身的令牌的应用令牌缓存。
- 这两种类型的应用程序的管理用户帐户和可以从用户令牌缓存中获取一个帐户、 从其标识符获取帐户或删除帐户。
- 公共客户端应用程序有四个方法来获取令牌 （四个身份验证流）。 机密客户端应用程序有三种方法来获取令牌 （和一种方法来计算的标识提供者 URL 的授权终结点）。 有关详细信息，请参阅[获取令牌](msal-acquire-cache-tokens.md)。

如果已经使用 ADAL，您可能注意到，，ADAL 的身份验证与上下文不同，在 MSAL 的客户端 ID (也称为*应用程序 ID*或*应用程序 ID*) 在应用程序的构造时一次传递。 它不需要应用程序获取令牌时再次传递。 这是同样适用于公共和机密客户端应用。 机密客户端应用程序的构造函数还传递客户端凭据： 它们与标识提供程序共享的机密。

## <a name="next-steps"></a>后续步骤
学习内容：
- [客户端应用程序配置选项](msal-client-application-configuration.md)
- [实例化客户端应用程序，使用 MSAL.NET](msal-net-initializing-client-applications.md)
- [通过使用 MSAL.js 实例化客户端应用程序](msal-js-initializing-client-applications.md)
