---
title: 将登录用户的 web 应用移动到生产-Microsoft 标识平台 |Microsoft
description: 了解如何构建用于登录用户的 web 应用（转到生产环境）
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6c486e59f32afd09a9934ae2298172ccb4ee2414
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768109"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>登录用户的 Web 应用：移至生产环境

现在，你已了解如何获取令牌来调用 web Api，了解如何将其转移到生产环境。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>后续步骤

### <a name="same-site"></a>同一站点

请确保了解 Chrome 浏览器的新版本可能存在的问题

> [!div class="nextstepaction"]
> [如何在 Chrome 浏览器中处理 SameSite cookie 更改](howto-handle-samesite-cookie-changes-chrome-browser.md)

### <a name="scenario-for-calling-web-apis"></a>用于调用 web Api 的方案

Web 应用在用户登录后，可以代表已登录用户调用 web Api。 从 web 应用调用 web Api 是以下方案的对象：

> [!div class="nextstepaction"]
> [用于调用 Web API 的 Web 应用](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>深入探讨： ASP.NET Core web 应用教程

通过此 ASP.NET Core 教程了解用于登录用户的其他方式： 

> [!div class="nextstepaction"]
> [使 web 应用可以登录用户，并使用 Microsoft 标识平台为开发人员调用 Api](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

本渐进教程提供适用于 web 应用的生产就绪代码，其中包括如何在中添加帐户登录：

- 你的组织
- 多个组织
- 工作或学校帐户或个人 Microsoft 帐户
- [Azure AD B2C](https://aka.ms/aadb2c)
- 国家云

## <a name="sample-code-java-web-app"></a>示例代码： Java web 应用

有关此示例中的 Java web 应用的详细信息，请参阅 GitHub： 

> [!div class="nextstepaction"]
> [一个 Java Web 应用程序，该应用程序使用 Microsoft 标识平台登录用户并调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
