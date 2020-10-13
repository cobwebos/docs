---
title: REST API 错误代码 - Azure Key Vault
description: 在 Azure Key Vault Web 服务中执行操作时，可能会返回这些错误代码。
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: 30b7e34f2a791cfd8dec1a6d8e81d706fa07939f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631216"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Azure Key Vault REST API 错误代码
 
在 Azure Key Vault Web 服务中执行操作时，可能会返回以下错误代码。
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401：未经身份验证的请求

401 表示请求对 Key Vault 进行身份验证。 

如果符合以下条件，则会对请求进行身份验证：

- Key Vault 知道调用方的标识；并且
- 允许调用方尝试访问 Key Vault 资源。 

有多种不同的原因会导致请求返回 401。

### <a name="no-authentication-token-attached-to-the-request"></a>未将身份验证令牌附加到请求。 

下面是设置机密值的示例 PUT 请求：

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

“Authorization”标头是访问令牌，每次调用 Key Vault 执行数据平面操作时，都需要提供此令牌。 如果缺少该标头，则响应必须是 401。

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>令牌缺少关联的适当资源。 

从 Azure OAUTH 终结点请求访问令牌时，必须提供名为“resource”的参数。 该值对于令牌提供程序而言非常重要，因为它限定了令牌的目标使用范围。 用于访问 Key Vault 的 **所有** 令牌的资源是 *https： \/ /vault.keyvault.net* (，无尾随斜杠) 。

### <a name="the-token-is-expired"></a>令牌已过期

令牌采用 base64 编码，可以在 [http://jwt.calebb.net](http://jwt.calebb.net) 之类的网站中将值解码。 下面是上述令牌在解码后的情况：

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

在此令牌中可以看到许多重要组成部分：

- aud（受众）：令牌的资源。 请注意，此资源为 `https://vault.azure.net`。 对于不显式匹配此值的任何资源（例如 Graph），此令牌将不适用。
- iat（颁发时间）：颁发令牌时距离纪元开始时间的计时周期数。
- nbf（不早于）：此令牌生效时距离纪元开始时间的计时周期数。
- exp（过期时间）：此令牌过期时距离纪元开始时间的计时周期数。
- appid（应用程序 ID）：发出此请求的应用程序 ID 的 GUID。
- tid（租户 ID）：发出此请求的主体的租户 ID 的 GUID

必须在令牌中正确标识所有值才能使请求正常工作。 如果所有设置正确，则请求不会导致 401。

### <a name="troubleshooting-401"></a>故障排除 401

在向 Key Vault 发出请求之前，应从令牌生成时间点开始调查 401 错误。 通常使用代码来请求令牌。 收到令牌后，会将其传入 Key Vault 请求。 如果代码在本地运行，则可以使用 Fiddler 捕获对 `https://login.microsoftonline.com` 的请求/响应。 请求如下所示：

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

用户提供的以下信息必须正确：

- Key Vault 租户 ID
- 设置为 https%3A%2F%2Fvault.azure.net（URL 编码）的资源值
- 客户端 ID
- 客户端机密

确保请求的剩余部分几乎完全相同。

如果只能获取响应访问令牌，可将其解码（如上所示），以确保租户 ID、客户端 ID（应用 ID）和资源正确。

## <a name="http-403-insufficient-permissions"></a>HTTP 403：权限不足

HTTP 403 表示请求已完成身份验证（知道请求方标识），但标识无权访问请求的资源。 此错误有两种原因：

- 没有为标识设置访问策略。
- 不会在密钥保管库的防火墙设置中批准请求资源的 IP 地址。

如果客户的应用程序未使用客户端 ID，但客户认为已使用，则往往会出现 HTTP 403。 这通常意味着，未为实际调用方标识正确设置访问策略。

### <a name="troubleshooting-403"></a>排除故障 403

首先启用日志记录。 有关说明，请参阅 [Azure Key Vault 日志记录](logging.md)。

启用日志记录后，可以确定 403 错误是由访问策略还是防火墙策略造成的。

#### <a name="error-due-to-firewall-policy"></a>防火墙策略造成的错误

“客户端地址(00.00.00.00)未获授权，调用方不是受信任的服务”

有一个有限的“Azure 信任的服务”列表。 Azure 网站**不是**受信任的 Azure 服务。 有关详细信息，请参阅博客文章 [Key Vault 防火墙访问 Azure 应用 Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services)。

必须将 Azure 网站的 IP 地址添加到 Key Vault 才能使其正常工作。

如果错误是访问策略造成的：请查找请求的对象 ID，并确保该对象 ID 与用户尝试将访问策略分配到的对象相匹配。 AAD 中往往有多个同名的对象，因此选择正确的对象非常重要。 可以通过删除并重新添加访问策略，来查看是否存在多个同名的对象。

此外，大多数访问策略不需要按门户中所示使用“已授权的应用程序”。 已授权的应用程序用于“代理”身份验证方案，而这种方案极少出现。 


## <a name="http-429-too-many-requests"></a>HTTP 429：请求过多

当请求数超过时间范围规定的最大次数时，将发生限制。 如果发生限制，Key Vault 的响应将是 HTTP 429。 发出的不同类型的请求有规定的最大次数。 例如：创建 HSM 2048 位密钥的请求规定为每 10 秒最多 5 次，但所有其他 HSM 事务的限制为每 10 秒最多 1000 个请求。 因此，在确定限制的原因时，请务必了解所发出的调用类型。
一般情况下，对 Key Vault 的请求数限制为每 10 秒 2000 个请求。 密钥操作除外，具体请参阅 [Key Vault 服务限制](service-limits.md)

### <a name="troubleshooting-429"></a>故障排除 429
使用以下方法克服限制：

- 确定所请求资源是否存在模式并尝试在调用方应用程序中缓存这些模式，以此减少对 Key Vault 发出的请求数。 

- 发生 Key Vault 限制时，调整请求代码以使用指数退避进行重试。 以下文章解释了算法：[如何限制应用](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- 如果通过缓存无法减少请求数，并且计时退避不起作用，请考虑将密钥拆分到多个 Key Vault 中。 单个订阅的服务限制是单个 Key Vault 限制的 5 倍。 如果使用 5 个以上的 Key Vault，应考虑使用多个订阅。 

可在以下文章中找到详细的指导，包括如何请求提高限制：[Azure Key Vault 限制指南](overview-throttling.md)
