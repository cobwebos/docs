---
title: 重定向 URI/回复 URL 的局限性和限制 - Microsoft 标识平台
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9cc6ab0342682bce7befdfe412221ec581312be
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389598"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>重定向 URI/答复 URL 限制和局限

重定向 URI（或回复 URL）是在为应用成功授权并为其授予授权代码或访问令牌后，授权服务器将用户发送到的位置。 该代码或令牌包含在重定向 URI 或回复令牌中，因此在应用注册过程中注册正确的位置非常重要。

## <a name="maximum-number-of-redirect-uris"></a>最大重定向 URI 数

下表显示了注册应用时可添加的最大重定向 URI 数。

| 正在登录的帐户 | 最大重定向 URI 数 | 说明 |
|--------------------------|---------------------------------|-------------|
| 任何组织的 Azure Active Directory (Azure AD) 租户中的 Microsoft 工作或学校帐户 | 256 | 应用程序清单中的 `signInAudience` 字段设置为 *AzureADMyOrg* 或 *AzureADMultipleOrgs* |
| 个人 Microsoft 帐户、工作和学校帐户 | 100 个 | 应用程序清单中 `signInAudience` 字段设置为*AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>最大 URI 长度

对于要添加到应用注册中的每个重定向 URI，最多可以使用 256 个字符。

## <a name="supported-schemes"></a>支持的方案
目前 Azure AD 应用程序模型支持在任何组织的 Azure Active Directory （Azure AD）租户中使用 Microsoft 工作或学校帐户登录的应用的 HTTP 和 HTTPS 方案。 这是应用程序清单中 `signInAudience` 字段设置为*AzureADMyOrg*或*AzureADMultipleOrgs*。 对于登录到个人 Microsoft 帐户和工作和学校帐户（`signInAudience` 设置为*AzureADandPersonalMicrosoftAccount*）的应用，只允许使用 HTTPS 方案。

> [!NOTE]
> 新的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验不允许开发人员在 UI 上向 HTTP 方案添加 uri。 仅通过应用程序清单编辑器为登录工作或学校帐户的应用添加 HTTP Uri。 今后，新应用无法在重定向 URI 中使用 HTTP 方案。 但是，在重定向 Uri 中包含 HTTP 方案的旧版应用程序将继续工作。 开发人员必须在重定向 Uri 中使用 HTTPS 方案。

## <a name="restrictions-using-a-wildcard-in-uris"></a>在 URI 中使用通配符时的限制

通配符 URI（例如 `https://*.contoso.com`）可提供便利，但应避免使用。 在重定向 URI 中使用通配符会影响安全性。 根据 OAuth 2.0 规范（[RFC 6749 第 3.1.2 部分](https://tools.ietf.org/html/rfc6749#section-3.1.2)），重定向终结点 URI 必须是绝对 URI。 

Azure AD 应用程序模型不支持配置为登录个人 Microsoft 帐户和工作或学校帐户的应用的通配符 Uri。 但是，目前对于组织 Azure AD 租户中配置为使用工作或学校帐户登录的应用，允许使用通配符 URI。 
 
> [!NOTE]
> 新的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验不允许开发人员在 UI 中添加通配符 URI。 对于使用工作或学校帐户登录的应用，仅支持通过应用清单编辑器添加通配符 URI。 今后，新应用无法在重定向 URI 中使用通配符。 但是，在重定向 URI 中包含通配符的早期应用可继续正常运行。

如果方案所需的重定向 URI 数目超过允许的最大限制，请不要添加通配符重定向 URI，而是考虑以下方法之一。

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

### <a name="add-redirect-uris-to-service-principals"></a>将重定向 URI 添加到服务主体

另一种方法是将重定向 URI 添加到表示任何 Azure AD 租户中的应用注册的[服务主体](app-objects-and-service-principals.md#application-and-service-principal-relationship)。 无法使用状态参数，或者方案要求将新的重定向 URI 添加到你支持的每个新租户的应用注册时，可以使用此方法。 

## <a name="next-steps"></a>后续步骤

- 了解[应用程序清单](reference-app-manifest.md)
