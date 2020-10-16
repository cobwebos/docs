---
title: 如何在 Safari 中处理智能跟踪保护 (ITP) | Azure
titleSuffix: Microsoft identity platform
description: 当不再允许第三方 Cookie 时的单页应用 (SPA) 身份验证。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: hirsin
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 064c9a00e1cd7c139f3f42a053dcf8a5db13f161
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104574"
---
# <a name="handle-itp-in-safari-and-other-browsers-where-third-party-cookies-are-blocked"></a>在 Safari 和其他阻止第三方 Cookie 的浏览器中处理 ITP

当今的许多浏览器都会阻止第三方 Cookie - 即发送到其他域（而不是浏览器地址栏中显示的域）的请求中包含的 Cookie。 这会中断隐式流，并需要使用新的身份验证模式才能成功使用户登录。 在 Microsoft 标识平台中，如果第三方 Cookie 已被阻止，我们会使用带有 PKCE 和刷新令牌的授权流使用户保持登录状态。

## <a name="what-is-intelligent-tracking-protection-itp"></a>什么是智能跟踪保护 (ITP)？

Apple Safari 提供默认已启用的隐私保护功能，称为[智能跟踪保护](https://webkit.org/tracking-prevention-policy/) (*ITP*)。 ITP 会阻止“第三方”Cookie - 包含在跨域请求中的 Cookie。

常见的用户跟踪形式是，在后台将一个 iframe 加载到第三方站点，并使用 Cookie 在整个 Internet 中关联用户。 遗憾的是，此模式也是单页应用 (SPA) 中实现[隐式流](v2-oauth2-implicit-grant-flow.md)的标准方式。 当浏览器阻止第三方 Cookie 以阻止用户跟踪时，SPA 也会中断。

并非只有 Safari 通过阻止第三方 Cookie 来增强用户隐私保护。 Brave 默认已阻止第三方 Cookie，而 Chromium（基于 Google Chrome 和 Microsoft Edge 的平台）也已宣布，今后会停止支持第三方 Cookie。

本文中所述的解决方法适用于所有这些浏览器或者任何阻止第三方 Cookie 的场合。

## <a name="overview-of-the-solution"></a>解决方法概述

若要继续在 SPA 中对用户进行身份验证，应用开发人员必须使用[授权代码流](v2-oauth2-auth-code-flow.md)。 在授权代码流中，标识提供者会颁发一个代码，SPA 使用该代码兑换访问令牌和刷新令牌。 当应用需要其他令牌时，可以使用[刷新令牌流](v2-oauth2-auth-code-flow.md#refresh-the-access-token)来获取新令牌。 适用于 SPA 的 Microsoft 标识平台库 MSAL.js 2.0 为 SPA 实现授权代码流，只需进行较小的更新，便可以直接替代 MSAL.js 1.x。

对于 Microsoft 标识平台，SPA 和本机客户端遵循类似的协议指导：

* 使用 [PKCE 代码质询](https://tools.ietf.org/html/rfc7636)
    * PKCE 是 Microsoft 标识平台上的 SPA 所必需的。 建议对本机客户端和机密客户端使用 PKCE。
* 不使用客户端机密

SPA 有两个额外的限制：

* [必须将重定向 URI 标记为类型 `spa`](v2-oauth2-auth-code-flow.md#redirect-uri-setup-required-for-single-page-apps) 才能在登录终结点上启用 CORS。
* 通过授权代码流颁发给 `spa` 重定向 URI 的刷新令牌的生存期为 24 小时，而不是 90 天。

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="显示单页面应用与 security token service 终结点之间的 OAuth 2 授权代码流的关系图。" border="false":::

## <a name="performance-and-ux-implications"></a>性能和 UX 影响

某些使用隐式流的应用程序会尝试通过 `prompt=none` 打开登录 iframe，在未重定向的情况下登录。 在大多数浏览器中，此请求会在响应中包含当前已登录用户的令牌（假设已被授予同意）。 此模式意味着应用程序无需提供完整页面重定向即可使用户登录，从而改善性能和用户体验 - 用户在访问网页时即已登录。 由于在阻止了第三方 Cookie 的情况下，iframe 中的 `prompt=none` 不再是一个选项，因此应用程序必须访问顶级框架中的登录页才能让系统颁发授权代码。

可通过两种方式完成登录：

* **完整页面重定向**
    * 首次加载 SPA 时，如果不存在任何会话（或者会话已过期），则会将用户重定向到登录页。 用户的浏览器将访问登录页，提供包含用户会话的 Cookie，然后使用片段中的代码和令牌重定向回到应用程序。
    * 重定向会导致 SPA 加载两次。 请遵循有关 SPA 缓存的最佳做法，以免应用完全下载两次。
    * 考虑在应用中包含预加载序列，以便在应用完全解包并执行 JavaScript 有效负载之前，查找登录会话并重定向到登录页。
* **弹出窗口**
    * 如果完整页面重定向的用户体验 (UX) 不适用于应用程序，请考虑使用弹出窗口来处理身份验证。
    * 当弹出窗口在身份验证后完成重定向到应用程序时，重定向处理程序中的代码将在本地存储中存储代码和令牌，供应用程序使用。 与大多数库一样，MSAL.js 支持使用弹出窗口进行身份验证。
    * 浏览器正在逐渐削弱对弹出窗口的支持，因此弹出窗口不一定是最可靠的选项。 在创建弹出窗口之前可能需要用户与 SPA 交互，以满足浏览器的要求。

>[!NOTE]
> Apple [将弹出窗口方法描述](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/)为一种临时兼容性修复方法，此方法旨在使原始窗口能够访问第三方 Cookie。 尽管 Apple 将来可能会去除这种权限转移，但这不会影响本文中的指导。 此处，弹出窗口用作登录页的第一方导航，以便会话可被找到且授权代码可被提供。 将来应该也会继续使用这种方式。

### <a name="a-note-on-iframe-apps"></a>有关 iframe 应用的说明

Web 应用中的一种常见模式是使用 iframe 将一个应用嵌入到另一个应用。 顶级框架处理用户身份验证，iframe 中托管的应用程序可以相信用户已登录，并使用隐式流以无提示方式提取令牌。 当第三方 Cookie 被阻止时，无提示令牌获取方式将不再起作用 - 在 iframe 中嵌入的应用程序必须改用弹出窗口来访问用户的会话，因为它无法导航到登录页。

## <a name="security-implications-of-refresh-tokens-in-the-browser"></a>浏览器中的刷新令牌的安全影响

向浏览器颁发刷新令牌被认为是一个安全问题。 跨站点脚本 (XSS) 攻击或已遭入侵的 JS 包可能会盗取并在远程使用刷新令牌，直到该令牌过期或吊销。 为了最大程度地降低刷新令牌被盗的风险，将为 SPA 颁发有效期仅为 24 小时的令牌。 24 小时后，应用必须通过顶级框架访问登录页，以获取新的授权代码。

选择这种生存期受限的刷新令牌模式是为了在安全性与 UX 降级之间实现平衡。 如果不使用刷新令牌或第三方 Cookie，在需要新令牌或其他令牌时，授权代码流（根据 [OAuth 安全最佳做法当前草案](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14)中的建议）会变得很繁琐。 每当令牌过期时（对于 Microsoft 标识平台令牌，通常每隔一小时就会过期），都需要为获取一个令牌而执行完整页面重定向或显示弹出窗口。

## <a name="next-steps"></a>后续步骤

详细了解[授权代码流](v2-oauth2-auth-code-flow.md)。

参考 [MSAL.js 2.0 快速入门](quickstart-v2-javascript-auth-code.md)尝试运行授权代码流。
