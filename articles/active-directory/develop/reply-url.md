---
title: 重定向 URI/答复 URL 限制和局限性的 Microsoft 标识平台
description: 回复 Url/重定向 Url 限制和限制
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: article
ms.subservice: develop
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07be7d0c70193fec88782fea681e33d6b4cf4b40
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486228"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>重定向 URI/答复 URL 限制和局限

重定向 URI 或回复 URL 是成功获得授权，授权服务器将向用户发送一次应用程序的位置，并授予的授权代码或访问令牌。 包含代码或令牌，则在重定向 URI 或回复令牌，因此，必须将正确的位置注册为应用注册过程的一部分。

## <a name="maximum-number-of-redirect-uris"></a>重定向 Uri 的最大数目

下表显示了重定向 Uri 注册你的应用时，可以添加的最大数目。 

| 正在登录的帐户 | 重定向 Uri 的最大数目 | 描述 |
|--------------------------|---------------------------------|-------------|
| Microsoft 工作或学校中任何组织的 Azure Active Directory (Azure AD) 租户的帐户 | 256 | `signInAudience` 应用程序清单中的字段设置为*AzureADMyOrg*或*AzureADMultipleOrgs* |
| 个人 Microsoft 帐户和工作和学校帐户 | 100 | `signInAudience` 应用程序清单中的字段设置为*AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>URI 的最大长度

对于每个重定向添加到应用程序注册的 URI，可以使用最多 256 个字符。

## <a name="restrictions-using-a-wildcard-in-uris"></a>在 Uri 中使用通配符的限制

通配符 Uri，如`https://*.contoso.com`，非常方便，但应避免使用。 在重定向中使用通配符 URI 具有安全隐患。 根据 OAuth 2.0 规范 ([的 RFC 6749 第 3.1.2 节](https://tools.ietf.org/html/rfc6749#section-3.1.2))，重定向终结点 URI 必须是绝对 URI。 

Azure AD 应用程序模型不支持通配符 Uri 的应用程序配置为登录到个人 Microsoft 帐户和工作或学校帐户。 但是，应用程序配置为登录工作或学校组织的 Azure AD 租户中的帐户现在允许通配符 Uri。 
 
> [!NOTE]
> 新[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验不允许开发人员将通配符 Uri 添加 UI 上。 添加通配符 URI 的应用的登录工作或学校帐户仅支持通过应用程序清单编辑器。 今后，新的应用程序将无法再在重定向 URI 中使用通配符。 但是，较旧的应用包含通配符重定向 Uri 将继续工作。

如果你的方案要求的详细信息重定向 Uri 超过最大限制允许，而不是添加通配符重定向 URI，请考虑以下方法之一。

### <a name="use-a-state-parameter"></a>使用 state 参数

如果有大量的子域，并且你的方案要求你重定向到同一页面的开始位置的用户身份验证成功后的，使用状态参数可能会有所帮助。 

在这种方法：

1. 创建每个应用程序来处理从授权终结点接收的安全令牌的"共享"重定向 URI。
1. 参数中，你的应用程序可以发送应用程序特定的参数 （例如子域 URL 位置发起的用户或任何像这样的品牌信息)。 使用状态参数时，防止 CSRF 保护中的规定[部分的 RFC 6749 10.12](https://tools.ietf.org/html/rfc6749#section-10.12))。 
1. 特定于应用程序的参数包括适用于要呈现正确的应用程序体验的用户，也就是说，构造相应的应用程序状态所需的所有信息。 从状态参数的 HTML 因此请确保你的 Azure AD 授权终结点带不传递 HTML 中此参数的内容。
1. 当 Azure AD 会发送到"共享"重定向 URI 的响应时，它将发送回应用程序的状态参数。
1. 应用程序然后可以将值的状态参数来确定要发送到用户，进一步的 URL。 请确保验证 CSRF 保护。

> [!NOTE]
> 此方法允许遭到入侵的客户端，若要修改参数，从而将用户重定向到不同的 URL，这是中发送的其他参数[打开重定向程序威胁](https://tools.ietf.org/html/rfc6819#section-4.2.4)RFC 6819 中所述。 因此，客户端必须通过加密状态，或通过某些其他方式如验证域名重定向 URI 与令牌中的对其进行验证来保护这些参数。

### <a name="add-redirect-uris-to-service-principals"></a>添加到服务主体重定向 Uri

另一种方法是添加重定向到 Uri[服务主体](app-objects-and-service-principals.md#application-and-service-principal-relationship)，可表示任何 Azure AD 租户中的应用注册。 不能使用状态参数或你的方案需要您为您支持每个新租户将新的重定向 Uri 添加到应用注册时，可以使用此方法。 

## <a name="next-steps"></a>后续步骤

- 了解有关[应用程序清单](reference-app-manifest.md)
