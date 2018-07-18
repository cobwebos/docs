---
title: 应用程序代理应用程序的应用程序页无法正确显示 | Microsoft Docs
description: 当页面在已集成到 Azure AD 的应用程序代理应用程序中无法正确显示时所要遵循的指南。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: ee06018cd500937c69824f796c137e3972c55f6c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36334694"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>应用程序代理应用程序的应用程序页无法正确显示

如果导航到某个页面后其中的某些内容未能正确显示，则可在本文的帮助下针对“Azure Active Directory 应用程序代理”应用程序中的问题进行故障排除。

## <a name="overview"></a>概述
发布应用程序代理应用时，通过访问该应用程序只能访问根目录下的页面。 如果页面未正确显示，则代表用于该应用程序的根内部 URL 可能缺少某些页面资源。 要解决此问题，请确保已将页面的*所有*资源作为应用程序的一部分予以发布。

通过打开网络跟踪程序（如 Fiddler 或 Internet Explorer/Edge 中的 F12 工具），加载页面，并查找 404 错误，即可验证缺少资源是否是问题所在。 这样可以找出当前找不到且可能需要发布的页面。

例如，假定已使用内部 URL http://myapps/expenses 发布了一个费用应用程序，但该应用使用了样式表 http://myapps/style.css。 在这种情况下，该样式表未在应用程序中予以发布；所以，如果在加载该开支应用时尝试加载 style.css，就会引发 404 错误。 在此示例中，通过使用内部 URL http://myapp/ 来发布该应用程序，即可解决这个问题。

## <a name="problems-with-publishing-as-one-application"></a>作为一个应用程序发布时会出现的问题

如果无法在同一应用程序内发布所有资源，则需发布多个应用程序并在它们之间启用链接。

为此，我们建议使用[自定义域](manage-apps/application-proxy-configure-custom-domain.md)解决方案。 但是，若要使用该解决方案，则必须拥有域证书，且应用程序必须使用完全限定的域名 (FQDN)。 有关其他选项，请参阅[断开链接的故障排除文档](application-proxy-page-links-broken-problem.md)。

## <a name="next-steps"></a>后续步骤
[使用 Azure AD 应用程序代理发布应用程序](manage-apps/application-proxy-publish-azure-portal.md)
