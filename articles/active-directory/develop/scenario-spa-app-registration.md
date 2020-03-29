---
title: 注册单页应用 - Microsoft 标识平台 | Azure
description: 了解如何生成单页应用程序（应用注册）
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 5b18b10748e0587920c6965f1d235376da928469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701886"
---
# <a name="single-page-application-app-registration"></a>单页应用程序：应用程序注册

此页介绍用于单页应用程序 (SPA) 的应用注册详细信息。

按步骤[将新应用程序注册到 Microsoft 标识平台](quickstart-register-app.md)，然后为应用程序选择支持的帐户。 SPA 方案可以支持对组织中的帐户或任何组织和个人 Microsoft 帐户进行身份验证。

接下来，了解适用于单页应用程序的应用程序注册的具体方面。

## <a name="register-a-redirect-uri"></a>注册重定向 URI

隐式流将重定向中的令牌发送到在 Web 浏览器上运行的单页应用程序。 因此，请务必注册一个重定向 URI，以便应用程序可以从其接收令牌，这是很重要的要求。 请确保重定向 URI 与应用程序的 URI 完全匹配。

在 [Azure 门户](https://go.microsoft.com/fwlink/?linkid=2083908)中，转到已注册的应用程序。 在应用程序的“身份验证”页上，选择“Web”平台********。 在“重定向 URI”字段中输入应用程序的重定向 URI 的值****。

## <a name="enable-the-implicit-flow"></a>启用隐式流

在同一“身份验证”页的“高级设置”下，还必须启用“隐式授权”************。 如果应用程序只是让用户登录并获取 ID 令牌，则只需选择“ID 令牌”复选框****。

如果应用程序还需获取访问令牌来调用 API，则还应选择“访问令牌”复选框****。 有关详细信息，请参阅 [ID 令牌](./id-tokens.md)和[访问令牌](./access-tokens.md)。

## <a name="api-permissions"></a>API 权限

单页应用程序可代表已登录用户调用 API。 它们需要请求委托的权限。 有关详细信息，请参阅[添加访问 Web API 的权限](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用的代码配置](scenario-spa-app-configuration.md)
