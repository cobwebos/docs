---
title: 使用 Microsoft 标识平台，以便在无浏览器的设备上的用户登录 |Azure
description: 使用设备代码授予生成嵌入式无浏览器身份验证流。
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
ms.date: 06/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79718b14210bfdf139bca76db91c57c38a791434
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052247"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>Microsoft 标识平台和 OAuth 2.0 设备代码流

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Microsoft 标识平台支持[设备代码授予](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12)，它允许用户登录到输入受限的设备，例如智能电视、 IoT 设备或打印机。  若要启用此流，设备会让用户在另一台设备上的浏览器中访问一个网页，以进行登录。  用户登录后，设备可以获取所需的访问令牌和刷新令牌。  

> [!IMPORTANT]
> 在此期间，Microsoft 标识平台终结点仅支持设备流用于 Azure AD 租户，但不适用于个人帐户。  这意味着，您必须使用将设置为一个租户的终结点或`organizations`终结点。  这种支持将尽快启用。 
>
> 受邀加入 Azure AD 租户的个人帐户可以使用设备流授予，但只能在该租户的上下文中使用。
>
> 作为附加的说明， `verification_uri_complete` response 字段不包含或支持这一次。  

> [!NOTE]
> Microsoft 标识平台终结点不支持所有 Azure Active Directory 方案和功能。 若要确定是否应使用 Microsoft 标识平台终结点，请阅读 [Microsoft 标识平台限制](active-directory-v2-limitations.md)。

## <a name="protocol-diagram"></a>协议图

整个设备代码流如下图所示。 本文稍后介绍每个步骤。

![设备代码流](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>设备授权请求

客户端首先必须检查与用于启动身份验证的设备和用户代码的身份验证服务器。 客户端从 `/devicecode` 终结点收集此请求。 在此请求中，客户端应包含需要从用户获取的权限。 从发送此请求的那一刻起，用户只有 15 分钟的时间（通常是 `expires_in` 的值）完成登录，因此，仅在用户指出他们已准备好登录时才发出此请求。

> [!TIP]
> 尝试在 Postman 中执行此请求！
> [![在 Postman 中运行](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| 参数 | 条件 | 描述 |
| --- | --- | --- |
| `tenant` | 必选 |要向其请求权限的目录租户。 这可采用 GUID 或友好名称格式。  |
| `client_id` | 需要 | **应用程序 （客户端） ID**的[Azure 门户-应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)分配给您的应用程序的体验。 |
| `scope` | 建议 | 希望用户同意的[范围](v2-permissions-and-consent.md)的空格分隔列表。  |

### <a name="device-authorization-response"></a>设备授权响应

成功响应是一个 JSON 对象，其中包含允许用户登录所需的信息。  

| 参数 | 格式 | 描述 |
| ---              | --- | --- |
|`device_code`     | String | 一个长字符串，用于验证客户端与授权服务器之间的会话。 客户端使用此参数从授权服务器请求访问令牌。 |
|`user_code`       | String | 用于标识的辅助设备上的会话的用户显示一个短字符串。|
|`verification_uri`| URI | 用户在登录时应使用 `user_code` 转到的 URI。 |
|`expires_in`      | int | `device_code` 和 `user_code` 过期之前的秒数。 |
|`interval`        | int | 在发出下一个轮询请求之前客户端应等待的秒数。 |
| `message`        | String | 用户可读的字符串，包含面向用户的说明。 可以通过在请求中包含 `?mkt=xx-XX` 格式的**查询参数**并填充相应的语言区域性代码，将此字符串本地化。 |

## <a name="authenticating-the-user"></a>对用户进行身份验证

收到后`user_code`和`verification_uri`，客户端会显示给用户，指导他们使用其移动电话或电脑浏览器进行登录。  此外，客户端可以使用 QR 码或类似的机制来显示 `verfication_uri_complete`，这需要执行输入用户的 `user_code` 的步骤。

尽管用户是在 `verification_uri` 中进行身份验证，但客户端应使用 `device_code` 来轮询所请求令牌的 `/token` 终结点。

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| 参数 | 必选 | 描述|
| -------- | -------- | ---------- |
| `grant_type` | 需要 | 必须是 `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | 必选 | 必须与初始请求中使用的 `client_id` 匹配。 |
| `device_code`| 需要 | 设备授权请求中返回的 `device_code`。  |

### <a name="expected-errors"></a>预期错误

设备代码流是轮询协议，因此必须预料到您的客户端要在用户进行身份验证完成之前收到错误。  

| 错误 | 描述 | 客户端操作 |
| ------ | ----------- | -------------|
| `authorization_pending` | 用户尚未完成身份验证，但尚未取消流。 | 在至少 `interval` 秒之后重复请求。 |
| `authorization_declined` | 最终用户拒绝了授权请求。| 停止轮询，并恢复到未经过身份验证状态。  |
| `bad_verification_code`| `device_code`发送到`/token`终结点无法识别。 | 验证客户端是否在请求中发送了正确的 `device_code`。 |
| `expired_token` | 至少已经过去了 `expires_in` 秒，不再可以使用此 `device_code` 进行身份验证。 | 停止轮询并恢复到未经过身份验证的状态。 |

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
| `token_type` | String| 始终为“Bearer”。 |
| `scope` | 空格分隔的字符串 | 如果返回了访问令牌，则会列出该访问令牌的有效范围。 |
| `expires_in`| int | 包含的访问令牌在生效之前所要经过的秒数。 |
| `access_token`| 不透明字符串 | 针对请求的[范围](v2-permissions-and-consent.md)颁发。  |
| `id_token`   | JWT | 如果原始 `scope` 参数包含 `openid` 范围，则颁发。  |
| `refresh_token` | 不透明字符串 | 如果原始 `scope` 参数包含 `offline_access`，则颁发。  |

可以使用刷新令牌获取新访问令牌和刷新令牌使用相同的流中所述[OAuth 代码流文档](v2-oauth2-auth-code-flow.md#refresh-the-access-token)。  
