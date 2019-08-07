---
title: Azure AD 代表草案规范的服务到服务身份验证 OAuth 2.0 |Microsoft Docs
description: 本文介绍如何通过 OAuth2.0 代理流使用 HTTP 消息实现服务到服务身份验证。
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf0c639dd5fb678af7fca9224292218331b10ee6
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834753"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>代理流中使用委托用户标识的服务到服务调用

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

OAuth 2.0 代理 (OBO) 流使调用服务或 Web API 的应用程序能够将用户身份验证传递给其他服务或 Web API。 OBO 流通过请求链传播委托用户权标识和权限。 要使中间层服务向下游服务发出身份验证请求，该服务必须代表用户保护 Azure Active Directory (Azure AD) 提供的访问令牌。

> [!IMPORTANT]
> 从 2018 年 5 月起，`id_token` 不能用于代理流。  单页应用 (SPA) 必须将访问令牌传递给中间层机密客户端，才能执行 OBO 流。 有关能够执行代理调用的客户端的更多详细信息，请参阅[限制](#client-limitations)。

## <a name="on-behalf-of-flow-diagram"></a>代理流示意图

在使用 [OAuth 2.0 授权代码授权流](v1-protocols-oauth-code.md)的应用程序上对用户进行身份验证后，启动 OBO 流。 此时，应用程序将访问令牌（令牌 A）发送到包含用户声明并同意访问 API A 的中间层 Web API (API A)。然后，API A 向下游 Web API (API B) 发出经过身份验证的请求。

这些步骤构成了代理流：![以 OAuth 2.0 代理流的顺序显示步骤](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. 客户端应用程序使用令牌 A 向 API A 发出请求。
1. API A 向 Azure AD 令牌颁发终结点进行身份验证并请求访问 API B 的令牌。
1. Azure AD 令牌颁发终结点使用令牌 A 验证 API A 的凭据，并颁发访问 API B 的令牌（令牌 B）。
1. 向 API B 发出的请求在授权标头中包含令牌 B。
1. API B 返回受保护资源中的数据。

>[!NOTE]
>在用于请求下游服务的令牌的访问令牌中，受众声明必须是发出 OBO 请求的服务 ID。 该令牌还必须使用 Azure Active Directory 全局签名密钥（这是通过门户中的“应用注册”注册的应用程序的默认密钥）进行签名。

## <a name="register-the-application-and-service-in-azure-ad"></a>在 Azure AD 中注册应用程序和服务

在 Azure AD 中注册中间层服务和客户端应用程序。

### <a name="register-the-middle-tier-service"></a>注册中间层服务

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部栏中选择帐户，并在“目录”列表下为应用程序选择 Active Directory 租户。
1. 在左窗格中，选择“更多服务”，然后选择“Azure Active Directory”。
1. 依次选择“应用注册”、“新建注册”。
1. 输入应用程序的友好名称，并选择应用程序类型。
1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。
1. 将重定向 URI 设置为基 URL。
1. 选择“注册”以创建应用程序。
1. 在退出 Azure 门户之前生成客户端密码。
1. 在 Azure 门户中，选择应用程序，然后选择“证书和机密”。
1. 选择“新建客户端密码”并添加持续时间为一年或两年的机密。
1. 保存此页时，Azure 门户将显示机密值。 复制机密值并将其保存在安全位置。

> [!IMPORTANT]
> 在实现中配置应用程序设置时需要此机密。 此机密值不会再次显示，并且无法通过任何其他方式检索。 因此，当它在 Azure 门户中可见时请立即记录。

### <a name="register-the-client-application"></a>注册客户端应用程序

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部栏中选择帐户，并在“目录”列表下为应用程序选择 Active Directory 租户。
1. 在左窗格中，选择“更多服务”，然后选择“Azure Active Directory”。
1. 依次选择“应用注册”、“新建注册”。
1. 输入应用程序的友好名称，并选择应用程序类型。
1. 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。
1. 将重定向 URI 设置为基 URL。
1. 选择“注册”以创建应用程序。
1. 为应用程序配置权限。 在“API 权限”中，依次选择“添加权限”、“我的 API”。
1. 在文本字段中键入中间层服务的名称。
1. 选择 "**选择权限**", 然后选择 "**访问\<服务名称 >** "。

### <a name="configure-known-client-applications"></a>配置已知的客户端应用程序

在此方案中，中间层服务在没有用户干预的情况下，需要获取用户对访问下游 API 的许可。 在身份验证过程的许可步骤中必须提前显示授权访问下游 API 的选项。

按照以下步骤将 Azure AD 中的客户端应用注册与中间层服务的注册显式绑定。 此操作将客户端和中间层所需的许可合并到一个对话框中。

1. 转到中间层服务注册，然后选择“清单”以打开清单编辑器。
1. 找到 `knownClientApplications` 数组属性，然后将客户端应用程序的客户端 ID 添加为元素。
1. 选择“保存”以保存清单。

## <a name="service-to-service-access-token-request"></a>服务到服务访问令牌请求

要请求访问令牌，请使用以下参数向特定于租户的 Azure AD 终结点发出 HTTP POST：

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

客户端应用程序由共享密钥或证书提供保护。

### <a name="first-case-access-token-request-with-a-shared-secret"></a>第一种情况：使用共享机密访问令牌请求

使用共享密钥时，服务到服务访问令牌请求包含以下参数：

| 参数 |  | 描述 |
| --- | --- | --- |
| grant_type |必需 | 令牌请求的类型。 OBO 请求使用 JSON Web 令牌 (JWT)，因此值必须是 urn:ietf:params:oauth:grant-type:jwt-bearer。 |
| assertion |必需 | 请求中使用的访问令牌值。 |
| client_id |必需 | 在注册到 Azure AD 期间分配给调用服务的应用 ID。 要在 Azure 门户中查找应用 ID，请选择“Active Directory”，选择目录，然后选择应用程序名称。 |
| client_secret |必需 | 在 Azure AD 中为调用服务注册的密钥。 注册时应已记下此值。 |
| 资源 |必需 | 接收服务（受保护资源）的应用 ID URI。 要在 Azure 门户中查找应用 ID URI，请选择“Active Directory”并选择目录。 选择应用程序名称，选择“所有设置”，然后选择“属性”。 |
| requested_token_use |必需 | 指定应如何处理请求。 在代理流中，该值必须是 **on_behalf_of**。 |
| 范围 |必需 | 空格分隔的令牌请求范围的列表。 对于 OpenID Connect，必须指定范围 **openid**。|

#### <a name="example"></a>示例

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

### <a name="second-case-access-token-request-with-a-certificate"></a>第二种情况：使用证书访问令牌请求

使用证书的服务到服务访问令牌请求包含以下参数：

| 参数 |  | 描述 |
| --- | --- | --- |
| grant_type |必需 | 令牌请求的类型。 OBO 请求使用 JWT 访问令牌，因此值必须是 urn:ietf:params:oauth:grant-type:jwt-bearer。 |
| assertion |必需 | 请求中使用的令牌值。 |
| client_id |必需 | 在注册到 Azure AD 期间分配给调用服务的应用 ID。 要在 Azure 门户中查找应用 ID，请选择“Active Directory”，选择目录，然后选择应用程序名称。 |
| client_assertion_type |必需 |值必须是 `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |必需 | JSON Web 令牌使用作为凭据向应用程序注册的证书进行创建和签名。 请参阅[证书凭据](active-directory-certificate-credentials.md)了解断言格式以及如何注册证书。|
| 资源 |必需 | 接收服务（受保护资源）的应用 ID URI。 要在 Azure 门户中查找应用 ID URI，请选择“Active Directory”并选择目录。 选择应用程序名称，选择“所有设置”，然后选择“属性”。 |
| requested_token_use |必需 | 指定应如何处理请求。 在代理流中，该值必须是 **on_behalf_of**。 |
| 范围 |必需 | 空格分隔的令牌请求范围的列表。 对于 OpenID Connect，必须指定范围 **openid**。|

这些参数与共享密钥请求几乎相同，只是 `client_secret parameter` 被以下两个参数替换：`client_assertion_type` 和 `client_assertion`。

#### <a name="example"></a>示例

以下 HTTP POST 请求具有证书的 https://graph.windows.net Web API 的访问令牌。 `client_id` 标识请求访问令牌的服务。

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

## <a name="service-to-service-access-token-response"></a>服务到服务访问令牌响应

成功响应是具有以下参数的 JSON OAuth 2.0 响应：

| 参数 | 描述 |
| --- | --- |
| token_type |指示令牌类型值。 Azure AD 唯一支持的类型是 **Bearer**。 有关持有者令牌的详细信息，请参阅 [OAuth 2.0 授权框架：持有者令牌用法 (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)。 |
| 范围 |令牌中授予的访问权限的范围。 |
| expires_in |访问令牌有效的时间长度（以秒为单位）。 |
| expires_on |访问令牌的过期时间。 该日期表示为自 1970-01-01T0:0:0Z UTC 至过期时间的秒数。 此值用于确定缓存令牌的生存期。 |
| 资源 |接收服务（受保护资源）的应用 ID URI。 |
| access_token |请求的访问令牌。 调用方服务可以使用此令牌向接收方服务进行身份验证。 |
| id_token |请求的 ID 令牌。 调用服务可以使用此令牌验证用户的身份，并开始与用户建立会话。 |
| refresh_token |所请求的访问令牌的刷新令牌。 当前访问令牌过期后，调用方服务可以使用此令牌请求另一个访问令牌。 |

### <a name="success-response-example"></a>成功响应示例

以下示例演示对 https://graph.windows.net Web API 的访问令牌请求的成功响应。

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

### <a name="error-response-example"></a>错误响应示例

Azure AD 令牌终结点在尝试获取通过条件访问策略 (例如多重身份验证) 设置的下游 API 的访问令牌时, 会返回错误响应。 中间层服务应向客户端应用程序呈现此错误, 以便客户端应用程序可以提供用户交互, 以满足条件访问策略。

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

中间层服务可以通过在 `Authorization` 标头中设置令牌，使用获取的访问令牌向下游 Web API 发出经过身份验证的请求。

### <a name="example"></a>示例

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>使用 OAuth2.0 OBO 流获得的 SAML 断言

某些基于 OAuth 的 Web 服务需要访问在非交互式流中接受 SAML 断言的其他 Web 服务 API。 Azure Active Directory 可以提供 SAML 断言，以响应将基于 SAML 的 Web 服务用作目标资源的代理流。

>[!NOTE]
>这是非标准的 OAuth 2.0 代理流扩展，它允许基于 OAuth2 的应用程序访问使用 SAML 令牌的 Web 服务 API 终结点。

> [!TIP]
> 当从前端 Web 应用程序调用受 SAML 保护的 Web 服务时，只需调用 API 并使用用户的现有会话启动正常的交互式身份验证流。 当服务到服务调用需要 SAML 令牌来提供用户上下文时，只需使用 OBO 流。

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>使用带共享密钥的 OBO 请求获取 SAML 令牌

SAML 断言的服务到服务请求包含以下参数：

| 参数 |  | 描述 |
| --- | --- | --- |
| grant_type |必需 | 令牌请求的类型。 对于使用 JWT 的请求，该值必须是 urn:ietf:params:oauth:grant-type:jwt-bearer。 |
| assertion |必需 | 请求中使用的访问令牌值。|
| client_id |必需 | 在注册到 Azure AD 期间分配给调用服务的应用 ID。 要在 Azure 门户中查找应用 ID，请选择“Active Directory”，选择目录，然后选择应用程序名称。 |
| client_secret |必需 | 在 Azure AD 中为调用服务注册的密钥。 注册时应已记下此值。 |
| 资源 |必需 | 接收服务（受保护资源）的应用 ID URI。 这是将成为 SAML 令牌受众的资源。 要在 Azure 门户中查找应用 ID URI，请选择“Active Directory”并选择目录。 选择应用程序名称，选择“所有设置”，然后选择“属性”。 |
| requested_token_use |必需 | 指定应如何处理请求。 在代理流中，该值必须是 **on_behalf_of**。 |
| requested_token_type | 必需 | 指定请求令牌的类型。 值可以是 urn:ietf:params:oauth:token-type:saml2 或 urn:ietf:params:oauth:token-type:saml1，具体取决于访问资源的要求。 |

响应包含以 UTF8 和 Base64url 编码的 SAML 令牌。

- **源自 OBO 调用的 SAML 断言的 SubjectConfirmationData**：如果目标应用程序需要 SubjectConfirmationData 中的接收方值，则该值必须是资源应用程序配置中的非通配符回复 URL。
- **SubjectConfirmationData 节点**：该节点不能包含 InResponseTo 属性，因为它不是 SAML 响应的一部分。 接收 SAML 令牌的应用程序必须能够在没有 InResponseTo 属性的情况下接受 SAML 断言。

- **许可**：必须授予许可，才能接收包含 OAuth 流上用户数据的 SAML 令牌。 有关权限和获取管理员许可的信息，请参阅 [Azure Active Directory v1.0 终结点中的权限和许可](https://docs.microsoft.com/azure/active-directory/develop/v1-permissions-and-consent)。

### <a name="response-with-saml-assertion"></a>使用 SAML 断言进行响应

| 参数 | 描述 |
| --- | --- |
| token_type |指示令牌类型值。 Azure AD 唯一支持的类型是 **Bearer**。 有关持有者令牌的详细信息，请参阅 [OAuth 2.0 授权框架：持有者令牌用法 (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)。 |
| 范围 |令牌中授予的访问权限的范围。 |
| expires_in |访问令牌有效的时间长度（以秒为单位）。 |
| expires_on |访问令牌的过期时间。 该日期表示为自 1970-01-01T0:0:0Z UTC 至过期时间的秒数。 此值用于确定缓存令牌的生存期。 |
| 资源 |接收服务（受保护资源）的应用 ID URI。 |
| access_token |返回 SAML 断言的参数。 |
| refresh_token |刷新令牌。 当前 SAML 断言过期后，调用方服务可以使用此令牌请求另一个访问令牌。 |

- token_type：Bearer
- expires_in：3296
- ext_expires_in：0
- expires_on：1529627844
- 资源：`https://api.contoso.com`
- access_token：\<SAML 断言\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token：\<刷新令牌\>

## <a name="client-limitations"></a>客户端限制

具有通配符回复 URL 的公共客户端无法为 OBO 流使用 `id_token`。 但是，机密客户端仍可兑现通过隐式授予流获取的访问令牌，即使公共客户端已注册通配符重定向 URI。

## <a name="next-steps"></a>后续步骤

详细了解 OAuth 2.0 协议和执行使用客户端凭据的服务到服务身份验证的其他方法：

* [在 Azure AD 中使用 OAuth 2.0 客户端凭据授予执行服务到服务身份验证](v1-oauth2-client-creds-grant-flow.md)
* [Azure AD 中的 OAuth 2.0](v1-protocols-oauth-code.md)
