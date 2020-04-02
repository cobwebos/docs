---
title: Azure 活动目录身份验证管理操作参考指南
description: 此操作参考指南描述为确保身份验证管理而应采取的检查和操作
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 8b4ec003888d75a582d25feef8ed2ce010fa7996
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546236"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Azure 活动目录身份验证管理操作参考指南

Azure AD[操作参考指南](active-directory-ops-guide-intro.md)的这一部分介绍了为保护和管理凭据、定义身份验证体验、委派分配、测量使用情况和根据企业安全状态定义访问策略而应采取的检查和操作。

> [!NOTE]
> 这些建议自发布之日起是最新的，但可能会随时间而变化。 随着 Microsoft 产品和服务随时间推移而变化，组织应持续评估其身份实践。

## <a name="key-operational-processes"></a>关键操作流程

### <a name="assign-owners-to-key-tasks"></a>将所有者分配给关键任务

管理 Azure 活动目录需要持续执行关键操作任务和进程，这些任务和进程可能不是推出项目的一部分。 设置这些任务以优化环境仍然很重要。 关键任务及其推荐的所有者包括：

| 任务 | “所有者” |
| :- | :- |
| 在 Azure AD 中管理单一登录 （SSO） 配置的生命周期 | IAM 运营团队 |
| 为 Azure AD 应用程序设计条件访问策略 | InfoSec 架构团队 |
| 在 SIEM 系统中存档登录活动 | InfoSec 运营团队 |
| 在 SIEM 系统中存档风险事件 | InfoSec 运营团队 |
| 会审并调查安全报告 | InfoSec 运营团队 |
| 会审并调查风险事件 | InfoSec 运营团队 |
| 对标记为 Azure AD 标识保护的风险和漏洞报告的用户进行会审并调查 | InfoSec 运营团队 |

> [!NOTE]
> Azure AD 标识保护需要 Azure AD 高级 P2 许可证。 要查找适合您要求的许可，请参阅比较 [Azure AD 免费版和 Azure AD 高级版的基本可用功能](https://azure.microsoft.com/pricing/details/active-directory/)。

在查看列表时，您可能会发现需要为缺少所有者的任务分配所有者，或者调整所有者与上述建议不一致的任务的所有权。

#### <a name="owner-recommended-reading"></a>业主推荐阅读

- [在 Azure Active Directory 中分配管理员角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure 中的监管](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="credentials-management"></a>凭据管理

### <a name="password-policies"></a>密码策略

安全管理密码是身份和访问管理中最重要的部分之一，通常也是攻击的最大目标。 Azure AD 支持一些功能，可帮助防止攻击成功。

使用下表查找缓解需要解决的问题的建议解决方案：

| 问题 | 建议 |
| :- | :- |
| 没有防止弱密码的机制 | 启用 Azure AD[自助服务密码重置 （SSPR）](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)和[密码保护](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) |
| 没有检测泄露密码的机制 | 启用[密码哈希同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization)（PHS） 以获得见解 |
| 使用 AD FS，无法移动到托管身份验证 | 启用[AD FS 外联网智能锁定](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)和/或[Azure AD 智能锁定](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout) |
| 密码策略使用基于复杂性的规则，如长度、多个字符集或过期 | 重新考虑微软[推荐的做法](https://aka.ms/passwordguidance)，并切换到密码管理的方法，并部署 Azure [AD 密码保护](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)。 |
| 用户未注册使用多重身份验证 （MFA） | [注册所有用户的安全信息](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-mfa-policy)，以便将其用作验证用户身份及其密码的机制 |
| 没有根据用户风险吊销密码 | 部署 Azure AD[标识保护用户风险策略](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)，以使用 SSPR 强制更改泄漏的凭据的密码 |
| 没有智能锁定机制来保护恶意身份验证免受来自已识别 IP 地址的不良参与者的攻击 | 使用密码哈希同步或[直通身份验证](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start)（PTA） 部署云托管身份验证 |

#### <a name="password-policies-recommended-reading"></a>密码策略建议读取

- [Azure AD 和 AD FS 最佳实践：防御密码喷雾攻击 - 企业移动性和安全性](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>启用自助服务密码重置和密码保护

需要更改或重置密码的用户是帮助台呼叫的最大数量和成本来源之一。 除了成本之外，更改密码作为降低用户风险的工具是改善组织安全状况的基本步骤。

至少，建议您部署 Azure AD[自助服务密码重置](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)（SSPR） 和本地[密码保护](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy)，以实现：

- 转移帮助台呼叫。
- 替换使用临时密码。
- 替换任何依赖于本地解决方案的现有自助服务密码管理解决方案。
- [消除组织中的弱密码](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)。

> [!NOTE]
> 对于具有 Azure AD 高级 P2 订阅的组织，建议部署 SSPR 并将其用作[身份保护用户风险策略](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)的一部分。

### <a name="strong-credential-management"></a>强大的凭据管理

密码本身不够安全，无法阻止不良参与者访问您的环境。 至少，必须启用具有特权帐户的任何用户进行多重身份验证 （MFA）。 理想情况下，您应该启用[合并注册](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)，并要求所有用户使用[合并注册体验](https://docs.microsoft.com/azure/active-directory/user-help/user-help-security-info-overview)注册 MFA 和 SSPR。 最终，我们建议您采用一种策略，[提供弹性](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls)，以降低因不可预见的情况而锁定的风险。

![组合用户体验流程](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>本地中断身份验证恢复能力

除了简单性和启用泄露凭据检测的好处外，Azure AD 密码哈希同步 （PHS） 和 Azure MFA 允许用户访问 SaaS 应用程序和 Office 365，尽管由于[非 Petya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/)等网络攻击导致本地中断。 也可以在与联合结合时启用 PHS。 启用 PHS 允许在联合身份验证服务不可用时备份身份验证。

如果本地组织缺少中断恢复能力策略或具有未与 Azure AD 集成的策略，则应部署 Azure AD PHS 并定义包含 PHS 的灾难恢复计划。 启用 Azure AD PHS 将允许用户在本地活动目录不可用时针对 Azure AD 进行身份验证。

![密码哈希同步流](./media/active-directory-ops-guide/active-directory-ops-img5.png)

要更好地了解身份验证选项，请参阅为[Azure Active Directory 混合标识解决方案选择正确的身份验证方法](https://docs.microsoft.com/azure/active-directory/hybrid/choose-ad-authn)。

### <a name="programmatic-usage-of-credentials"></a>凭据的编程使用

使用 PowerShell 的 Azure AD 脚本或使用 Microsoft 图形 API 的应用程序需要安全身份验证。 执行这些脚本和工具的凭据管理不当会增加凭据被盗的风险。 如果使用的脚本或应用程序依赖于硬编码的密码或密码提示，应首先查看配置文件或源代码中的密码，然后替换这些依赖项，并尽可能使用 Azure 托管标识、集成 Windows 身份验证或[证书](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-access-api-with-certificates)。 对于无法使用以前的解决方案的应用程序，请考虑使用[Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)。

如果您确定有具有密码凭据的服务主体，并且不确定这些密码凭据如何由脚本或应用程序保护，请与应用程序的所有者联系，以更好地了解使用模式。

Microsoft 还建议您联系应用程序所有者以了解使用模式（如果有具有密码凭据的服务主体）。

## <a name="authentication-experience"></a>身份验证体验

### <a name="on-premises-authentication"></a>本地身份验证

使用集成 Windows 身份验证 （IWA） 或无缝单一登录 （SSO） 托管身份验证（具有密码哈希同步或直通身份验证）的联合身份验证是企业网络内部使用本地域控制器的视线的最佳用户体验。 它最大限度地减少了凭据提示疲劳，并降低了用户成为网络钓鱼攻击的牺牲品的风险。 如果您已经在使用与小灵通或 PTA 进行云托管身份验证，但用户在本地身份验证时仍需要键入其密码，则应立即[部署无缝 SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)。 另一方面，如果您当前与最终迁移到云托管身份验证的计划联合，则应在迁移项目中实现无缝 SSO。

### <a name="device-trust-access-policies"></a>设备信任访问策略

与组织中的用户一样，设备也是要保护的核心标识。 可以使用设备标识随时随地保护你的资源。通过以下方法，对设备进行身份验证并考虑其信任类型可提高您的安全状态和可用性：

- 避免摩擦，例如，当设备受信任时，使用 MFA
- 阻止对不受信任的设备的访问
- 对于 Windows 10 设备，[无缝地为本地资源提供单一登录](https://docs.microsoft.com/azure/active-directory/devices/azuread-join-sso)。

通过使用以下方法之一，可以将设备标识在 Azure AD 中管理，从而实现此目标：

- 组织可以使用[Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune)来管理设备并强制实施合规性策略，证明设备运行状况，并根据设备是否符合标准设置条件访问策略。 Microsoft Intune 可以管理 iOS 设备、Mac 桌面（通过 JAMF 集成）、Windows 桌面（本机使用 Windows 10 的移动设备管理，并与 Microsoft 端点配置管理器共同管理）和 Android 移动设备。
- [混合 Azure AD 联接](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-managed-domains)在具有 Active Directory 域加入计算机设备的环境中提供组策略或 Microsoft 终结点配置管理器的管理。 组织可以通过小灵通或具有无缝 SSO 的 PTA 部署托管环境。 将设备带到 Azure AD 可跨云和本地资源的 SSO 最大限度地提高用户工作效率，同时使您能够通过 [条件访问](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) 同时安全地访问云和本地资源。

如果域加入的 Windows 设备未在云中注册，或者域加入的 Windows 设备在云中注册，但没有条件访问策略，则应注册未注册的设备，并在这两种情况下，在条件访问策略中使用[混合 Azure AD 联接作为控件](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)。

![需要混合设备的条件访问策略中的授予屏幕截图](./media/active-directory-ops-guide/active-directory-ops-img6.png)

如果您使用 MDM 或 Microsoft Intune 管理设备，但在条件访问策略中不使用设备控件，则建议使用["要求设备](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices#require-device-to-be-marked-as-compliant)"在这些政策中标记为符合控件。

![需要设备合规性的条件访问策略中的授予屏幕截图](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>设备信任访问策略建议读取

- [如何：规划混合 Azure 活动目录联接实现](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
- [标识和设备访问权限配置](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello for Business

在 Windows 10[中，适用于企业的 Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)在 PC 上使用强的双重身份验证替换密码。 适用于企业的 Windows Hello 为用户提供了更简化的 MFA 体验，并减少了您对密码的依赖。 如果您尚未开始推出 Windows 10 设备，或者仅部分部署它们，我们建议您升级到 Windows 10，并在所有设备上[启用适用于企业的 Windows Hello。](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)

如果要了解有关无密码身份验证的更多内容，请参阅[没有密码的 Azure 活动目录 的世界](https://aka.ms/passwordlessdoc)。

## <a name="application-authentication-and-assignment"></a>应用程序身份验证和分配

### <a name="single-sign-on-for-apps"></a>应用的单一登录

为整个企业提供标准化的单一登录机制对于最佳用户体验、降低风险、报告能力和治理至关重要。 如果使用支持 SSO 与 Azure AD 的应用程序，但当前配置为使用本地帐户，则应将这些应用程序重新配置为将 SSO 与 Azure AD 一起使用。 同样，如果使用 Azure AD 支持 SSO 但正在使用其他标识提供程序的任何应用程序，则应将这些应用程序重新配置为在 Azure AD 中使用 SSO。 对于不支持联合协议但支持基于表单的身份验证的应用程序，我们建议您将应用程序配置为使用 Azure AD 应用程序代理使用[密码保管](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting)。

![基于 AppProxy 密码的登录](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> 如果您没有机制来发现组织中的非托管应用程序，我们建议您使用云访问安全代理解决方案 （CASB）（如[Microsoft 云应用安全](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security)）实现发现过程。

最后，如果您有 Azure AD 应用库并使用支持 SSO 的应用程序与 Azure AD 一起，我们建议您[在应用库中列出应用程序](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing)。

#### <a name="single-sign-on-recommended-reading"></a>单一登录推荐阅读

- [什么是应用程序访问和 Azure 活动目录的单一登录](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>将 AD FS 应用程序迁移到 Azure AD

[将应用从 AD FS 迁移到 Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure)可实现安全性、更一致的可管理性和更好的协作体验的其他功能。 如果在 AD FS 中配置了支持 Azure AD 的 SSO 的应用程序，则应将这些应用程序重新配置为将 SSO 与 Azure AD 一起使用。 如果在 AD FS 中配置了 Azure AD 不支持的不常见配置的应用程序，则应与应用所有者联系以了解特殊配置是否属于应用程序的绝对要求。 如果不需要，则应将应用程序重新配置为将 SSO 与 Azure AD 一起使用。

![Azure AD 作为主标识提供程序](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [ADFS 的 Azure AD 连接运行状况](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)可用于收集有关可能迁移到 Azure AD 的每个应用程序的配置详细信息。

### <a name="assign-users-to-applications"></a>将用户分配给应用程序

最好使用组映射[用户，](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)因为它们允许更大的灵活性和大规模管理的能力。 使用组的好处包括[基于属性的动态组成员身份](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)和[委派给应用所有者](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-accessmanagement-managing-group-owners)。 因此，如果您已经在使用和管理组，我们建议您采取以下操作来大规模改进管理：

- 将组管理和治理委派给应用程序所有者。
- 允许自助访问应用程序。
- 定义动态组，如果用户属性可以一致地确定对应用程序的访问。
- 使用[Azure AD 访问审核](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)对用于应用程序访问的组实施证明。

另一方面，如果您发现具有分配给单个用户的应用程序，请确保围绕这些应用程序实现[治理](https://docs.microsoft.com/azure/active-directory/governance/index)。

#### <a name="assign-users-to-applications-recommended-reading"></a>将用户分配给建议读取的应用程序

- [向 Azure Active Directory 中的应用程序分配用户和组](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)
- [在 Azure Active Directory 中委托应用注册权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-app-roles)
- [Azure Active Directory 中的动态组成员资格规则](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

## <a name="access-policies"></a>访问策略

### <a name="named-locations"></a>命名位置

借助 Azure AD 中的[命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)，可以在组织中标记受信任的 IP 地址范围。 Azure AD 使用命名位置以：

- 防止风险事件中出现误报。 从受信任的网络位置登录可降低用户的登录风险。
- 配置[基于位置的条件访问](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)。

![命名位置](./media/active-directory-ops-guide/active-directory-ops-img10.png)

根据优先级，使用下表查找最符合组织需求的建议解决方案：

| **优先** | **方案** | **建议** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | 如果您使用 PHS 或 PTA，并且尚未定义命名位置 | 定义命名位置以改善风险事件的检测 |
| 2 | 如果您是联合的，并且不使用"内部公司网络"声明，并且尚未定义命名位置 | 定义命名位置以改善风险事件的检测 |
| 3 | 如果在条件访问策略中不使用命名位置，并且条件访问策略中没有风险或设备控件 | 将条件访问策略配置为包括命名位置 |
| 4 | 如果您联合使用"内部公司网络"声明，并且尚未定义命名位置 | 定义命名位置以改善风险事件的检测 |
| 5 | 如果使用具有 MFA 的可信 IP 地址，而不是指定位置，并将其标记为受信任 | 定义命名位置并将其标记为受信任位置，以改进风险事件的检测 |

### <a name="risk-based-access-policies"></a>基于风险的访问策略

Azure AD 可以计算每个登录和每个用户的风险。 在访问策略中使用风险作为标准可以提供更好的用户体验，例如，减少身份验证提示，提高安全性，例如，仅在需要用户时提示用户，并自动执行响应和补救。

![登录风险策略](./media/active-directory-ops-guide/active-directory-ops-img11.png)

如果您已经拥有支持在访问策略中使用风险的 Azure AD 高级 P2 许可证，但未使用它们，我们强烈建议为您的安全状况增加风险。

#### <a name="risk-based-access-policies-recommended-reading"></a>建议阅读基于风险的访问策略

- [如何：配置登录风险策略](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)
- [如何：配置用户风险策略](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)

### <a name="client-application-access-policies"></a>客户端应用程序访问策略

Microsoft Intune 应用程序管理 （MAM） 能够将数据存储加密、PIN、远程存储清理等数据保护控制推送到兼容的客户端移动应用程序（如 Outlook Mobile）。 此外，还可以创建条件访问策略，[以限制](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)从已批准或兼容的应用访问云服务（如 Exchange Online）。

如果您的员工安装支持 MAM 的应用程序（如 Office 移动应用）来访问公司资源（如在线交换或 SharePoint Online），并且您也支持 BYOD（自带设备），我们建议您部署应用程序 MAM 策略，以便在个人拥有的设备中管理应用程序配置，而无需 MDM 注册，然后更新条件访问策略，仅允许支持 MAM 的客户端进行访问。

![条件访问授予控制](./media/active-directory-ops-guide/active-directory-ops-img12.png)

如果员工针对公司资源安装支持 MAM 的应用程序，并且访问受限于 Intune 托管设备上，则应考虑部署应用程序 MAM 策略来管理个人设备的应用程序配置，并更新条件访问策略，以便仅允许来自支持 MAM 的客户端进行访问。

### <a name="conditional-access-implementation"></a>条件访问实现

条件访问是改善组织安全状态的重要工具。 因此，遵循以下最佳实践非常重要：

- 确保所有 SaaS 应用程序至少应用了一个策略
- 避免将 **"所有应用**"筛选器与**块**控制相结合，以避免锁定风险
- 避免使用**所有用户**作为筛选器，并无意中添加**来宾**
- **将所有"遗留"策略迁移到 Azure 门户**
- 捕获用户、设备和应用程序的所有条件
- 使用条件访问策略[实现 MFA，](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)而不是使用**每个用户的 MFA**
- 具有一小组可应用于多个应用程序的核心策略
- 定义空异常组并将它们添加到策略中，使其具有异常策略
- 规划没有 MFA 控制的[碎玻璃](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency)帐户
- 确保跨 Office 365 客户端应用程序（例如团队、业务 OneDrive、Outlook 等）获得一致的体验。通过为服务（如在线交换和共享点在线）实现同一组控件
- 政策分配应通过小组而不是个人执行
- 定期检查策略中使用的异常组，以限制用户退出安全状态的时间。 如果您拥有 Azure AD P2，则可以使用访问审核来自动执行该过程

#### <a name="conditional-access-recommended-reading"></a>条件访问建议读取

- [Azure 活动目录中的条件访问最佳做法](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)
- [标识和设备访问权限配置](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure 活动目录条件访问设置引用](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference)
- [常用条件访问策略](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

## <a name="access-surface-area"></a>访问表面积

### <a name="legacy-authentication"></a>旧式身份验证

强大的凭据（如 MFA）无法使用旧版身份验证协议保护应用，这使得它成为恶意参与者的首选攻击媒介。 锁定旧版身份验证对于改善访问安全状态至关重要。

旧版身份验证是一个术语，指应用使用的身份验证协议，例如：

- 不使用现代身份验证的旧 Office 客户端（例如，Office 2010 客户端）
- 使用邮件协议（如 IMAP/SMTP/POP）的客户端

攻击者非常喜欢这些协议 - 事实上，近[100% 的密码喷雾攻击](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)使用旧版身份验证协议！ 黑客使用旧版身份验证协议，因为他们不支持交互式登录，这是其他安全挑战（如多重身份验证和设备身份验证）所必需的。

如果遗留身份验证在环境中广泛使用，则应计划尽快将旧版客户端迁移到支持[现代身份验证](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016)的客户端。 在同一令牌中，如果某些用户已经使用现代身份验证，但其他用户仍使用旧身份验证，则应执行以下步骤来锁定旧身份验证客户端：

1. 使用[登录活动报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)标识仍在使用旧版身份验证的用户并计划修正：

   a. 升级到支持现代身份验证的客户端到受影响的用户。
   
   b. 计划一个截止时间范围，以锁定以下步骤。
   
   c. 确定哪些旧应用程序对旧版身份验证具有硬依赖性。 请参阅下面的步骤 3。

2. 为不使用旧版 aus 的用户禁用源（例如 Exchange 邮箱）的旧协议，以避免更多曝光。
3. 对于其余帐户（理想情况下是非人工标识（如服务帐户），使用[条件访问限制身份验证后的旧协议](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417)。

#### <a name="legacy-authentication-recommended-reading"></a>建议读取旧身份验证

- [启用或禁用 POP3 或 IMAP4 对 Exchange 服务器中邮箱的访问](https://docs.microsoft.com/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>同意授予

在非法同意授予攻击中，攻击者创建 Azure AD 注册应用程序，请求访问数据，如联系信息、电子邮件或文档。 用户在登陆恶意网站时，可能会通过网络钓鱼攻击授予恶意应用程序的同意。

以下是具有 Microsoft 云服务权限的应用列表：

- 具有应用或委派\*的应用。读取写入权限
- 具有委派权限的应用可以代表用户读取、发送或管理电子邮件
- 使用以下权限授予 的应用：

| 资源 | 权限 |
| :- | :- |
| Office 365 Exchange Online | 大通。访问用户.所有 |
| | EWS.访问用户.所有 |
| | Mail.Read |
| Microsoft Graph API | Mail.Read |
| | 邮件.阅读.共享 |
| | 邮件.阅读写作 |

- 应用授予已登录用户的完整用户模拟。 例如：

|资源 | 权限 |
| :- | :- |
| Microsoft Graph API| Directory.AccessAsUser.All |
| Azure REST API | user_impersonation |

为了避免这种情况，您应该参考[Office 365 中检测和修复非法同意授予](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants)，以识别和修复任何具有非法赠款或具有超过必要授权的应用程序的应用程序。 其次，[完全取消自助服务](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-user-consent)，[建立治理程序](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)。 最后，安排对应用权限的定期审核，并在不需要时将其删除。

#### <a name="consent-grants-recommended-reading"></a>同意授予建议阅读

- [微软图形 API 权限](https://docs.microsoft.com/graph/permissions-reference)

### <a name="user-and-group-settings"></a>用户和组设置

下面是如果没有明确业务需求，可以锁定的用户和组设置：

#### <a name="user-settings"></a>用户设置

- **外部用户**- 外部协作可以通过团队、Power BI、在线共享点和 Azure 信息保护等服务在企业中有机地进行。 如果您有用于控制用户启动的外部协作的显式约束，建议使用[Azure AD 授权管理](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)或控制操作（如通过帮助台）启用外部用户。 如果不想允许为服务进行有机外部协作，则可以[阻止成员完全邀请外部用户](https://docs.microsoft.com/azure/active-directory/b2b/delegate-invitations)。 或者，您还可以在外部用户邀请中[允许或阻止特定域](https://docs.microsoft.com/azure/active-directory/b2b/allow-deny-list)。
- **应用注册**- 启用应用注册时，最终用户可以自行注册应用程序并授予对其数据的权限。 应用注册的典型示例是启用 Outlook 插件的用户，或者启用 Alexa 和 Siri 等语音助理，以便阅读其电子邮件和日历或代表他们发送电子邮件。 如果客户决定关闭应用注册，InfoSec 和 IAM 团队必须参与异常管理（根据业务需求需要的应用注册），因为他们需要使用管理员帐户注册应用程序，并且很可能需要设计一个流程来操作流程。
- **管理门户**- 组织可以锁定 Azure 门户中的 Azure AD 边栏选项卡，以便非管理员无法访问 Azure 门户中的 Azure AD 管理并感到困惑。 转到 Azure AD 管理门户中的用户设置以限制访问：

![管理门户受限访问](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> 非管理员仍可以通过命令行和其他编程接口访问 Azure AD 管理接口。

#### <a name="group-settings"></a>组设置

**自助服务组管理/用户可以创建安全组/O365 组。** 如果云中的组当前没有自助服务计划，客户可能会决定将其关闭，直到他们准备好使用此功能。

#### <a name="groups-recommended-reading"></a>组建议阅读

- [什么是 Azure Active Directory B2B 协作？](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [将应用程序与 Azure 活动目录集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- [Azure 活动目录中的应用、权限和同意。](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)
- [使用组管理对 Azure 活动目录中资源的访问](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
- [在 Azure 活动目录中设置自助服务应用程序访问管理](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="traffic-from-unexpected-locations"></a>来自意外位置的流量

攻击者来自世界各地。 通过使用以位置为条件的条件访问策略来管理此风险。 条件访问策略[的位置条件](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)使您能够阻止对没有业务原因从中登录的位置进行访问。

![创建新命名位置](./media/active-directory-ops-guide/active-directory-ops-img14.png)

如果可用，请使用安全信息和事件管理 （SIEM） 解决方案来分析和查找跨区域的访问模式。 如果不使用 SIEM 产品，或者它未从 Azure AD 中引入身份验证信息，我们建议您使用[Azure 监视器](https://docs.microsoft.com/azure/azure-monitor/overview)来标识跨区域的访问模式。

## <a name="access-usage"></a>访问使用情况

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Azure AD 日志已存档并与事件响应计划集成

Azure AD 的登录活动、审核和风险事件对于故障排除、使用情况分析和取证调查至关重要。 Azure AD 通过保留期有限的 REST API 提供对这些源的访问。 安全信息和事件管理 （SIEM） 系统或等效存档技术是长期存储审核和支持的关键。 要启用 Azure AD 日志的长期存储，必须将它们添加到现有的 SIEM 解决方案或使用 Azure[监视器](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor)。 可用作事件响应计划和调查的一部分的存档日志。

#### <a name="logs-recommended-reading"></a>日志建议读取

- [Azure Active Directory 审核 API 参考](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)
- [Azure Active Directory 登录活动报告 API 参考](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-sign-in-activity-reference)
- [使用证书通过 Azure AD 报告 API 获取数据](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-with-certificates)
- [用于 Azure 活动目录标识保护的 Microsoft 图形](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)
- [Office 365 管理活动 API 参考](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference)
- [如何使用 Azure 活动目录电源 BI 内容包](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>总结

安全标识基础结构有 12 个方面。 此列表将帮助您进一步保护和管理凭据、定义身份验证体验、委派分配、测量使用情况以及根据企业安全状态定义访问策略。

- 将所有者分配给关键任务。
- 实施解决方案以检测弱密码或泄露的密码，改进密码管理和保护，并进一步保护用户访问资源。
- 管理设备标识，随时保护您的资源，不受任何位置的影响。
- 实现无密码身份验证。
- 在整个组织中提供标准化的单一登录机制。
- 将应用从 AD FS 迁移到 Azure AD，以实现更好的安全性和更一致的可管理性。
- 通过使用组将用户分配给应用程序，以允许更大的灵活性和大规模管理的能力。
- 配置基于风险的访问策略。
- 锁定旧版身份验证协议。
- 发现并补救非法同意授予。
- 锁定用户和组设置。
- 启用 Azure AD 日志的长期存储，以便进行故障排除、使用情况分析和取证调查。

## <a name="next-steps"></a>后续步骤

开始使用[标识治理操作检查和操作](active-directory-ops-guide-govern.md)。
