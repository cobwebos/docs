---
title: 将活动目录与 Azure 活动目录进行比较
description: 本文档将活动目录域服务 （ADDS） 与 Azure 活动目录 （AD） 进行比较。 它概述了两个标识解决方案中的关键概念，并解释了它的不同或相似的方式。
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 02/26/2020
ms.author: martinco
ms.openlocfilehash: 5075ae57df6a7306f0c860690931c846e52c2a89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926897"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>将活动目录与 Azure 活动目录进行比较

Azure 活动目录是云标识和访问管理解决方案的下一个演变。 Microsoft 在 Windows 2000 中引入了 Active Directory 域服务，使组织能够使用每个用户使用单个标识管理多个本地基础结构组件和系统。

Azure AD 通过为跨云和本地的所有应用为组织提供身份即服务 （IDaaS） 解决方案，将此方法提升到一个新的水平。

大多数 IT 管理员都熟悉活动目录域服务概念。 下表概述了活动目录概念和 Azure 活动目录之间的差异和相似性。

|概念|Active Directory (AD)|Azure Active Directory |
|:-|:-|:-|
|**用户**|||
|预配：用户 | 组织手动创建内部用户或使用内部或自动预配系统（如 Microsoft 标识管理器）与 HR 系统集成。|现有 AD 组织使用[Azure AD 连接](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)将标识同步到云。</br> Azure AD 添加了支持，以便从[云 HR 系统](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)自动创建用户。 </br>Azure AD 可以在启用[SCIM 的](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)SaaS 应用中预配标识，以自动向应用提供必要的详细信息，以便用户访问。 |
|预配：外部标识| 组织在专用外部 AD 林中手动创建外部用户，从而导致管理管理外部标识的生命周期（来宾用户）| Azure AD 提供一个特殊的标识类来支持外部标识。 [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/)将管理指向外部用户标识的链接，以确保它们有效。 |
| 权利管理和组| 管理员使用户成为组的成员。 然后，应用和资源所有者向组授予对应用或资源的访问权限。| [组](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)在 Azure AD 中也可用，管理员也可以使用组向资源授予权限。 在 Azure AD 中，管理员可以手动将成员身份分配给组，或使用查询动态地将用户包括到组。 </br> 管理员可以使用 Azure AD 中[的权利管理](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)，使用户使用工作流（如有必要）基于时间的条件，访问应用和资源的集合。 |
| 管理管理|组织将使用 AD 中的域、组织单位和组的组合来委派管理权限来管理它控制的目录和资源。| Azure AD 通过其基于角色的访问控制 （RBAC） 系统提供[内置角色](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)，对[创建自定义角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview)以委派对标识系统、应用及其控制的资源的访问权限的支持有限。</br>通过[特权标识管理 （PIM）](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)可以增强管理角色，以便提供对特权角色的及时、有时间限制或基于工作流的访问。 |
| 凭据管理| 活动目录中的凭据基于密码、证书身份验证和智能卡身份验证。 密码使用基于密码长度、过期和复杂性的密码策略进行管理。|Azure AD 对云和本地使用智能[密码保护](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)。 保护包括智能锁定加上阻止常用和自定义密码短语和替换。 </br>Azure AD[通过多重身份验证](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)和无[密码](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)技术（如 FIDO2）显著提高安全性。 </br>Azure AD 为用户提供[自助服务密码重置](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)系统，从而降低了支持成本。 |
| **应用**|||
| 基础设施应用|活动目录构成了许多基础结构本地组件的基础，例如 DNS、DHCP、IPSec、WiFi、NPS 和 VPN 访问|在新的云世界中，Azure AD 是访问应用与依赖网络控制的新控制平面。 当用户对[进行身份验证时，条件访问 （CA）](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)将控制哪些用户，将有权访问哪些应用在要求条件下。|
| 传统应用和传统应用| 大多数本地应用使用 LDAP、Windows 集成身份验证（NTLM 和 Kerberos）或基于标头的身份验证来控制对用户的访问。| Azure AD 可以使用在本地运行的 Azure AD[应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)提供对这些类型的本地应用的访问。 使用此方法，Azure AD 可以在迁移或需要与旧应用共存时使用 Kerberos 对本地活动目录用户进行身份验证。 |
| SaaS 应用|活动目录不支持本地的 SaaS 应用，并且需要联合系统（如 AD FS）。|支持 OAuth2、SAML 和 WS 身份验证的\*SaaS 应用可以集成以使用 Azure AD 进行身份验证。 |
| 具有现代身份验证的业务线 （LOB） 应用|组织可以使用带有活动目录的 AD FS 来支持需要现代身份验证的 LOB 应用。| 需要现代身份验证的 LOB 应用可以配置为使用 Azure AD 进行身份验证。 |
| 中端/守护族服务|在本地环境中运行的服务通常使用 AD 服务帐户或组托管服务帐户 （gMSA） 运行。 然后，这些应用将继承服务帐户的权限。| Azure AD 提供[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index)，用于在云中运行其他工作负荷。 这些标识的生命周期由 Azure AD 管理，并且绑定到资源提供程序，不能用于其他目的来获得后门访问。|
| **设备**|||
| 移动电话|没有第三方解决方案，Active Directory 不支持本机支持移动设备。| 微软的移动设备管理解决方案微软Intune与 Azure AD 集成。 Microsoft Intune 向标识系统提供设备状态信息，供身份验证期间进行评估。 |
| 窗口桌面|Active Directory 提供使用组策略、系统中心配置管理器或其他第三方解决方案对加入 Windows 设备进行域连接以对其进行管理的功能。|Windows 设备可以[连接到 Azure AD](https://docs.microsoft.com/azure/active-directory/devices/)。 条件访问可以检查设备是否作为身份验证过程的一部分加入 Azure AD。 窗口设备也可以管理与[微软Intune。](https://docs.microsoft.com/intune/what-is-intune) 在这种情况下，条件访问将考虑设备是否是投诉（例如，最新的安全修补程序和病毒签名），然后再允许访问应用。|
| Windows 服务器| Active Directory 使用组策略或其他管理解决方案为本地 Windows 服务器提供强大的管理功能。| Azure 中的 Windows 服务器虚拟机可以使用[Azure AD 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/)进行管理。 当 VM 需要访问标识系统目录或资源时，可以使用[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index)。|
| Linux/Unix 工作负载|Active Directory 不支持没有第三方解决方案的非 Windows，尽管可以将 Linux 计算机配置为将 Active Directory 配置为 Kerberos 领域进行身份验证。|Linux/Unix VM 可以使用[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index)来访问标识系统或资源。 某些组织将这些工作负载迁移到云容器技术，这些技术也可以使用托管标识。|

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 活动目录？](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
- [自我管理型 Azure Active Directory 域服务、Azure Active Directory 和托管型 Azure Active Directory 域服务的比较](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions)
- [有关 Azure Active Directory 的常见问题](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-faq)
- [Azure 活动目录中有哪些新增功能？](https://docs.microsoft.com/azure/active-directory/fundamentals/whats-new)
