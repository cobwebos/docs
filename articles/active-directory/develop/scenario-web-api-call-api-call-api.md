---
title: 用于调用 web Api 的 web API-Microsoft 标识平台 |Microsoft
description: 了解如何生成可调用 web Api 的 web API。
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
ms.openlocfilehash: 701f1f7c26731f3e9653955907f5f16d2688cdb2
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76043442"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>用于调用 web Api 的 web API：调用 API

获得令牌后，可以调用受保护的 web API。 可以从 ASP.NET 的控制器或 ASP.NET Core 的 web API 执行此操作。

## <a name="controller-code"></a>控制器代码

下面的代码将继续在调用 web Api 的 web API 中显示的示例代码[：获取应用程序的令牌](scenario-web-api-call-api-acquire-token.md)。 在 API 控制器的操作中调用该代码。 它调用名为*todolist*的下游 API。

获取令牌后，将其用作持有者令牌以调用下游 API。

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

// After the token has been returned by Microsoft Authentication Library (MSAL), add it to the HTTP authorization header before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [用于调用 web Api 的 web API：移至生产环境](scenario-web-api-call-api-production.md)
