---
title: Microsoft 标识平台管理员许可协议
description: 介绍 Microsoft 标识平台终结点中的授权，包括范围、权限和许可。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/3/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 537d609c1281929203d1891f37614b7627e1683a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81868668"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Microsoft 标识平台中的管理员同意

某些权限需要获得管理员同意才能在租户中授予。  你还可以使用管理员许可终结点向整个租户授予权限。

## <a name="recommended-sign-the-user-into-your-app"></a>建议：让用户登录到应用

在构建使用管理员许可终结点的应用程序时，应用通常需要一个页面或视图，使管理员能够批准应用的权限。 此页面可以是应用注册流的一部分、应用设置的一部分，或者专用的“连接”流。 在许多情况下，合理的结果是应用只在用户使用工作或学校 Microsoft 帐户登录之后才显示此“连接”视图。

将用户登录到应用后，便可识别管理员所属的组织，然后要求他们批准必要的权限。 尽管在严格意义上不需要这样做，但这有助于为组织用户带来更直观的体验。 若要将用户登录，请遵循 [Microsoft 标识平台协议教程](active-directory-v2-protocols.md)。

## <a name="request-the-permissions-from-a-directory-admin"></a>向目录管理员请求权限

准备好向组织管理员请求权限时，可将用户重定向到 Microsoft 标识平台*管理员许可终结点*。

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| 参数     | 条件     | 说明                                                                               |
|--------------:|--------------:|:-----------------------------------------------------------------------------------------:|
| `tenant` | 必须 | 要向其请求权限的目录租户。 可以采用 GUID 或友好名称格式提供或使用 `organizations` 以一般方式引用，如示例所示。 不要使用 "公用"，因为个人帐户不能提供管理员同意，但在租户的上下文中除外。 若要确保与管理租户的个人帐户的兼容性最佳，请尽可能使用租户 ID。 |
| `client_id` | 必须 | Azure 门户的**应用程序（客户端） ID** [-应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)分配给应用程序的体验。 |
| `redirect_uri` | 必选 |要向其发送响应以供应用处理的重定向 URI。 其必须与在门户中注册的重定向 URI 之一完全匹配。 |
| `state` | 建议 | 请求中包含的值，也会在令牌响应中返回。 其可以是关于想要的任何内容的字符串。 在发出身份验证请求出现之前，使用该状态对有关用户在应用中的状态的信息（例如前面所在的页面或视图）进行编码。 |
|`scope`        | 必选      | 定义应用程序请求的权限集。 这可以是静态范围（使用 /.default）或动态范围。  这可以包括 OIDC 范围（`openid`、`profile`、`email`）。 |


此时，Azure AD 会要求租户管理员进行登录来完成请求。 系统要求管理员批准你在 `scope` 参数中请求的所有权限。  如果你使用了静态 (`/.default`) 值，则其功能将类似于 v1.0 管理员许可终结点，并请求对应用所需权限中找到的所有范围的许可。

### <a name="successful-response"></a>成功的响应

如果管理员批准了应用的权限，成功响应如下所示：

```
http://localhost/myapp/permissions?admin_consent=True&tenant=fa00d692-e9c7-4460-a743-29f2956fd429&state=12345&scope=https%3a%2f%2fgraph.microsoft.com%2fCalendars.Read+https%3a%2f%2fgraph.microsoft.com%2fMail.Send
```

| 参数         | 说明                                                                                       |
|------------------:|:-------------------------------------------------------------------------------------------------:|
| `tenant`| 向应用程序授予所请求权限的目录租户（采用 GUID 格式）。|
| `state`           | 同样随令牌响应返回的请求中所包含的值。 其可以是关于想要的任何内容的字符串。 该状态用于对发出身份验证请求出现之前，有关用户在应用中的状态的信息（例如前面所在的页面或视图）编码。|
| `scope`          | 为应用程序授予访问权限的权限集。|
| `admin_consent`   | 将设置为 `True`。|

### <a name="error-response"></a>错误响应

`http://localhost/myapp/permissions?error=consent_required&error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z&admin_consent=True&tenant=fa15d692-e9c7-4460-a743-29f2956fd429&state=12345`

除了在成功响应中看到的参数外，错误参数如下所示。

| 参数          | 说明                                                                                      |
|-------------------:|:-------------------------------------------------------------------------------------------------:|
| `error`            | 用于分类发生的错误类型与响应错误的错误码字符串。|
| `error_description`| 帮助开发人员识别错误根本原因的具体错误消息。|
| `tenant`| 向应用程序授予所请求权限的目录租户（采用 GUID 格式）。|
| `state`           | 同样随令牌响应返回的请求中所包含的值。 其可以是关于想要的任何内容的字符串。 该状态用于对发出身份验证请求出现之前，有关用户在应用中的状态的信息（例如前面所在的页面或视图）编码。|
| `admin_consent`   | 将设置为 `True`，以指示此响应发生在管理员同意流上。|

## <a name="next-steps"></a>后续步骤
- 请参阅[如何将应用转换为多租户应用](howto-convert-app-to-be-multi-tenant.md)
- 了解如何[在授权代码授予流期间在 OAuth 2.0 协议层提供许可支持](v2-oauth2-auth-code-flow.md#request-an-authorization-code)。
- 了解[多租户应用程序如何使用许可框架](active-directory-devhowto-multi-tenant-overview.md)来实现“用户”许可和“管理员”许可，为更高级的多层应用程序模式提供支持。
- 了解[Azure AD 应用程序许可体验](application-consent-experience.md)
