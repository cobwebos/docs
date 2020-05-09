---
title: 使用资源所有者密码凭据授予进行登录 | Azure
titleSuffix: Microsoft identity platform
description: 支持使用资源所有者密码凭据 (ROPC) 授予的无浏览器身份验证流。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 26b3cb343aba2d45d5a14944a7f8856715bca100
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690103"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Microsoft 标识平台和 OAuth 2.0 资源所有者密码凭据

Microsoft 标识平台支持 [OAuth 2.0 资源所有者密码凭据 (ROPC) 授予](https://tools.ietf.org/html/rfc6749#section-4.3)，后者允许应用程序通过直接处理用户的密码来登录用户。  本文介绍如何在应用程序中直接针对协议进行编程。  如果可能，建议你改用受支持的 Microsoft 身份验证库 (MSAL) 来[获取令牌并调用受保护的 Web API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。  另请参阅[使用 MSAL 的示例应用](sample-v2-code.md)。

> [!WARNING]
> Microsoft 建议不要使用  ROPC 流。 在大多数情况下，可以使用我们建议的更安全的替代方案。 此流需要应用程序中存在很高程度的信任，并且带有在其他流中不存在的风险。 仅当无法使用其他更安全的流时，才使用此流。

> [!IMPORTANT]
>
> * Microsoft 标识平台终结点仅支持 Azure AD 租户的 ROPC，而不支持个人帐户。 这意味着，必须使用特定于租户的终结点 (`https://login.microsoftonline.com/{TenantId_or_Name}`) 或 `organizations` 终结点。
> * 受邀加入 Azure AD 租户的个人帐户不能使用 ROPC。
> * 没有密码的帐户不能通过 ROPC 登录。 对于这种情况，建议改用适合应用的其他流。
> * 如果用户需使用多重身份验证 (MFA) 来登录应用程序，则系统会改为阻止用户。
> * [混合标识联合身份验证](/azure/active-directory/hybrid/whatis-fed)方案（例如，用于对本地帐户进行身份验证的 Azure AD 和 ADFS）不支持 ROPC。 如果用户已整页重定向到本地标识提供者，则 Azure AD 将无法针对该标识提供者测试用户名和密码。 不过，ROPC 支持[传递身份验证](/azure/active-directory/hybrid/how-to-connect-pta)。

## <a name="protocol-diagram"></a>协议图

下图显示了 ROPC 流。

![显示资源所有者密码凭据流的关系图](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>授权请求

ROPC 流是单一请求：它将客户端标识和用户的凭据发送到 IDP，然后接收返回的令牌。 在执行此操作之前，客户端必须请求用户的电子邮件地址 (UPN) 和密码。 成功请求后，客户端应立即从内存中安全地释放用户的凭据。 客户端不得保存客户凭据。

> [!TIP]
> 尝试在 Postman 中执行此请求！
> [![尝试在 Postman 中运行此请求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```HTTP
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| 参数 | 条件 | 说明 |
| --- | --- | --- |
| `tenant` | 必须 | 一个目录租户，用户需登录到其中。 此参数可采用 GUID 或友好名称格式。 此参数不能设置为 `common` 或 `consumers`，但可以设置为 `organizations`。 |
| `client_id` | 必选 | [Azure 门户 - 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页分配给应用的应用程序（客户端）ID。 |
| `grant_type` | 必选 | 必须设置为 `password`。 |
| `username` | 必选 | 用户的电子邮件地址。 |
| `password` | 必须 | 用户的密码。 |
| `scope` | 建议 | 以空格分隔的[范围](v2-permissions-and-consent.md)或权限的列表，这是应用需要的。 在交互式流中，管理员或用户必须提前同意这些范围。 |
| `client_secret`| 有时必需 | 如果应用是公共客户端，则无法包括 `client_secret` 或 `client_assertion`。  如果应用是机密客户端，则它必须包括在内。 |
| `client_assertion` | 有时必需 | 使用证书生成的不同形式的 `client_secret`。  有关更多详细信息，请参阅[证书凭据](active-directory-certificate-credentials.md)。 |

### <a name="successful-authentication-response"></a>成功的身份验证响应

下面的示例显示成功的令牌响应：

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| 参数 | 格式 | 说明 |
| --------- | ------ | ----------- |
| `token_type` | String | 始终设置为 `Bearer`。 |
| `scope` | 空格分隔的字符串 | 如果返回访问令牌，此参数会列出访问令牌有效的范围。 |
| `expires_in`| int | 包含的访问令牌有效的秒数。 |
| `access_token`| 不透明字符串 | 为请求的[作用域](v2-permissions-and-consent.md)颁发。 |
| `id_token` | JWT | 如果原始 `scope` 参数包含 `openid` 范围，则颁发。 |
| `refresh_token` | 不透明字符串 | 如果原始 `scope` 参数包含 `offline_access`，则颁发。 |

可以运行 [OAuth 代码流文档](v2-oauth2-auth-code-flow.md#refresh-the-access-token)中描述的同一个流，使用刷新令牌来获取新的访问令牌和刷新令牌。

### <a name="error-response"></a>错误响应

如果用户未提供正确的用户名或密码，或者客户端未收到请求的许可，则身份验证会失败。

| 错误 | 说明 | 客户端操作 |
|------ | ----------- | -------------|
| `invalid_grant` | 身份验证失败 | 凭据不正确，或者客户端没有所请求范围的许可。 如果没有授予范围，则会返回 `consent_required` 错误。 如果发生这种情况，客户端应通过 Webview 或浏览器向用户发送交互式提示。 |
| `invalid_request` | 请求的构造方式不正确 | 授予类型在 `/common` 或 `/consumers` 身份验证上下文中不受支持。  请改用 `/organizations` 或租户 ID。 |

## <a name="learn-more"></a>了解详细信息

* 请通过[示例控制台应用程序](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)自行试用 ROPC。
* 若要确定是否应使用 v2.0 终结点，请阅读 [Microsoft 标识平台限制](active-directory-v2-limitations.md)。
