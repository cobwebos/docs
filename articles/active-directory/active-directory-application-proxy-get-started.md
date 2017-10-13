---
title: "如何提供对本地应用的安全远程访问"
description: "介绍如何使用 Azure AD 应用程序代理来提供对本地应用的安全远程访问。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 426056d394af0a9ded28202615cb80c7b50e59fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>如何提供对本地应用程序的安全远程访问

现今的员工想要随时随地都能在任何设备上高效工作。 他们想要在自己的设备上工作，无论设备是平板电脑、手机还是笔记本电脑。 而且他们期望能够访问其所有的应用程序：云中的 SaaS 应用和本地的公司应用。 传统上，提供本地应用程序访问权限会涉及虚拟专用网络 (VPN) 或外围网络 (DMZ)。 这些解决方案不仅复杂且难以确保安全性，而且设置和管理成本也很高。

还有更好的办法！

在移动至上、云至上的世界里，现代化员工需要现代化的远程访问解决方案。 Azure AD 应用程序代理是 Azure Active Directory 的一项功能，以服务形式提供远程访问。 这意味着它很容易部署、使用和管理。

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="what-is-azure-active-directory-application-proxy"></a>什么是 Azure Active Directory 应用程序代理？
Azure AD 应用程序代理针对本地托管的 Web 应用程序提供单一登录 (SSO) 和安全远程访问。 可发布的某些应用包括 SharePoint 站点、Outlook Web Access 或其他任何 LOB Web 应用程序。 这些本地 Web 应用程序会与 Azure AD（O365 所用的相同标识和控制平台）集成。 最终用户可以像访问 O365 以及其他与 Azure AD 集成的 SaaS 应用一样访问本地应用程序。 无需更改网络基础结构或要求 VPN 为用户提供此解决方案。

## <a name="why-is-application-proxy-a-better-solution"></a>为什么应用程序代理是更好的解决方案？
Azure AD 应用程序代理可对所有本地应用程序提供简单、安全且经济高效的远程访问解决方案。

Azure AD 应用程序代理的特性：

* **简单**
   * 无需更改或更新应用程序即可使用应用程序代理。 
   * 用户可获得一致的身份验证体验。 他们可以使用 MyApps 门户单一登录到云中的 SaaS 应用和本地的应用。 
* **安全**
   * 使用 Azure AD 应用程序代理发布应用时，可以利用 Azure 中丰富的授权控件和安全性分析功能。 可以获得云级安全性和 Azure 安全功能，例如条件访问和双重验证。
   * 无需通过防火墙打开任何入站连接即可让用户进行远程访问。 
* **经济高效**
   * 应用程序代理在云中运行，因此可以节省时间和资金。 本地解决方案通常需要设置和维护外围网络、边缘服务器或其他复杂的基础结构。  

## <a name="what-kind-of-applications-work-with-application-proxy"></a>哪种应用程序可与应用程序代理搭配使用？
通过 Azure AD 应用程序代理，可以访问各种类型的内部应用程序：

* 使用 [Windows 集成身份验证](active-directory-application-proxy-sso-using-kcd.md)方法进行身份验证的 Web 应用程序  
* 使用基于窗体或[基于标头](application-proxy-ping-access.md)的访问的 Web 应用程序  
* 想要公开给不同设备上丰富应用程序的 Web API  
* 托管在[远程桌面网关](application-proxy-publish-remote-desktop.md)后面的应用程序  
* 与 Active Directory 身份验证库 (ADAL) 集成的富客户端应用

## <a name="how-does-application-proxy-work"></a>应用程序代理的工作原理是什么？
需要配置以下两个组件才能使应用程序代理正常工作：连接器和外部终结点。 

连接器是网络中 Windows Server 上的轻型代理。 连接器可帮助流量从云中的应用程序代理服务流向本地应用程序。 它只使用出站连接，因此不需要开放任何入站端口，或在外围网络中放置任何对象。 连接器是无状态的，可根据需要从云中提取信息。 有关连接器的详细信息（例如，它们如何均衡负载和执行身份验证），请参阅[了解 Azure AD 应用程序代理连接器](application-proxy-understand-connectors.md)。 

网络外部的用户通过外部终结点访问应用程序。 他们可以直接转到指定的外部 URL，或者通过 MyApps 门户访问应用程序。 当用户转到其中一个终结点时，将在 Azure AD 中进行身份验证，并通过连接器路由到本地应用程序。

 ![AzureAD 应用程序代理关系图](./media/active-directory-application-proxy-get-started/azureappproxxy.png)

1. 用户通过应用程序代理服务访问应用程序，然后定向到 Azure AD 登录页进行身份验证。
2. 成功登录之后，系统将生成令牌并发送给客户端设备。
3. 客户端将令牌发送到应用程序代理服务，该服务检索令牌中的用户主体名称 (UPN) 和安全主体名称 (SPN)，然后将请求定向到应用程序代理连接器。
4. 如果已配置单一登录，则连接器代表用户执行所需的任何其他身份验证。
5. 连接器将请求发送到本地应用程序。  
6. 通过应用程序代理服务和连接器将响应发送给用户。

### <a name="single-sign-on"></a>单一登录
Azure AD 应用程序代理针对使用集成 Windows 身份验证 (IWA) 的应用程序或声明感知应用程序提供单一登录 (SSO)。 如果应用程序使用 IWA，则应用程序代理将模拟使用 Kerberos 约束委派的用户来提供 SSO。 如果有信任 Azure Active Directory 的声明感知应用程序，则 SSO 可用，因为用户已经由 Azure AD 进行身份验证。

有关 Kerberos 的详细信息，请参阅[全面了解 Kerberos 约束委派 (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd)。

### <a name="managing-apps"></a>管理应用
使用应用程序代理发布应用后，可以像在 Azure 门户中管理其他任何企业应用一样来对其进行管理。 可以使用条件访问和双重验证等 Azure Active Directory 安全功能来控制用户权限以及自定义应用的品牌。 

## <a name="get-started"></a>入门

在配置应用程序代理之前，请确保已安装受支持的 [Azure Active Directory 版本](https://azure.microsoft.com/pricing/details/active-directory/)，并创建了一个你是其全局管理员的 Azure AD 目录。

通过两个步骤开始使用应用程序代理：

1. [启用应用程序代理并配置连接器](active-directory-application-proxy-enable.md)。    
2. [发布应用程序](active-directory-application-proxy-publish.md) - 使用快速方便的向导发布本地应用，即可远程访问。

## <a name="whats-next"></a>后续步骤
发布第一个应用后，还可以使用应用程序代理执行其他许多操作：

* [启用单一登录](active-directory-application-proxy-sso-using-kcd.md)
* [使用自己的域名发布应用程序](active-directory-application-proxy-custom-domains.md)
* [了解 Azure AD 应用程序代理连接器](application-proxy-understand-connectors.md)
* [使用现有的本地代理服务器](application-proxy-working-with-proxy-servers.md) 
* [设置自定义主页](application-proxy-office365-app-launcher.md)

有关最新新闻和更新，请参阅 [应用程序代理博客](http://blogs.technet.com/b/applicationproxyblog/)

