---
title: 将单页面应用移至生产-Microsoft 标识平台 |Microsoft
description: 了解如何构建单页应用程序（转到生产环境）
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
ms.openlocfilehash: 01b923e0d013fab1815456e55eac6036ded772bb
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701835"
---
# <a name="single-page-application-move-to-production"></a>单页应用程序：移至生产环境

了解如何获取令牌来调用 web Api 后，请了解如何迁移到生产。

## <a name="improve-your-app"></a>改善应用

[启用日志记录](msal-logging.md)以使你的应用程序生产就绪。

## <a name="test-your-integration"></a>测试集成

按照 [Microsoft 标识平台集成清单](identity-platform-integration-checklist.md)测试你的集成。

## <a name="next-steps"></a>后续步骤

深入了解快速入门示例，其中介绍了如何使用 MSAL 登录用户并获取访问令牌来调用 Microsoft Graph API 的代码：

> [!div class="nextstepaction"]
> [JavaScript SPA 教程](./tutorial-v2-javascript-spa.md)

演示如何使用 MSAL 获取自己的后端 web API 的令牌的示例：

> [!div class="nextstepaction"]
> [使用 ASP.NET 后端的 SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

此示例演示如何使用 MSAL 在注册到 Azure Active Directory B2C （Azure AD B2C）的应用中登录用户：

> [!div class="nextstepaction"]
> [SPA 与 Azure AD B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
