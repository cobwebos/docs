---
title: 配置 Azure AD 库应用程序密码单一登录时遇到的问题 | Microsoft Docs
description: 了解在为 Azure AD 应用程序库中所列应用程序配置密码单一登录时人们面对的常见问题
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: f19b684a6c7426134844a2657b886280af2f061c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34067055"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>配置 Azure AD 库应用程序密码单一登录时遇到的问题

阅读本文有助于了解在配置 Azure AD 库应用程序密码单一登录时经常会遇到的常见问题。

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>凭据已填写，但扩展却未提交凭据

这种问题通常发生在以下情景中：应用程序供应商为了添加字段而在近期更改了他们的登录页面、改变了用于检测用户名和密码字段的标识符，或修改了登录体验作用于应用程序的方式。 幸运的是，在许多情况下，Microsoft 能够与应用程序供应商合作来快速解决这些问题。

但是，尽管 Microsoft 的技术能够在集成中断时进行自动检测，但是可能无法立即找到这些问题，或者这些问题需要一些时间才能解决。 如果其中一个集成无法正确工作，请打开支持案例以尽快解决这一问题。

如果与该应用程序的供应商联系，请向他们发送我们的方法，这样，Microsoft 就可以与他们合作，将其应用程序与 Azure Active Directory 进行本机集成。 可以将该供应商发送至[列出 Azure Active Directory 应用程序库中的应用程序](./develop/active-directory-app-gallery-listing.md)来让他们开始工作。

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>凭据已填写并提交，但页面却显示凭据不正确

若要解决此问题，请先尝试以下操作：

-   先让用户使用为他们保存的凭据尝试**直接登录应用程序网站**。

  * 如果登录有效，再让用户在[应用程序访问面板](https://myapps.microsoft.com/)“应用”部分的“应用程序磁贴”上，单击“更新凭据”，以将凭据更新为最新的已知用户名和密码。

   * 如果或另一个管理员为该用户分配凭据，请导航到该应用程序的“用户和组”选项卡、选择分配并单击“更新凭据”按钮，找到该用户或组的应用程序分配。

-   如果用户自己分配凭据，让用户“检查以确保他们的密码在应用程序中未过期”，如果已经过期，直接登录应用程序**更新过期密码**。

   * 密码在应用程序中更新好后，要求用户在[应用程序访问面板](https://myapps.microsoft.com/)“应用”部分中的“应用程序磁贴”上，单击“更新凭据”按钮，以将凭据更新到大家所知的最新有效的用户名和密码。

   * 如果或另一个管理员为该用户分配凭据，请导航到该应用程序的“用户和组”选项卡、选择分配并单击“更新凭据”按钮，找到该用户或组的应用程序分配。

-   让用户通过遵循下面的[如何安装访问面板浏览器扩展](#how-to-install-the-access-panel-browser-extension)部分中的步骤更新访问面板浏览器扩展。

-   确保访问面板浏览器扩展正在运行并且已在用户浏览器中启用。

-   确保用户未在 **incognito、inPrivate 或私有模式**下试图从访问面板登录应用程序。 在这些模式下访问面板扩展不受支持。

如果前面的建议无效，可能是应用程序端已发生变更，已暂时中断了应用程序与 Azure AD 的集成。 例如，当应用程序供应商在页面上引入与手动和自动化输入表现不同的脚本，从而导致自动集成（就像我们这样的）中断时，就会发生这样情况。 幸运的是，在许多情况下，Microsoft 能够与应用程序供应商合作来快速解决这些问题。

但是，尽管 Microsoft 的技术能够在应用程序集成中断时进行自动检测，但是可能无法立即找到这些问题，或者这些问题可能需要一些时间才能解决。 当集成无法正常工作时，可以打开支持案例以尽快解决这一问题。 

除此之外，**如果与该应用程序的供应商联系**，请**向他们发送我们的方法**，如此一来，我们就可以与他们合作，将他们的应用程序与 Azure Active Directory 进行本机集成。 可以将该供应商发送至[列出 Azure Active Directory 应用程序库中的应用程序](./develop/active-directory-app-gallery-listing.md)来让他们开始工作。

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>扩展在 Chrome 和 Firefox 中有效，但在 Internet Explorer 中无效

针对这一问题有两个主要原因：

-   根据 Internet Explorer 中启用的安全设置，如果网站不属于**受信区域**，我们的脚本有时会在执行应用程序时遭到屏蔽。

  *  为了解决这一问题，请指导用户**添加应用程序的网站**到**Internet Explorer 安全设置**内的“受信任的站点”列表。 可以将用户发送到文章[如何将站点添加到受信任的站点列表](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5)，了解指导说明的详细信息。

-   在少数情况下，Internet Explorer 的安全验证有时可以导致页面的加载速度慢于脚本的执行速度。

   * 不幸的是，这种情况会根据浏览器的版本、计算机的速度或被访问的站点而有所变化。 在这种情况下，我们建议联系支持，这样可以解决具体应用程序的集成。

除此之外，**如果与该应用程序的供应商联系**，请**向他们发送我们的方法**，如此一来，我们就可以与他们合作，将他们的应用程序与 Azure Active Directory 进行本机集成。 可以将该供应商发送至[列出 Azure Active Directory 应用程序库中的应用程序](./develop/active-directory-app-gallery-listing.md)来让他们开始工作。

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>检查应用程序的登录页面近期是否已变更，或需要其他字段

如果应用程序的登录页面已彻底变更，有时这会导致我们的集成中断。 例如，当应用程序供应商向体验添加登录字段、验证码或多重身份验证时就会出现这种情况。 幸运的是，在许多情况下，Microsoft 能够与应用程序供应商合作来快速解决这些问题。

但是，尽管 Microsoft 的技术能够在应用程序集成中断时进行自动检测，但是可能无法立即找到这些问题，或者这些问题可能需要一些时间才能解决。 当集成无法正常工作时，可以打开支持案例以尽快解决这一问题。 

除此之外，**如果与该应用程序的供应商联系**，请**向他们发送我们的方法**，如此一来，我们就可以与他们合作，将他们的应用程序与 Azure Active Directory 进行本机集成。 可以将该供应商发送至[列出 Azure Active Directory 应用程序库中的应用程序](./develop/active-directory-app-gallery-listing.md)来让他们开始工作。

## <a name="how-to-install-the-access-panel-browser-extension"></a>如何安装访问面板浏览器扩展

若要安装访问面板浏览器扩展，请按照以下步骤操作：

1.  在某个支持的浏览器中打开[访问面板](https://myapps.microsoft.com)，并在 Azure AD 中以“用户”身份登录。

2.  在访问面板中单击“密码-SSO 应用程序”。

3.  在出现询问是否安装该软件的提示时，选择“立即安装”。

4.  将根据所用的浏览器将你定向到下载链接。 将扩展“添加”到浏览器中。

5.  如果浏览器出现提示，选择“启用”或“允许”扩展。

6.  安装完成后，“重启”浏览器会话。

7.  登录到访问面板，并查看是否可以**启动**密码 - SSO 应用程序

也可以通过下面的直接链接下载适用于 Chrome 和 Firefox 的扩展：

-   [Chrome 访问面板扩展](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 访问面板扩展](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>后续步骤
[使用应用程序代理为应用提供单一登录](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

