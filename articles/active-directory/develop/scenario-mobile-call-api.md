---
title: 调用 Web API 的移动应用 - 调用 Web API | Microsoft 标识平台
description: 了解如何构建调用 Web API 的移动应用（调用 Web API）
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
ms.openlocfilehash: 1ada6ee6247deb3d4c72edb8237a40a0f47f96be
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268317"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>调用 Web API 的移动应用 - 调用 Web API

在应用登录用户并接收令牌以后，MSAL 会公开有关用户、用户的环境以及所颁发令牌的多项信息。 应用可以使用这些值来调用 Web API 或向用户显示欢迎消息。

首先，我们将查看 MSAL 结果。 接下来，我们看看如何使用 `AuthenticationResult` 或 `result` 中的访问令牌来调用受保护的 Web API。

## <a name="msal-result"></a>MSAL 结果
MSAL 提供以下值： 

- `AccessToken`：用于在 HTTP 持有者请求中调用受保护的 Web API。
- `IdToken`：包含已登录用户的有用信息，例如用户的名称、主租户，以及存储的唯一标识符。
- `ExpiresOn`：令牌的过期时间。 MSAL 处理应用的自动刷新。
- `TenantId`：用户登录时使用的租户的标识符。 对于来宾用户 (Azure Active Directory B2B), 此值将确定用户登录时所用的租户, 而不是用户的主租户。  
- `Scopes`：通过令牌授予的作用域。 授予的作用域可能是请求的作用域的子集。

MSAL 也提供 `Account` 的抽象。 `Account` 表示当前用户的已登录帐户。

- `HomeAccountIdentifier`：用户的主租户的标识符。
- `UserName`：用户的首选用户名。 Azure Active Directory B2C 用户的此项可能为空。
- `AccountIdentifier`：已登录用户的标识符。 大多数情况下，此值会与 `HomeAccountIdentifier` 值相同，除非用户是另一租户中的来宾。

## <a name="call-an-api"></a>调用 API

有了访问令牌以后，即可轻松调用 Web API。 应用将使用此令牌构造一个 HTTP 请求，然后运行该请求。

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

### <a name="msal-for-ios-and-macos"></a>适用于 iOS 和 macOS 的 MSAL

用于获取令牌的方法返回`MSALResult`对象。 `MSALResult`公开可用于调用 web API 的属性。`accessToken` 访问受保护的 Web API 之前，应将访问令牌添加到 HTTP 授权标头中。

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

反应

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

## <a name="making-several-api-requests"></a>发出多个 API 请求

如需调用同一 API 多次，或者需要调用多个 API，请在生成应用时考虑以下事项：

- **增量许可**：Microsoft 标识平台允许应用在需要权限的时候获取用户许可，而不是在开始时统一这样做。 应用在每次准备调用 API 时，应该只请求需要使用的作用域。
- **条件访问**：在某些情况下，你可能会在发出几个 API 请求时收到其他条件访问要求。 如果第一个请求没有应用条件访问策略，并且应用尝试以无提示方式访问需要条件访问的新 API，则可能会发生这种情况。 若要处理这种情况，请务必捕获来自无提示请求的错误，并准备好进行交互式请求。  若要了解详细信息，请参阅[条件访问指南](conditional-access-dev-guide.md)。

## <a name="calling-several-apis-in-xamarin-or-uwp---incremental-consent-and-conditional-access"></a>在 Xamarin 或 UWP 中调用若干 API - 增量许可和条件访问

如果需要为同一用户调用多个 API，则在为用户获取令牌后，可以通过随后调用 `AcquireTokenSilent` 获取令牌来避免重复要求用户提供凭据。

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

需要交互的情况是：

- 用户已同意第一个 API，但现在需要同意更多范围（增量许可）
- 第一个 API 不需要多重身份验证，但下一个 API 需要。

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
> [转移到生产环境](scenario-mobile-production.md)
