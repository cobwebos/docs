---
title: 重定向 URI & 回复 URL 限制-Microsoft 标识平台 |Microsoft
description: 回复 Url/重定向 Url 限制 & 限制
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
ms.openlocfilehash: bfc13c1057f74fb1eb5a41210ffaf166e69bb06e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920321"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>重定向 URI/答复 URL 限制和局限

"重定向 URI" 或 "回复 URL" 是授权服务器将用户发送到的位置，一旦应用成功获得授权，并获得授权代码或访问令牌即可。 代码或令牌包含在重定向 URI 或回复令牌中，因此在应用注册过程中注册正确的位置非常重要。

## <a name="maximum-number-of-redirect-uris"></a>重定向 Uri 的最大数目

下表显示了在注册应用程序时可添加的重定向 Uri 的最大数目。

| 已登录的帐户 | 重定向 Uri 的最大数目 | 描述 |
|--------------------------|---------------------------------|-------------|
| 任何组织的 Azure Active Directory （Azure AD）租户中的 Microsoft 工作或学校帐户 | 256 | 应用程序清单中 `signInAudience` "字段设置为*AzureADMyOrg*或*AzureADMultipleOrgs* |
| 个人 Microsoft 帐户、工作和学校帐户 | 100 | 应用程序清单中 `signInAudience` 字段设置为*AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>最大 URI 长度

对于添加到应用注册的每个重定向 URI，最多可以使用256个字符。

## <a name="supported-schemes"></a>支持的方案
目前 Azure AD 应用程序模型支持在任何组织的 Azure Active Directory （Azure AD）租户中使用 Microsoft 工作或学校帐户登录的应用的 HTTP 和 HTTPS 方案。 这是应用程序清单中 `signInAudience` 字段设置为*AzureADMyOrg*或*AzureADMultipleOrgs*。 对于登录到个人 Microsoft 帐户和工作和学校帐户（`signInAudience` 设置为*AzureADandPersonalMicrosoftAccount*）的应用，只允许使用 HTTPS 方案。

> [!NOTE]
> 新的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验不允许开发人员在 UI 上向 HTTP 方案添加 uri。 仅通过应用程序清单编辑器为登录工作或学校帐户的应用添加 HTTP Uri。 今后，新应用无法在重定向 URI 中使用 HTTP 方案。 但是，在重定向 Uri 中包含 HTTP 方案的旧版应用程序将继续工作。 开发人员必须在重定向 Uri 中使用 HTTPS 方案。

## <a name="restrictions-using-a-wildcard-in-uris"></a>在 Uri 中使用通配符的限制

通配符 Uri （如 `https://*.contoso.com`）非常方便，但应避免这样做。 在重定向 URI 中使用通配符具有安全含义。 根据 OAuth 2.0 规范（[RFC 6749 的第3.1.2 部分](https://tools.ietf.org/html/rfc6749#section-3.1.2)），重定向终结点 uri 必须是绝对 uri。 

Azure AD 应用程序模型不支持配置为登录个人 Microsoft 帐户和工作或学校帐户的应用的通配符 Uri。 但是，对于配置为在组织的 Azure AD 租户中登录工作或学校帐户的应用，允许使用通配符 Uri。 
 
> [!NOTE]
> 新的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验不允许开发人员在 UI 上添加通配符 uri。 仅通过应用程序清单编辑器为登录工作或学校帐户的应用添加通配符 URI。 今后，新应用无法在重定向 URI 中使用通配符。 但是，重定向 Uri 中包含通配符的旧应用将继续工作。

如果你的方案需要的重定向 Uri 超过允许的最大限制，请考虑以下方法之一，而不是添加通配符重定向 URI。

### <a name="use-a-state-parameter"></a>使用 state 参数

如果你有多个子域，并且如果你的方案要求你在身份验证成功后将用户重定向到他们开始使用的同一页面，则使用状态参数可能会很有帮助。 

此方法：

1. 为每个应用程序创建一个 "共享" 重定向 URI，以处理从授权终结点接收的安全令牌。
1. 您的应用程序可以在 state 参数中发送特定于应用程序的参数（例如用户所源自的子域 URL，或标记信息）。 使用 state 参数时，请根据[RFC 6749 第10.12 节](https://tools.ietf.org/html/rfc6749#section-10.12)所述，防范 CSRF 的保护。 
1. 特定于应用程序的参数将包含应用程序为用户呈现正确体验所需的所有信息，即构造相应的应用程序状态。 Azure AD 授权终结点从 state 参数中提取 HTML，因此请确保未在此参数中传递 HTML 内容。
1. 当 Azure AD 将响应发送到 "shared" 重定向 URI 时，它会将状态参数发回给应用程序。
1. 然后，应用程序可以使用状态参数中的值来确定要将用户进一步发送到的 URL。 请确保验证 CSRF 保护。

> [!NOTE]
> 此方法允许受损的客户端修改 state 参数中发送的其他参数，从而将用户重定向到其他 URL，该 URL 是 RFC 6819 中所述的[开放重定向程序威胁](https://tools.ietf.org/html/rfc6819#section-4.2.4)。 因此，客户端必须通过加密状态或通过其他一些方式来验证这些参数，如根据令牌验证重定向 URI 中的域名。

### <a name="add-redirect-uris-to-service-principals"></a>向服务主体添加重定向 Uri

另一种方法是在任何 Azure AD 租户中将重定向 Uri 添加到代表应用注册的[服务主体](app-objects-and-service-principals.md#application-and-service-principal-relationship)。 当无法使用状态参数或方案时，你可以使用此方法，要求你为你支持的每个新租户将新的重定向 Uri 添加到你的应用注册。 

## <a name="next-steps"></a>后续步骤

- 了解[应用程序清单](reference-app-manifest.md)
