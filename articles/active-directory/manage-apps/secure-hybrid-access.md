---
title: Azure AD 安全混合访问 | Microsoft Docs
description: 本文介绍将旧版本地、公有云或私有云应用程序与 Azure AD 集成的合作伙伴解决方案。 通过将应用交付控制器或网络连接到 Azure AD 保护旧版应用。
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 9/10/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94a118b6d526d538015b7aa076b2715ed68af338
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032040"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>安全混合访问：使用 Azure Active Directory 保护旧应用

你现在可以通过将本地和云旧式身份验证应用程序连接到 Azure Active Directory (AD) 来保护这些应用程序：

- [Azure AD 应用程序代理](#secure-hybrid-access-through-azure-ad-application-proxy)

- [现有的应用程序传送控制器和网络](#secure-hybrid-access-through-networking-and-delivery-controllers)

- [虚拟专用网络 (VPN) 应用程序](#secure-hybrid-access-through-vpn-applications)

您可以通过 Azure AD [条件性访问](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) 和 Azure AD [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)等 Azure AD 功能，在所有应用程序中弥补缺口并增强安全状况。

## <a name="secure-hybrid-access-through-azure-ad-application-proxy"></a>通过 Azure AD 应用程序代理安全混合访问
  
使用 [应用程序代理](https://aka.ms/whyappproxy) 可以提供对本地 web 应用程序的 [安全远程访问](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) 。 用户不需要使用 VPN。 在 [单一登录](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-sso)后，用户可以轻松地从任何设备连接到其应用程序。 应用程序代理将远程访问作为一项服务提供，可让你 [轻松地将本地应用程序发布](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application) 给公司网络外部的用户。 它可帮助你扩展云访问管理，而无需修改你的本地应用程序。 作为下一步，[计划 Azure AD 应用程序代理部署](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan)。

## <a name="azure-ad-partner-integrations"></a>Azure AD 合作伙伴集成

### <a name="secure-hybrid-access-through-networking-and-delivery-controllers"></a>通过网络和传递控制器实现安全的混合访问

除了 [Azure AD 应用程序代理](https://aka.ms/whyappproxy)外，使你能够使用 [零信任框架](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)，以及与第三方提供商的 Microsoft 合作伙伴。 你可以使用现有的网络和传递控制器，并轻松保护对你的业务流程至关重要但在 Azure AD 之前无法保护的旧版应用程序。 您已经拥有了开始保护这些应用程序所需的一切。

![图像显示了使用网络合作伙伴和应用程序代理的安全混合访问](media/secure-hybrid-access/secure-hybrid-access.png)

以下网络供应商提供预先构建的解决方案以及与 Azure AD 集成的详细指南。

- [Akamai 企业应用程序访问 (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Citrix 应用程序传送控制器 (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)

- [Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

### <a name="secure-hybrid-access-through-vpn-applications"></a>通过 VPN 应用程序进行安全的混合访问

使用 VPN 解决方案，你可以随时在任何位置从任何设备对企业网络提供安全访问，同时保护组织的数据。 通过使 Azure AD 作为标识提供者 (IDP) ，你可以使用新式身份验证和授权方法（如 Azure AD [单一登录](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) 和 [多重身份验证](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) ）来保护本地旧版应用程序。  

![映像显示了使用 VPN 合作伙伴和应用代理的安全混合访问 ](media/secure-hybrid-access/app-proxy-vpn.png)

以下 VPN 供应商提供预先构建的解决方案和与 Azure AD 集成的详细指南。

• [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

• [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

• [F5 大 IP APM](https://aka.ms/f5-hybridaccessguide)

• [Palo Alto Networks 全局保护](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

• [Zscaler Private Access (ZPA) ](https://aka.ms/zscaler-hybridaccessguide)
