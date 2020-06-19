---
title: 重定向 URI 和 回复 URL 限制 - Microsoft 标识平台 | Azure
description: 回复 URL/重定向 URI 限制和局限
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: b7aefc54a20e23ae969750532e7e3bc824f69c56
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725306"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>重定向 URI/答复 URL 限制和局限

重定向 URI（或回复 URL）是在应用成功获得了授权，并被授予了授权代码或访问令牌后，授权服务器将用户发送到的位置。 代码或令牌包含在重定向 URI 或回复令牌中，因此在应用注册过程中注册正确的位置非常重要。

 下面的限制适用于回复 URL：

* 回复 URL 必须以方案 `https` 开头。

* 回复 URL 区分大小写。 它的大小写必须与正在运行的应用程序的 URL 路径匹配。 例如，如果应用程序将作为其路径 `.../abc/response-oidc` 的一部分包含在内，请不要在回复 URL 中指定 `.../ABC/response-oidc`。 由于 Web 浏览器将路径视为区分大小写，因此在重定向到大小写不匹配的 `.../ABC/response-oidc` URL 时，可能会排除与 `.../abc/response-oidc` 关联的 cookie。
    
## <a name="maximum-number-of-redirect-uris"></a>重定向 URI 的最大数量

下表显示在注册应用时可以添加的重定向 URI 的最大数量。

| 登录的帐户 | 重定向 URI 的最大数量 | 说明 |
|--------------------------|---------------------------------|-------------|
| 任何组织的 Azure Active Directory (Azure AD) 租户中的 Microsoft 工作或学校帐户 | 256 | 应用程序清单中的 `signInAudience` 字段设置为 AzureADMyOrg 或 AzureADMultipleOrgs |
| 个人 Microsoft 帐户以及工作和学校帐户 | 100 | 应用程序清单中的 `signInAudience` 字段设置为 AzureADandPersonalMicrosoftAccount |

## <a name="maximum-uri-length"></a>最大 URI 长度

对于向应用注册添加的每个重定向 URI，最多可以使用 256 个字符。

## <a name="supported-schemes"></a>支持的方案
对于在任何组织的 Azure Active Directory (Azure AD) 租户中登录 Microsoft 工作或学校帐户的应用，Azure AD 应用程序模型目前支持 HTTP 和 HTTPS 方案。 即，应用程序清单中的 `signInAudience` 字段设置为 AzureADMyOrg 或 AzureADMultipleOrgs。 对于登录个人 Microsoft 帐户以及工作和学校帐户的应用（即，`signInAudience` 设置为 AzureADandPersonalMicrosoftAccount），只允许使用 HTTPS 方案。

> [!NOTE]
> 新的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验不允许开发人员在 UI 上添加使用 HTTP 方案的 URI。 仅支持通过应用清单编辑器，为登录工作或学校帐户的应用添加 HTTP URI。 今后，新应用无法在重定向 URI 中使用 HTTP 方案。 但是，在重定向 URI 中包含 HTTP 方案的旧应用程序将继续正常工作。 开发人员必须在重定向 URI 中使用 HTTPS 方案。

## <a name="restrictions-using-a-wildcard-in-uris"></a>在 URI 中使用通配符的限制

通配符 URI（如 `https://*.contoso.com`）非常方便，但应避免这样做。 在重定向 URI 中使用通配符会影响安全性。 根据 OAuth 2.0 规范（[RFC 6749 的第 3.1.2 节](https://tools.ietf.org/html/rfc6749#section-3.1.2)），重定向终结点 URI 必须为绝对 URI。 

对于配置为登录个人 Microsoft 帐户和工作或学校帐户的应用，Azure AD 应用程序模型不支持通配符 URI。 但是，对于配置为在组织的 Azure AD 租户中登录工作或学校帐户的应用，现在允许使用通配符 URI。 
 
> [!NOTE]
> 新的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验不允许开发人员在 UI 上添加通配符 URI。 仅支持通过应用清单编辑器，为登录工作或学校帐户的应用添加通配符 URI。 今后，新应用无法在重定向 URI 中使用通配符。 但是，在重定向 URI 中包含通配符的旧应用程序将继续正常工作。

如果你的方案需要的重定向 URI 超过允许的最大限制，请考虑以下方法，而不是添加通配符重定向 URI。

### <a name="use-a-state-parameter"></a>使用状态参数

如果你有多个子域，并且如果你的方案要求你在身份验证成功后将用户重定向到他们开始访问的同一页面，则使用状态参数可能会很有帮助。 

在此方法中：

1. 为每个应用程序创建一个“共享”重定向 URI，以处理从授权终结点收到的安全令牌。
1. 应用程序可以在状态参数中发送特定于应用程序的参数（例如用户所来自的子域 URL，或类似于品牌信息的任何内容）。 使用状态参数时，请按照 [RFC 6749 的第 10.12 节](https://tools.ietf.org/html/rfc6749#section-10.12)中的指定防范 CSRF 保护。 
1. 特定于应用程序的参数将包含应用程序为用户呈现正确体验（即构造相应的应用程序状态）所需的所有信息。 Azure AD 授权终结点从状态参数中提取 HTML，因此请确保未在此参数中传递 HTML 内容。
1. 当 Azure AD 将响应发送到“共享”重定向 URI 时，它会将状态参数发回给应用程序。
1. 应用程序随后可以使用状态参数中的值确定要将用户进一步发送到的 URL。 确保对 CSRF 保护进行验证。

> [!NOTE]
> 此方法允许遭到入侵的客户端修改状态参数中发送的其他参数，从而将用户重定向到其他 URL（这是在 RFC 6819 中所述的[开放重定向程序威胁](https://tools.ietf.org/html/rfc6819#section-4.2.4)）。 因此，客户端必须对状态加密或通过其他一些方式进行验证（如根据令牌验证重定向 URI 中的域名），从而保护这些参数。

## <a name="next-steps"></a>后续步骤

- 了解 [应用程序清单](reference-app-manifest.md)
