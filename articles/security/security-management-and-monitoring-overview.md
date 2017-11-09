---
title: "Azure 安全管理和监视概述 | Microsoft Docs"
description: " Azure 提供安全机制来帮助管理和监视 Azure 云服务和虚拟机。  本文概述了这些核心安全功能和服务。 "
services: security
documentationcenter: na
author: TerryLanfear
manager: StevenPo
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: terrylan
ms.openlocfilehash: 6787877deabafd0b7308e190cb45b4036049b05b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-management-and-monitoring-overview"></a>Azure 安全管理和监视概述
Azure 提供安全机制来帮助管理和监视 Azure 云服务和虚拟机。 本文概述了这些核心安全功能和服务。 提供了一些文章链接，其中每篇文章都包含详细的信息，方便用户了解更多内容。

Microsoft 云服务的安全性是你与 Microsoft 之间的一个合作关系和共担责任。 共担责任意味着 Microsoft 负责 Microsoft Azure 及其数据中心的物理安全（通过使用上锁的入口门、护栏和保安等安全保护措施）。 此外，Azure 还在软件层提供了强级别的云安全，以满足其要求苛刻的客户的安全、隐私及合规性需求。

拥有自己的数据和标识，需负责对其进行保护，同时确保本地资源的安全性以及所控制的云组件的安全性。 Microsoft 提供安全控制和功能，帮助你保护数据和应用程序。 你对安全的责任取决于云服务的类型。

下图总结了 Microsoft 和客户的责任平衡。

![共担责任][1]

有关安全管理的深入探讨，请参阅 [Azure 中的安全管理](azure-security-management.md)。

下面是本文介绍的核心功能：

* 基于角色的访问控制
* 反恶意软件
* 多重身份验证
* ExpressRoute
* 虚拟网络网关
* Privileged Identity Management
* 标识保护
* 安全中心

## <a name="role-based-access-control"></a>基于角色的访问控制
基于角色的访问控制 (RBAC) 为 Azure 资源提供精细的访问管理。 使用 RBAC，可以仅授予用户执行其作业所需的访问权限。  RBAC 还有助于确保用户离开组织后无法访问云中的资源。

了解更多：

* [有关 RBAC 的 Active Directory 团队博客](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
* [Azure 基于角色的访问控制](../active-directory/role-based-access-control-configure.md)

## <a name="antimalware"></a>反恶意软件
通过 Azure 我们可以使用来自 Microsoft、Symantec、Trend Micro、McAfee 和 Kaspersky 等大型安全性供应商的恶意软件清除软件，以确保用户的虚拟机免受恶意文件、广告软件和其他威胁的侵害。

Microsoft 反恶意软件提供了为 PaaS 角色和虚拟机安装反恶意软件代理的能力。 基于 System Center Endpoint Protection，此功能将经验证的本地安全技术引入到了云。

我们还在 Azure 平台中为 Trend 的 [Deep Security](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ 和 [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ 产品提供了深度集成。 DeepSecurity 是一个防病毒解决方案，SecureCloud 是一个加密解决方案。 DeepSecurity 使用扩展模型部署在 VM 内部。 使用门户 UI 和 PowerShell，用户可以选择使用即将启动的新 VM 或已部署的现有 VM 内部的 DeepSecurity。

在 Azure 上也支持 Symantec End Point Protection (SEP)。 通过门户集成，客户能够表明他们想要在 VM 内使用 SEP。 SEP 可以通过 Azure 门户安装在全新的 VM 上，也可以使用 PowerShell 安装在现有 VM 上。

了解更多：

* [在 Azure 虚拟机上部署反恶意软件解决方案](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件](azure-security-antimalware.md)
* [如何在 Windows VM 上安装和配置 Trend Micro Deep Security 即服务](../virtual-machines/windows/classic/install-trend.md)
* [如何在 Windows VM 上安装和配置 Symantec Endpoint Protection](../virtual-machines/windows/classic/install-symantec.md)
* [用于保护 Azure 虚拟机的新反恶意软件选项 – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>多重身份验证
Azure 多重身份验证 (MFA) 是需要使用多个验证方法的身份验证方法，为用户登录和事务额外提供一层重要的安全保障。 MFA 可帮助保护对数据和应用程序的访问，同时满足用户对简单登录过程的需求。 它通过各种验证选项（例如电话、短信、移动应用通知或验证码和第三方 OATH 令牌）来提供强大的身份验证。

了解更多：

* [多重身份验证](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [什么是 Azure 多重身份验证？](../multi-factor-authentication/multi-factor-authentication.md)
* [Azure 多重身份验证的工作原理](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="expressroute"></a>ExpressRoute
Microsoft Azure ExpressRoute 可让你通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。 使用 ExpressRoute 可与 Microsoft Azure、Office 365 和 CRM Online 等 Microsoft 云服务建立连接。 可以从任意位置之间的 (IP VPN) 网络、点到点以太网或在共置设施上通过连接服务提供商的虚拟交叉连接来建立这种连接。 ExpressRoute 连接不通过公共 Internet 。 与通过 Internet 的典型连接相比，ExpressRoute 连接提供更高的可靠性、更快的速度、更低的延迟和更高的安全性。

了解更多：

* [ExpressRoute 技术概述](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>虚拟网络网关
VPN 网关（也称为 Azure 虚拟网络网关）用于在虚拟网络和本地位置之间发送网络流量。 VPN 网关还用于在 Azure 的多个虚拟网络之间发送流量 （VNet 到 VNet）。  VPN 网关提供 Azure 和基础结构间的安全跨界连接。

了解更多：

* [关于 VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Azure 网络安全概述](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
用户有时候需要在 Azure 资源或者其他 SaaS 应用程序中执行特权操作。 这通常意味着，组织必须授予他们永久的 Azure Active Directory (Azure AD) 访问特权。 这会给云托管的资源不断增大安全风险，因为组织无法充分监视这些用户正在使用特权访问执行哪些操作。
此外，如果有访问特权的用户帐户被泄露，这个缺口可能会影响总体云安全性。 Azure AD Privileged Identity Management 可通过降低特权的暴露时间和加强对使用情况的了解来帮助解决此风险。  

Privileged Identity Management 针对角色和“实时”管理员访问引入了临时管理员的概念，这是需要完成指定角色激活过程的用户。 激活过程会在指定的时段内（如 8 小时），将 Azure AD 中的用户角色分配从非活动更改为活动。

了解更多：

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Azure AD Privileged Identity Management 入门](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>标识保护
Azure Active Directory (AD) 标识保护提供了可疑登录活动和潜在漏洞的统一视图来帮助保护企业。 标识保护基于诸如暴力攻击、泄漏凭据，以及从陌生位置和受感染设备登录等信号，检测用户和特权（管理）标识的可疑登录活动。

通过提供通知和建议的补救措施，标识保护有助于实时降低风险。 它会计算用户风险严重性，并且可以配置基于风险的策略，以自动保护应用程序访问免受将来的威胁。

了解更多：

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [第 9 频道：Azure AD 和标识展示：Identity Protection 预览](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>安全中心
Azure 安全中心帮助预防、检测和响应威胁，同时提高 Azure 资源的可见性并控制其安全性。 它提供 Azure 订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

安全中心通过以下方式帮助优化和监视 Azure 资源的安全：

* 允许你根据公司安全需求和应用程序类型或每个订阅中数据的敏感性，为 Azure 订阅资源定义策略。
* 监视 Azure 虚拟机、网络和应用程序的状态。
* 提供按优先级顺序排列的安全警报的列表，其中包括来自集成合作伙伴解决方案的警报，以及进行快速调查所需的信息和有关如何修复攻击的建议。

了解更多：

* [Azure 安全中心简介](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
