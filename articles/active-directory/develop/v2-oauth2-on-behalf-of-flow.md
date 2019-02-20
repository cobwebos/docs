---
title: Azure AD v2.0 OAuth2.0 代理流 | Microsoft Docs
description: 本文介绍如何使用 OAuth2.0 代理流通过 HTTP 消息实现服务到服务身份验证。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0b1e784d4ca92f0da0e37d4afc1efcf09282cb4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162860"
---
# <a name="azure-active-directory-v20-and-oauth-20-on-behalf-of-flow"></a>Azure Active Directory v2.0 和 OAuth 2.0 代理流

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

OAuth 2.0 代理流 (OBO) 适用于这样的用例：应用程序调用某个服务/Web API，而后者又需要调用另一个服务/Web API。 思路是通过请求链传播委托用户标识和权限。 要使中间层服务向下游服务发出身份验证请求，该服务需要代表用户保护 Azure Active Directory (Azure AD) 提供的访问令牌。

> [!NOTE]
> v2.0 终结点并非支持所有 Azure AD 方案和功能。 若要确定是否应使用 v2.0 终结点，请阅读 [v2.0 限制](active-directory-v2-limitations.md)。 具体而言，具有 Microsoft 帐户 (MSA) 和 Azure AD 受众的应用不支持已知的客户端应用程序。 因此，OBO 的常见同意模式不适用于同时登录个人和工作或学校帐户的客户端。 若要详细了解如何处理该流的此步骤，请参阅[为中间层应用程序获得同意](#gaining-consent-for-the-middle-tier-application)。


> [!IMPORTANT]
> 自 2018 年 5 月起，派生 `id_token` 的某些隐式流不能用于 OBO 流。 单页应用 (SPA) 应改为将**访问**令牌传递给中间层机密客户端，才能执行 OBO 流。 有关哪些客户端可以执行 OBO 调用的详细信息，请参阅[限制](#client-limitations)。

## <a name="protocol-diagram"></a>协议图

假设已在应用程序中使用 [OAuth 2.0 授权代码授权流](v2-oauth2-auth-code-flow.md)对用户进行身份验证。 此时，应用程序已获得 API A 的访问令牌（令牌 A），其中包含用户对访问中间层 Web API (API A) 的声明和许可。 现在，API A 需要向下游 Web API (API B) 发出身份验证请求。

所遵循的步骤构成 OBO 流，并借助以下关系图进行说明。

![OAuth2.0 代理流](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. 客户端应用程序使用令牌 A（其中包含 API A 的 `aud` 声明）向 API A 发出请求。
1. API A 向 Azure AD 令牌颁发终结点进行身份验证并请求访问 API B 的令牌。
1. Azure AD 令牌颁发终结点使用令牌 A 验证 API A 的凭据，并颁发访问 API B 的令牌（令牌 B）。
1. 令牌 B 在向 API B 发出的请求的授权标头中设置。
1. API B 返回受保护资源中的数据。

> [!NOTE]
> 在此方案中，中间层服务无需用户干预，就要获取用户对访问下游 API 的许可。 因此，在身份验证过程的同意步骤中会提前显示授权访问下游 API 的选项。 若要了解如何为应用设置此选项，请参阅[为中间层应用程序获得同意](#gaining-consent-for-the-middle-tier-application)。 

## <a name="service-to-service-access-token-request"></a>服务到服务访问令牌请求

若要请求访问令牌，请使用以下参数向特定于租户的 v2.0 令牌终结点发出 HTTP POST。

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

有两种情况，具体取决于客户端应用程序选择由共享密钥还是由证书保护。

### <a name="first-case-access-token-request-with-a-shared-secret"></a>第一种情况：使用共享机密访问令牌请求

使用共享密钥时，服务到服务访问令牌请求包含以下参数：

| 参数 |  | 说明 |
| --- | --- | --- |
| `grant_type` | 必选 | 令牌请求的类型。 对于使用 JWT 的请求，该值必须为 `urn:ietf:params:oauth:grant-type:jwt-bearer`。 |
| `client_id` | 必选 | [应用程序注册门户](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)或新的[应用注册(预览版)门户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)已分配给应用的应用程序（客户端）ID。 |
| `client_secret` | 必选 | 在用于注册应用的门户中为应用生成的应用程序机密。 |
| `assertion` | 必选 | 请求中使用的令牌值。 |
| `scope` | 必选 | 空格分隔的令牌请求范围的列表。 有关详细信息，请参阅[作用域](v2-permissions-and-consent.md)。 |
| `requested_token_use` | 必选 | 指定应如何处理请求。 在 OBO 流中，该值必须设置为 `on_behalf_of`。 |

#### <a name="example"></a>示例

以下 HTTP POST 通过 `user.read` 作用域请求 https://graph.microsoft.com Web API 的访问令牌和刷新令牌。

```
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read+offline_access
&requested_token_use=on_behalf_of
```

### <a name="second-case-access-token-request-with-a-certificate"></a>第二种情况：使用证书访问令牌请求

使用证书的服务到服务访问令牌请求包含以下参数：

| 参数 |  | 说明 |
| --- | --- | --- |
| `grant_type` | 必选 | 令牌请求的类型。 对于使用 JWT 的请求，该值必须为 `urn:ietf:params:oauth:grant-type:jwt-bearer`。 |
| `client_id` | 必选 | [应用程序注册门户](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)或新的[应用注册(预览版)门户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)已分配给应用的应用程序（客户端）ID。 |
| `client_assertion_type` | 必选 | 值必须是 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`。 |
| `client_assertion` | 必选 | 断言（JSON Web 令牌），需使用作为应用程序凭据注册的证书进行创建和签名。 若要了解如何注册证书以及断言的格式，请参阅[证书凭据](active-directory-certificate-credentials.md)。 |
| `assertion` | 必选 | 请求中使用的令牌值。 |
| `requested_token_use` | 必选 | 指定应如何处理请求。 在 OBO 流中，该值必须设置为 `on_behalf_of`。 |
| `scope` | 必选 | 空格分隔的令牌请求范围的列表。 有关详细信息，请参阅[作用域](v2-permissions-and-consent.md)。|

请注意，这些参数与共享密钥请求的参数几乎相同，只不过 `client_secret` 参数替换为以下两个参数：`client_assertion_type` 和 `client_assertion`。

#### <a name="example"></a>示例

以下 HTTP POST 通过 `user.read` 作用域请求具有证书的 https://graph.microsoft.com Web API 的访问令牌。

```
// line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=https://graph.microsoft.com/user.read+offline_access
```

## <a name="service-to-service-access-token-response"></a>服务到服务访问令牌响应

成功响应是具有以下参数的 JSON OAuth 2.0 响应。

| 参数 | 说明 |
| --- | --- |
| `token_type` | 指示令牌类型值。 Azure AD 支持的唯一类型是 `Bearer`。 有关持有者令牌的详细信息，请参阅 [OAuth 2.0 授权框架：持有者令牌用法 (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)。 |
| `scope` | 令牌中授予的访问权限的范围。 |
| `expires_in` | 访问令牌有效的时间长度（以秒为单位）。 |
| `access_token` | 请求的访问令牌。 调用方服务可以使用此令牌向接收方服务进行身份验证。 |
| `refresh_token` | 所请求的访问令牌的刷新令牌。 当前访问令牌过期后，调用方服务可以使用此令牌请求另一个访问令牌。 仅当已请求 `offline_access` 作用域时提供刷新令牌。 |

### <a name="success-response-example"></a>成功响应示例

以下示例演示对 https://graph.microsoft.com Web API 的访问令牌请求的成功响应。

```
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
}
```

> [!NOTE]
> 上述访问令牌是 v1.0 格式的令牌。 这是因为该令牌是基于要访问的资源提供的。 Microsoft Graph 请求 v1.0 令牌，因此当客户端请求 Microsoft Graph 的令牌时，Azure AD 会生成 v1.0 访问令牌。 只有应用程序才能查看访问令牌。 客户端不应该检查它们。 

### <a name="error-response-example"></a>错误响应示例

如果已对下游 API 设置条件访问策略（如多重身份验证），则在尝试获取下游 API 的访问令牌时，令牌终结点会返回错误响应。 中间层服务应向客户端应用程序显示此错误，以便客户端应用程序可以提供用户交互，以满足条件访问策略。

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>使用访问令牌访问受保护资源

现在，中间层服务可以通过在 `Authorization` 标头中设置令牌，使用上面获取的令牌向下游 Web API 发 出身份验证请求。

### <a name="example"></a>示例

```
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="gaining-consent-for-the-middle-tier-application"></a>为中间层应用程序获得同意

根据应用程序的受众，可以考虑使用不同的策略来确保 OBO 流的成功。 在所有情况下，最终目标都是确保给予适当的同意。 但是，如何实现这一点取决于应用程序支持哪些用户。 

### <a name="consent-for-azure-ad-only-applications"></a>同意 Azure AD 专用应用程序

#### <a name="default-and-combined-consent"></a>/.default 和组合同意

对于只需要登录工作或学校帐户的应用程序，传统的“已知客户端应用程序”方法就足够了。 中间层应用程序将客户端添加到其清单中的已知客户端应用程序列表中，然后，客户端可以为自身和中间层应用程序触发组合同意流。 在 v2.0 终结点上，使用 [`/.default` 范围](v2-permissions-and-consent.md#the-default-scope)完成此操作。 当使用已知的客户端应用程序和 `/.default` 触发同意屏幕时，同意屏幕将显示客户端到中间层 API 的权限，同时还会请求中间层 API 所需的任何权限。 用户同意这两个应用程序，接着 OBO 流便开始工作。 

目前，个人 Microsoft 帐户系统不支持组合同意，因此这种方法不适合想要专门登录个人帐户的应用。 在租户中用作来宾帐户的个人 Microsoft 帐户使用 Azure AD 系统进行处理，可以通过组合同意。 

#### <a name="pre-authorized-applications"></a>预授权应用程序

预览应用程序门户的一项新功能是“预授权应用程序”。 通过这种方式，资源可以指示给定应用程序始终具有接收某些范围的权限。 这主要用于使前端客户端和后端资源之间的连接更顺畅。 一个资源可以声明多个预授权应用程序 - 任何此类应用程序都可以在 OBO 流中请求这些权限，并在未经用户同意的情况下接收这些权限。

#### <a name="admin-consent"></a>管理员同意

租户管理员可以通过为中间层应用程序提供管理员同意，保证应用程序有权调用其所需的 API。 为此，管理员可以在其租户中找到中间层应用程序，打开“所需的权限”页面，然后选择为应用授予权限。 若要详细了解管理员同意功能，请参阅[同意和权限文档](v2-permissions-and-consent.md)。 

### <a name="consent-for-azure-ad--microsoft-account-applications"></a>同意 Azure AD + Microsoft 帐户应用程序

由于个人帐户权限模型的限制以及缺少控制租户，个人帐户的同意要求与 Azure AD 略有不同。 既没有租户提供租户范围内的同意，也没有能力进行组合同意。 因此，会出现其他策略 - 请注意，这些策略也适用于仅需要支持 Azure AD 帐户的应用程序。 

#### <a name="use-of-a-single-application"></a>使用单一应用程序

在某些情况下，可能只有一对中间层和前端客户端。 在这种情况下，你可能会发现将其作为单一应用程序更轻松，完全无需使用中间层应用程序。 若要在前端和 Web API 之间进行身份验证，可以使用 cookie、id_token 或为应用程序本身请求的访问令牌。 然后，从此单一应用程序请求同意后端资源。 

## <a name="client-limitations"></a>客户端限制

如果客户端使用隐式流来获取 id_token，且该客户端在回复 URL 中也具有通配符，则 id_token 不能用于 OBO 流。  但是，即使发起客户端已注册通配符回复 URL，通过隐式授予流获取的访问令牌仍可由机密客户端兑换。 

## <a name="next-steps"></a>后续步骤

详细了解 OAuth 2.0 协议和使用客户端凭据执行服务到服务身份验证的其他方法。

* [Azure AD v2.0 中的 OAuth 2.0 客户端凭据授予](v2-oauth2-client-creds-grant-flow.md)
* [Azure AD v2.0 中的 OAuth 2.0 代码流](v2-oauth2-auth-code-flow.md)
* [使用 `/.default` 范围](v2-permissions-and-consent.md#the-default-scope) 
