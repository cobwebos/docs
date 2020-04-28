---
title: 注销时从令牌缓存中删除帐户 - Microsoft 标识平台 | Azure
description: 了解如何在注销时从令牌缓存中删除帐户
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e138b3513b42dda47b0a114d866d657e18e3e393
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181641"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>调用 Web API 的 Web 应用：在全局注销时从令牌缓存中删除帐户

你已在[用于将用户登录的 Web 应用：登录和注销](scenario-web-app-sign-user-sign-in.md)中了解了如何向 Web 应用中添加登录信息。

对于调用 web api 的 web 应用，注销是不同的。 当用户从你的应用程序或从任何应用程序中注销时，你必须从令牌缓存中删除与该用户关联的令牌。

## <a name="intercept-the-callback-after-single-sign-out"></a>在单一注销后拦截回调

要清除与已注销帐户相关联的令牌缓存条目，应用程序可以拦截 `logout` 后事件。 Web 应用会在令牌缓存中存储每个用户的访问令牌。 通过拦截 `logout` 后回调，Web 应用程序可以从缓存中删除用户。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft Identity. Web 负责为您实现注销。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET 示例不会在全局注销时删除缓存中的帐户。

# <a name="java"></a>[Java](#tab/java)

Java 示例不会在全局注销时删除缓存中的帐户。

# <a name="python"></a>[Python](#tab/python)

Python 示例不会在全局注销时删除缓存中的帐户。

---

## <a name="next-steps"></a>后续步骤

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [获取 Web 应用的令牌](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [获取 Web 应用的令牌](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [获取 Web 应用的令牌](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [获取 Web 应用的令牌](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
