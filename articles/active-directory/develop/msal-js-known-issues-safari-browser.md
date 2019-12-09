---
title: 已知的 Safari 浏览器问题（MSAL） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解在使用 Safari 浏览器时使用适用于 JavaScript 的 Microsoft 身份验证库（MSAL）的已知问题。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1fda1471d629ab8d84d06a3cc5bcfae0a6d9ec5
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916326"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Safari 浏览器与 MSAL 的已知问题 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Safari 12 和 ITP 2.0 上的无提示续订

Apple iOS 12 和 MacOS 10.14 操作系统包含[Safari 12 浏览器](https://developer.apple.com/safari/whats-new/)的发行版。 出于安全和隐私目的，Safari 12 包括[智能跟踪防护 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/)。 这实质上导致了浏览器删除了设置的第三方 cookie。 ITP 2.0 还将标识提供者设置的 cookie 视为第三方 cookie。

### <a name="impact-on-msaljs"></a>对 MSAL 的影响

MSAL 使用隐藏的 Iframe 作为 `acquireTokenSilent` 调用的一部分执行无提示令牌获取和续订。 无提示令牌请求依赖于可以访问通过 Azure AD 设置的 cookie 所表示的经过身份验证的用户会话的 Iframe。 由于 ITP 2.0 阻止访问这些 cookie，MSAL 无法以无提示方式获取和续订令牌，这会导致 `acquireTokenSilent` 失败。

目前没有针对此问题的解决方案，我们正在通过标准社区评估选项。

### <a name="work-around"></a>解决

默认情况下，在 Safari 浏览器上启用 ITP 设置。 可以通过导航到**首选项** -> **隐私**"并取消选中"**阻止跨站点跟踪**"选项来禁用此设置。

![safari 设置](./media/msal-js-known-issue-safari-browser/safari.png)

需要使用交互式获取令牌调用来处理 `acquireTokenSilent` 失败，这会提示用户登录。
若要避免重复登录，可以实现的方法是处理 `acquireTokenSilent` 故障，并为用户提供一个在 Safari 中禁用 ITP 设置的选项，然后再继续进行交互式调用。 禁用此设置后，后续的静默令牌续订会成功。
