---
title: 注册单页应用-Microsoft 标识平台 |Microsoft
description: 了解如何生成单页面应用程序（应用程序注册）
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: f964d4b4c7032599cf8f74b285f819581fae907b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423657"
---
# <a name="single-page-application-app-registration"></a>单页应用程序：应用注册

此页介绍了单页应用程序（SPA）的应用注册细节。

按照以下步骤向[Microsoft 标识平台注册新应用程序](quickstart-register-app.md)，并为应用程序选择支持的帐户。 SPA 方案可支持对组织中的帐户或任何组织和个人 Microsoft 帐户进行身份验证。

接下来，了解适用于单页应用程序的应用程序注册的特定方面。

## <a name="register-a-redirect-uri"></a>注册重定向 URI

隐式流将重定向中的令牌发送到 web 浏览器上运行的单页面应用程序。 因此，请务必注册应用程序可以接收令牌的重定向 URI。 确保重定向 URI 完全匹配应用程序的 URI。

在[Azure 门户](https://go.microsoft.com/fwlink/?linkid=2083908)中，请切换到已注册的应用程序。 在应用程序的 "**身份验证**" 页上，选择 " **Web**平台"。 在 "**重定向 uri** " 字段中输入应用程序的重定向 uri 的值。

## <a name="enable-the-implicit-flow"></a>启用隐式流

在同一 "**身份验证**" 页上的 "**高级设置**" 下，还必须启用**隐式授予**。 如果你的应用程序只是登录用户并获取 ID 令牌，则可以选择 " **id 令牌**" 复选框。

如果你的应用程序还需要获取访问令牌来调用 Api，请确保同时选中 "**访问令牌**" 复选框。 有关详细信息，请参阅[ID 令牌](./id-tokens.md)和[访问令牌](./access-tokens.md)。

## <a name="api-permissions"></a>API 权限

单页应用程序可以代表已登录用户调用 Api。 它们需要请求委托的权限。 有关详细信息，请参阅[添加访问 Web api 的权限](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用程序的代码配置](scenario-spa-app-configuration.md)
