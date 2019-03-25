---
title: Azure Active Directory 中断性变更引用 |Microsoft Docs
description: 了解对可能会影响应用程序的 Azure AD 协议所做的更改。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fcc400f952cc89f5fb4bf6e8d6f0f331483868e
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401298"
---
# <a name="whats-new-for-authentication"></a>身份验证的新增功能 

>获取有关此页更新的通知。 只需将[此 URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) 添加到 RSS 源阅读器即可。

身份验证系统会持续更改和添加功能，以增强安全性并提高标准符合性。 为了让大家随时了解最新的开发成果，本文将提供有关以下详细信息的信息：

- 最新功能
- 已知问题
- 协议更改
- 已弃用的功能

> [!TIP] 
> 此页会定期更新，因此请经常访问。 除非另有说明，否则这些更改仅适用于新注册的应用程序。  

## <a name="upcoming-changes"></a>即将推出的更改

目前没有计划。 

## <a name="march-2019"></a>2019 年 3 月

### <a name="looping-clients-will-be-interrupted"></a>将中断循环的客户端

**生效日期**：2019 年 3 月 25日日

**受影响的终结点**：v1.0 和 v2.0

**受影响的协议**：所有流

客户端应用程序可以有时错误行为，通过短时间内发出数百个相同的登录请求。  这些请求可能会或可能不会成功，但它们都会影响用户体验不佳和更高的工作负荷的 IDP，增加的所有用户的延迟时间并减少，IDP 的可用性。  这些应用程序正常使用的边界之外运行，并且应更新为正常运行。  

发出多个时间的重复请求的客户端将发送`invalid_grant`错误： `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`。 

大多数客户端将不需要更改行为，以避免此错误。  只有配置错误的客户端 （不带令牌缓存或那些已暴露提示循环） 将受此错误。  客户端根据每个实例本地 （通过 cookie) 跟踪于以下因素：

* 用户提示，如果有

* 作用域或所请求资源

* 客户端 ID

* 重定向 URI

* 响应类型和模式

在短时间的时间 （5 分钟） 发出多个请求 （15 +） 的应用将收到`invalid_grant`错误，它们将循环。  正在请求具有足够长的生存期 （10 分钟最小值，默认为 60 分钟），因此重复请求此时间段内的令牌是不必要的。  

所有应用程序应处理`invalid_grant`通过显示交互式提示，而不是以无提示方式请求令牌。  若要避免此错误，客户端应确保它们正确缓存他们收到的令牌。


## <a name="october-2018"></a>2018 年 10 月

### <a name="authorization-codes-can-no-longer-be-reused"></a>授权代码无法再重复使用

**生效日期**：2018 年 11 月 15 日

**受影响的终结点**：v1.0 和 v2.0

**受影响的协议**：[代码流](v2-oauth2-auth-code-flow.md)

从 2018 年 11 月 15 日起，Azure AD 不再允许对应用使用以前用过的身份验证代码。 此项安全变更有助于使 Azure AD 与 OAuth 规范保持一致，将在 v1 和 v2 终结点上强制实施。

如果应用重复使用授权代码来获取多个资源的令牌，则我们建议使用该代码获取刷新令牌，然后使用该刷新令牌获取其他资源的其他令牌。 授权代码只能使用一次，但刷新令牌可对多个资源使用多次。 尝试在 OAuth 代码流期间重用身份验证代码的任何新应用都将收到 invalid_grant 错误。

有关刷新令牌的详细信息，请参阅[刷新访问令牌](v1-protocols-oauth-code.md#refreshing-the-access-tokens)。  如果使用 ADAL 或 MSAL，则由库为你处理 - 将“AcquireTokenByAuthorizationCodeAsync”的第二个实例替换为“AcquireTokenSilentAsync”。 

## <a name="may-2018"></a>2018 年 5 月

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID 令牌不能用于 OBO 流

**日期**：2018 年 5 月 1 日

**受影响的终结点**：v1.0 和 v2.0

**受影响的协议**：隐式流和 [OBO 流](v1-oauth2-on-behalf-of-flow.md)

在 2018 年 5 月 1 日之后，id_token 不能用作新应用程序的 OBO 流中的断言。 应改为使用访问令牌来保护 API，即使在同一应用程序的客户端和中间层之间也是如此。 在 2018 年 5 月 1 日之前注册的应用将继续有效，并能够使用 id_tokens 交换访问令牌；但是，此模式并不是最佳做法。

要解决此更改，可以执行以下操作：

1. 使用一个或多个作用域为应用程序创建 Web API。 此显式入口点可实现更精细的控制和安全性。
1. 在 [Azure 门户](https://portal.azure.com)或[应用注册门户](https://apps.dev.microsoft.com)的应用清单中，确保允许应用通过隐式流发出访问令牌。 这通过 `oauth2AllowImplicitFlow` 密钥进行控制。
1. 客户端应用程序通过 `response_type=id_token` 请求 id_token 时，还会请求上面创建的 Web API 的访问令牌 (`response_type=token`)。 因此，使用 v2.0 终结点时，`scope` 参数应与 `api://GUID/SCOPE` 类似。 在 v1.0 终结点上，`resource` 参数应为 Web API 应用 URI。
1. 将此访问令牌传递到中间层，代替 id_token。  
