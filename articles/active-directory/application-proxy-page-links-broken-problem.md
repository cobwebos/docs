---
title: Links on the page don't work for an Application Proxy application | Microsoft Docs
description: "如何对与 Azure AD 集成的应用程序代理应用程序上断开的链接问题进行故障排除"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: e4b2556533fcb22556ca526d5815908fdefb0788
ms.lasthandoff: 04/18/2017


---

# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>页面上的链接对应用程序代理应用程序不起作用

本文可帮助诊断 Azure Active Directory 应用程序代理应用程序中的链接无法正常工作的原因。

## <a name="overview"></a>概述 
发布应用程序代理应用后，应用程序中默认可以工作的链接仅是指向已发布根 URL 内包含的目标的链接。 应用程序内的链接无法正常工作，应用程序的内部 URL 可能未包括应用程序内链接的所有目标。

**为何发生这种情况？** 单击应用程序中的某个链接后，应用程序代理尝试将此 URL 解析为同一应用程序内的内部 URL 或外部可用的 URL。 如果链接指向不在同一应用程序内的内部 URL，它不属于这些存储桶中的任意一个，从而导致出现“未找到”错误。

## <a name="ways-you-can-resolve-broken-links"></a>解决断开的链接的方法

可通过三种方法来解决此问题。 按复杂性递增的顺序列出了以下选项。

1.  请确保内部 URL 是包含应用程序所有相关链接的根 URL。 这将允许所有链接解析为同一应用程序内发布的内容。

    如果更改了内部 URL，但不想更改用户的登陆页面，则将主页 URL 更改为以前发布的内部 URL。 这可以通过转到“Azure Active Directory”-&gt;“应用注册”-&gt; 选择应用程序 -&gt;“属性”来实现。 在此属性选项卡上，会看到“主页 URL”字段，可将其调整为所需的登陆页面。

2.  如果应用程序使用完全限定的域名 (FQDN)，则使用[自定义域](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains)发布应用程序。 此功能允许相同 URL 同时在内部和外部使用。

    此选项确保应用程序内的链接可通过应用程序代理从外部访问，因为应用程序内指向内部 URL 的链接也可从外部识别。 请注意，所有链接仍需要属于已发布的应用程序。 但使用此选项，链接不需要属于同一应用程序，而是可以属于多个应用程序。

3.  如果这些选项都不可行，可加入新功能的预览以便进行 URL 转换/重写。 使用此选项，应用程序 HTML 正文中存在的内部 URL 或链接将转换为或“映射”到已发布的外部应用代理 URL。 此方法仅适用于 HTML 或 CSS 中的链接，不适用于通过 JS 生成的链接。 

因此，我们强烈建议尽可能使用[自定义域](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains)解决方案。 如果确实想要加入预览，请将电子邮件发送到 <aadapfeedback@microsoft.com>，其中包含 applicationId。

## <a name="next-steps"></a>后续步骤
[使用现有的本地代理服务器](application-proxy-working-with-proxy-servers.md)


