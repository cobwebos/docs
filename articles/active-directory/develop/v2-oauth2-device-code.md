---
title: Use Microsoft identity platform to sign in users on browser-less devices | Azure
description: Build embedded and browser-less authentication flows using the device authorization grant.
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
ms.openlocfilehash: 9c948c59a90e0db17b4704188221cfc3c3d82310
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207605"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft identity platform and the OAuth 2.0 device authorization grant flow

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

The Microsoft identity platform supports the [device authorization grant](https://tools.ietf.org/html/rfc8628), which allows users to sign in to input-constrained devices such as a smart TV, IoT device, or printer.  若要启用此流，设备会让用户在另一台设备上的浏览器中访问一个网页，以进行登录。  用户登录后，设备可以获取所需的访问令牌和刷新令牌。  

This article describes how to program directly against the protocol in your application.  When possible, we recommend you use the supported Microsoft Authentication Libraries (MSAL) instead to [acquire tokens and call secured web APIs](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Also take a look at the [sample apps that use MSAL](sample-v2-code.md).

> [!NOTE]
> The Microsoft identity platform endpoint doesn't support all Azure Active Directory scenarios and features. To determine whether you should use the Microsoft identity platform endpoint, read about [Microsoft identity platform limitations](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>协议图

整个设备代码流如下图所示。 本文稍后介绍每个步骤。

![设备代码流](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>设备授权请求

The client must first check with the authentication server for a device and user code that's used to initiate authentication. 客户端从 `/devicecode` 终结点收集此请求。 在此请求中，客户端应包含需要从用户获取的权限。 从发送此请求的那一刻起，用户只有 15 分钟的时间（通常是 `expires_in` 的值）完成登录，因此，仅在用户指出他们已准备好登录时才发出此请求。

> [!TIP]
> 尝试在 Postman 中执行此请求！
> [![Try running this request in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| 参数 | 条件 | 描述 |
| --- | --- | --- |
| `tenant` | 需要 | Can be /common, /consumers, or /organizations.  It can also be the directory tenant that you want to request permission from in GUID or friendly name format.  |
| `client_id` | 需要 | The **Application (client) ID** that the [Azure portal – App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) experience assigned to your app. |
| `scope` | 推荐 | 希望用户同意的[范围](v2-permissions-and-consent.md)的空格分隔列表。  |

### <a name="device-authorization-response"></a>设备授权响应

成功响应是一个 JSON 对象，其中包含允许用户登录所需的信息。  

| 参数 | 格式 | 描述 |
| ---              | --- | --- |
|`device_code`     | 字符串 | 一个长字符串，用于验证客户端与授权服务器之间的会话。 The client uses this parameter to request the access token from the authorization server. |
|`user_code`       | 字符串 | A short string shown to the user that's used to identify the session on a secondary device.|
|`verification_uri`| URI | 用户在登录时应使用 `user_code` 转到的 URI。 |
|`expires_in`      | int | `device_code` 和 `user_code` 过期之前的秒数。 |
|`interval`        | int | 在发出下一个轮询请求之前客户端应等待的秒数。 |
| `message`        | 字符串 | 用户可读的字符串，包含面向用户的说明。 可以通过在请求中包含 `?mkt=xx-XX` 格式的**查询参数**并填充相应的语言区域性代码，将此字符串本地化。 |

> [!NOTE]
> The `verification_uri_complete` response field is not included or supported at this time.  We mention this because if you read the [standard](https://tools.ietf.org/html/rfc8628) you see that `verification_uri_complete` is listed as an optional part of the device code flow standard.

## <a name="authenticating-the-user"></a>对用户进行身份验证

After receiving the `user_code` and `verification_uri`, the client displays these to the user, instructing them to sign in using their mobile phone or PC browser.

If the user authenticates with a personal account (on /common or /consumers), they will be asked to sign in again in order to transfer authentication state to the device.  They will also be asked to provide consent, to ensure they are aware of the permissions being granted.  This does not apply to work or school accounts used to authenticate. 

尽管用户是在 `verification_uri` 中进行身份验证，但客户端应使用 `device_code` 来轮询所请求令牌的 `/token` 终结点。

``` 
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| 参数 | 需要 | 描述|
| -------- | -------- | ---------- |
| `tenant`  | 需要 | The same tenant or tenant alias used in the initial request. | 
| `grant_type` | 需要 | 必须是 `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | 需要 | 必须与初始请求中使用的 `client_id` 匹配。 |
| `device_code`| 需要 | 设备授权请求中返回的 `device_code`。  |

### <a name="expected-errors"></a>预期错误

The device code flow is a polling protocol so your client must expect to receive errors before the user has finished authenticating.  

| 错误 | 描述 | 客户端操作 |
| ------ | ----------- | -------------|
| `authorization_pending` | The user hasn't finished authenticating, but hasn't canceled the flow. | 在至少 `interval` 秒之后重复请求。 |
| `authorization_declined` | 最终用户拒绝了授权请求。| 停止轮询，并恢复到未经过身份验证状态。  |
| `bad_verification_code`| The `device_code` sent to the `/token` endpoint wasn't recognized. | 验证客户端是否在请求中发送了正确的 `device_code`。 |
| `expired_token` | 至少已经过去了 `expires_in` 秒，不再可以使用此 `device_code` 进行身份验证。 | Stop polling and revert to an unauthenticated state. |   

### <a name="successful-authentication-response"></a>成功的身份验证响应

成功的令牌响应如下：

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
| `token_type` | 字符串| 始终为“Bearer”。 |
| `scope` | 空格分隔的字符串 | 如果返回了访问令牌，则会列出该访问令牌的有效范围。 |
| `expires_in`| int | 包含的访问令牌在生效之前所要经过的秒数。 |
| `access_token`| 不透明字符串 | 针对请求的[范围](v2-permissions-and-consent.md)颁发。  |
| `id_token`   | JWT | 如果原始 `scope` 参数包含 `openid` 范围，则颁发。  |
| `refresh_token` | 不透明字符串 | 如果原始 `scope` 参数包含 `offline_access`，则颁发。  |

You can use the refresh token to acquire new access tokens and refresh tokens using the same flow documented in the [OAuth Code flow documentation](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
