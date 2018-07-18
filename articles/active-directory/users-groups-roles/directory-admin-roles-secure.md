---
title: 在 Azure AD 中确保管理访问权限安全性的最佳做法 | Microsoft Docs
description: 确保组织的管理访问权限和管理帐户是安全的。 适用于配置 Azure AD、Azure 和 Microsoft Online Services 的系统架构师和 IT 专业人员。
services: active-directory
keywords: ''
author: curtand
manager: mtillman
ms.author: curtand
ms.date: 06/25/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer, MarkMorow
ms.openlocfilehash: 1d08518a23dfe4a9cc63bf3f35ee69ea1815595f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38239945"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>确保 Azure AD 中混合部署和云部署的特权访问安全性

在现代组织中，大多数或所有业务资产的安全性取决于管理 IT 系统的特权帐户的完整性。 恶意操作者（包括网络攻击者）通常会以管理员帐户和特权访问的其他元素为目标，试图通过凭据窃取攻击快速获得敏感数据和系统的访问权限。 就云服务来说，预防此类攻击并对其做出响应是云服务提供商和客户的共同责任。 若要详细了解对终结点和云的最新威胁，请参阅 [Microsoft 安全情报报告](https://www.microsoft.com/security/sir/default.aspx)。 本文有助于你制定路线图，消除当前计划和此处所述指南的差距。

> [!NOTE] 
> Microsoft 致力于实现最高信任度、透明度、合标性和合规性。 若要详细了解 Microsoft 全球事件响应团队如何减轻云服务攻击影响，以及如何将安全措施内置到 Microsoft 商业产品和云服务中，请参阅 [Microsoft 信任中心 - 安全性](https://www.microsoft.com/en-us/trustcenter/security)；若要了解 Microsoft 符合性目标，请参阅 [Microsoft 信任中心 - 符合性](https://www.microsoft.com/en-us/trustcenter/compliance)。

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
就大多数组织来说，业务资产的安全性取决于管理 IT 系统的特权帐户的完整性。 网络攻击者会重点攻击针对基础结构系统（例如 Active Directory 和 Azure Active Directory）的特权访问，以便获得组织敏感数据的访问权限。 

传统方法着重于对充当主要安全外围的网络的入口点和出口点进行保护，但随着 Internet 上 SaaS 应用和个人设备使用的增多，这种方法的效果也越来越不佳。 在复杂的现代企业中，对网络安全外围的自然替代方式是在组织的标识层中使用身份验证和授权控件。 

特权管理帐户可以有效地控制这种新的“安全外围”。 必须保护特权访问，不管环境是本地环境、云环境还是混合了本地托管服务和云托管服务的环境。 若要保护管理访问权限免受恶意攻击者的攻击，必须采用完整而缜密的方法将组织的系统与风险因素隔离开来。 

保护特权访问需要对以下各项进行更改：
* 流程、管理做法和知识管理
* 技术组件，例如主机防御、帐户保护和标识管理

本文的着重点主要在如何创建一个路线图，以便对 Azure AD、Microsoft Azure、Office 365 等云服务中托管或报告的标识和访问权限进行保护。 对于具有本地管理帐户的组织，请参阅[确保特权访问安全性](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)中提供的指南，了解如何通过 Active Directory 管理本地的和混合的特权访问。 

> [!NOTE] 
> 本文中的指南主要讲述 Azure Active Directory Premium 计划 P1 和 P2 中包括的 Azure Active Directory 功能。 Azure Active Directory Premium P2 包括在 EMS E5 套件和 Microsoft 365 E5 套件中。 本指南假定你的组织已经为你的用户购买 Azure AD Premium P2 许可证。 如果没有这些许可证，某些指南内容可能不适用于你的组织。 另外，本文中的“全局管理员”一词与“公司管理员”或“租户管理员”是同义词。

## <a name="develop-a-roadmap"></a>制定路线图 

Microsoft 建议你制定并遵循一个路线图，防止特权访问受到网络攻击者的攻击。 始终可以根据你在组织内的现有职能和具体需求调整自己的路线图。 路线图的每个阶段都应提高攻击者攻击本地资产、云资产和混合资产的特许访问权限的成本和难度。 Microsoft 建议实施以下四个路线图阶段：这个建议的路线图先根据 Microsoft 在网络攻击事件和响应实施方面的经验，对最有效且最快速的实施措施进行计划。 此路线图的时间线为近似值。

![采用时间线的路线图的各个阶段](./media/directory-admin-roles-secure/roadmap-timeline.png)

* 阶段 1（24-48 小时）：建议立即执行的关键项目

* 阶段 2（2-4 周）：减轻最常用攻击手段的危害

* 阶段 3（1-3 月）：构建管理活动的可见性和完全控制

* 阶段 4（至少六个月）：继续构建防御措施，进一步强化安全平台

设计此路线图框架是为了最大程度地使用可能已部署的 Microsoft 技术。 也可利用关键的最新安全技术和即将发布的安全技术，并集成其他供应商提供的安全工具，这些工具可能已部署或正考虑部署。 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>阶段 1：建议立即执行的关键项目

![阶段 1](./media/directory-admin-roles-secure/stage-one.png)

路线图的阶段 1 着重于那些执行速度快且容易的关键任务。 建议在第一个 24-48 小时内快速执行这些项目，确保安全地进行基本级别的特权访问。 此阶段的安全特权访问路线图包括以下操作：

### <a name="general-preparation"></a>常规准备

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>启用 Azure AD Privileged Identity Management

如果尚未启用 Azure AD Privileged Identity Management (PIM)，请在生产租户中启用它。 启用 Privileged Identity Management 以后，会收到有关特权访问角色更改的通知电子邮件。 向目录中特许权限高的角色添加更多用户时，这些通知相当于早期警告。

Azure AD Privileged Identity Management 包括在 Azure AD Premium P2 或 EMS E5 中。 这些解决方案有助于保护对本地环境和云中环境中的应用程序和资源的访问权限。 如果还没有 Azure AD Premium P2 或 EMS E5，但希望评估本路线图中提到的更多功能，请注册[企业移动性 + 安全性 90 天免费试用版](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)。 有了这些许可证试用版，就可以尝试 Azure AD Privileged Identity Management 和 Azure AD Identity Protection，以便使用 Azure AD 高级安全报告、审核和警报来监视活动。

启用 Azure AD Privileged Identity Management 以后，请执行以下操作：

1. 使用生产租户的全局管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。

2. 若要选择的租户是需要在其中使用 Privileged Identity Management 的租户，请选择 Azure 门户右上角的用户名。

3. 选择“所有服务”，然后从列表中筛选出“Azure AD Privileged Identity Management”。

4. 打开“所有服务”列表中的 Open Privileged Identity Management，将其固定到仪表板。

系统会自动在租户中为第一个在租户中使用 Azure AD Privileged Identity Management 的人分配“安全管理员”和“特权角色管理员”角色。 只有特权角色管理员才能管理用户的 Azure AD 目录角色分配。 另外，在添加 Azure AD Privileged Identity Management 以后，会显示安全向导，引导你完成初始发现和分配体验。 现在可以退出向导，不做任何其他的更改。 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>对特许权限高的角色中的帐户进行标识和分类 

启用 Azure AD Privileged Identity Management 后，请查看目录角色为“全局管理员”、“特权角色管理员”、“Exchange Online 管理员”和“SharePoint Online 管理员”的用户。 如果租户中没有 Azure AD PIM，则可使用 [PowerShell API](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)。 请从全局管理员角色开始，因为该角色是通用的：分配了此管理员角色的用户在组织已订阅的所有云服务中具有相同的权限，不管该角色是通过 Office 365 门户或 Azure 门户分配的，还是通过用于 Microsoft PowerShell 的 Azure AD 模块分配的。 

请删除在这些角色中不再需要的任何帐户，并对剩余的分配给管理员角色的帐户分类：

* 单独分配给管理用户，也可用于非管理性目的（例如，个人电子邮件）
* 单独分配给管理用户，按规定只能用于管理目的
* 跨多个用户共享
* 适用于不受限紧急访问情况
* 适用于自动化脚本
* 适用于外部用户

#### <a name="define-at-least-two-emergency-access-accounts"></a>定义至少两个紧急访问帐户 

确保不要进入这样一种境地：因无法登录或激活现有的充当管理员的单个用户帐户而意外地导致自己无法管理 Azure AD 租户。 例如，如果组织与某个本地标识提供者联合进行身份验证，则当该标识提供者不可用时，用户就会无法进行本地登录。 可在租户中存储两个或更多个紧急访问帐户，缓解意外丧失管理访问权限造成的影响。

可以使用紧急访问帐户来帮助组织限制现有 Azure Active Directory 环境中的特权访问。 这些帐户拥有极高的特权，不要将其分配给特定的个人。 紧急访问帐户只能用于“不受限”紧急情况，即不能使用正常管理帐户的情况。 组织务必控制和减少紧急帐户的使用，只在必要时使用。 

评估已经获得或有资格获得全局管理员角色的帐户。 如果使用 *.onmicrosoft.com 域（用于“不受限”紧急访问）看不到任何仅限云的帐户，请创建此类帐户。 有关详细信息，请参阅[在 Azure AD 中管理紧急访问管理帐户](directory-emergency-access.md)。

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>启用多重身份验证，并注册所有其他的特许权限高的单用户非联合管理员帐户 

对于所有永久分配了一个或多个 Azure AD 管理员角色（全局管理员、特权角色管理员、Exchange Online 管理员和 SharePoint Online 管理员）的单个用户，要求在登录时进行 Azure 多重身份验证 (MFA)。 根据指南启用[针对管理员帐户的多重身份验证 (MFA)](../authentication/howto-mfa-userstates.md)，确保所有这些用户都已注册到 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)。 有关详细信息，可参阅[保护对 Office 365 中数据和服务的访问权限](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)指南的步骤 2 和步骤 3。 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>阶段 2：减轻最常用攻击手段的危害

![阶段 2](./media/directory-admin-roles-secure/stage-two.png)

路线图的阶段 2 着重于减轻进行凭据窃取和滥用时最常用攻击手段的危害。根据设计，大约在 2-4 周内实施。 此阶段的安全特权访问路线图包括以下操作。

### <a name="general-preparation"></a>常规准备

#### <a name="conduct-a-inventory-of-services-owners-and-admins"></a>制作一个包括服务、所有者和管理员在内的清单

随着自带设备 (BYOD) 和在家办公策略实施人数的增加以及企业无线连接人数的增加，必须对连接到网络的人员进行监视。 有效的安全审核通常会显示那些在网络上运行但不受 IT 部门支持（因此可能不安全）的设备、应用程序和程序。 有关详细信息，请参阅 [Azure 安全管理和监视概述](../../security/security-management-and-monitoring-overview.md)。 请确保在清单进程中包括所有下述任务。 

* 标识具有管理角色的用户及其可以在其中进行管理的服务。
* 通过 Azure AD PIM 找出组织中哪些用户具有 Azure AD 的管理员访问权限，包括未在阶段 1 中列出的其他角色。
* 除了在 Azure AD 中定义的角色，Office 365 还带有一组可以分配给组织中用户的管理员角色。 每个管理员角色都映射到常用业务功能，为组织中的人员提供在 Office 365 管理中心执行特定任务的权限。 通过 Office 管理中心找出组织中哪些用户可以通过管理员身份来访问 Office 365，包括通过不在 Azure AD 中管理的角色进行访问。 有关详细信息，请参阅[关于 Office 365 管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)和 [Office 365 安全方面的最佳做法](https://support.office.com/article/Security-best-practices-for-Office-365-9295e396-e53d-49b9-ae9b-0b5828cdedc3)。
* 在组织所依赖的其他服务（例如 Azure、Intune 或 Dynamics 365）中执行清单操作。
* 确保管理员帐户（用于管理目的的帐户，不只是用户的日常帐户）有附加的工作电子邮件地址，并且已注册使用 Azure MFA 或使用本地 MFA。
* 要求用户提供进行管理访问的业务理由。
* 删除那些不需要管理员访问权限的个人和服务的此类权限。

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>确定管理角色中那些需要切换到工作或学校帐户的 Microsoft 帐户 

有时候，某个组织的初始全局管理员会在开始使用 Azure AD 时重复使用其现有的 Microsoft 帐户凭据。 应将这些 Microsoft 帐户替换为单个基于云的或同步的帐户。 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>对于全局管理员帐户，请确保使用单独的用户帐户和邮件转发功能 

全局管理员帐户不应有个人电子邮件地址，因为个人电子邮件地址经常会受到网络攻击者的钓鱼攻击。 若要将 Internet 风险（钓鱼攻击、无意的 Web 浏览）与管理权限隔离开来，请为每个具有管理权限的用户创建专用的帐户。 

如果尚未这样做，请为执行全局管理员任务的用户创建单独的帐户，确保他们不会无意中打开电子邮件或运行与其管理员帐户相关联的程序。 确保这些帐户将其电子邮件转发到工作邮箱。  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>确保管理帐户的密码最近更改过

确保所有用户在过去 90 天中至少有一次登录到其管理帐户并更改了密码。 另外，对于多个用户知道密码的共享帐户，确保最近更改了这些帐户的密码。

#### <a name="turn-on-password-hash-synchronization"></a>启用密码哈希同步

密码哈希同步是一种功能，用于将用户密码哈希的哈希从本地 Active Directory 实例同步到基于云的 Azure AD 实例。 即使决定使用 Active Directory 联合身份验证服务 (AD FS) 或其他标识提供者进行联合身份验证，也可以选择性地设置密码哈希同步，作为在本地基础结构（例如 AD 或 ADFS 服务器）发生故障或变得临时不可用时的备用身份验证方式。 这样用户就可以在登录到该服务时使用登录到本地 AD 实例时使用的密码。 另外，如果用户在其他未连接到 Azure AD 的服务上使用过相同的电子邮件地址和密码，则还可以通过 Identity Protection 将这些密码哈希与已知泄露的密码进行比较，从而检测出那些泄露的凭据。  有关详细信息，请参阅[使用 Azure AD Connect 同步实现密码哈希同步](./../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)。

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>要求对所有特权角色用户和公开的用户进行多重身份验证 (MFA)

Azure AD 建议你要求对所有用户进行多重身份验证 (MFA)，这包括管理员用户，以及所有其他在帐户泄露情况下会造成重大影响的用户（例如，财务官）。 这可降低因密码泄露而受到攻击的风险。

启用以下功能：

* [为暴露风险高的帐户启用 MFA](../authentication/multi-factor-authentication-security-best-practices.md)，例如为组织执行官的帐户启用 MFA 
* [为每个与单个用户相关联的管理员帐户启用 MFA](../authentication/howto-mfa-userstates.md)，这适用于其他连接的 SaaS 应用 
* 为 Microsoft SaaS 应用的所有管理员启用 MFA，包括其角色在 Exchange Online 和 Office 门户中管理的管理员

如果使用 Windows Hello for Business，则可通过 Windows Hello 登录体验来满足 MFA 要求。 有关详细信息，请参阅 [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport)。 

#### <a name="configure-identity-protection"></a>配置 Identity Protection 

Azure AD Identity Protection 是一种基于算法的监视和报告工具，可以用来检测影响组织标识的潜在漏洞。 可以配置自动响应，以便对那些检测到的可疑活动进行响应，并采取相应的解决措施。 有关详细信息，请参阅 [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)。

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>获取 Office 365 安全功能分数（如果使用 Office 365）

安全功能分数会找出你正在使用的 Office 365 服务（例如 OneDrive、SharePoint 和 Exchange），然后查看你的设置和活动并将其与 Microsoft 建立的基线进行比较。 将会根据你遵循最佳安全做法的情况对你评分。 对于 Office 365 商业高级版或企业版订阅，任何具有管理员权限（全局管理员或自定义管理员角色）的用户都可以访问安全功能分数，网址为：[https://securescore.office.com](https://securescore.office.com/)。

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>查看 Office 365 安全性和符合性指南（如果使用 Office 365）

[安全性和符合性计划](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57)概述了 Office 365 客户应如何配置 Office 365 并利用其他 EMS 功能。 然后查看步骤 3-6，了解如何[保护对 Office 365 中数据和服务的访问权限](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)，并查看指南，了解如何[监视 Office 365 中的安全性和符合性](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6)。


#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>配置 Office 365 活动监视（如果使用 Office 365）

可以监视组织中的人员如何使用 Office 365 服务，以便标识那些具有管理帐户但因未登录到相关门户而可能不需要 Office 365 访问权限的用户。 有关详细信息，请参阅 [Office 365 管理中心中的活动报表](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263)。

#### <a name="establish-incidentemergency-response-plan-owners"></a>确定事件/紧急情况响应计划所有者

有效地进行事件响应是一项复杂的任务。 因此，建立成功的事件响应功能需要严格的计划和大量的资源。 必须持续监视网络攻击并建立相关流程，确定事件处理的优先级。 若要与其他内部小组和计划所有者建立关系并通信，必须通过有效的方法来收集、分析和报告数据。 有关详细信息，请参阅 [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717)（Microsoft 安全响应中心）。 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>如果尚未这样做，请确保本地特权管理帐户的安全性

如果 Azure Active Directory 租户与本地 Active Directory 同步，则请遵循[安全特权访问路线图](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)：阶段 1 中的指南。 其中包括：为需要执行本地管理任务的用户创建单独的管理员帐户、为 Active Directory 管理员部署特权访问工作站，以及针对工作站和服务器创建唯一的本地管理员密码。

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>管理 Azure 访问权限的组织所需的其他步骤

#### <a name="complete-an-inventory-of-subscriptions"></a>完成订阅清单

通过企业门户和 Azure 门户确定组织中用于托管生产应用程序的订阅。 

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>从管理员角色中删除 Microsoft 帐户

不应将其他程序（例如 Xbox、Live、Outlook）的 Microsoft 帐户用作组织订阅的管理员帐户。 从所有 Microsoft 帐户中删除管理员状态，将其替换为 Active Directory 工作或学校帐户（例如，chris@contoso.com）。

#### <a name="monitor-azure-activity"></a>监视 Azure 活动

Azure 活动日志提供 Azure 中订阅级别事件的历史记录。 它提供的信息涉及谁创建、更新和删除了什么资源，以及这些事件的发生时间。 有关详细信息，请参阅[审核和接收关于 Azure 订阅中重要操作的通知](../../monitoring-and-diagnostics/monitor-quick-audit-notify-action-in-subscription.md)。


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>组织通过 Azure AD 管理其他云应用的访问权限时所需的其他步骤 

#### <a name="configure-conditional-access-policies"></a>配置条件访问策略

为本地应用程序和托管在云中的应用程序准备条件访问策略。 如果有加入了用户工作区的设备，请参阅[使用 Azure Active Directory 设备注册设置本地条件访问](../active-directory-device-registration-on-premises-setup.md)中提供的详细信息。


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>阶段 3：构建管理活动的可见性并对其进行完全控制

![阶段 3](./media/directory-admin-roles-secure/stage-three.png)

阶段 3 以阶段 2 的缓解措施为基础，按设计应在大约 1-3 个月内实施。 此阶段的安全特权访问路线图包括以下组件。

### <a name="general-preparation"></a>常规准备

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>完成对管理员角色用户的访问权限审核

更多的公司用户是通过云服务获得特许访问权限的，因此可能会导致平台越来越缺少管理。 这包括成为 Office 365 全局管理员、Azure 订阅管理员的用户，以及可以通过 VM 或 SaaS 应用行使管理访问权限的用户。 组织应采取与上述操作不同的操作，让所有员工（尤其是管理员）以非特权用户身份处理日常业务事务，只在需要的时候才行使管理员权限。 由于管理员角色的用户自初次核准之后会产生数量的增长，因此请进行访问权限审核，标识并确认每个有资格激活管理权限的用户。 

请执行以下操作：

* 确定哪些用户是 Azure AD 管理员，启用按需恰时管理访问权限以及基于角色的安全控制。
* 将没有明确理由使用管理员特许访问权限的用户转换为其他角色（如果没有符合条件的角色，则删除这些用户）。

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>针对所有用户持续推出更强的身份验证 

要求 C 套件高管、高级经理、IT 和安全部门的关键人员以及其他身份暴露可能性高的用户进行现代的强身份验证，例如 Azure MFA 或 Windows Hello。 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>使用专用工作站进行 Azure AD 管理

攻击者会尝试以特权帐户为目标获取组织数据和系统的访问权限，这样就可以通过恶意代码来更改程序逻辑，或者窥视管理员输入某个凭据，对数据的完整性和验证造成损害。 特权访问工作站 (PAW) 为敏感任务提供专用操作系统，使其免受 Internet 攻击和威胁向量攻击。 从日常使用的工作站和设备中分离这些敏感任务和帐户可提供强大的保护，使其免受网络钓鱼攻击、应用程序和操作系统漏洞攻击、各种模拟攻击和凭据盗窃攻击（例如按键日志记录、哈希传递和票证传递）。 部署特权访问工作站可以降低管理员输入管理员凭据时遇到的风险，除非是在经过强化的桌面环境中。 有关详细信息，请参阅[特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)。

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>查看美国国家标准与技术协会提供的有关如何处理事件的建议 

国家标准与技术协会 (NIST) 提供处理事件的指南，尤其是分析事件相关数据并确定每个事件的适当响应的指南。 有关详细信息，请参阅 [The (NIST) Computer Security Incident Handling Guide (SP 800-61, Revision 2)](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)（(NIST) 计算机安全事件处理指南（SP 800-61，修订版 2））。

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>对其他管理角色实施适用于 JIT 的 Privileged Identity Management (PIM)

对于 Azure Active Directory，请使用 [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) 功能。 对特权角色进行有时间限制的激活时，可执行以下操作：

* 激活执行特定任务所需的管理权限
* 在激活过程中强制实施 MFA
* 使用警报将所做的带外更改通知给管理员
* 允许用户在预配置的时限内保留某些特权
* 允许安全管理员发现所有特权标识、查看审核报告，以及创建访问权限审核，以便标识每个有资格激活管理权限的用户

如果已在使用 Azure AD Privileged Identity Management，请根据需要调整具有时限的特权的时间范围（例如，设置维护时段）。

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>确定是否向基于密码的登录协议公开（如果使用 Exchange Online）

在过去，协议假定用户名/密码组合已嵌入设备中、电子邮件帐户中、电话中，等等。 但现在，随着云中的网络攻击风险的增加，建议你确定每一位在其凭据泄漏的情况下可能会对组织造成灾难性后果的用户，通过实施强身份验证要求和条件访问，阻止其通过用户名/密码登录电子邮件。 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>针对 Office 365 角色完成角色审核评估（如果使用 Office 365）

评估是否所有管理员用户都有正确的角色（根据此评估执行删除和重新分配操作）。

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>审核在 Office 365 中使用的安全事件管理方法，并将其与自己的组织进行比较

可以从 [Microsoft Office 365 中的安全事件管理](https://www.microsoft.com/download/details.aspx?id=54302)下载此报告。

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>继续保护本地特权管理帐户

如果 Azure Active Directory 连接到本地 Active Directory，则请遵循[安全特权访问路线图](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)：阶段 2 中的指南。 其中包括：为所有管理员部署特权访问工作站、要求 MFA、使用 Just Enough Admin 进行 DC 维护、缩小域攻击面、部署 ATA 进行攻击检测。

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>管理 Azure 访问权限的组织所需的其他步骤

#### <a name="establish-integrated-monitoring"></a>建立集成监视

[Azure 安全中心](../../security-center/security-center-intro.md)提供跨 Azure 订阅的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>清点托管的虚拟机中的特权帐户

大多数情况下，不需为用户提供可以访问所有 Azure 订阅或资源的不受限权限。 可以使用 Azure AD 管理员角色对组织中的职责进行分隔，仅向需要进行特定作业的用户授权相应的访问权限。 例如，使用 Azure AD 管理员角色，让一个管理员只管理员某个订阅中的 VM，让另一个管理员管理同一订阅中的 SQL 数据库。 有关详细信息，请参阅 [Azure 门户中基于角色的访问控制入门](../../role-based-access-control/overview.md)。

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>为 Azure AD 管理员角色实施 PIM

将 Privileged Identity Management 与 Azure AD 管理员角色配合使用，对 Azure 资源的访问权限进行管理、控制和监视。 可以降低权限的暴露时间并通过报表和警报增加对使用情况的可见性，从而使用 PIM 保护特权帐户免受网络攻击。 有关详细信息，请参阅[使用 Privileged Identity Management 管理对 Azure 资源的 RBAC 访问](../../role-based-access-control/pim-azure-resource.md)。

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>使用 Azure 日志集成将相关的 Azure 日志发送到 SIEM 系统 

可以通过 Azure 日志集成将原始日志从 Azure 资源集成到组织的现有安全信息和事件管理 (SIEM) 系统。 [Azure 日志集成](../../security/security-azure-log-integration-overview.md)从 Windows 事件查看器日志收集 Windows 事件，以及从 Azure 活动日志、Azure 安全中心警报和 Azure 诊断日志收集 Azure 资源。 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>组织通过 Azure AD 管理其他云应用的访问权限时所需的其他步骤

#### <a name="implement-user-provisioning-for-connected-apps"></a>为连接的应用实施用户预配

可以通过 Azure AD 自动创建、维护和删除云 (SaaS) 应用程序（如 Dropbox、Salesforce、ServiceNow 等）中的用户标识。 有关详细信息，请参阅[使用 Azure AD 自动预配和取消预配 SaaS 应用程序的用户](../active-directory-saas-app-provisioning.md)。

#### <a name="integrate-information-protection"></a>集成信息保护

可以使用 MCAS 根据 Azure 信息保护分类标签来调查文件并设置策略，从而增加云中数据的可见性并加强对此类数据的控制。 对云中的文件进行扫描和分类，并应用 Azure 信息保护标签。 有关详细信息，请参阅 [Azure 信息保护集成](https://docs.microsoft.com/cloud-app-security/azip-integration)。

#### <a name="configure-conditional-access"></a>配置条件访问

根据组、位置和应用程序敏感性为 [SaaS 应用](https://azure.microsoft.com/overview/what-is-saas/)和 Azure AD 连接应用配置条件访问。 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>监视连接的云应用中的活动

若要确保用户的访问权限在连接的应用程序中 也受保护，建议你利用 [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)。 这样可以保护企业对云应用的访问权限，以及保护管理员帐户，方便你执行以下操作：

* 扩展对云应用的可见性和控制
* 针对访问权限、活动和数据共享创建策略
* 自动标识风险活动、异常行为和威胁
* 防止数据泄漏
* 将风险降至最低并自动实施威胁防护和策略

Cloud App Security SIEM 代理将 Cloud App Security 与 SIEM 服务器集成，以便集中监视 Office 365 警报和活动。 它还可以在服务器上运行，从 Cloud App Security 拉取警报和活动并将其流式传输到 SIEM 服务器中。 有关详细信息，请参阅 [SIEM 集成](https://docs.microsoft.com/cloud-app-security/siem)。

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>阶段 4：持续构建防御，采取更主动的安全姿态


![阶段 4](./media/directory-admin-roles-secure/stage-four.png)

路线图的阶段 4 以阶段 3 的可见性为基础，按设计应在 6 个月或更长时间内实施。 完成路线图有助于制定强大的特权访问保护措施，以便防范那些目前已知存在的可能攻击。 遗憾的是，安全威胁手段始终在发展且不断变化，因此建议你不断地查看安全状况，重点提高以你的环境为目标的攻击者的攻击成本，降低其成功率。

确保特权访问的安全性是为现代组织中的业务资产建立安全保障的第一步，也是很重要的一步，但对于完整的安全计划来说，这不是唯一的一步。完整的计划还包括各种元素，例如策略、操作、信息安全性、服务器、应用程序、电脑、设备、云结构，以及其他可以提供持续安全保障的组件。 

除了管理特权访问帐户，建议你经常审核以下项目：

* 确保管理员以非特权用户的身份执行日常业务操作。
* 仅在需要的时候才授予特许访问权限，操作完后即将其删除（恰时）。
* 保留并查看与特权帐户相关的审核活动。

若要深入了解如何构建完整的安全路线图，请参阅 [Microsoft 云 IT 体系结构资源](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources)。 若要深入了解如何运用 Microsoft 服务来帮助实现上述任意主题中的功能，请联系 Microsoft 代表或参阅 [Build critical cyber defenses to protect your enterprise](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx)（构建关键的网络防御来保护企业）。

这个最后进行的阶段的安全特权访问路线图包括以下组件。

### <a name="general-preparation"></a>常规准备

#### <a name="review-admin-roles-in-azure-active-directory"></a>查看 Azure Active Directory 中的管理员角色 

确定当前的内置 Azure AD 管理员角色是否仍为最新，并确保用户只有与权限相对应的所需角色和委托。 使用 Azure AD 时，可以指定不同的管理员来履行不同的职责。 有关详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](directory-assign-admin-roles.md)。

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>审核那些可以管理已加入 Azure AD 的设备的用户

有关详细信息，请参阅[如何配置联接到混合 Azure Active Directory 的设备](../device-management-hybrid-azuread-joined-devices-setup.md)。

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>审核[内置 Office 365 管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)的成员
前提是使用 Office 365。
‎
#### <a name="validate-incident-response-plan"></a>验证事件响应计划

若要改进计划，Microsoft 建议你经常验证计划的执行是否复合预期：

* 审核现有的路线图，看是否漏掉了什么
* 根据事后分析修订现有的最佳做法，或者定义新的最佳做法
* 确保通过组织分发更新的事件响应计划和最佳做法


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>管理 Azure 访问权限的组织所需的其他步骤 

确定是否需要[将 Azure 订阅所有权转让给其他帐户](../../billing/billing-subscription-transfer.md)。
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>“不受限”紧急措施：在紧急情况下需要做的事情

![紧急情况](./media/directory-admin-roles-secure/emergency.jpeg)

1. 将事件相关信息通知给主要的管理员和安全官。

2. 审核攻击 playbook。 

3. 访问登录 Azure AD 所需的“不受限”帐户用户名/密码组合。 

4. 通过[提交 Azure 支持请求](../../azure-supportability/how-to-create-azure-support-request.md)从 Microsoft 获取帮助。

5. 查看 [Azure AD 登录报告](../active-directory-reporting-azure-portal.md)。 事件发生之后，可能需要一段时间该事件才会出现在报告中。

6. 对于混合环境，如果使用联合身份验证但 AD FS 服务器不可用，则可能需要临时从联合身份验证方式切换出来，改用密码哈希同步。这样就会将域联合身份验证恢复成托管身份验证，直至 AD FS 服务器可用。

7. 监视特权帐户的电子邮件。

8. 确保保存相关日志的备份，以备将来可能会发生的取证和法庭调查之用。

若要详细了解 Microsoft Office 365 如何处理安全事件，请参阅 [Security Incident Management in Microsoft Office 365](http://aka.ms/Office365SIM)（Microsoft Office 365 中的安全事件管理）。

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>常见问题解答：我们收到的有关如何保护特权访问的常见问题  


**问：** 如果还没有实施任何安全访问组件，该怎么办？

**答：** 定义至少两个“不受限”帐户，指定对特权管理员帐户实施 MFA，并将用户帐户与全局管理员帐户分开。


**文：** 在遭到攻击后，首先需要解决的最重要问题是什么？

**答：** 务必要求对身份暴露可能性高的个人进行最强的身份验证。


**问：** 如果停用了特权管理员，该怎么办？

**答：** 请创建一个始终保持最新的全局管理员帐户。


**问：** 如果只剩下一位全局管理员，而该管理员联系不上，该怎么办？ 

**答：** 使用其中一个“不受限”帐户，立即获取特许访问权限。


**问：** 如何保护组织中的管理员？

**答：** 让管理员始终以标准的“非特权”用户身份执行日常业务操作。
 

**问：** 在 Azure AD 中创建管理员帐户的最佳做法是什么？

**答：** 针对特定的管理任务保留特许访问权限。


**问：** 可以通过哪些现有工具减少持久进行的管理访问？

**答：** Privileged Identity Management (PIM) 和 Azure AD 管理员角色。


**问：** 对于将管理员帐户同步到 Azure AD，Microsoft 的态度是什么？

**答：** 0 级管理员帐户（包括可以直接或间接对 AD 林、域或域控制器以及所有资产进行直接或间接管理控制的帐户、组和其他资产）只能用于本地 AD 帐户，通常不对适用于云的 Azure AD 进行同步。 


**问：** 如何防止管理员在门户中分配随机的管理员访问权限？

**答：** 要求所有用户和大多数管理员使用非特权帐户。 一开始确定组织的占用空间，从而确定应该向哪些管理员帐户授予特权。 监视新创建的管理用户。


## <a name="next-steps"></a>后续步骤

* [Microsoft 信任中心确保产品安全性](https://www.microsoft.com/en-us/trustcenter/security) - Microsoft 云产品和服务的安全特性

* 
  [Microsoft 信任中心 - 符合性](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) - Microsoft 推出的一组范围广泛的符合性产品/服务，适用于云服务

* [有关如何执行风险评估的指南](https://www.microsoft.com/en-us/trustcenter/guidance/risk-assessment) - 管理 Microsoft 云服务的安全性和符合性要求

### <a name="other-ms-online-services"></a>其他 MS Online Services 

* [Microsoft Intune 安全性](https://www.microsoft.com/en-us/trustcenter/security/intune-security) - Intune 从云端提供移动设备管理、移动应用程序管理和电脑管理功能。

* [Microsoft Dynamics 365 安全性](https://www.microsoft.com/en-us/trustcenter/security/dynamics365-security) – Dynamics 365 是 Microsoft 的基于云的解决方案，该方案将客户关系管理 (CRM) 和企业资源规划 (ERP) 功能统合在一起。

 
