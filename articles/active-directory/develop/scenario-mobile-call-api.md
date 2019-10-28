---
title: 调用 web api 的移动应用-调用 web API
titleSuffix: Microsoft identity platform
description: 了解如何生成调用 web Api 的移动应用程序（调用 web API）
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
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e70b828219fc497fc07e2bc128eb480a532a176
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802569"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>调用 web Api 的移动应用-调用 web API

应用登录用户和收到的令牌后，MSAL 会公开有关用户、用户环境和颁发的令牌的几个信息。 应用可以使用这些值来调用 web API 或向用户显示欢迎消息。

首先，我们将看看 MSAL 结果。 接下来，我们将介绍如何使用 `AuthenticationResult` 或 `result` 中的访问令牌来调用受保护的 web API。

## <a name="msal-result"></a>MSAL 结果
MSAL 提供下列值： 

- `AccessToken`：用于调用 HTTP 持有者请求中的受保护 web Api。
- `IdToken`：包含有关已登录用户的有用信息，如用户的姓名、家庭租户和存储的唯一标识符。
- `ExpiresOn`：令牌的过期时间。 MSAL 处理应用的自动刷新。
- `TenantId`：用户登录时所用的租户的标识符。 对于来宾用户（Azure Active Directory B2B），此值将确定用户登录时所用的租户，而不是用户的主租户。  
- `Scopes`：已向令牌授予的作用域。 授予的作用域可能是你请求的作用域的子集。

MSAL 还为 `Account`提供抽象。 一个 `Account` 表示当前用户的登录帐户。

- `HomeAccountIdentifier`：用户的主租户的标识符。
- `UserName`：用户首选的用户名。 对于 Azure Active Directory B2C 用户，此情况可能为空。
- `AccountIdentifier`：已登录用户的标识符。 在大多数情况下，此值将与 `HomeAccountIdentifier` 的值相同，除非用户是另一个租户中的来宾。

## <a name="call-an-api"></a>调用 API

拥有访问令牌后，就可以轻松地调用 web API。 你的应用将使用令牌来构造 HTTP 请求，然后运行请求。

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>适用于 iOS 和 MacOS 的 MSAL

用于获取令牌的方法将返回 `MSALResult` 的对象。 `MSALResult` 公开可用于调用 web API 的 `accessToken` 属性。 访问受保护的 Web API 之前，应将访问令牌添加到 HTTP 授权标头中。

Objective-C：

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

Swift：

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="making-several-api-requests"></a>发出几个 API 请求

如果需要多次调用同一 API，或者需要调用多个 Api，请在构建应用时考虑以下事项：

- **增量许可**： Microsoft 标识平台允许应用在需要权限的情况下获取用户许可，而不是在开始时提供。 每次你的应用程序都可以调用 API 时，它应只请求需要使用的范围。
- **条件性访问**：在某些情况下，你可能会在发出几个 API 请求时获得其他条件性访问要求。 如果第一个请求已应用条件性访问策略，并且你的应用程序尝试以无提示方式访问需要条件访问的新 API，则会发生这种情况。 若要处理这种情况，请务必捕获无提示请求中的错误，并准备好进行交互式请求。  若要了解详细信息，请参阅[条件性访问指南](conditional-access-dev-guide.md)。

## <a name="calling-several-apis-in-xamarin-or-uwp---incremental-consent-and-conditional-access"></a>在 Xamarin 或 UWP 中调用多个 Api-增量许可和条件访问

如果需要为同一个用户调用多个 Api，一旦你为用户获取了令牌，你就可以通过随后调用 `AcquireTokenSilent` 获取令牌来避免重复询问用户提供凭据。

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

需要交互的情况如下：

- 用户同意第一个 API，但现在需要同意更多作用域（增量许可）
- 第一个 API 不需要多重身份验证，而是下一个 API。

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转到生产](scenario-mobile-production.md)
