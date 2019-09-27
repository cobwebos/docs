---
title: Microsoft 标识平台管理员许可协议 |Microsoft Docs
description: 介绍 Microsoft 标识平台终结点中的授权，包括范围、权限和许可。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e642ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa63b1343fcc981629dd96e2209bf26ec2cc2bd5
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326217"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Microsoft 标识平台上的管理员许可

某些权限需要管理员同意，才能在租户内授予用户权限。  你还可以使用管理员同意终结点向整个租户授予权限。  

## <a name="recommended-sign-the-user-into-your-app"></a>建议：让用户登录到应用

生成使用管理员同意终结点的应用程序时，应用需要一个页面/视图，使管理员能够批准应用的权限。 此页面可以是应用注册流的一部分、应用设置的一部分，或专用“连接”流的一部分。 在许多情况下，合理的结果是应用只在用户使用工作或学校 Microsoft 帐户登录之后才显示此“连接”视图。

将用户登录到应用后，便可识别管理员所属的组织，然后要求他们批准必要的权限。 尽管在严格意义上不需要这样做，但有助于为组织用户带来更直观的体验。 若要将用户登录，请遵循 [Microsoft 标识平台协议教程](active-directory-v2-protocols.md)。

## <a name="request-the-permissions-from-a-directory-admin"></a>向目录管理员请求权限

准备好向组织管理员请求权限时，可将用户重定向到 Microsoft 标识平台*管理员许可终结点*。

```
// Line breaks are for legibility only.
    GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
  client_id=6731de76-14a6-49ae-97bc-6eba6914391e
  &state=12345
  &redirect_uri=http://localhost/myapp/permissions
    &scope=
    https://graph.microsoft.com/calendars.read 
    https://graph.microsoft.com/mail.send
```


| 参数     | 条件     | 描述                                                                               |
|--------------:|--------------:|:-----------------------------------------------------------------------------------------:|
| `tenant` | 必填 | 要向其请求权限的目录租户。 可以采用 GUID 或友好名称格式提供或使用 `common` 以一般方式引用，如示例所示。 |
| `client_id` | 必填 | Azure 门户的**应用程序 (客户端) ID** [-应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)分配给应用程序的体验。 |
| `redirect_uri` | 必填 |要向其发送响应以供应用处理的重定向 URI。 其必须与在门户中注册的重定向 URI 之一完全匹配。 |
| `state` | 建议 | 同样随令牌响应返回的请求中所包含的值。 其可以是关于想要的任何内容的字符串。 在发出身份验证请求出现之前，使用该状态对有关用户在应用中的状态的信息（例如前面所在的页面或视图）进行编码。 |
|`scope`        | 必填      | 定义应用程序请求的权限集。 这可以是静态的（使用/.default）或动态作用域。  这可能包括 OIDC 作用域（`openid`、 `profile`、 `email`）。 | 


此时，Azure AD 会要求租户管理员进行登录来完成请求。 系统会要求管理员批准在`scope`参数中请求的所有权限。  如果使用了 static （`/.default`）值，则它将像 v1.0 管理员同意终结点一样工作，并请求为在应用所需的权限中找到的所有范围请求许可。

### <a name="successful-response"></a>成功的响应

如果管理员批准了应用的权限，成功响应如下所示：

```
http://localhost/myapp/permissions?admin_consent=True&tenant=fa00d692-e9c7-4460-a743-29f2956fd429&state=12345&scope=https%3a%2f%2fgraph.microsoft.com%2fCalendars.Read+https%3a%2f%2fgraph.microsoft.com%2fMail.Send
```

| 参数         | 描述                                                                                       |
|------------------:|:-------------------------------------------------------------------------------------------------:|
| `tenant`| 向应用程序授予所请求权限的目录租户（采用 GUID 格式）。|
| `state`           | 同样随令牌响应返回的请求中所包含的值。 可以是所需的任何内容的字符串。 该状态用于对发出身份验证请求出现之前，有关用户在应用中的状态的信息（例如前面所在的页面或视图）编码。|
| `scope`          | 为应用程序授予访问权限的权限集。|
| `admin_consent`   | 将设置为 `True`。|

### <a name="error-response"></a>错误响应

`http://localhost/myapp/permissions?error=consent_required&error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z&admin_consent=True&tenant=fa15d692-e9c7-4460-a743-29f2956fd429&state=12345`

添加到成功响应中出现的参数时，会出现错误参数，如下所示。

| 参数          | 描述                                                                                      |
|-------------------:|:-------------------------------------------------------------------------------------------------:|
| `error`            | 用于分类发生的错误类型与响应错误的错误码字符串。|
| `error_description`| 帮助开发人员识别错误根本原因的具体错误消息。|
| `tenant`| 向应用程序授予所请求权限的目录租户（采用 GUID 格式）。|
| `state`           | 同样随令牌响应返回的请求中所包含的值。 可以是所需的任何内容的字符串。 该状态用于对发出身份验证请求出现之前，有关用户在应用中的状态的信息（例如前面所在的页面或视图）编码。|
| `admin_consent`   | 将设置为`True` ，以指示此响应发生在管理员许可流中。|

## <a name="next-steps"></a>后续步骤
- 请参阅[如何将应用转换为多租户应用](howto-convert-app-to-be-multi-tenant.md)
- 了解如何[在授权代码授予流期间在 OAuth 2.0 协议层上获得许可](v2-oauth2-auth-code-flow.md#request-an-authorization-code)。
- 了解[多租户应用程序如何使用同意框架](active-directory-devhowto-multi-tenant-overview.md)来实现 "用户" 和 "管理员" 同意，从而支持更高级的多层应用程序模式。
- 了解[Azure AD 应用程序许可体验](application-consent-experience.md)
