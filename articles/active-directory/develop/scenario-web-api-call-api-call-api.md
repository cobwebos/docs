---
title: 调用 Web API 的 Web API - Microsoft 标识平台 | Azure
description: 了解如何构建调用 Web API 的 Web API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6bbd24978891efd147b0c317c1746d13961ce5e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885083"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>调用 Web API 的 Web API：调用 API

有了令牌后，就可以调用受保护的 Web API 了。 可以从 Web API 的控制器执行此操作。

## <a name="controller-code"></a>控制器代码

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

以下代码继续演示[调用 Web API 的 Web API：获取应用的令牌](scenario-web-api-call-api-acquire-token.md)中显示的示例代码。 该代码在 API 控制器的操作中调用。 它调用名为“todolist”  的下游 API。

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

# <a name="java"></a>[Java](#tab/java)

以下代码继续演示[调用 Web API 的 Web API：获取应用的令牌](scenario-web-api-call-api-acquire-token.md)中显示的示例代码。 该代码在 API 控制器的操作中调用。 它调用下游 API MS Graph。

获取令牌后，将其用作持有者令牌以调用下游 API。

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
使用 MSAL Python 演示此流的示例尚不可用。

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [调用 Web API 的 Web API：转移到生产环境](scenario-web-api-call-api-production.md)
