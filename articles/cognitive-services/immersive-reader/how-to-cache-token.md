---
title: 缓存身份验证令牌
titleSuffix: Azure Cognitive Services
description: 本文将演示如何缓存身份验证令牌。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 6850012272251c910362b61363dacd9090976680
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918411"
---
# <a name="how-to-cache-the-authentication-token"></a>如何缓存身份验证令牌

本文演示如何缓存身份验证令牌以提高应用程序的性能。

## <a name="using-aspnet"></a>使用 ASP.NET

导入 **System.identitymodel** NuGet 包，该软件包用于获取令牌。 接下来，使用以下代码获取，并 `AuthenticationResult` 使用在 [创建沉浸式读者资源](./how-to-create-immersive-reader.md)时获取的身份验证值。

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

`AuthenticationResult`对象具有属性， `AccessToken` 该属性是使用 SDK 启动沉浸式读取器时将使用的实际标记。 它还包含一个 `ExpiresOn` 属性，该属性指示令牌将过期的时间。 在启动沉浸式阅读器之前，可以检查令牌是否已过期，并仅在其已过期的情况下获取新令牌。

## <a name="using-nodejs"></a>使用 Node.JS

将 [**请求**](https://www.npmjs.com/package/request) npm 包添加到项目。 使用以下代码获取令牌，并使用在 [创建沉浸式读者资源](./how-to-create-immersive-reader.md)时获取的身份验证值。

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

`expires_on`属性是令牌过期的日期和时间，表示为自1970年1月1日起的秒数。 使用此值来确定令牌是否已过期，然后再尝试获取新令牌。

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK 参考](./reference.md)