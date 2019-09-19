---
title: 用于登录用户的 Web 应用（移到生产环境）- Microsoft 标识平台
description: 了解如何构建用于登录用户的 Web 应用（移到生产环境）
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd6717c132d32d54c16e7678695a09e35181a057
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086550"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>用于登录用户的 Web 应用 - 移到生产环境

现在你已了解如何获取用于调用 Web API 的令牌，接下来学习如何将其移到生产环境。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>后续步骤

### <a name="calling-web-apis-scenario"></a>调用 Web API 方案

Web 应用登录用户后，它就可以代表已登录用户调用 Web API。 从 Web 应用调用 Web API 是以下方案的目标：

> [!div class="nextstepaction"]
> [用于调用 Web API 的 Web 应用](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---aspnet-core-web-app-tutorial"></a>深层 ASP.NET Core web 应用教程

了解使用 ASP.NET Core 登录用户的其他方法教程：[ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)。 本示例是一个循序渐进的教程，其中包含用于 web 应用的生产就绪代码，其中包括如何在中添加帐户登录：

- 你的组织，
- 多个组织，
- 工作或学校帐户或个人 Microsoft 帐户
- 对于[Azure AD B2C](https://aka.ms/aadb2c)，
- 或在国家/地区云中。

### <a name="sample-code---java-web-app"></a>示例代码-Java web 应用

从 GitHub 上的示例中了解有关 Java web 应用的详细信息：[一个 Java Web 应用程序，该应用程序使用 Microsoft 标识平台登录用户并调用 Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
