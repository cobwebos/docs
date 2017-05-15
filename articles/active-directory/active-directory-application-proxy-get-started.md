---
title: "如何提供对本地应用的安全远程访问"
description: "介绍如何使用 Azure AD 应用程序代理来提供对本地应用的安全远程访问。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 2b0e5d4d1862305cda279fcf8cba895b8e6796bb
ms.contentlocale: zh-cn
ms.lasthandoff: 04/28/2017


---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>如何提供对本地应用程序的安全远程访问
> [!NOTE]
> 应用程序代理是一项仅当升级到高级版或基本版的 Azure Active Directory 才可用的功能。 有关详细信息，请参阅 [Azure Active Directory 版本](active-directory-editions.md)。

现今的员工想要随时随地都能在任何设备上高效工作。 他们想要在自己的设备上工作，无论设备是平板电脑、手机还是笔记本电脑。 而且他们期望能够访问其所有的应用程序：云中的 SaaS 应用和本地的公司应用。 传统上，提供本地应用程序访问权限会涉及虚拟专用网 (VPN)、非军事区 (DMZ) 或本地反向代理。 这些解决方案不仅复杂且难以确保安全性，而且设置和管理成本也很高。

还有更好的办法！

在移动至上、云至上的世界里，现代化员工需要现代化的远程访问解决方案。 Azure AD 应用程序代理是 Azure Active Directory Premium 产品的一项功能，它提供远程访问作为服务。 这意味着它很容易部署、使用和管理。

## <a name="what-is-azure-active-directory-application-proxy"></a>什么是 Azure Active Directory 应用程序代理？
Azure AD 应用程序代理针对本地托管的 Web 应用程序提供单一登录 (SSO) 和安全远程访问。 这可能包括 SharePoint 站点、Outlook Web Access 或其他任何你拥有的 LOB Web 应用程序。 这些本地 Web 应用程序会与 Azure AD（O365 所用的相同标识和控制平台）集成。 接着，最终用户可以像访问 O365 以及其他与 Azure AD 集成的 SaaS 应用一样访问本地应用程序。 无需更改网络基础结构或要求 VPN 为用户提供此解决方案。

## <a name="why-is-application-proxy-a-better-solution"></a>为什么应用程序代理是更好的解决方案？
Azure AD 应用程序代理可对所有本地应用程序提供简单、安全且经济高效的远程访问解决方案。

Azure AD 应用程序代理：  

* 在云端运行，因此可以节省时间和金钱。 本地解决方案则需要你设置和维护 DMZ、边缘服务器或其他复杂的基础结构。  
* 比本地解决方案更容易设置和保护，因为你不必通过防火墙开放任何入站连接。  
* 提供出色的安全性。 当你使用 Azure AD 应用程序代理发布应用时，可以利用 Azure 中丰富的授权控件和安全性分析功能。 你无需更改任何应用，即可为所有现有应用获取高级安全功能。  
* 让用户享有一致的身份验证体验。 利用单一登录，最终用户只需使用一个密码，就能轻松简单地访问所需的所有应用，从而实现高效工作。  

## <a name="what-kind-of-applications-work-with-azure-ad-application-proxy"></a>哪种应用程序可与 Azure AD 应用程序代理搭配使用？
通过 Azure AD 应用程序代理，你可以访问各种类型的内部应用程序：

* 使用集成 Windows 身份验证方法进行身份验证的 Web 应用程序  
* 使用基于窗体的访问的 Web 应用程序  
* 想要公开给不同设备上丰富应用程序的 Web API  
* 托管在远程桌面网关之后的应用程序  

## <a name="how-does-the-service-work-with-connectors"></a>服务如何使用连接器？
应用程序代理的工作原理如下：在网络内部安装一个名为连接器的精简 Windows Server 服务。 通过此连接器，你就不需要开放任何入站端口，或在 DMZ 中放置任何对象。 如果应用有大量流量，可添加更多连接器，由服务负责负载均衡。 连接器是无状态的，并且会根据需要从云中拉取所有内容。

有关连接器的信息，请参阅[了解 Azure AD 应用程序代理连接器](application-proxy-understand-connectors.md)。 

当用户远程访问应用程序时，他们会连接到已发布的终结点。 用户在 Azure AD 中进行身份验证，然后通过连接器路由到本地应用程序。

 ![AzureAD 应用程序代理关系图](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

1. 用户通过应用程序代理访问应用程序，然后定向到 Azure AD 登录页进行身份验证。
2. 成功登录之后，系统将生成令牌并发送给用户。
3. 用户将令牌发送到应用程序代理，应用程序代理检索令牌的用户主体名称 (UPN) 和安全主体名称 (SPN)，然后将请求定向到连接器。
4. 连接器代表用户请求可用于内部 (Windows) 身份验证的 Kerberos 票证。 这称为 Kerberos 约束委派。
5. Active Directory 检索 Kerberos 票证。
6. 票证将发送到应用程序服务器并进行验证。
7. 响应通过应用程序代理发送到用户。

### <a name="single-sign-on"></a>单一登录
Azure AD 应用程序代理针对使用集成 Windows 身份验证 (IWA) 的应用程序或声明感知应用程序提供单一登录 (SSO)。 如果你的应用程序使用 IWA，则应用程序代理将模拟使用 Kerberos 约束委派的用户来提供 SSO。 如果你有信任 Azure Active Directory 的声明感知应用程序，则 SSO 可用，因为用户已经由 Azure AD 进行身份验证。

有关 Kerberos 的详细信息，请参阅[全面了解 Kerberos 约束委派 (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd)。

## <a name="how-to-get-started"></a>如何入门
确保拥有 Azure AD 基本或高级版订阅以及你本人担任全局管理员的 Azure AD 目录。 你还需要拥有 Azure AD 基本或高级版的许可证才能让目录管理员和用户访问应用。 有关详细信息，请参阅 [Azure Active Directory 版本](active-directory-editions.md)。

设置应用程序代理由两个步骤组成：

1. [启用应用程序代理并配置连接器](active-directory-application-proxy-enable.md)。    
2. [发布应用程序](active-directory-application-proxy-publish.md) - 使用快速方便的向导发布本地应用，然后即可远程访问。

## <a name="whats-next"></a>后续步骤
应用程序代理还有其他用途：

* [使用你自己的域名发布应用程序](active-directory-application-proxy-custom-domains.md)
* [使用现有的本地代理服务器](application-proxy-working-with-proxy-servers.md) 
* [启用单一登录](active-directory-application-proxy-sso-using-kcd.md)
* [使用声明感知应用程序](active-directory-application-proxy-claims-aware-apps.md)
* [启用条件性访问](active-directory-application-proxy-conditional-access.md)

有关最新新闻和更新，请参阅 [应用程序代理博客](http://blogs.technet.com/b/applicationproxyblog/)


