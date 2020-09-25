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
ms.openlocfilehash: b1582af2bbd97579852ead0d4462f80f3a50fe6a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257140"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>调用 Web API 的 Web API：调用 API

有了令牌后，就可以调用受保护的 Web API 了。 通常从 web API 的控制器或页面调用下游 Api。

## <a name="controller-code"></a>控制器代码

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

使用 *Microsoft. Identity*时，有三种使用方案：

- [调用 Microsoft Graph](#call-microsoft-graph)
- [调用 Microsoft Graph 的 web API](#call-web-api-other-than-microsoft-graph)
- [手动获取令牌](#acquire-a-token-manually)

#### <a name="call-microsoft-graph"></a>调用 Microsoft Graph

在这种情况下，你已添加到 " `.AddMicrosoftGraph()` [代码配置](scenario-web-api-call-api-app-configuration.md#option-1-call-microsoft-graph)" 中指定的*Startup.cs*中，并且可以直接在 `GraphServiceClient` 控制器或页构造函数中插入，以便在操作中使用。 以下示例 Razor 页面显示已登录用户的照片。

```CSharp
 [Authorize]
 [AuthorizeForScopes(Scopes = new[] { "user.read" })]
 public class IndexModel : PageModel
 {
     private readonly GraphServiceClient _graphServiceClient;

     public IndexModel(GraphServiceClient graphServiceClient)
     {
         _graphServiceClient = graphServiceClient;
     }

     public async Task OnGet()
     {
         var user = await _graphServiceClient.Me.Request().GetAsync();
         try
         {
             using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
             {
                 byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                 ViewData["photo"] = Convert.ToBase64String(photoByte);
             }
             ViewData["name"] = user.DisplayName;
         }
         catch (Exception)
         {
             ViewData["photo"] = null;
         }
     }
 }
```

#### <a name="call-web-api-other-than-microsoft-graph"></a>调用 Microsoft Graph 之外的 web API

在这种情况下，你已添加到 " `.AddDownstreamWebApi()` [代码配置](scenario-web-api-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)" 中指定的*Startup.cs* ，可以直接将 `IDownstreamWebApi` 服务注入控制器或页构造函数并在操作中使用它：

```CSharp
 [Authorize]
 [AuthorizeForScopes(ScopeKeySection = "TodoList:Scopes")]
 public class TodoListController : Controller
 {
     private IDownstreamWebApi _downstreamWebApi;
     private const string ServiceName = "TodoList";

     public TodoListController(IDownstreamWebApi downstreamWebApi)
     {
         _downstreamWebApi = downstreamWebApi;
     }

     public async Task<ActionResult> Details(int id)
     {
         var value = await _downstreamWebApi.CallWebApiForUserAsync(
             ServiceName,
             options =>
             {
                 options.RelativePath = $"me";
             });
         return View(value);
     }
```

`CallWebApiForUserAsync`方法还具有强类型的泛型重写，使你能够直接接收对象。 例如，下面的方法接收到一个 `Todo` 实例，该实例是 WEB API 返回的 JSON 的强类型表示形式。

```CSharp
 // GET: TodoList/Details/5
 public async Task<ActionResult> Details(int id)
 {
     var value = await _downstreamWebApi.CallWebApiForUserAsync<object, Todo>(
         ServiceName,
         null,
         options =>
         {
             options.HttpMethod = HttpMethod.Get;
             options.RelativePath = $"api/todolist/{id}";
         });
     return View(value);
 }
```

#### <a name="acquire-a-token-manually"></a>手动获取令牌

如果已决定使用服务手动获取令牌 `ITokenAcquisition` ，则现在需要使用该令牌。 在这种情况下，以下代码将继续在 [调用 Web api 的 WEB api](scenario-web-api-call-api-acquire-token.md)中显示的示例代码：获取应用的令牌。 该代码在 API 控制器的操作中调用。 它调用下游 API（名为 *todolist*）。

 获取令牌后，将其用作持有者令牌以调用下游 API。

```csharp
 private async Task CallTodoListService(string accessToken)
 {
  // After the token has been returned by Microsoft.Identity.Web, add it to the HTTP authorization header before making the call to access the todolist service.
 _httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

 // Call the todolist service.
 HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
 // ...
 }
 ```

# <a name="java"></a>[Java](#tab/java)

下面的代码将继续在调用 web Api 的 web API 中显示的示例代码 [：获取应用程序的令牌](scenario-web-api-call-api-acquire-token.md)。 该代码在 API 控制器的操作中调用。 它调用下游 API MS Graph。

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
> [调用 Web API 的 Web API：移到生产环境](scenario-web-api-call-api-production.md)
