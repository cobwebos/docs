---
title: Azure 标识和访问安全最佳实践 | Microsoft 文档
description: 本文提供一系列有关使用内置 Azure 功能进行标识管理和访问控制的最佳实践。
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/17/2018
ms.author: barclayn
ms.openlocfilehash: 64d940552f2790c08e8087f279990d0a6c595bac
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245711"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure 标识管理和访问控制安全最佳实践

许多人将标识视为安全性的新边界层，从传统的以网络为中心的角度来看可以接管这种角色。 安全关注与投资主轴的这种演变来源于这样一个事实：网络周边的漏洞越来越多，在 [BYOD](https://aka.ms/byodcg) 设备和云应用程序呈爆炸性增长之前，周边防御不如以前那么有效。

本文介绍一系列 Azure 标识管理和访问控制安全最佳实践。 这些最佳做法衍生自我们的 [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) 经验和客户经验。

对于每项最佳做法，本文将说明：

* 最佳实践是什么
* 为何要启用该最佳实践
* 如果无法启用该最佳实践，可能的结果是什么
* 最佳实践的可能替代方案
* 如何学习启用最佳实践

这篇 Azure 标识管理和访问控制安全最佳实践以共识以及 Azure 平台功能和特性集（因为在编写本文时已存在）为基础。 看法和技术将随着时间改变，本文会定期更新以反映这些更改。

本文中介绍的 Azure 标识管理和访问控制安全最佳实践包括：

* 将标识视为主要安全边界
* 集中化标识管理
* 启用单一登录
* 启用条件访问
* 启用密码管理
* 对用户强制执行多重身份验证
* 使用基于角色的访问控制
* 降低特权帐户的泄露风险
* 控制资源所在的位置

## <a name="treat-identity-as-the-primary-security-perimeter"></a>将标识视为主要安全边界

许多人认为标识是主要安全边界。 这表明以往对网络安全的关注发生了转变。 网络边界出现越来越多的漏洞，在 [BYOD](https://aka.ms/byodcg) 设备和云应用程序激增之前，该边界防御不似之前那样有效。
[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) 是用于标识和访问管理的 Azure 解决方案。 Azure AD 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 它将核心目录服务、应用程序访问管理和标识保护融入一个解决方案中。

以下部分列出了使用 Azure AD 实现标识和访问安全性的最佳做法。

## <a name="centralize-identity-management"></a>集中化标识管理

在[混合标识](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?)方案中，我们建议集成本地目录和云目录。 通过集成，IT 团队可以在一个位置管理帐户，无需考虑帐户创建的位置。 集成可提供通用标识用于访问云和本地资源，从而提高用户的生产率。


**最佳做法**：将本地目录与 Azure AD 进行集成。  
**详细信息**：使用 [Azure AD Connect](../active-directory/connect/active-directory-aadconnect.md) 将本地目录与云目录同步。

**最佳做法**：启用密码哈希同步。  
**详细信息**：密码哈希同步功能用于将用户密码哈希的哈希从本地 Active Directory 实例同步到基于云的 Azure AD 实例。

即使决定使用 Active Directory 联合身份验证服务 (AD FS) 或其他标识提供者进行联合身份验证，也可以选择性地将密码哈希同步设置为在本地服务器发生故障或临时不可用时的备份方式。 这样用户就可以在登录到该服务时使用登录到本地 Active Directory 实例时使用的密码。 如果用户在其他未连接到 Azure AD 的服务上使用过相同的电子邮件地址和密码，则还可以通过 Identity Protection 将这些密码哈希与已知泄露的密码进行比较，从而检测出那些泄露的凭据。

有关详细信息，请参阅[使用 Azure AD Connect 同步实现密码哈希同步](../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)。

未将其本地标识与云标识集成的组织在管理帐户方面可能开销更大。 这种开销增加了出错和安全漏洞的可能性。

## <a name="enable-single-sign-on"></a>启用单一登录

在移动优先、云优先的世界中，你希望能够从任意位置实现对设备、应用和服务的单一登录 (SSO)，以便你的用户随时随地都能高效工作。 如果要管理多个标识解决方案，则不仅会给 IT 人员造成管理问题，而且用户还必须记住多个密码。

通过对所有应用和资源使用相同的标识解决方案，可以实现 SSO。 并且不论资源是位于本地还是云中，用户均可以使用相同凭据集登录和访问所需资源。

**最佳做法**：启用 SSO。  
**详细信息**：Azure AD [将本地 Active Directory](../active-directory/connect/active-directory-aadconnect.md) 扩展到云。 用户可以将他们的主要工作或学校帐户用于他们加入域的设备、公司资源以及完成工作所需的所有 Web 和 SaaS 应用程序。 用户无需记住多组用户名和密码，系统会根据组织的组成员身份和员工身份的状态，自动预配（或取消设置）应用程序访问权限。 可以控制库应用或者自行通过 [Azure AD 应用程序代理](../active-directory/active-directory-application-proxy-get-started.md)开发和发布的本地应用的该访问权限。

用户可使用 SSO 基于 Azure AD 中的工作或学校帐户访问 [SaaS 应用程序](../active-directory/active-directory-appssoaccess-whatis.md)。 这不仅适用于 Microsoft SaaS 应用，还适用于其他应用，例如 [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) 和 [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md)。 应用程序可配置为使用 Azure AD 作为[基于 SAML 的标识](../active-directory/fundamentals-identity.md)提供者。 作为安全控制机制，Azure AD 不会发出允许用户登录应用程序的令牌，除非用户已通过 Azure AD 获取了访问权限。 可以直接或者通过用户所属的组授予访问权限。

在用户拥有多个密码的情况下，没有为用户和应用程序创建用于建立 SSO 的通用标识的组织更易发生泄漏。 这种情况增加了用户重复使用同一密码或使用弱密码的可能性。

## <a name="turn-on-conditional-access"></a>启用条件访问

用户可以通过从任意位置使用各种设备和应用访问组织的资源。 作为 IT 管理员，你希望确保这些设备符合安全性和符合性标准。 仅关注谁可以访问资源不再能满足需求。

为了平衡安全与效率，在做出访问控制决策之前，需要考虑资源的访问方式。 使用 Azure AD 条件访问便可处理该需求。 使用条件访问时，可以根据条件就云应用的访问实施自动化的访问控制决策。

**最佳做法**：管理和控制对公司资源的访问。  
**详细信息**：根据组、位置和应用程序敏感性为 SaaS 应用和 Azure AD 连接应用配置 Azure AD [条件访问](../active-directory/active-directory-conditional-access-azure-portal.md)。

## <a name="enable-password-management"></a>启用密码管理

如果有多个租户或者你想要允许用户[重置自己的密码](../active-directory/active-directory-passwords-update-your-own-password.md)，则必须使用适当的安全策略来防止滥用。

**最佳做法**：为用户设置自助服务密码重置 (SSPR)。  
**详细信息**：使用 Azure AD [自助服务密码重置](../active-directory-b2c/active-directory-b2c-reference-sspr.md)功能。

**最佳做法**：监视 SSPR 的使用情况或者是否真的在使用 SSPR。  
**详细信息**：通过使用 Azure AD [密码重置注册活动报表](../active-directory/active-directory-passwords-get-insights.md)监视正在注册的用户。 Azure AD 提供的报表功能可帮助使用预生成的报表来回答问题。 如果有相应的授权，还可以创建自定义查询。

## <a name="enforce-multi-factor-verification-for-users"></a>对用户强制执行多重身份验证

建议对所有用户要求进行双重验证。 这包括组织中的管理员和其他人员，如果他们的帐户泄露，可能会产生重大影响（例如，财务官员）。

要求双重验证有多种选项。 最佳选项取决于你的目标、正在运行的 Azure AD 版本以及许可计划。 请参阅[如何要求对用户进行双重验证](../active-directory/authentication/howto-mfa-userstates.md)了解最佳选项。 有关许可和定价的详细信息，请参阅 [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) 和 [Azure 多重身份验证](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)定价页。

以下是启用双重验证的选项和优势：

**选项 1**：[通过更改用户状态启用多重身份验证](../active-directory/authentication/howto-mfa-userstates.md)。   
**优势**：这是要求进行双重验证的传统方法。 它适用于[云中的 Azure 多重身份验证和 Azure 多重身份验证服务器](../active-directory/authentication/concept-mfa-whichversion.md)。 使用此方法要求用户每次登录时都执行双重验证并重写条件访问策略。

**选项 2**：[通过条件访问策略启用多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md#enable-multi-factor-authentication-with-conditional-access)。   
**优势**：此选项允许使用[条件访问](../active-directory/active-directory-conditional-access-azure-portal.md)在特定条件下提示进行双重验证。 特定条件可以是用户从不同位置、不受信任的设备或你认为存在风险的应用程序登录。 定义要求双重验证的特定条件可以避免不断提示用户这种令人不快的用户体验。

这是为用户启用双重验证最灵活的方式。 启用条件访问策略仅适用于云中的 Azure 多重身份验证，并且是 Azure AD 的高级功能。 有关此方法的详细信息，请参阅[部署基于云的 Azure 多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)。

**选项 3**：通过评估 [Azure AD Identity Protection](../active-directory/authentication/tutorial-risk-based-sspr-mfa.md) 的用户和登录风险，通过条件访问策略启用多重身份验证。   
**优势**：此选项使你能够：

- 检测影响组织标识的潜在漏洞。
- 配置自动响应与组织标识相关的可疑操作。
- 调查可疑事件，并采取适当的措施进行解决。

此方法使用 Azure AD Identity Protection 风险评估来确定是否需要基于所有云应用程序的用户和登录风险进行双重验证。 此方法需要 Azure Active Directory P2 授权。 有关此方法的详细信息，请参阅 [Azure Active Directory Identity Protection](../active-directory/identity-protection/overview.md)。

> [!Note]
> 选项 1 通过更改用户状态启用多重身份验证来替代条件访问策略。 因为选项 2 和 3 使用条件访问策略，所以无法将选项 1 与其他两个选项结合使用。

未添加额外标识保护层（如双重验证）的组织将更容易受到凭据窃取攻击。 凭据窃取攻击可能导致数据泄漏。

## <a name="use-role-based-access-control-rbac"></a>使用基于角色的访问控制 (RBAC)

对于想要实施数据访问安全策略的组织而言，必须根据[需要知道](https://en.wikipedia.org/wiki/Need_to_know)和[最低权限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)安全策略限制访问权限。 可使用[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 向特定范围的用户、组和应用程序分配权限。 角色分配的范围可以是订阅、资源组或单个资源。

可以使用 Azure 中[内置的 RBAC](../role-based-access-control/built-in-roles.md) 角色向用户分配权限。 未使用 RBAC 等功能实施数据访问控制的组织可能会给其用户分配不必要的权限。 允许用户访问他们不应有权访问的某些类型的数据（例如，对业务有重大影响的数据）可能会导致数据泄漏。

## <a name="lower-exposure-of-privileged-accounts"></a>降低特权帐户的泄露风险

保护特权访问是保护业务资产的首要步骤。 减少拥有访问权限的人员以保护信息或资源安全，这样可以减小恶意用户获得访问权限，或者已授权用户无意中影响敏感资源的可能性。

特权帐户是指掌控和管理 IT 系统的帐户。 网络攻击者会攻击这些帐户来获取组织数据和系统的访问权限。 为了保护特权访问，应隔离此类帐户和系统，使其免受恶意用户的威胁。

建议制定并遵循一个路线图，防止特权访问受到网络攻击者的攻击。 有关创建详细路线图以保护在 Azure AD、Microsoft Azure、Office 365 和其他云服务中托管或报告的标识和访问的信息，请查看[确保 Azure AD 中混合部署和云部署的特权访问安全性](../active-directory/users-groups-roles/directory-admin-roles-secure.md)。

以下内容总结了[确保 Azure AD 中混合部署和云部署的特权访问安全性](../active-directory/users-groups-roles/directory-admin-roles-secure.md)中介绍的最佳做法：

**最佳做法**：管理、控制和监视对特权帐户的访问权限。   
**详细信息**：启用 [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/active-directory-securing-privileged-access.md)。 启用 Privileged Identity Management 以后，会收到有关特权访问角色更改的通知电子邮件。 向目录中的高特权角色添加更多用户时，这些通知相当于早期警告。

**最佳做法**：对角色为高特权角色的帐户进行标识和分类。   
**详细信息**：启用 Azure AD Privileged Identity Management 后，请查看角色为全局管理员、特权角色管理员和其他高特权角色的用户。 请删除在这些角色中不再需要的任何帐户，并对剩余的分配给管理员角色的帐户分类：

- 单独分配给管理用户，可用于非管理性目的（例如，个人电子邮件）
- 单独分配给管理用户，按规定只能用于管理目的
- 跨多个用户共享
- 适用于紧急访问情况
- 适用于自动化脚本
- 适用于外部用户

**最佳做法**：实行“实时”(JIT) 访问可进一步降低特权的曝光时间，并提高对特权帐户使用情况的可见性。   
**详细信息**：利用 Azure AD Privileged Identity Management，可以：

- 限制用户只接受他们的权限 JIT。
- 分配时限更短的角色，确信权限会自动撤消。

**最佳做法**：定义至少两个紧急访问帐户。   
**详细信息**：可以使用紧急访问帐户来帮助组织限制现有 Azure Active Directory 环境中的特权访问。 这些帐户拥有极高的特权，不要将其分配给特定的个人。 紧急访问帐户只能用于不能使用正常管理帐户的情况。 组织必须将紧急账户的使用限制在必要时间范围内。

评估已经获得或有资格获得全局管理员角色的帐户。 如果使用 `*.onmicrosoft.com` 域（用于紧急访问）看不到任何仅限云的帐户，请创建此类帐户。 有关详细信息，请参阅“在 Azure AD 中管理紧急访问管理帐户”。

**最佳做法**：启用多重身份验证，并注册所有其他高特权单用户非联合管理员帐户。  
**详细信息**：对于所有永久分配了一个或多个 Azure AD 管理员角色（全局管理员、特权角色管理员、Exchange Online 管理员和 SharePoint Online 管理员）的单个用户，要求在登录时进行 Azure 多重身份验证。 根据指南启用[针对管理员帐户的多重身份验证](../active-directory/authentication/howto-mfa-userstates.md)，确保所有这些用户都已[注册](https://aka.ms/mfasetup)。

**最佳做法**：采取措施来缓解最常用的攻击技术的冲击。  
**详细信息**：[确定管理角色中需要切换到工作或学校帐户的 Microsoft 帐户](../active-directory/users-groups-roles/directory-admin-roles-secure.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[对于全局管理员帐户，请确保使用单独的用户帐户和邮件转发功能](../active-directory/users-groups-roles/directory-admin-roles-secure.md)  

[确保最近更改过管理帐户的密码](../active-directory/users-groups-roles/directory-admin-roles-secure.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[启用密码哈希同步](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)  

[要求对所有特权角色用户和公开的用户进行多重身份验证](../active-directory/users-groups-roles/directory-admin-roles-secure.md#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[获取 Office 365 安全功能分数（如果使用 Office 365）](../active-directory/users-groups-roles/directory-admin-roles-secure.md#obtain-your-office-365-secure-score-if-using-office-365)  

[查看 Office 365 安全性和符合性指南（如果使用 Office 365）](../active-directory/users-groups-roles/directory-admin-roles-secure.md#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[配置 Office 365 活动监视（如果使用 Office 365）](../active-directory/users-groups-roles/directory-admin-roles-secure.md#configure-office-365-activity-monitoring-if-using-office-365)  

[确定事件/紧急情况响应计划所有者](../active-directory/users-groups-roles/directory-admin-roles-secure.md#establish-incidentemergency-response-plan-owners)  

[保护本地特权管理帐户](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)

如果不保护特权访问，你可能会拥有过多高特权角色用户，并且更易受到攻击。 恶意操作者（包括网络攻击者）通常会以管理员帐户和特权访问的其他元素为目标，通过凭据窃取获得敏感数据和系统的访问权限。

## <a name="control-locations-where-resources-are-created"></a>控制创建资源的位置

非常重要的一点是，既要允许云操作员执行任务，同时又要防止他们违反管理组织资源所需的惯例。 想要控制创建资源的位置的组织应该对这些位置进行硬编码。

可以使用 [Azure 资源管理器](../azure-resource-manager/resource-group-overview.md)创建安全策略，其中的定义描述了会明确遭到拒绝的操作或资源。 可以在所需范围（例如订阅、资源组或是单个资源）分配这些策略定义。

> [!NOTE]
> 安全策略与 RBAC 不同。 它们实际上使用 RBAC 授权用户来创建这些资源。
>
>

无法控制资源创建方式的组织更容易因用户创建的资源超过所需数目，而产生滥用服务的情况。 强化资源创建过程是保护多租户方案的重要步骤。

## <a name="actively-monitor-for-suspicious-activities"></a>主动监视可疑活动

主动身份监视系统可以快速检测可疑行为并触发警报以进行进一步调查。 下表列出了两个可帮助组织监视其标识的 Azure AD 功能：

**最佳做法**：有一种方法可确定：

- [未受跟踪](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md)的登录尝试。
- 针对特定帐户的[暴力](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md)攻击。
- 从多个位置的登录尝试。
- 从[受感染的设备](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md)登录。
- 可疑 IP 地址。

**详细信息**：使用 Azure AD Premium [异常报告](../active-directory/active-directory-view-access-usage-reports.md)。 制定相应的流程和过程，使 IT 管理员每天或按需（通常在事件响应方案中）运行这些报告。

**最佳做法**：安装一个主动监视系统，用于通知风险，并且可以根据业务需求调整风险等级（高、中或低）。   
**详细信息**：使用 [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md)，它会在自己的仪表板上标记当前风险并通过电子邮件发送每日摘要通知。 要帮助保护组织的标识，可以配置基于风险的策略，该策略可在达到指定风险级别时自动响应检测到的问题。

不主动监视其标识系统的组织将面临用户凭据泄露的风险。 如果不知道有人通过这些凭据实施可疑活动，组织就无法缓解这种类型的威胁。

## <a name="next-step"></a>后续步骤

有关通过 Azure 设计、部署和管理云解决方案时可以使用的更多安全最佳做法，请参阅 [Azure 安全最佳做法和模式](security-best-practices-and-patterns.md)。
