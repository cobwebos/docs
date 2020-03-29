---
title: 缓存身份验证令牌
titleSuffix: Azure Cognitive Services
description: 本文将介绍如何缓存身份验证令牌。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: e652aa29b1c1935fcc4887dbe13ef9b683a8bd05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946162"
---
# <a name="how-to-cache-the-authentication-token"></a>如何缓存身份验证令牌

本文演示如何缓存身份验证令牌以提高应用程序的性能。

## <a name="using-aspnet"></a>使用ASP.NET

导入用于获取令牌的**Microsoft.身份模型.客户端.ActiveDirectory** NuGet 包。 接下来，使用以下代码获取 的`AuthenticationResult`，使用[创建沉浸式读取器资源](./how-to-create-immersive-reader.md)时获得的身份验证值。

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

该`AuthenticationResult`对象具有一`AccessToken`个属性，该属性是使用 SDK 启动沉浸式读取器时将使用的实际令牌。 它还具有一个`ExpiresOn`属性，表示令牌何时过期。 在启动沉浸式读取器之前，您可以检查令牌是否已过期，并且仅在令牌过期时才获取新令牌。

## <a name="using-nodejs"></a>使用节点.JS

将[**请求**](https://www.npmjs.com/package/request)npm 包添加到您的项目。 使用以下代码获取令牌，使用[创建沉浸式读取器资源](./how-to-create-immersive-reader.md)时获得的身份验证值。

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

该`expires_on`属性是令牌过期的日期和时间，表示为自 1970 年 1 月 1 日 UTC 以来的秒数。 在尝试获取新令牌之前，使用此值确定令牌是否已过期。

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