---
title: 从 web 应用调用 web api |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何构建调用 Web API 的 Web 应用（调用受保护的 Web API）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 815b1789c54d1ce505c16dc89e199d451ae9a588
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396121"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>调用 Web API 的 Web 应用：调用 Web API

现在你已有令牌，可以调用受保护的 Web API 了。 通常会从 web 应用的控制器或页面调用下游 API。

## <a name="call-a-protected-web-api"></a>调用受保护的 Web API

调用受保护的 Web API 取决于所选的语言和框架：

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

如果使用的是 *web.config*，则可以使用三种方法来调用 API：

- [选项1：通过 Microsoft Graph SDK 调用 Microsoft Graph](#option-1-call-microsoft-graph-with-the-sdk)
- [选项2：使用帮助器类调用下游 web API](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [选项3：在不使用 helper 类的情况下调用下游 web API](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>选项1：通过 SDK 调用 Microsoft Graph

要调用 Microsoft Graph。 在这种情况下，你已添加到 " `AddMicrosoftGraph` [代码配置](scenario-web-app-call-api-app-configuration.md#option-1-call-microsoft-graph)" 中指定的*Startup.cs*中，并且可以直接在 `GraphServiceClient` 控制器或页构造函数中插入，以便在操作中使用。 以下示例 Razor 页面显示已登录用户的照片。

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

#### <a name="option-2-call-a-downstream-web-api-with-the-helper-class"></a>选项2：使用帮助器类调用下游 web API

需要调用 Microsoft Graph 之外的 web API。 在这种情况下，你已按照 `AddDownstreamWebApi` [代码配置](scenario-web-app-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)中的指定添加到*Startup.cs* ，并且可以直接在 `IDownstreamWebApi` 控制器或页构造函数中注入服务，并在操作中使用它：

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
}
```

`CallWebApiForUserAsync`还具有强类型的泛型重写，使你能够直接接收对象。 例如，下面的方法接收 `Todo` 实例，该实例是 WEB API 返回的 JSON 的强类型表示形式。

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

#### <a name="option-3-call-a-downstream-web-api-without-the-helper-class"></a>选项3：在不使用 helper 类的情况下调用下游 web API

你已决定使用服务手动获取令牌 `ITokenAcquisition` ，现在需要使用该令牌。 在这种情况下，以下代码将继续在 web 应用中显示的示例代码 [，该应用程序调用 Web api：获取应用程序的令牌](scenario-web-app-call-api-acquire-token.md)。 代码在 web 应用控制器的操作中调用。

获取令牌后，将其用作持有者令牌以调用下游 API，在本例中为 Microsoft Graph。

 ```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   ViewData["Me"] = me;
  }

  return View();
}
```
> [!NOTE]
> 可以使用相同的原则来调用任何 Web API。
>
> 大多数 Azure web Api 都提供 SDK，可简化对 Microsoft Graph 的调用。 例如， [创建一个 web 应用程序，该应用程序使用 Azure AD 获取对 Blob 存储的访问权限，该应用程序](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=dotnet) 使用 Microsoft 和 AZURE 存储 SDK 的 web 应用的示例。

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

    HttpURLConnection conn = (HttpURLConnection) url.openConnection();

    // Set the appropriate header fields in the request header.
    conn.setRequestProperty("Authorization", "Bearer " + accessToken);
    conn.setRequestProperty("Accept", "application/json");

    String response = HttpClientHelper.getResponseStringFromConn(conn);

    int responseCode = conn.getResponseCode();
    if(responseCode != HttpURLConnection.HTTP_OK) {
        throw new IOException(response);
    }

    JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转移到生产环境](scenario-web-app-call-api-production.md)
