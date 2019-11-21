---
title: Use Microsoft identity platform to sign in users using resource owner password credential (ROPC) grant | Azure
description: 支持使用资源所有者密码凭据授予的无浏览器身份验证流。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4504a1ae60aaac790ca15c120433159c2ff78fa
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207773"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credentials"></a>Microsoft identity platform and the OAuth 2.0 Resource Owner Password Credentials

Microsoft identity platform supports the [OAuth 2.0 Resource Owner Password Credentials (ROPC) grant](https://tools.ietf.org/html/rfc6749#section-4.3), which allows an application to sign in the user by directly handling their password.  This article describes how to program directly against the protocol in your application.  When possible, we recommend you use the supported Microsoft Authentication Libraries (MSAL) instead to [acquire tokens and call secured web APIs](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Also take a look at the [sample apps that use MSAL](sample-v2-code.md).

> [!WARNING]
> Microsoft recommends you do _not_ use the ROPC flow. In most scenarios, more secure alternatives are available and recommended. This flow requires a very high degree of trust in the application, and carries risks which are not present in other flows. You should only use this flow when other more secure flows can't be used.

> [!IMPORTANT]
>
> * The Microsoft identity platform endpoint only supports ROPC for Azure AD tenants, not personal accounts. 这意味着，必须使用特定于租户的终结点 (`https://login.microsoftonline.com/{TenantId_or_Name}`) 或 `organizations` 终结点。
> * 受邀加入 Azure AD 租户的个人帐户不能使用 ROPC。
> * 没有密码的帐户不能通过 ROPC 登录。 对于这种情况，建议改用适合应用的其他流。
> * 如果用户需使用多重身份验证 (MFA) 来登录应用程序，则系统会改为阻止用户。
> * ROPC is not supported in [hybrid identity federation](/azure/active-directory/hybrid/whatis-fed) scenarios (for example, Azure AD and ADFS used to authenticate on-premises accounts). If users are full-page redirected to an on-premises identity providers, Azure AD is not able to test the username and password against that identity provider. [Pass-through authentication](/azure/active-directory/hybrid/how-to-connect-pta) is supported with ROPC, however.

## <a name="protocol-diagram"></a>协议图

下图显示了 ROPC 流。

![Diagram showing the resource owner password credential flow](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>授权请求

The ROPC flow is a single request: it sends the client identification and user's credentials to the IDP, and then receives tokens in return. 在这样做之前，客户端必须请求用户的电子邮件地址 (UPN) 和密码。 在成功进行请求之后，客户端应立即以安全方式释放内存中的用户凭据， 而不得保存这些凭据。

> [!TIP]
> 尝试在 Postman 中执行此请求！
> [![Try running this request in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
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

| 参数 | 条件 | 描述 |
| --- | --- | --- |
| `tenant` | 需要 | 一个目录租户，用户需登录到其中。 这可采用 GUID 或友好名称格式。 此参数不能设置为 `common` 或 `consumers`，但可以设置为 `organizations`。 |
| `client_id` | 需要 | The Application (client) ID that the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page assigned to your app. | 
| `grant_type` | 需要 | 必须设置为 `password`。 |
| `username` | 需要 | 用户的电子邮件地址。 |
| `password` | 需要 | 用户的密码。 |
| `scope` | 推荐 | 以空格分隔的[范围](v2-permissions-and-consent.md)或权限的列表，这是应用需要的。 In an interactive flow, the admin or the user must consent to these scopes ahead of time. |
| `client_secret`| Sometimes required | If your app is a public client, then the `client_secret` or `client_assertion` cannot be included.  If the app is a confidential client, then it must be included. | 
| `client_assertion` | Sometimes required | A different form of `client_secret`, generated using a certificate.  See [certificate credentials](active-directory-certificate-credentials.md) for more details. | 

### <a name="successful-authentication-response"></a>成功的身份验证响应

The following example shows a successful token response:

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

| 参数 | 格式 | 描述 |
| --------- | ------ | ----------- |
| `token_type` | 字符串 | 始终设置为 `Bearer`。 |
| `scope` | 空格分隔的字符串 | 如果返回了访问令牌，则此参数会列出该访问令牌的有效范围。 |
| `expires_in`| int | 包含的访问令牌的有效时间，以秒为单位。 |
| `access_token`| 不透明字符串 | 针对请求的[范围](v2-permissions-and-consent.md)颁发。 |
| `id_token` | JWT | 如果原始 `scope` 参数包含 `openid` 范围，则颁发。 |
| `refresh_token` | 不透明字符串 | 如果原始 `scope` 参数包含 `offline_access`，则颁发。 |

可以运行 [OAuth 代码流文档](v2-oauth2-auth-code-flow.md#refresh-the-access-token)中描述的同一个流，使用刷新令牌来获取新的访问令牌和刷新令牌。

### <a name="error-response"></a>错误响应

如果用户未提供正确的用户名或密码，或者客户端未收到请求的许可，则身份验证会失败。

| 错误 | 描述 | 客户端操作 |
|------ | ----------- | -------------|
| `invalid_grant` | 身份验证失败 | 凭据不正确，或者客户端没有所请求范围的许可。 If the scopes aren't granted, a `consent_required` error will be returned. 如果发生这种情况，客户端应通过 Webview 或浏览器向用户发送交互式提示。 |
| `invalid_request` | 请求的构造方式不正确 | The grant type isn't supported on the `/common` or `/consumers` authentication contexts.  Use `/organizations` or a tenant ID instead. |

## <a name="learn-more"></a>了解更多

* 请通过[示例控制台应用程序](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)自行试用 ROPC。
* To determine whether you should use the v2.0 endpoint, read about [Microsoft identity platform limitations](active-directory-v2-limitations.md).
