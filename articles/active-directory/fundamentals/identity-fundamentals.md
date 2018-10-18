---
title: Azure 标识和访问管理的基础知识有哪些？ - Azure Active Directory | Microsoft Docs
description: 了解 Azure Active Directory Premium 版提供的高级保护功能和其他工具。
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2018
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: f7baa29c77ae4af9813bfc755a39cc07288a3ad2
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734660"
---
# <a name="what-are-the-fundamentals-of-azure-identity-and-access-management"></a>Azure 标识和访问管理的基础知识有哪些？
Azure AD Premium 是一种基于云且具有高级保护功能的标识和访问管理解决方案。 这些高级功能有助于为所有应用提供安全标识、标识保护（通过 [Microsoft 智能安全图](https://www.microsoft.com/security/intelligence)进行增强）和 [Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md)。 Azure AD 有助于实时保护用户的标识，帮助你围绕组织的数据创建基于风险和自适应的访问策略。

请观看以下短视频，快速了解 Azure AD 标识管理和保护：
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

Azure AD 还提供了一组工具，可以帮助保护、自动化和管理环境，包括重置密码、用户和组管理以及应用请求。 Azure AD 还可以帮助管理用户拥有的设备以及访问和控制软件即服务 (SaaS) 应用。

有关 Azure Active Directory Premium 版本和相关工具的费用的详细信息，请参阅 [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>通过 Azure AD 和 Office 365 连接本地 Active Directory
通过[混合标识管理](https://aka.ms/aadframework)将本地目录与 Azure AD 集成，从而将本地 Active Directory 实施扩展到云中。 [Azure AD Connect](../connect/active-directory-aadconnect.md) 提供此集成，为用户提供对本地资源和云服务（如 Office 365）的单一标识和单一登录 (SSO) 访问。

Azure AD Connect 替换了旧版标识集成工具（例如 DirSync 和 Azure AD Sync），有助于支持本地和 Azure AD 之间的标识同步需求。 Azure AD Connect 同步通过以下方式启用：

- 同步。 负责创建用户、组和其他对象。 它还负责确保本地用户的标识信息与 Azure AD 中的标识信息相匹配。 当用户在 Azure AD 中更新密码时，启用密码回写功能还有助于使本地目录保持同步。

- 身份验证。 在设置 Azure AD 混合标识解决方案时，选择正确的身份验证方法非常重要。 可以为组织选择云身份验证（密码哈希同步/直通身份验证）或联合身份验证 (AD FS)。 有关可用选项的详细信息，请参阅[选择 Azure Active Directory 混合标识解决方案的正确身份验证方法](https://aka.ms/auth-options)。

- 运行状况监视。 Azure AD Connect Health 在 Azure 门户上提供监视和中心位置以查看此活动。 有关详细信息，请参阅[在云中监视本地标识基础结构和同步服务](../connect-health/active-directory-aadconnect-health.md)。

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>通过自助服务和单一登录体验提高工作效率，降低支持人员成本
当用户只有一个用户名和密码时可以节省时间，并且在每台设备上都可获得一致的体验。 另外，执行自助任务也可以节省时间，例如，无需等待支持人员的帮助，即可[重置遗忘的密码](../user-help/active-directory-passwords-update-your-own-password.md)或请求访问某个应用程序。

为了进一步提升 SSO 和一致性体验，Azure AD [将本地 Active Directory 扩展](../connect/active-directory-aadconnect.md)到云中，从而使用户将其主要组织帐户用于已加入域的设备、公司资源，以及完成工作所需使用的所有 Web 和 SaaS 应用程序。 

此外，可以根据组成员身份和用户的员工状态自动预配（或取消预配）应用程序访问，帮助控制对通过 [Azure AD 应用程序代理](../manage-apps/application-proxy.md)开发和发布的库应用或自己的本地应用的访问权限。

## <a name="manage-and-control-access-to-your-organizational-resources"></a>管理和控制对组织资源的访问
Microsoft 标识和访问管理解决方案有助于保护对组织数据中心和云中的应用和资源的访问。 此访问管理有助于提供其他级别的身份验证，例如[多重身份验证](../authentication/concept-mfa-howitworks.md)和[条件访问策略](../conditional-access/overview.md)。 通过高级安全报告、审核和警报来监视可疑活动，也有助于减少潜在的安全问题。

通过 Azure AD Premium 中的条件访问策略，可以为任何 Azure AD 连接的应用（例如 SaaS 应用、在云中或本地运行的自定义应用或 Web 应用）创建基于策略的访问规则。 Azure AD 会实时评估规则，并在用户尝试访问应用时强制实施它们。 如果发现可疑活动，Azure 标识保护策略允许你自动执行操作（通过阻止访问、强制实施多重身份验证或重置用户密码）。

## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management
Azure Active Directory Premium 2 版产品随附的 [Privileged Identity Management (PIM)](../privileged-identity-management/pim-getting-started.md) 用于发现、限制和监视管理帐户及其对 Azure Active Directory 和其他 Microsoft 联机服务中资源的访问权限。 PIM 还有助于在所需的确切时间段内管理按需管理访问，这意味着你使管理员可以在其帐户返回到正常用户状态之前，在预先配置的时段内请求对其特权进行多因素身份验证的临时提升。

## <a name="next-steps"></a>后续步骤
有关 Azure AD 体系结构的详细信息，请参阅[什么是 Azure AD 体系结构？](active-directory-architecture.md)。
