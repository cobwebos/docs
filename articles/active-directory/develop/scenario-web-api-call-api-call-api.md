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
ms.openlocfilehash: 6063d143e2f217426bdf1db217fde46f8542d314
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965205"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>用于调用 web Api 的 web API-调用 API

获得令牌后，可以调用受保护的 web API。 这是从 ASP.NET/ASP.NET Core web API 的控制器中完成的。

## <a name="controller-code"></a>控制器代码

下面是受保护的 web API 中所示的示例代码的继续符，[用于调用 Web api-获取](scenario-web-api-call-api-acquire-token.md)在 API 控制器的操作中调用的令牌（名为 todolist）。

获取令牌后，将其用作持有者令牌以调用下游 API。

```CSharp
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

// Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转到生产](scenario-web-api-call-api-production.md)
