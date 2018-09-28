---
title: Azure Active Directory 中断性变更引用 |Microsoft Docs
description: 对可能会影响你的应用程序的 Azure AD 协议所做的更改。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 14217d03cdb56c5c641ab8f8c3fc0748e8e815a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987745"
---
# <a name="whats-new-for-authentication"></a>身份验证的新增功能 

|
>通过将此 [URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) 添加到 RSS 源阅读器获取有关何时重新访问此页面来获得更新的通知。

身份验证系统会持续更改和添加功能，以增强安全性并提高标准符合性。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新功能
- 已知问题
- 协议更改
- 已弃用的功能

> [!TIP] 
> 本页面会定期更新，请不时回来查看。 除非另有说明，否则这些更改仅适用于新注册的应用程序。  

## <a name="upcoming-changes"></a>即将推出的更改

### <a name="authorization-codes-can-no-longer-be-reused"></a>授权代码无法再重复使用

**生效日期**：2018 年 10 月 10 日 **受影响的终结点**：v1.0 和 v2.0 **受影响的协议**：[代码流](v2-oauth2-auth-code-flow.md)

从 2018 年 10 月 10 日起，Azure AD 不再允许对新应用使用以前用过的身份验证代码。 在 2018 年 10 月 10 日之前创建的任何应用仍可重复使用身份验证代码。 此项安全变更有助于使 Azure AD 与 OAuth 规范保持一致，将在 v1 和 v2 终结点上强制实施。

如果应用重复使用授权代码来获取多个资源的令牌，则我们建议使用该代码获取刷新令牌，然后使用该刷新令牌获取其他资源的其他令牌。 授权代码只能使用一次，但刷新令牌可对多个资源使用多次。 尝试在 OAuth 代码流期间重用身份验证代码的任何新应用都将收到 invalid_grant 错误。

有关刷新令牌的详细信息，请参阅[刷新访问令牌](v1-protocols-oauth-code.md#refreshing-the-access-tokens)。

## <a name="may-2018"></a>2018 年 5 月

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID 令牌不能用于 OBO 流

**日期**：2018 年 5 月 1 日之前 **受影响的终结点**：v1.0 和 v2.0 **受影响的协议**：隐式流和 [OBO 流](v1-oauth2-on-behalf-of-flow.md)

在 2018 年 5 月 1 日之后，id_token 不能用作新应用程序的 OBO 流中的断言。  应改为使用访问令牌来保护 API，即使在同一应用程序的客户端和中间层之间也是如此。  在 2018 年 5 月 1 日之前注册的应用将继续有效，并能够使用 id_tokens 交换访问令牌 - 但是，这并不是最佳做法。

要解决此更改，可以执行以下操作：

1. 使用一个或多个作用域为中间层应用程序创建 Web API。  如此可实现更精细的控制和安全性。
1. 在应用清单中，即 [Azure 门户](https://portal.azure.com)或[应用注册门户](https://apps.dev.microsoft.com)，确保允许应用通过隐式流发出访问令牌。 可通过 `oauth2AllowImplicitFlow` 密钥控制。
1. 客户端应用程序通过 `response_type=id_token` 请求 id_token 时，还会请求上面创建的 Web API 的访问令牌 (`response_type=token`)。  因此，使用 v2.0 终结点时，`scope` 参数应与 `api://GUID/SCOPE` 类似。  在 v1.0 终结点上，`resource` 参数应为 Web API 应用 URI。
1. 将此访问令牌传递到中间层，代替 id_token。  