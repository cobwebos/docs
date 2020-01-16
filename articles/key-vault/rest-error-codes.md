---
title: REST API 错误代码-Azure Key Vault
description: Azure Key Vault web 服务上的操作可能会返回这些错误代码。
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: 9ea77a6822a851951ea7363b9cf496fa0df534ed
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982096"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Azure Key Vault REST API 错误代码
 
Azure Key Vault web 服务上的操作可能会返回以下错误代码。
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401：未经身份验证的请求

401表示请求未经身份验证，Key Vault。 

请求在以下情况中进行身份验证：

- Key vault 知道调用方的标识;与
- 允许调用方尝试访问 Key Vault 资源。 

请求可能返回401的原因有多种。

### <a name="no-authentication-token-attached-to-the-request"></a>没有连接到请求的身份验证令牌。 

下面是一个示例 PUT 请求，设置机密的值：

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

"Authorization" 标头是每次调用数据平面操作的 Key Vault 所需的访问令牌。 如果缺少标头，则响应必须为401。

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>令牌缺少关联的正确资源。 

从 Azure OAUTH 终结点请求访问令牌时，名为 "resource" 的参数是必需的。 值对令牌提供程序很重要，因为它将令牌的范围限定为其预期用途。 \* 用于访问 Key Vault 的*所有*令牌的资源均 <https:\//vault.keyvault.net> （无尾随斜杠）。

### <a name="the-token-is-expired"></a>令牌已过期

标记经过 base64 编码，可以在[http://jwt.calebb.net](http://jwt.calebb.net)之类的网站中对值进行解码。 下面是上述令牌解码：

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

此令牌中可以看到许多重要部分：

- aud （受众）：标记的资源。 请注意，这是 <https://vault.azure.net>。 此标记不适用于未显式匹配此值的任何资源，如 graph。
- iat （发出于）：发出令牌后开始的时间的计时周期数。
- nbf （而不是之前）：此令牌生效后的开始时间的计时周期数。
- exp （过期）：此令牌过期后的开始时间的计时周期数。
- appid （应用程序 ID）：发出此请求的应用程序 ID 的 GUID。
- tid （租户 ID）：发出此请求的主体的租户 ID 的 GUID

必须在令牌中正确标识所有值才能使请求生效，这一点很重要。 如果所有内容都正确，则请求不会导致401。

### <a name="troubleshooting-401"></a>疑难解答401

在向密钥保管库发出请求之前，应从令牌生成的点调查401s。 通常使用代码来请求令牌。 收到令牌后，会将其传递到 Key Vault 请求。 如果代码在本地运行，则可以使用 Fiddler 捕获 https://login.microsoftonline.com 的请求/响应。 请求如下所示：

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

以下用户提供的信息很正确：

- 密钥保管库租户 ID
- 资源值已设置为 https %3 A %2 F %2 F vault. azure （URL 编码）
- 客户端 ID
- 客户端机密

确保请求的其余部分几乎完全相同。

如果只能获取响应访问令牌，则可以对其进行解码（如上所示），以确保租户 ID、客户端 ID （应用 ID）和资源。

## <a name="http-403-insufficient-permissions"></a>HTTP 403：权限不足

HTTP 403 表示请求已通过身份验证（它知道请求的标识），但标识无权访问请求的资源。 有两个原因：

- 标识没有访问策略。
- 请求资源的 IP 地址未列入密钥保管库的防火墙设置中的允许列表。

如果客户的应用程序未使用客户认为的客户端 ID，则通常会出现 HTTP 403。 这通常意味着没有为实际调用标识正确设置访问策略。

### <a name="troubleshooting-403"></a>疑难解答403

首先，打开日志记录。 有关如何执行此操作的说明，请参阅[Azure Key Vault 日志记录](key-vault-logging.md)。

启用日志记录后，可以确定403是否由访问策略或防火墙策略引起。

#### <a name="error-due-to-firewall-policy"></a>由于防火墙策略导致的错误

"客户端地址（00.00.00.00）未获得授权，并且调用方不是受信任的服务"

有一个有限的 "Azure 可信服务" 列表。 Azure 网站**不**是受信任的 azure 服务。 有关详细信息，请参阅博客文章[Key Vault 防火墙访问 Azure 应用 Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services)。

必须将 Azure 网站的 IP 地址添加到 Key Vault，才能使其正常工作。

如果访问策略为，则查找请求的对象 ID，并确保对象 ID 与用户尝试将访问策略分配到的对象相匹配。 AAD 中通常存在多个具有相同名称的对象，因此选择正确的对象非常重要。 通过删除并重新添加访问策略，可以查看是否存在具有相同名称的多个对象。

此外，大多数访问策略不需要使用 "授权应用程序"，如门户中所示。 授权应用程序用于 "代表" 身份验证方案，这种情况很少发生。 


## <a name="http-429-too-many-requests"></a>HTTP 429：请求太多

当请求数超出了时间范围规定的最大值时，会发生限制。 如果发生限制，则 Key Vault 的响应将为 HTTP 429。 发出的请求类型有最大的。 例如：创建 HSM 2048 位密钥每10秒5次请求，但所有其他 HSM 事务的限制均为1000。 因此，请务必了解确定限制的原因时所进行的调用类型。
通常，对 Key Vault 的请求数限制为2000个请求/10 秒。 异常是关键操作，如[Key Vault 服务限制](key-vault-service-limits.md)中所述

### <a name="troubleshooting-429"></a>疑难解答429
限制使用以下方法：

- 通过确定请求的资源是否有模式并尝试在调用应用程序中缓存这些模式，减少对 Key Vault 发出的请求数。 

- 发生 Key Vault 限制时，请调整请求代码以使用指数回退重试。 此算法在此处进行了说明：[如何限制应用](key-vault-ovw-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- 如果缓存的请求数无法减少，并且计时回退不起作用，请考虑将密钥拆分为多个密钥保管库。 单个订阅的服务限制为每个 Key Vault 限制的5倍。 如果使用5个以上的密钥保管库，应考虑使用多个订阅。 

可在此处找到包含增加限制的请求的详细指南： [Key Vault 限制指南](key-vault-ovw-throttling.md)


