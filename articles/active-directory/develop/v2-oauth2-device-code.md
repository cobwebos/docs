---
title: OAuth 2.0 设备代码流 |Microsoft
titleSuffix: Microsoft identity platform
description: 无需浏览器即可登录用户。 使用 device authorization grant 构建嵌入和无浏览器的身份验证流。
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
ms.openlocfilehash: 1035d5cd7c992bea74180b482bb8e3c2c9e0f461
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423253"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft 标识平台和 OAuth 2.0 device authorization grant flow

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Microsoft 标识平台支持[设备授权授予](https://tools.ietf.org/html/rfc8628)，这允许用户登录到输入设备（例如智能电视、IoT 设备或打印机）。  若要启用此流，设备会让用户在另一台设备上的浏览器中访问一个网页，以进行登录。  用户登录后，设备可以获取所需的访问令牌和刷新令牌。  

本文介绍如何在应用程序中直接对协议进行编程。  如果可能，我们建议你改为使用受支持的 Microsoft 身份验证库（MSAL）来[获取令牌并调用受保护的 Web api](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。  另请参阅[使用 MSAL 的示例应用](sample-v2-code.md)。

> [!NOTE]
> Microsoft 标识平台终结点并不支持所有 Azure Active Directory 方案和功能。 若要确定是否应使用 Microsoft 标识平台终结点，请阅读[microsoft 标识平台限制](active-directory-v2-limitations.md)。

## <a name="protocol-diagram"></a>协议图

整个设备代码流如下图所示。 本文稍后介绍每个步骤。

![设备代码流](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>设备授权请求

客户端必须首先向身份验证服务器核实用于启动身份验证的设备和用户代码。 客户端从 `/devicecode` 终结点收集此请求。 在此请求中，客户端应包含需要从用户获取的权限。 从发送此请求的那一刻起，用户只有 15 分钟的时间（通常是 `expires_in` 的值）完成登录，因此，仅在用户指出他们已准备好登录时才发出此请求。

> [!TIP]
> 尝试在 Postman 中执行此请求！
> [![尝试在 Postman 中运行此请求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| 参数 | 条件 | Description |
| --- | --- | --- |
| `tenant` | 需要 | 可以是/common、/consumers 或/organizations。  它还可以是要从 GUID 或友好名称格式请求权限的目录租户。  |
| `client_id` | 需要 | Azure 门户的**应用程序（客户端） ID** [-应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)分配给应用程序的体验。 |
| `scope` | 推荐 | 希望用户同意的[范围](v2-permissions-and-consent.md)的空格分隔列表。  |

### <a name="device-authorization-response"></a>设备授权响应

成功响应是一个 JSON 对象，其中包含允许用户登录所需的信息。  

| 参数 | 格式 | Description |
| ---              | --- | --- |
|`device_code`     | String | 一个长字符串，用于验证客户端与授权服务器之间的会话。 客户端使用此参数来请求授权服务器的访问令牌。 |
|`user_code`       | String | 显示给用户的简短字符串，用于标识辅助设备上的会话。|
|`verification_uri`| URI | 用户在登录时应使用 `user_code` 转到的 URI。 |
|`expires_in`      | int | `device_code` 和 `user_code` 过期之前的秒数。 |
|`interval`        | int | 在发出下一个轮询请求之前客户端应等待的秒数。 |
| `message`        | String | 用户可读的字符串，包含面向用户的说明。 可以通过在请求中包含 `?mkt=xx-XX` 格式的**查询参数**并填充相应的语言区域性代码，将此字符串本地化。 |

> [!NOTE]
> 此时不包括或支持 `verification_uri_complete` 响应字段。  我们提到这样做的原因是，如果您阅读了[标准版](https://tools.ietf.org/html/rfc8628)，则会看到 `verification_uri_complete` 作为设备代码流标准的可选部分列出。

## <a name="authenticating-the-user"></a>对用户进行身份验证

接收到 `user_code` 并 `verification_uri`之后，客户端会向用户显示这些用户，指示他们使用移动电话或 PC 浏览器登录。

如果用户使用个人帐户（在/common 或/consumers 上）进行身份验证，则系统会要求再次登录，以便将身份验证状态传输到设备。  还将要求他们提供许可，以确保他们知道所授予的权限。  这不适用于用于身份验证的工作或学校帐户。 

尽管用户是在 `verification_uri` 中进行身份验证，但客户端应使用 `device_code` 来轮询所请求令牌的 `/token` 终结点。

``` 
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| 参数 | 需要 | Description|
| -------- | -------- | ---------- |
| `tenant`  | 需要 | 初始请求中使用的同一个租户或租户别名。 | 
| `grant_type` | 需要 | 必须是 `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | 需要 | 必须与初始请求中使用的 `client_id` 匹配。 |
| `device_code`| 需要 | 设备授权请求中返回的 `device_code`。  |

### <a name="expected-errors"></a>预期错误

设备代码流是一种轮询协议，因此客户端必须在用户完成身份验证之前接收到错误。  

| 错误 | Description | 客户端操作 |
| ------ | ----------- | -------------|
| `authorization_pending` | 用户尚未完成身份验证，但未取消流。 | 在至少 `interval` 秒之后重复请求。 |
| `authorization_declined` | 最终用户拒绝了授权请求。| 停止轮询，并恢复到未经过身份验证状态。  |
| `bad_verification_code`| 无法识别发送到 `/token` 终结点的 `device_code`。 | 验证客户端是否在请求中发送了正确的 `device_code`。 |
| `expired_token` | 至少已经过去了 `expires_in` 秒，不再可以使用此 `device_code` 进行身份验证。 | 停止轮询并还原到未经过身份验证的状态。 |   

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

| 参数 | 格式 | Description |
| --------- | ------ | ----------- |
| `token_type` | String| 始终为“Bearer”。 |
| `scope` | 空格分隔的字符串 | 如果返回了访问令牌，则会列出该访问令牌的有效范围。 |
| `expires_in`| int | 包含的访问令牌在生效之前所要经过的秒数。 |
| `access_token`| 不透明字符串 | 针对请求的[范围](v2-permissions-and-consent.md)颁发。  |
| `id_token`   | JWT | 如果原始 `scope` 参数包含 `openid` 范围，则颁发。  |
| `refresh_token` | 不透明字符串 | 如果原始 `scope` 参数包含 `offline_access`，则颁发。  |

可以使用刷新令牌获取新的访问令牌，并使用[OAuth 代码流文档](v2-oauth2-auth-code-flow.md#refresh-the-access-token)中所述的相同流来刷新令牌。  
