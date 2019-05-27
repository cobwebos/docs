---
title: 移动应用程序调用 web Api 的调用的 web API |Microsoft 标识平台
description: 了解如何构建一个移动应用，调用 web Api （调用 web API）
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
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
ms.openlocfilehash: 44b6c203583a082228c2ba1f4c5f6fdb04d059be
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962384"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>调用 web Api-移动应用程序调用 web API

您的应用程序具有已注册用户并接收令牌后，MSAL 将公开有关用户、 用户的环境和颁发的令牌的信息的多个片段。 您的应用程序可以使用这些值来调用 web API 或向用户显示一条欢迎消息。

首先，我们将介绍 MSAL 结果。 然后我们将介绍如何使用中的访问令牌`AuthenticationResult`或`result`以调用受保护的 web API。

## <a name="msal-result"></a>MSAL 结果
MSAL 提供以下值： 

- `AccessToken`：用于调用受保护的 web Api 中的持有者 HTTP 请求。
- `IdToken`：包含有关登录的用户，用户的名称、 的主租户和存储的唯一标识符等有用信息。
- `ExpiresOn`：令牌过期时间。 MSAL 负责应用的自动刷新。
- `TenantId`：用户登录的租户的标识符。 对于来宾用户 (Azure Active Directory B2B)，此值将确定用户登录的用户的主租户的租户。  
- `Scopes`：被授予与你的令牌作用域。 授予作用域可能是您请求的作用域的子集。

MSAL 还提供抽象以`Account`。 `Account`表示当前用户的登录的帐户。

- `HomeAccountIdentifier`：用户的主租户的标识符。
- `UserName`：用户的首选的用户名。 这可能是空的 Azure Active Directory B2C 用户。
- `AccountIdentifier`：已登录用户的标识符。 此值将与相同`HomeAccountIdentifier`值在大多数情况下，除非该用户是另一个租户中的来宾。

## <a name="call-an-api"></a>调用的 API

访问令牌后，很容易地调用 web API。 您的应用程序将使用该令牌创建的 HTTP 请求，然后运行该请求。

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

### <a name="ios"></a>iOS

```swift
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)

        // Put access token in HTTP request.
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in
            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }
            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            // Successfully got data from Graph.
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

```CSharp
httpClient = new HttpClient();

// Put access token in HTTP request.
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>发出几个 API 请求

如果你需要调用相同的 API 几次，或如果您需要调用多个 Api，在生成您的应用程序时请考虑以下因素：

- **增量许可**:Microsoft 标识平台允许应用程序获得用户同意的情况下，随着权限是必需的而不是所有开头。 每次您的应用程序已准备好调用 API，它应请求仅需要使用作用域。
- **条件性访问**:在某些情况下，您可能会收到其他条件性访问要求进行多个 API 请求时。 如果第一个请求具有没有应用的条件性访问策略，并且您的应用程序尝试以静默方式访问需要条件性访问的新 API，可以发生这种情况。 若要处理这种情况，请确保捕获错误与无提示请求并准备好进行交互式请求。  若要了解详细信息，请参阅[条件性访问指南](conditional-access-dev-guide.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [迁移到生产环境](scenario-mobile-production.md)
