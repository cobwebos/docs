---
title: 从移动应用调用 web API |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何构建调用 web Api 的移动应用程序。 （调用 web API。）
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
ms.openlocfilehash: bd848fa6f74f049f97956ef1736ac2b08f3a6148
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160145"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>从移动应用调用 web API

应用登录用户并接收令牌后，Microsoft 身份验证库（MSAL）会公开有关用户、用户环境和已颁发令牌的信息。 应用可以使用这些值来调用 web API 或向用户显示欢迎消息。

本文首先介绍 MSAL 结果。 接下来，我们将介绍如何使用 `AuthenticationResult` 或 `result` 中的访问令牌来调用受保护的 web API。

## <a name="msal-result"></a>MSAL 结果
MSAL 提供下列值： 

- `AccessToken` 调用 HTTP 持有者请求中的受保护 web Api。
- `IdToken` 包含有关已登录用户的有用信息。 此信息包括用户的姓名、家庭租户和存储的唯一标识符。
- `ExpiresOn` 为令牌的过期时间。 MSAL 处理应用的自动刷新。
- `TenantId` 是用户登录的租户的标识符。 对于 Azure Active Directory （Azure AD） B2B 中的来宾用户，此值标识用户登录的租户。 该值不能识别用户的主租户。  
- `Scopes` 指示已向令牌授予的作用域。 授予的作用域可能是你请求的作用域的子集。

MSAL 还为 `Account` 值提供抽象。 `Account` 值表示当前用户的登录帐户：

- `HomeAccountIdentifier` 标识用户的主租户。
- `UserName` 是用户的首选用户名。 对于 Azure AD B2C 用户，此值可能为空。
- `AccountIdentifier` 标识已登录的用户。 在大多数情况下，此值与 `HomeAccountIdentifier` 值相同，除非用户是另一个租户中的来宾。

## <a name="call-an-api"></a>调用 API

拥有访问令牌后，就可以调用 web API 了。 您的应用程序将使用该令牌生成 HTTP 请求，然后运行该请求。

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

用于获取令牌的方法将返回 `MSALResult` 的对象。 `MSALResult` 公开 `accessToken` 属性。 你可以使用 `accessToken` 来调用 web API。 在调用来访问受保护的 web API 之前，将此属性添加到 HTTP 授权标头。

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

## <a name="make-several-api-requests"></a>发出几个 API 请求

如果需要多次调用同一 API，或者需要调用多个 Api，请在构建应用时考虑以下主题：

- **增量许可**： Microsoft 标识平台允许应用在需要权限时（而不是在开始时）获得用户许可。 每次你的应用程序都可以调用 API 时，它应只请求所需的作用域。

- **条件性访问**：发出几个 API 请求时，可能需要满足其他条件访问要求。 如果第一个请求没有条件性访问策略，并且你的应用程序尝试以无提示方式访问需要条件访问的新 API，则要求可能会以这种方式增加。 若要解决此问题，请务必捕获无提示请求中的错误，并准备好进行交互式请求。  有关详细信息，请参阅[条件性访问指南](../azuread-dev/conditional-access-dev-guide.md)。

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>使用增量许可和条件访问来调用多个 Api

如果需要为同一用户调用多个 Api，请在为用户获取令牌后，通过随后调用 `AcquireTokenSilent` 获取令牌来避免重复询问用户凭据：

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

以下情况需要交互：

- 用户同意第一个 API，但现在需要同意更多范围。 在这种情况下，将使用增量同意。
- 第一个 API 不需要多重身份验证，而是下一个 API。

```csharp
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
