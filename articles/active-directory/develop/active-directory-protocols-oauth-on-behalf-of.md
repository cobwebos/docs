---
title: "使用 OAuth2.0 代理草案规范的 Azure AD 服务到服务身份验证 | Microsoft Docs"
description: "本文介绍如何使用 OAuth2.0 代理流通过 HTTP 消息实现服务到服务身份验证。"
services: active-directory
documentationcenter: .net
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: nacanuma
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 0bb74816f216f0965c3ec780c4895cf7e488c3cf
ms.contentlocale: zh-cn
ms.lasthandoff: 06/08/2017

---
# 代理流中使用委派用户标识的服务到服务调用
OAuth 2.0 代理流适用于这样的用例：其中应用程序调用某个服务/web API，而后者又需要调用另一个服务/web API。 思路是通过请求链传播委托用户标识和权限。 要使中间层服务向下游服务发出身份验证请求，该服务需要代表用户保护 Azure Active Directory (Azure AD) 提供的访问令牌。

## 代理流示意图
假设已在应用程序中使用 [OAuth 2.0 授权代码授权流](active-directory-protocols-oauth-code.md)对用户进行身份验证。 此时，应用程序已获得访问令牌（令牌 A），其中包含用户对访问中间层 Web API (API A) 的声明和许可。 现在，API A 需要向下游 Web API (API B) 发出身份验证请求。

所遵循的步骤构成代理流，并借助以下关系图进行说明。

![OAuth2.0 代理流](media/active-directory-protocols-oauth-on-behalf-of/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. 客户端应用程序使用令牌 A 向 API A 发出请求。
2. API A 向 Azure AD 令牌颁发终结点进行身份验证并请求访问 API B 的令牌。
3. Azure AD 令牌颁发终结点使用令牌 A 验证 API A 的凭据，并颁发访问 API B 的令牌（令牌 B）。
4. 令牌 B 设置在向 API B 发出的请求的 authorization 标头中。
5. API B 返回受保护资源中的数据。

## 在 Azure AD 中注册应用程序和服务
在 Azure AD 中注册客户端应用程序和中间层服务。
### 注册中间层服务
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在顶栏上单击你的帐户，并在“目录”列表下选择要注册应用程序的 Active Directory 租户。
3. 单击左侧导航栏中的“更多服务”，然后选择“Azure Active Directory”。
4. 单击“应用注册”并选择“新建应用程序注册”。
5. 输入应用程序的友好名称，并选择应用程序类型。 根据应用程序类型将登录 URL 或重定向 URL 设置为基 URL。 单击“创建”，创建应用程序。
6. 在 Azure 门户中选择应用程序，然后单击“设置”。 在“设置”菜单中选择“密钥”并添加密钥 - 选择 1 年或 2 年密钥持续时间。 保存此页时，将显示密钥值，请复制该值并将其保存在安全位置 - 稍后在实现中配置应用程序设置时需要此密钥 - 此密钥值将不再显示，也无法通过其他任何方式检索，因此，在 Azure 门户中显示后，请尽快记下此值。

### 注册客户端应用程序
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在顶栏上单击你的帐户，并在“目录”列表下选择要注册应用程序的 Active Directory 租户。
3. 单击左侧导航栏中的“更多服务”，然后选择“Azure Active Directory”。
4. 单击“应用注册”并选择“新建应用程序注册”。
5. 输入应用程序的友好名称，并选择应用程序类型。 根据应用程序类型将登录 URL 或重定向 URL 设置为基 URL。 单击“创建”，创建应用程序。
6. 配置应用程序的权限 - 在“设置”菜单中，选择“所需权限”部分，单击“添加”，单击“选择 API”，然后在文本框中键入中间层服务的名称。 然后单击“选择权限”并选择“访问 <服务名称>”。

### 配置已知的客户端应用程序
在此方案中，中间层服务无需用户干预，就要获取用户对访问下游 API 的许可。 因此，在身份验证过程的许可步骤中必须提前显示授权访问下游 API 的选项。
为实现此目的，请遵循以下步骤将 Azure AD 中的客户端应用注册显式绑定到中间层服务的注册，从而将客户端和中间层所需的许可合并到单个对话中。
1. 导航到中间层服务注册，然后单击“清单”打开清单编辑器。
2. 在清单中找到 `knownClientApplications` 数组属性，然后将客户端应用程序的客户端 ID 添加为元素。
3. 单击保存按钮保存清单。

## 服务到服务访问令牌请求
若要请求访问令牌，请使用以下参数向特定于租户的 Azure AD 终结点发出 HTTP POST。

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```
有两种情况，具体取决于客户端应用程序选择由共享密钥还是由证书保护。

### 第一种情况：使用共享密钥访问令牌请求
使用共享密钥时，服务到服务访问令牌请求包含以下参数：

| 参数 |  | 说明 |
| --- | --- | --- |
| grant_type |必填 | 令牌请求的类型。 对于使用 JWT 的请求，该值必须是 **urn:ietf:params:oauth:grant-type:jwt-bearer**。 |
| assertion |必填 | 请求中使用的令牌值。 |
| client_id |必填 | 注册到 Azure AD 期间分配给调用服务的应用 ID。 若要查找应用 ID，请在 Azure 管理门户中，依次单击“Active Directory”、该目录、应用程序名称。 |
| client_secret |必填 | 在 Azure AD 中为调用服务注册的密钥。 注册时应已记下此值。 |
| resource |必填 | 接收服务（受保护资源）的应用 ID URI。 若要查找应用 ID URI，请在 Azure 管理门户中，依次单击“Active Directory”、该目录、应用程序名称、“所有设置”、“属性”。 |
| requested_token_use |必填 | 指定应如何处理请求。 在代理流中，该值必须是 **on_behalf_of**。 |
| 作用域 |必填 | 空格分隔的令牌请求范围的列表。 对于 OpenID Connect，必须指定范围 **openid**。|

#### 示例
以下 HTTP POST 请求 https://graph.windows.net Web API 的访问令牌。 `client_id` 标识请求访问令牌的服务。

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

### 第二种情况：使用证书访问令牌请求
使用证书的服务到服务访问令牌请求包含以下参数：

| 参数 |  | 说明 |
| --- | --- | --- |
| grant_type |必填 | 令牌请求的类型。 对于使用 JWT 的请求，该值必须是 **urn:ietf:params:oauth:grant-type:jwt-bearer**。 |
| assertion |必填 | 请求中使用的令牌值。 |
| client_id |必填 | 注册到 Azure AD 期间分配给调用服务的应用 ID。 若要查找应用 ID，请在 Azure 管理门户中，依次单击“Active Directory”、该目录、应用程序名称。 |
| client_assertion_type |必填 |值必须是 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |必填 | 断言（JSON Web 令牌），需使用作为凭据向应用程序注册的证书进行创建和签名。  有关如何注册证书以及断言的格式，请阅读[证书凭据](active-directory-certificate-credentials.md)。|
| resource |必填 | 接收服务（受保护资源）的应用 ID URI。 若要查找应用 ID URI，请在 Azure 管理门户中，依次单击“Active Directory”、该目录、应用程序名称、“所有设置”、“属性”。 |
| requested_token_use |必填 | 指定应如何处理请求。 在代理流中，该值必须是 **on_behalf_of**。 |
| 作用域 |必填 | 空格分隔的令牌请求范围的列表。 对于 OpenID Connect，必须指定范围 **openid**。|

请注意，参数几乎与共享密钥请求的参数相同，只不过 client_secret 参数替换为两个参数：client_assertion_type 和 client_assertion。

#### 示例
以下 HTTP POST 使用证书请求 https://graph.windows.net Web API 的访问令牌。 `client_id` 标识请求访问令牌的服务。

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## 服务到服务访问令牌响应
成功响应是具有以下参数的 JSON OAuth 2.0 响应。

| 参数 | 说明 |
| --- | --- |
| token_type |指示令牌类型值。 Azure AD 唯一支持的类型是 **Bearer**。 有关持有者令牌的详细信息，请参阅 [OAuth2.0 授权框架：持有者令牌用法 (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)。 |
| 作用域 |令牌中授予的访问权限的范围。 |
| expires_in |访问令牌有效的时间长度（以秒为单位）。 |
| expires_on |访问令牌的过期时间。 该日期表示为自 1970-01-01T0:0:0Z UTC 至过期时间的秒数。 此值用于确定缓存令牌的生存期。 |
| resource |接收服务（受保护资源）的应用 ID URI。 |
| access_token |请求的访问令牌。 调用方服务可以使用此令牌向接收方服务进行身份验证。 |
| id_token |请求的 ID 令牌。 调用服务可以使用此令牌验证用户的身份，并开始与用户建立会话。 |
| refresh_token |所请求的访问令牌的刷新令牌。 当前访问令牌过期后，调用方服务可以使用此令牌请求另一个访问令牌。 |

### 成功响应示例
以下示例演示对 https://graph.windows.net web API 的访问令牌请求的成功响应。

```
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.windows.net",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### 错误响应示例
如果已对下游 API 设置条件访问策略（如多重身份验证），则在尝试获取下游 API 的访问令牌时，Azure AD 令牌终结点会返回错误响应。 中间层服务应向客户端应用程序显示此错误，以便客户端应用程序可以提供用户交互，以满足条件访问策略。

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

## 使用访问令牌访问受保护资源
现在，中间层服务可以通过在 `Authorization` 标头中设置令牌，使用上面获取的令牌向下游 Web API 发 出身份验证请求。

### 示例
```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## 后续步骤
详细了解 OAuth 2.0 协议和使用客户端凭据执行服务到服务身份验证的其他方法。
* [在 Azure AD 中使用 OAuth 2.0 客户端凭据授予执行服务到服务身份验证](active-directory-protocols-oauth-service-to-service.md)
* [Azure AD 中的 OAuth 2.0](active-directory-protocols-oauth-code.md)

