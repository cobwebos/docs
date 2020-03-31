---
title: 重定向 URI &回复 URL 限制 - 微软身份平台 |蔚蓝
description: 回复 URL/重定向 URI 的局限性和限制
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 8fdc64632be8b5fcb3dca8de2ee833fef25719fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656732"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>重定向 URI/答复 URL 限制和局限

重定向 URI（或回复 URL）是在为应用成功授权并为其授予授权代码或访问令牌后，授权服务器将用户发送到的位置。 该代码或令牌包含在重定向 URI 或回复令牌中，因此在应用注册过程中注册正确的位置非常重要。

 以下限制适用于回复 URL：

    * 回复 URL 必须以方案 `https` 开头。
    * 回复 URL 区分大小写。 其大小写必须与正在运行的应用程序的 URL 路径的大小写匹配。 例如，如果应用程序在其路径中包括 `.../abc/response-oidc`，请不要在回复 URL 中指定 `.../ABC/response-oidc`。 由于 Web 浏览器将路径视为区分大小写，因此如果重定向到大小写不匹配的 `.../ABC/response-oidc` URL，则可能会排除与 `.../abc/response-oidc` 关联的 Cookie。
    
## <a name="maximum-number-of-redirect-uris"></a>最大重定向 URI 数

下表显示了注册应用时可添加的最大重定向 URI 数。

| 正在登录的帐户 | 最大重定向 URI 数 | 描述 |
|--------------------------|---------------------------------|-------------|
| 任何组织的 Azure Active Directory (Azure AD) 租户中的 Microsoft 工作或学校帐户 | 256 | 应用程序清单中的 `signInAudience` 字段设置为 *AzureADMyOrg* 或 *AzureADMultipleOrgs* |
| 个人 Microsoft 帐户、工作帐户和学校帐户 | 100 | `signInAudience`应用程序清单中的字段设置为*AzureADand个人 Microsoft 帐户* |

## <a name="maximum-uri-length"></a>最大 URI 长度

对于要添加到应用注册中的每个重定向 URI，最多可以使用 256 个字符。

## <a name="supported-schemes"></a>支持的方案
Azure AD 应用程序模型目前支持 HTTP 和 HTTPS 方案，用于在任何组织的 Azure Active Directory (Azure AD) 租户中登录 Microsoft 工作或学校帐户的应用程序。 也就是说，应用程序清单中的 `signInAudience` 字段设置为“AzureADMyOrg”** 或“AzureADMultipleOrgs”**。 对于在个人 Microsoft 帐户和工作帐户和学校帐户（`signInAudience`设置为*AzureADand个人 Microsoft 帐户*）中登录的应用，仅允许 HTTPS 方案。

> [!NOTE]
> 新的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验不允许开发人员在 UI 中添加使用 HTTP 方案的 URI。 对于登录工作或学校帐户的应用，仅支持通过应用清单编辑器添加 HTTP URI。 今后，新应用将无法在重定向 URI 中使用 HTTP 方案。 但是，在重定向 URI 中包含 HTTP 方案的早期应用将继续正常运行。 开发人员必须在重定向 URI 中使用 HTTPS 方案。

## <a name="restrictions-using-a-wildcard-in-uris"></a>在 URI 中使用通配符时的限制

通配符 URI（例如 `https://*.contoso.com`）可提供便利，但应避免使用。 在重定向 URI 中使用通配符会影响安全性。 根据 OAuth 2.0 规范（[RFC 6749 第 3.1.2 部分](https://tools.ietf.org/html/rfc6749#section-3.1.2)），重定向终结点 URI 必须是绝对 URI。 

Azure AD 应用程序模型不支持配置为登录个人 Microsoft 帐户和工作或学校帐户的应用的通配符 URI。 但是，目前对于组织 Azure AD 租户中配置为使用工作或学校帐户登录的应用，允许使用通配符 URI。 
 
> [!NOTE]
> 新的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验不允许开发人员在 UI 中添加通配符 URI。 对于使用工作或学校帐户登录的应用，仅支持通过应用清单编辑器添加通配符 URI。 今后，新应用无法在重定向 URI 中使用通配符。 但是，在重定向 URI 中包含通配符的早期应用可继续正常运行。

如果方案需要的重定向 URI 超过允许的最大值限制，而不是添加通配符重定向 URI，请考虑以下方法。

### <a name="use-a-state-parameter"></a>使用状态参数

如果你有多个子域，并且你的方案要求在对用户开始操作时所在的同一页面成功完成身份验证后重定向用户，则使用状态参数可能有帮助。 

在此方法中：

1. 为每个应用程序创建一个“共享的”重定向 URI，用于处理从授权终结点收到的安全令牌。
1. 应用程序可以在状态参数中发送应用程序特定的参数（例如用户的来源子域 URL，或品牌信息等）。 使用状态参数时，请根据 [RFC 6749 第 10.12 部分](https://tools.ietf.org/html/rfc6749#section-10.12)中的规定提供 CSRF 保护措施。 
1. 应用程序特定的参数包含应用程序为用户呈现正确体验（即，构造相应的应用程序状态）所需的所有信息。 Azure AD 授权终结点会去除状态参数中的 HTML，因此请确保不要在此参数中传递 HTML 内容。
1. 当 Azure AD 向“共享的”重定向 URI 发送响应时，会将状态参数发回到应用程序。
1. 然后，应用程序可以使用状态参数中的值来确定要进一步将用户发送到哪个 URL。 确保验证 CSRF 保护措施。

> [!NOTE]
> 此方法允许遭到攻击的客户端修改状态参数中发送的其他参数，从而将用户重定向到其他 URL，这就是 RFC 6819 中所述的[开放重定向程序威胁](https://tools.ietf.org/html/rfc6819#section-4.2.4)。 因此，客户端必须加密状态，或通过其他某些方式来验证这些参数（例如，根据令牌验证重定向 URI 中的域名），以此保护这些参数。

## <a name="next-steps"></a>后续步骤

- 了解[应用程序清单](reference-app-manifest.md)
