---
title: "应用程序代理应用程序的应用程序页无法正确显示 | Microsoft Docs"
description: "当页面在已集成到 Azure AD 的应用程序代理应用程序中无法正确显示时所要遵循的指南。"
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
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: d0b0ddba5ad9f8c584cd35c6f20edf76883b425d
ms.contentlocale: zh-cn
ms.lasthandoff: 04/18/2017

---

<a id="application-page-does-not-display-correctly-for-an-application-proxy-application" class="xliff"></a>

# 应用程序代理应用程序的应用程序页无法正确显示

如果导航到某个页面后其中的某些内容未能正确显示，则可在本文的帮助下针对 Azure Active Directory 应用程序代理应用程序中的问题进行故障排除。

<a id="overview" class="xliff"></a>

## 概述
发布应用程序代理应用时，通过访问该应用程序只能访问根目录下的页面。 如果页面未正确显示，则代表用于该应用程序的根内部 URL 可能缺少某些页面资源。 若要解决此问题，请确保已将页面的*所有*资源作为应用程序的一部分予以发布。

通过打开网络跟踪程序（如 Fiddler 或 Internet Explorer/Edge 中的 F12 工具），加载页面，然后查找 404 错误，即可验证这就是问题的根源。 这样可以找出当前找不到且可能仍需要发布的页面。

例如，假定已使用内部 URL <http://myapps/expenses> 发布了一个开支应用程序，但该应用使用了样式表 <http://myapps/style.css>。 在这种情况下，该样式表未在应用程序中予以发布；所以，如果在加载该开支应用时尝试加载 style.css，就会引发 404 错误。 在此示例中，通过转而使用内部 URL <http://myapp/> 来发布该应用程序，即可解决这个问题。

<a id="problems-with-publishing-as-one-application" class="xliff"></a>

## 作为一个应用程序发布时会出现的问题

如果无法在同一应用程序内发布所有资源，则需发布多个应用程序并在它们之间启用链接。

为此，我们建议使用[自定义域](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains)解决方案。 但是，若要使用该解决方案，则必须拥有域证书，且应用程序必须使用完全限定的域名 (FQDN)。 有关其他选项，请参阅[断开链接的故障排除文档](https://microsoft-my.sharepoint.com/personal/harshja_microsoft_com/_layouts/15/guestaccess.aspx?guestaccesstoken=IxuG3mFVbnPWI3Yn4Qi7wCNi8VIfHS5mwPt5quh8DMw%3d&docid=2_14558cd6ddea34c1c9887dc640feb5831&rev=1)。

<a id="next-steps" class="xliff"></a>

## 后续步骤
[使用 Azure AD 应用程序代理发布应用程序](application-proxy-publish-azure-portal.md)

