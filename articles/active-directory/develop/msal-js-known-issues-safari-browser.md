---
title: 已知问题在浏览器 （适用于 JavaScript 的 Microsoft 身份验证库） |Azure
description: 了解有关已知问题，javascript (MSAL.js) 使用 Microsoft 身份验证库时使用 Safari 浏览器。
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb89b1ef4dbbef234fba3152d7f85bbadfbdc64a
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873880"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>MSAL.js 使用 Safari 浏览器上的已知的问题 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>在 Safari 12 和 ITP 2.0 上无提示令牌的续订

Apple iOS 12 和 MacOS 10.14 操作系统包含的版本[Safari 12 浏览器](https://developer.apple.com/safari/whats-new/)。 出于安全和隐私功能，包括 Safari 12[智能跟踪防护 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/)。 这实质上会导致浏览器以删除所设置的第三方 cookie。 ITP 2.0 还将处理由标识提供者作为第三方 cookie 设置的 cookie。

### <a name="impact-on-msaljs"></a>MSAL.js 的影响

MSAL.js 使用隐藏的 Iframe 来作为的一部分执行无提示令牌获取和续订`acquireTokenSilent`调用。 无提示令牌请求依赖于有权访问由 Azure ad 中设置的 cookie 的身份验证的用户会话在 Iframe。 使用 ITP 2.0 时阻止访问这些 cookie，MSAL.js 未以无提示方式获取和续订令牌，而这会导致`acquireTokenSilent`失败。

此时没有针对此问题的解决方案和我们评估标准社区的选项。

### <a name="work-around"></a>解决

默认情况下在 Safari 浏览器上启用 ITP 设置。 可以通过导航到禁用此设置**首选项** -> **隐私**并取消选中**防止跨站点跟踪**选项。

![safari 设置](./media/msal-js-known-issue-safari-browser/safari.png)

将需要处理`acquireTokenSilent`具有一个交互式的失败获取令牌调用时，它会提示用户进行登录。
若要避免重复的登录名，可以实现一种方法是处理`acquireTokenSilent`失败，并提供一个选项以在继续进行交互式调用之前禁用 ITP 设置在 Safari 中的用户。 一旦设置处于禁用状态，后续的无提示令牌续订应该会成功。
