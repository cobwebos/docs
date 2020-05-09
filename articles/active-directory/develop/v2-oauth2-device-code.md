---
title: OAuth 2.0 设备代码流 | Azure
titleSuffix: Microsoft identity platform
description: 无需浏览器即可登录用户。 使用设备权限授予生成嵌入式无浏览器身份验证流。
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
ms.openlocfilehash: a0677603f02b429c269c0f93ef348b2b1d717a9f
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82689769"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft 标识平台和 OAuth 2.0 设备权限授予流

Microsoft 标识平台支持[设备权限授予](https://tools.ietf.org/html/rfc8628)，可让用户登录到智能电视、IoT 设备或打印机等输入受限设备。  若要启用此流，设备会让用户在另一台设备上的浏览器中访问一个网页，以进行登录。  用户登录后，设备可以获取所需的访问令牌和刷新令牌。

本文介绍如何在应用程序中直接针对协议进行编程。  如果可能，建议你改用受支持的 Microsoft 身份验证库 (MSAL) 来[获取令牌并调用受保护的 Web API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。  另请参阅[使用 MSAL 的示例应用](sample-v2-code.md)。

## <a name="protocol-diagram"></a>协议图

整个设备代码流类似于下图。 本文稍后介绍每个步骤。

![设备代码流](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>设备授权请求

客户端必须首先向身份验证服务器核实用于启动身份验证的设备和用户代码。 客户端从 `/devicecode` 终结点收集此请求。 在此请求中，客户端还应包括需要从用户获取的权限。 从发送此请求的那一刻起，用户只有 15 分钟的时间（通常是 `expires_in` 的值）完成登录，因此，仅在用户指出他们已准备好登录时才发出此请求。

> [!TIP]
> 尝试在 Postman 中执行此请求！
> [![尝试在 Postman 中运行此请求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| 参数 | 条件 | 说明 |
| --- | --- | --- |
| `tenant` | 必需 | 可以是/common、/consumers 或/organizations。  它也可以是要以 GUID 或友好名称格式向其请求权限的目录租户。  |
| `client_id` | 必须 | Azure 门户的**应用程序（客户端） ID** [-应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)分配给应用程序的体验。 |
| `scope` | 建议 | 希望用户同意的[范围](v2-permissions-and-consent.md)的空格分隔列表。  |

### <a name="device-authorization-response"></a>设备授权响应

成功的响应是一个 JSON 对象，其中包含允许用户登录所需的信息。

| 参数 | 格式 | 说明 |
| ---              | --- | --- |
|`device_code`     | String | 用于验证客户端和授权服务器之间的会话的长字符串。 客户端使用此参数来请求授权服务器提供访问令牌。 |
|`user_code`       | String | 向用户显示的短字符串，用于标识辅助设备上的会话。|
|`verification_uri`| URI | 用户在登录时应使用 `user_code` 转到的 URI。 |
|`expires_in`      | int | `device_code` 和 `user_code` 过期之前的秒数。 |
|`interval`        | int | 客户端在轮询请求之间应等待的秒数。 |
| `message`        | String | 用户可读的字符串，其中包含有关用户的说明。 可以通过在请求中包含 `?mkt=xx-XX` 格式的**查询参数**并填充相应的语言区域性代码，将此字符串本地化。 |

> [!NOTE]
> 此时不包括或不支持 `verification_uri_complete` 响应字段。  我们提到这一点是因为如果你阅读[标准](https://tools.ietf.org/html/rfc8628)，你会看到 `verification_uri_complete` 作为设备代码流标准的可选部分列出。

## <a name="authenticating-the-user"></a>对用户进行身份验证

收到 `user_code` 和 `verification_uri` 后，客户端会向用户显示这些信息，指示他们使用移动电话或电脑浏览器登录。

如果用户使用个人帐户（在/common 或/consumers 上）进行身份验证，则系统会要求再次登录，以便将身份验证状态传输到设备。  还将要求他们提供许可，以确保他们知道所授予的权限。  这不适用于用于身份验证的工作或学校帐户。

尽管用户是在 `verification_uri` 中进行身份验证，但客户端应使用 `device_code` 来轮询所请求令牌的 `/token` 终结点。

```HTTP
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| 参数 | 必须 | 描述|
| -------- | -------- | ---------- |
| `tenant`  | 必需 | 初始请求中使用的同一租户或租户别名。 |
| `grant_type` | 必须 | 必须是 `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | 必须 | 必须与初始请求中使用的 `client_id` 匹配。 |
| `device_code`| 必须 | 设备授权请求中返回的 `device_code`。  |

### <a name="expected-errors"></a>预期错误

由于设备代码流是一个轮询协议，因此，客户端必须预料到在用户完成身份验证之前会收到错误。

| 错误 | 说明 | 客户端操作 |
| ------ | ----------- | -------------|
| `authorization_pending` | 用户尚未完成身份验证，但未取消流。 | 在至少 `interval` 秒之后重复请求。 |
| `authorization_declined` | 最终用户拒绝了授权请求。| 停止轮询，并恢复到未经过身份验证状态。  |
| `bad_verification_code`| 未识别已发送到 `/token` 终结点的 `device_code`。 | 验证客户端是否在请求中发送了正确的 `device_code`。 |
| `expired_token` | 至少已经过去了 `expires_in` 秒，不再可以使用此 `device_code` 进行身份验证。 | 停止轮询，并恢复到未经过身份验证状态。 |

### <a name="successful-authentication-response"></a>成功的身份验证响应

成功的令牌响应如下所示：

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
| `token_type` | String| 始终为 Bearer。 |
| `scope` | 空格分隔的字符串 | 如果返回访问令牌，此参数会列出访问令牌有效的范围。 |
| `expires_in`| int | 包含的访问令牌失效之前的秒数。 |
| `access_token`| 不透明字符串 | 为请求的[作用域](v2-permissions-and-consent.md)颁发。  |
| `id_token`   | JWT | 如果原始 `scope` 参数包含 `openid` 范围，则颁发。  |
| `refresh_token` | 不透明字符串 | 如果原始 `scope` 参数包含 `offline_access`，则颁发。  |

可以运行 [OAuth 代码流文档](v2-oauth2-auth-code-flow.md#refresh-the-access-token)中所述的同一个流，使用刷新令牌来获取新的访问令牌和刷新令牌。
