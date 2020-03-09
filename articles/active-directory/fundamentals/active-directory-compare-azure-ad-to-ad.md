---
title: 比较 Active Directory 与 Azure Active Directory
description: 此文档比较 Active Directory 域服务（添加）到 Azure Active Directory （AD）。 它概述了这两个标识解决方案中的关键概念，并说明了其不同或相似之处。
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
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2020
ms.locfileid: "78926897"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>比较 Active Directory 与 Azure Active Directory

Azure Active Directory 是云的标识和访问管理解决方案的下一演变。 Microsoft 在 Windows 2000 中引入了 Active Directory 域服务，使组织能够使用每个用户的单个标识管理多个本地基础结构组件和系统。

Azure AD 通过为组织提供跨云和本地的所有应用的标识即服务（IDaaS）解决方案，将此方法带入到下一级别。

大多数 IT 管理员都熟悉 Active Directory 域服务的概念。 下表概述了 Active Directory 概念与 Azure Active Directory 之间的差异和相似之处。

|概念|Active Directory (AD)|Azure Active Directory |
|:-|:-|:-|
|**用户**|||
|预配：用户 | 组织手动创建内部用户，或使用内部或自动预配系统（如 Microsoft Identity Manager）与 HR 系统集成。|现有 AD 组织使用[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)将标识同步到云。</br> Azure AD 增加了从[云 HR 系统](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)自动创建用户的支持。 </br>Azure AD 可以在[已启用 SCIM](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)的 SaaS 应用中预配标识，以自动为应用提供必要的详细信息，以允许用户访问。 |
|预配：外部标识| 组织在专用的外部 AD 林中作为一般用户手动创建外部用户，导致管理外部标识生命周期（来宾用户）的管理开销| Azure AD 提供了一类特殊的标识以支持外部标识。 [AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/)将管理外部用户标识的链接，以确保它们有效。 |
| 权利管理和组| 管理员使用户成为组的成员。 然后，应用和资源所有者将授予组对应用或资源的访问权限。| 在 Azure AD 中还提供了[组](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)，管理员还可以使用组来授予对资源的权限。 在 Azure AD 中，管理员可以手动将成员身份分配给组，或者使用查询将用户动态加入到组中。 </br> 管理员可以使用 Azure AD 中的[权利管理](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)，通过使用工作流以及基于时间的条件（如有必要）授予用户访问应用和资源集合的权限。 |
| 管理员管理|组织将使用 AD 中的域、组织单位和组的组合来委派管理权限，以管理其控制的目录和资源。| Azure AD 为[内置角色](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)提供其基于角色的访问控制（RBAC）系统，并对[创建自定义角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview)以委托对其控制的标识系统、应用和资源的特权访问提供有限支持。</br>可以使用[Privileged Identity Management （PIM）](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)增强角色的管理，以提供对特权角色的实时、受时间限制或基于工作流的访问权限。 |
| 凭据管理| Active Directory 中的凭据基于密码、证书身份验证和智能卡身份验证。 密码是使用基于密码长度、有效期和复杂性的密码策略管理的。|Azure AD 使用适用于云和本地的智能[密码保护](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)。 保护包括智能锁定以及阻止常见和自定义密码短语和替换。 </br>Azure AD[通过多重身份验证](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)和[无密码](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)技术（例如 FIDO2），大大提高了安全性。 </br>Azure AD 通过向用户提供[自助服务密码重置](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)系统，降低了支持成本。 |
| 应用|||
| 基础结构应用|Active Directory 构成许多基础结构本地组件的基础，例如 DNS、DHCP、IPSec、WiFi、NPS 和 VPN 访问|在新的云环境中，Azure AD 是用于访问应用的新控制平面，而不是依赖于网络控制。 当用户进行身份验证时，[条件访问（CA）](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)将控制哪些用户将有权访问所需条件下的应用。|
| 传统应用和旧式应用| 大多数本地应用使用 LDAP、Windows 集成身份验证（NTLM 和 Kerberos）或基于标头的身份验证来控制对用户的访问。| Azure AD 可以使用在本地运行[Azure AD 应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)代理来访问这些类型的本地应用。 使用此方法 Azure AD 可以在迁移时使用 Kerberos 对 Active Directory 用户进行身份验证，或者需要与旧应用共存。 |
| SaaS 应用|Active Directory 不会以本机方式支持 SaaS 应用，需要联合系统，如 AD FS。|支持 OAuth2、SAML 和 WS\* 身份验证的 SaaS 应用可集成到使用 Azure AD 进行身份验证。 |
| 具有新式身份验证的业务线（LOB）应用|组织可以使用 Active Directory AD FS 来支持需要新式身份验证的 LOB 应用。| 需要新式身份验证的 LOB 应用可以配置为使用 Azure AD 进行身份验证。 |
| 中间层/守护程序服务|在本地环境中运行的服务通常使用 AD 服务帐户或组托管服务帐户（gMSA）来运行。 然后，这些应用将继承服务帐户的权限。| Azure AD 提供[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index)，以在云中运行其他工作负荷。 这些标识的生命周期由 Azure AD 管理，并绑定到资源提供程序，以获取后门访问。|
| **设备**|||
| 移动型|Active Directory 不支持无第三方解决方案的移动设备。| Microsoft 的移动设备管理解决方案 Microsoft Intune 与 Azure AD 集成。 Microsoft Intune 提供要在身份验证期间评估的标识系统的设备状态信息。 |
| Windows 桌面|Active Directory 提供使用组策略、System Center Configuration Manager 或其他第三方解决方案来加入 Windows 设备以对其进行管理的功能。|Windows 设备可以[加入到 Azure AD](https://docs.microsoft.com/azure/active-directory/devices/)。 条件性访问可以检查是否 Azure AD 将设备加入到身份验证过程中。 还可以通过[Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune)来管理 Windows 设备。 在这种情况下，条件性访问会在允许访问应用之前考虑设备是否投诉（例如，最新的安全修补程序和病毒签名）。|
| Windows 服务器| Active Directory 为使用组策略或其他管理解决方案的本地 Windows 服务器提供了强大的管理功能。| 可以通过[Azure AD 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/)来管理 Azure 中的 Windows server 虚拟机。 当 Vm 需要访问标识系统目录或资源时，可以使用[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index)。|
| Linux/Unix 工作负荷|尽管可以将 Linux 计算机配置为以 Kerberos 领域身份向 Active Directory 进行身份验证，但 Active Directory 不能以本机方式支持非 Windows，而无需第三方解决方案。|Linux/Unix Vm 可以使用[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index)来访问标识系统或资源。 某些组织将这些工作负荷迁移到云容器技术，这些技术也可以使用托管的标识。|

## <a name="next-steps"></a>后续步骤

- [什么是 Azure Active Directory？](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
- [比较自助式管理 Active Directory 域服务、Azure Active Directory 和托管 Azure Active Directory 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions)
- [常见问题 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-faq)
- [Azure Active Directory 中有哪些新功能？](https://docs.microsoft.com/azure/active-directory/fundamentals/whats-new)
