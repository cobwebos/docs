---
title: 获取用于调用 web Api 的 web API 的标记 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何构建一个可调用 web Api 的 web API，该 API 需要获取应用的令牌。
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9cf660cbf981079ca20111e34fcd34504d8dcbfb
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044120"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>用于调用 web Api 的 web API：获取应用的令牌

生成客户端应用程序对象后，使用该对象获取可用于调用 web API 的令牌。

## <a name="code-in-the-controller"></a>控制器中的代码

下面是在 API 控制器的操作中调用的代码示例。 它调用名为*todolist*的下游 API。

```csharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()` 类似于用于[调用 Web api 的 WEB api 中的方案：应用配置](scenario-web-api-call-api-app-configuration.md)。 `BuildConfidentialClient()` 使用只包含一个帐户的信息的缓存来实例化 `IConfidentialClientApplication`。 帐户由 `GetAccountIdentifier` 方法提供。

`GetAccountIdentifier` 方法使用与用户的标识关联的声明，web API 将为该用户接收 JSON Web 令牌（JWT）：

```csharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [用于调用 web Api 的 web API：调用 API](scenario-web-api-call-api-call-api.md)
