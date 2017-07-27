---
title: "Azure Active Directory B2C：请求访问令牌 | Microsoft Docs"
description: "本文介绍如何设置客户端应用程序和获取访问令牌。"
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: krassk
editor: 
ms.assetid: 1c75f17f-5ec5-493a-b906-f543b3b1ea66
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 7a28c92e921354cd4a623af29098a40e1c8b56b0
ms.contentlocale: zh-cn
ms.lasthandoff: 06/07/2017


---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure AD B2C：请求访问令牌

访问令牌（表示为 **access\_token**）是某种形式的安全令牌，客户端可以使用它来访问受[授权服务器](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics)保护的资源，如 Web API。 访问令牌表示为 [JWT](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens)，包含有关目标资源服务器以及向服务器授予的权限的信息。 调用资源服务器时，必须在 HTTP 请求中提供访问令牌。

本文介绍如何配置客户端应用程序，使其能够发出从 `authorize` 和 `token` 终结点获取 **access\_token** 的请求。

> [!NOTE]
> **Web API 链（代理）不受 Azure AD B2C 支持。**
>
> 许多体系结构包含需要调用另一个下游 Web API 的 Web API，这两者都受 Azure AD B2C 的保护。 此方案常见于具有 Web API 后端的本机客户端，该后端将调用 Azure AD B2C 图形 API 等 Microsoft Online 服务。
>
> 可以使用 OAuth 2.0 Jwt 持有者凭据授权（也称为代理流）来支持这种链接的 Web API 方案。 但是，Azure AD B2C 中目前尚未实现代理流。

## <a name="prerequisite"></a>先决条件

在请求访问令牌之前，首先需要注册 Web API，并发布可授予客户端应用程序的权限。 首先，请遵照[注册 Web API](active-directory-b2c-app-registration.md#register-a-web-api) 部分中的步骤操作。

## <a name="granting-permissions-to-a-web-api"></a>授予对 Web API 的权限

为了使客户端应用程序能够获取对 API 的特定权限，需要通过 Azure 门户向客户端应用程序授予这些权限。 若要向客户端应用程序授予权限，请执行以下操作：

1. 在 B2C 功能边栏选项卡中导航到“应用程序”菜单。
1. 如果没有客户端应用程序，请进行注册（[Web 应用](active-directory-b2c-app-registration.md#register-a-web-application)或[本机客户端](active-directory-b2c-app-registration.md#register-a-mobilenative-application)）。
1. 在应用程序的“设置”边栏选项卡上，选择“API 访问”。
1. 单击“添加”。
1. 选择 Web API 以及授予的（权限）范围。
1. 单击 **“确定”**。

> [!NOTE]
> Azure AD B2C 不要求客户端应用程序用户表示许可。 所有许可由管理员根据上述应用程序之间配置的权限提供。 如果吊销了对某个应用程序的授权，以前能够获取该权限的所有用户将不再能够获取。

## <a name="requesting-a-token"></a>请求令牌

若要获取资源应用程序的访问令牌，客户端应用程序需要在请求的 **scope** 参数中指定所需的权限。 例如，若要获取对应用 ID URI 为 `https://contoso.onmicrosoft.com/notes` 的资源应用程序的“读取”权限，范围应是 `https://contoso.onmicrosoft.com/notes/read`。 下面是对 `authorize` 终结点发出的授权代码请求的示例。

> [!NOTE]
> 此时，不支持将自定义域与访问令牌一起使用。 必须使用请求 URL 中的 yourtenantId.onmicrosoft.com 域。

```
https://login.microsoftonline.com/<yourTenantId>.onmicrosoft.com/oauth2/v2.0/authorize?p=<yourPolicyId>&client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

若要在同一请求中获取多个权限，可在单个 **scope** 参数中添加多个条目并用空格分隔。 例如：

解码的 URL：

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

编码的 URL：

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

为资源请求的范围/权限数可以超过为客户端应用程序授予的数目。 在这种情况下，如果至少授予了一个权限，则调用将会成功。 生成的 **access\_token** 的“scp”声明中只会填充已成功授予的权限。

> [!NOTE] 
> 不支持在同一请求中针对两个不同的 Web 资源请求权限。 此类请求将会失败。

### <a name="special-cases"></a>特殊情况

OpenID Connect 标准指定了多个特殊的“scope”值。 以下特殊范围表示“访问用户的配置文件”的权限：

* **openid**请求 ID 令牌
* **offline\_access**：请求刷新令牌（使用授权代码流）。

如果 `authorize` 请求中的“response\_type”参数包含“token”，“scope”参数必须包含至少一个授予的资源权限（“openid”和“offline\_access”除外）。 否则，`authorize` 请求将会终止并失败。

## <a name="the-returned-token"></a>返回的令牌

成功构建的 **access\_token**（通过 `authorize` 或 `token` 终结点）包含以下声明：

| Name | 声明 | 说明 |
| --- | --- | --- |
|目标受众 |`aud` |令牌授权访问的单个资源的\*应用程序 ID\*。 |
|范围 |`scp` |授予资源的权限。 多个授予的权限将以空格分隔。 |
|授权方 |`azp` |发起请求的客户端应用程序的\*应用程序 ID\*。 |

API 收到 **access\_token** 时，必须[验证该令牌](active-directory-b2c-reference-tokens.md)，证明该令牌可信并包含正确的声明。

我们始终乐于接受反馈和建议！ 如果在完成本主题的过程中遇到任何难题，或者在改进此内容方面有任何建议，请在页面底部提供反馈，我们将不胜感激。 对于功能请求，请将其添加到 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)。

