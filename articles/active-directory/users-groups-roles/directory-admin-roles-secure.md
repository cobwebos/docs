---
title: Azure AD 中管理员的安全访问方法 |Microsoft Docs
description: 确保组织的管理访问权限和管理帐户是安全的。 适用于配置 Azure AD、Azure 和 Microsoft Online Services 的系统架构师和 IT 专业人员。
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 04/30/2020
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c580a39db97e1ce50c3d244db3023bf422bca08
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837186"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>确保 Azure AD 中混合部署和云部署的特权访问安全性

业务资产的安全性取决于管理 IT 系统的特权帐户的完整性。 网络攻击者使用凭据盗窃攻击来以管理员帐户和其他特权访问为目标，以尝试获取对敏感数据的访问权限。

就云服务来说，预防此类攻击并对其做出响应是云服务提供商和客户的共同责任。 若要详细了解对终结点和云的最新威胁，请参阅 [Microsoft 安全情报报告](https://www.microsoft.com/security/operations/security-intelligence-report)。 本文有助于你制定路线图，消除当前计划和此处所述指南的差距。

> [!NOTE]
> Microsoft 致力于实现最高信任度、透明度、合标性和合规性。 若要详细了解 Microsoft 全球事件响应团队如何减轻云服务攻击影响，以及如何将安全措施内置到 Microsoft 商业产品和云服务中，请参阅 [Microsoft 信任中心 - 安全性](https://www.microsoft.com/trustcenter/security)；若要了解 Microsoft 符合性目标，请参阅 [Microsoft 信任中心 - 符合性](https://www.microsoft.com/trustcenter/compliance)。

在传统上，组织安全侧重于网络的入口点和出口点作为安全外围网络。 不过，Internet 上的 SaaS 应用和个人设备使此方法的效率更低。 在 Azure AD 中，我们将网络安全边界替换为你组织的标识层中的身份验证，并将用户分配到控制中的特权管理角色。 无论环境是本地、云还是混合环境，都必须保护其访问权限。

保护特权访问需要更改：

* 流程、管理做法和知识管理
* 技术组件，例如主机防御、帐户保护和标识管理

以你关注的 Microsoft 服务中管理和报告的方式保护你的特权访问。 如果你具有本地管理员帐户，请参阅在[保护特权访问](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)的 Active Directory 中的本地和混合特权访问指南。

> [!NOTE]
> 本文中的指南主要讲述 Azure Active Directory Premium 计划 P1 和 P2 中包括的 Azure Active Directory 功能。 Azure Active Directory Premium P2 包括在 EMS E5 套件和 Microsoft 365 E5 套件中。 本指南假定你的组织已经为你的用户购买 Azure AD Premium P2 许可证。 如果没有这些许可证，某些指南内容可能不适用于你的组织。 此外，在本文中，"全局管理员" 或 "租户管理员" 这一术语也是 "公司管理员" 或 "租户管理员"。

## <a name="develop-a-roadmap"></a>制定路线图

Microsoft 建议你制定并遵循一个路线图，防止特权访问受到网络攻击者的攻击。 始终可以根据你在组织内的现有职能和具体需求调整自己的路线图。 路线图的每个阶段都应提高攻击者攻击本地资产、云资产和混合资产的特许访问权限的成本和难度。 Microsoft 建议以下四个路线图阶段。 首先安排最有效和最快速的实现。 本文可作为你的指南，具体取决于 Microsoft 对网络攻击事件和响应实现的经验。 此路线图的时间线大致为近似值。

![采用时间线的路线图的各个阶段](./media/directory-admin-roles-secure/roadmap-timeline.png)

* 阶段 1（24-48 小时）：建议立即执行的关键项目

* 阶段 2（2-4 周）：减轻最常用攻击手段的危害

* 阶段 3（1-3 月）：构建管理活动的可见性和完全控制

* 阶段 4（至少六个月）：继续构建防御措施，进一步强化安全平台

设计此路线图框架是为了最大程度地使用可能已部署的 Microsoft 技术。 请考虑将从你已部署或正在考虑部署的其他供应商处的任何安全工具中捆绑到一起。

## <a name="stage-1-critical-items-to-do-right-now"></a>阶段1：要立即执行的关键项目

![阶段 1 要首先完成的关键项目](./media/directory-admin-roles-secure/stage-one.png)

路线图的阶段 1 着重于那些执行速度快且容易的关键任务。 建议在第一个 24-48 小时内快速执行这些项目，确保安全地进行基本级别的特权访问。 此阶段的安全特权访问路线图包括以下操作：

### <a name="general-preparation"></a>常规准备

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>启用 Azure AD Privileged Identity Management

建议你在 Azure AD 生产环境中启用 Azure AD Privileged Identity Management （PIM）。 启用 PIM 后，将收到有关特权访问角色更改的通知电子邮件。 将其他用户添加到高特权角色时，通知会提供早期警告。

Azure AD Privileged Identity Management 包括在 Azure AD Premium P2 或 EMS E5 中。 若要帮助保护对本地和云中的应用程序和资源的访问，请注册[企业移动性 + 安全性免费90天试用版](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)。 Azure AD Privileged Identity Management 和 Azure AD Identity Protection 使用 Azure AD 报告、审核和警报来监视安全活动。

打开 Azure AD Privileged Identity Management 后：

1. 使用作为 Azure AD 生产组织的全局管理员的帐户登录[Azure 门户](https://portal.azure.com/)。

2. 若要选择要在其中使用 Privileged Identity Management 的 Azure AD 组织，请在 Azure 门户右上角选择用户名。

3. 在 "Azure 门户" 菜单上，选择 "**所有服务**" 并筛选**Azure AD Privileged Identity Management**列表。

4. 打开“所有服务”列表中的 Open Privileged Identity Management，将其固定到仪表板。****

你的组织中使用 PIM 的第一个用户将分配到**安全管理员**和**特权角色管理员**角色。 只有特权角色管理员才能管理用户的 Azure AD 目录角色分配。 PIM 安全向导将引导您完成初始发现和分配体验。 现在可以退出向导，不做任何其他的更改。

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>对特许权限高的角色中的帐户进行标识和分类

打开 Azure AD Privileged Identity Management 之后，查看以下 Azure AD 角色的用户：

* 全局管理员
* 特权角色管理员
* Exchange Online 管理员
* SharePoint Online 管理员

如果你的组织中没有 Azure AD Privileged Identity Management，则可以使用[POWERSHELL API](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)。 从全局管理员角色开始，因为全局管理员在你的组织已订阅的所有云服务中具有相同的权限。 无论在何处分配这些权限，都将授予这些权限：在 Microsoft 365 管理中心、Azure 门户或 Microsoft PowerShell 的 Azure AD 模块中。

删除这些角色不再需要的任何帐户。 然后，对分配给管理员角色的剩余帐户进行分类：

* 分配给管理用户，但也用于非管理目的（例如，个人电子邮件）
* 分配给管理用户并仅用于管理目的
* 跨多个用户共享
* 适用于不受限紧急访问情况
* 适用于自动化脚本
* 适用于外部用户

#### <a name="define-at-least-two-emergency-access-accounts"></a>定义至少两个紧急访问帐户

用户可能被意外地锁定了其角色。 例如，如果联合本地标识提供程序不可用，则用户无法登录或激活现有的管理员帐户。 可以通过存储两个或更多个紧急访问帐户来准备意外访问。

紧急访问帐户有助于限制 Azure AD 组织内的特权访问。 这些帐户具有很高的特权，未分配给特定的个人。 紧急访问帐户仅限于不能使用正常管理帐户的 "中断玻璃" 方案的紧急情况。 确保控制并减少紧急帐户的使用情况，使之仅限需要此时间。

评估已经获得或有资格获得全局管理员角色的帐户。 如果未看到任何使用\*onmicrosoft.com 域的仅限云的帐户（对于 "中断玻璃" 紧急访问），请创建这些帐户。 有关详细信息，请参阅[在 Azure AD 中管理紧急访问管理帐户](directory-emergency-access.md)。

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>启用多重身份验证并注册所有其他特权高的单用户非联合管理员帐户

对于所有永久分配了一个或多个 Azure AD 管理员角色（全局管理员、特权角色管理员、Exchange Online 管理员和 SharePoint Online 管理员）的单个用户，要求在登录时进行 Azure 多重身份验证 (MFA)。 根据指南启用[针对管理员帐户的多重身份验证 (MFA)](../authentication/howto-mfa-userstates.md)，确保所有这些用户都已注册到 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)。 有关详细信息，可参阅[保护对 Office 365 中数据和服务的访问权限](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)指南的步骤 2 和步骤 3。 

## <a name="stage-2-mitigate-frequently-used-attacks"></a>阶段2：减少经常使用的攻击

![阶段 2 减轻常用攻击的危害](./media/directory-admin-roles-secure/stage-two.png)

路线图的阶段 2 着重于减轻进行凭据窃取和滥用时最常用的攻击手段导致的危害，并且可在约 2-4 周内实现。 此阶段的安全特权访问路线图包括以下操作。

### <a name="general-preparation"></a>常规准备

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>制作一个包括服务、所有者和管理员在内的清单

"自带设备办公" 的增加以及从家庭策略工作和无线连接的增长使监视连接到您的网络的用户至关重要。 安全审核可显示你的组织不支持的设备、应用程序和程序，以及表示高风险的设备、应用程序和程序。 有关详细信息，请参阅 [Azure 安全管理和监视概述](../../security/fundamentals/management-monitoring-overview.md)。 请确保在清单进程中包括所有下述任务。

* 标识具有管理角色的用户及其可以在其中进行管理的服务。
* 使用 Azure AD PIM 来找出组织中哪些用户具有对 Azure AD 的管理员访问权限。
* 除了在 Azure AD 中定义的角色，Office 365 还带有一组可以分配给组织中用户的管理员角色。 每个管理员角色都映射到常见业务功能，并向组织中的人员授予在[Microsoft 365 管理中心](https://admin.microsoft.com)执行特定任务的权限。 通过 Microsoft 365 管理中心找出组织中哪些用户可以通过管理员身份来访问 Office 365，包括通过不在 Azure AD 中管理的角色进行访问。 有关详细信息，请参阅[关于 office 365 管理角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)和[Office 365 的安全实践](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)。
* 在组织所依赖的服务（例如 Azure、Intune 或 Dynamics 365）中执行清点。
* 确保用于管理的帐户如下：

  * 已附加工作电子邮件地址
  * 已注册 Azure 多重身份验证，或在本地使用 MFA
* 要求用户提供进行管理访问的业务理由。
* 删除那些不需要管理员访问权限的个人和服务的此类权限。

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>确定管理角色中那些需要切换到工作或学校帐户的 Microsoft 帐户

如果初始全局管理员在开始使用 Azure AD 时重复使用其现有 Microsoft 帐户凭据，请将 Microsoft 帐户替换为单独的基于云或已同步的帐户。

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>对于全局管理员帐户，请确保使用单独的用户帐户和邮件转发功能

个人电子邮件帐户经常被网络攻击者钓鱼，这是使个人电子邮件地址不能被全局管理员帐户接受的风险。 若要帮助将 internet 风险与管理特权隔离开来，请为每个具有管理权限的用户创建专用帐户。

* 请确保为用户创建单独的帐户以执行全局管理任务
* 确保你的全局管理员不会意外地打开电子邮件或通过其管理员帐户运行程序
* 确保这些帐户将其电子邮件转发到工作邮箱

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>确保管理帐户的密码最近更改过

确保所有用户都已登录到其管理帐户，并在过去90天内至少更改了密码。 同时，验证是否有任何共享帐户最近更改了其密码。

#### <a name="turn-on-password-hash-synchronization"></a>启用密码哈希同步

Azure AD Connect 将用户密码哈希的哈希从本地 Active Directory 同步到基于云的 Azure AD 组织。 如果将联合身份验证与 Active Directory 联合身份验证服务（AD FS）结合使用，则可以将密码哈希同步用作备份。 如果本地 Active Directory 或 AD FS 服务器暂时不可用，则此备份可能非常有用。

密码哈希同步使用户能够使用登录到本地 Active Directory 实例时所用的同一密码登录到服务。 密码哈希同步允许 Identity Protection 通过将密码哈希与已知的密码进行比较来检测受损的凭据。 有关详细信息，请参阅[使用 Azure AD Connect 同步实现密码哈希同步](../hybrid/how-to-connect-password-hash-synchronization.md)。

#### <a name="require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users"></a>对于特权角色中的用户和公开的用户需要多重身份验证

Azure AD 建议你为所有用户需要多重身份验证（MFA）。 如果用户的帐户受到威胁（例如，财务专员），请务必考虑对其有重大影响的用户。 由于密码泄露，MFA 降低了攻击风险。

启用以下功能：

* [使用条件访问策略的 MFA](../authentication/howto-mfa-getstarted.md)，适用于组织中的所有用户。

如果使用 Windows Hello 企业版，则可以使用 Windows Hello 登录体验来满足 MFA 要求。 有关详细信息，请参阅 [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport)。

#### <a name="configure-identity-protection"></a>配置“标识保护”

Azure AD Identity Protection 是一种基于算法的监视和报告工具，用于检测影响组织标识的潜在漏洞。 可以配置自动响应，以便对那些检测到的可疑活动进行响应，并采取相应的解决措施。 有关详细信息，请参阅 [Azure Active Directory 标识保护](../active-directory-identityprotection.md)。

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>获取 Office 365 安全功能分数（如果使用 Office 365）

安全分数查看你正在使用的 Office 365 服务的设置和活动，并将其与 Microsoft 建立的基线进行比较。 你将根据你对安全做法的对齐方式获得评分。 对于 Office 365 商业高级版或企业版订阅，拥有管理员权限的任何人都可以访问安全[https://securescore.office.com](https://securescore.office.com/)分数。

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>查看 Office 365 安全性和符合性指南（如果使用 Office 365）

[安全性和符合性计划](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57)概述了 office 365 客户配置 office 365 并启用其他 EMS 功能的方法。 然后查看步骤 3-6，了解如何[保护对 Office 365 中数据和服务的访问权限](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)，并查看指南，了解如何[监视 Office 365 中的安全性和符合性](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6)。

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>配置 Office 365 活动监视（如果使用 Office 365）

监视你的组织中使用 Office 365 的用户识别拥有管理员帐户但可能不需要 Office 365 访问的人员，因为他们没有登录到这些门户。 有关详细信息，请参阅 [Microsoft 365 管理中心中的活动报表](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263)。

#### <a name="establish-incidentemergency-response-plan-owners"></a>确定事件/紧急情况响应计划所有者

建立成功的事件响应功能需要进行大量的规划和资源。 必须持续监视网络攻击，并建立事件处理的优先级。 收集、分析和报告事件数据来构建关系，并与其他内部组和计划所有者建立通信。 有关详细信息，请参阅 [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717)（Microsoft 安全响应中心）。

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>如果尚未这样做，请确保本地特权管理帐户的安全性

如果 Azure Active Directory 组织与本地 Active Directory 同步，则请遵循[安全特权访问路线图](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)：此阶段包括：

* 为需要执行本地管理任务的用户创建单独的管理员帐户
* 为 Active Directory 管理员部署特权访问工作站
* 为工作站和服务器创建唯一的本地管理员密码

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>管理 Azure 访问权限的组织所需的其他步骤

#### <a name="complete-an-inventory-of-subscriptions"></a>完成订阅清单

通过企业门户和 Azure 门户确定组织中用于托管生产应用程序的订阅。

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>从管理员角色中删除 Microsoft 帐户

来自其他程序的 Microsoft 帐户（例如 Xbox、Live 和 Outlook）不应用作组织订阅的管理员帐户。 从所有 Microsoft 帐户中删除管理员状态，并将替换为 Azure AD （例如chris@contoso.com，）工作或学校帐户。 出于管理目的，依赖于在 Azure AD 而不是其他服务中进行身份验证的帐户。

#### <a name="monitor-azure-activity"></a>监视 Azure 活动

Azure 活动日志提供 Azure 中订阅级别事件的历史记录。 它提供的信息涉及谁创建、更新和删除了什么资源，以及这些事件的发生时间。 有关详细信息，请参阅[审核和接收关于 Azure 订阅中重要操作的通知](../../azure-monitor/platform/quick-audit-notify-action-subscription.md)。

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>组织通过 Azure AD 管理其他云应用的访问权限时所需的其他步骤

#### <a name="configure-conditional-access-policies"></a>配置条件访问策略

为本地应用程序和托管在云中的应用程序准备条件访问策略。 如果你有用户加入工作区的设备，请[通过使用 Azure Active Directory 设备注册来获取有关设置本地条件性访问](../active-directory-device-registration-on-premises-setup.md)的详细信息。

## <a name="stage-3-take-control-of-admin-activity"></a>阶段3：控制管理活动

![阶段3：控制管理活动](./media/directory-admin-roles-secure/stage-three.png)

阶段3基于阶段2的缓解，并应在大约1-3 个月内实现。 此阶段的安全特权访问路线图包括以下组件。

### <a name="general-preparation"></a>常规准备

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>完成对管理员角色用户的访问权限审核

更多企业用户通过云服务获取特权访问权限，这可能导致非托管访问。 如今，用户可以成为 Office 365、Azure 订阅管理员的全局管理员，也可以通过 SaaS 应用进行管理员访问。

你的组织应将普通业务事务作为无特权用户处理，然后仅在需要时才授予管理员权限。 完成访问评审，以确定和确认有资格激活管理权限的用户。

我们建议：

1. 确定哪些用户是 Azure AD 管理员，启用按需恰时管理访问权限以及基于角色的安全控制。
2. 将没有明确理由使用管理员特许访问权限的用户转换为其他角色（如果没有符合条件的角色，则删除这些用户）。

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>针对所有用户持续推出更强的身份验证

要求高度公开的用户使用 Azure MFA 或 Windows Hello 等新式强身份验证。 高度公开的用户的示例包括：

* C 套件管理人员
* 高级管理器
* 关键 IT 和安全人员

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>使用专用工作站进行 Azure AD 管理

攻击者可能会尝试以特权帐户为目标，使其能够破坏数据的完整性和真实性。 它们通常使用恶意代码更改程序逻辑，或窥视管理员输入凭据。 特权访问工作站 (PAW) 为免受 Internet 攻击和威胁媒介的敏感任务提供专用操作系统。 从日常使用的工作站和设备中分离这些敏感任务和帐户可提供强大的保护：

* 网页仿冒攻击
* 应用程序和操作系统漏洞
* 模拟攻击
* 凭据盗窃攻击，如击键记录、哈希传递和票证传递

通过部署特权访问工作站，你可以降低管理员在未被强制使用的桌面环境中输入其凭据的风险。 有关详细信息，请参阅[特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)。

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>查看美国国家标准与技术协会提供的有关如何处理事件的建议

国家标准与技术协会 (NIST) 提供处理事件的指南，尤其是分析事件相关数据并确定每个事件的适当响应的指南。 有关详细信息，请参阅 [The (NIST) Computer Security Incident Handling Guide (SP 800-61, Revision 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)（(NIST) 计算机安全事件处理指南（SP 800-61，修订版 2））。

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>对其他管理角色实施适用于 JIT 的 Privileged Identity Management (PIM)

对于 Azure Active Directory，请使用 [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) 功能。 对特权角色进行有时间限制的激活时，可执行以下操作：

* 激活管理员权限以执行特定任务
* 在激活过程中强制实施 MFA
* 使用警报将所做的带外更改通知给管理员
* 允许用户在预先配置的时间内保留其特权访问
* 允许安全管理员：

  * 发现所有特权标识
  * 查看审核报告
  * 创建访问评审以识别有资格激活管理员权限的每个用户

如果已在使用 Azure AD Privileged Identity Management，请根据需要调整具有时限的特权的时间范围（例如，设置维护时段）。

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>确定是否向基于密码的登录协议公开（如果使用 Exchange Online）

我们建议你确定可能会对组织造成灾难性威胁的每个潜在用户。 对于这些用户，请制定严格的身份验证要求，并使用 Azure AD 的条件性访问，以防止用户使用用户名和密码登录到其电子邮件。 你可以[使用条件性访问阻止旧身份验证](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication)，并且可以通过 Exchange online[阻止基本身份验证](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online)。

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>针对 Office 365 角色完成角色审核评估（如果使用 Office 365）

评估是否所有管理员用户都有正确的角色（根据此评估执行删除和重新分配操作）。

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>审核在 Office 365 中使用的安全事件管理方法，并将其与自己的组织进行比较

可以从 [Microsoft Office 365 中的安全事件管理](https://www.microsoft.com/download/details.aspx?id=54302)下载此报告。

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>继续保护本地特权管理帐户

如果 Azure Active Directory 连接到本地 Active Directory，则请遵循[安全特权访问路线图](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)：阶段 2 中的指南。 在此阶段中，你可以：

* 为所有管理员部署特权访问工作站
* 需要进行 MFA
* 使用足够的管理员来维护域控制器，降低域的受攻击面
* 部署用于攻击检测的高级威胁评估

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>管理 Azure 访问权限的组织所需的其他步骤

#### <a name="establish-integrated-monitoring"></a>建立集成监视

[Azure 安全中心](../../security-center/security-center-intro.md)：

* 跨 Azure 订阅提供集成的安全监视和策略管理
* 帮助检测可能被忽略的威胁
* 适用于一系列广泛的安全解决方案

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>清点托管的虚拟机中的特权帐户

通常不需要向用户授予对所有 Azure 订阅或资源的无限制权限。 使用 Azure AD 管理员角色仅授予用户执行其作业所需的访问权限。 你可以使用 Azure AD 管理员角色让一个管理员只管理订阅中的 Vm，另一个管理员可以管理同一订阅中的 SQL 数据库。 有关详细信息，请参阅 [Azure 门户中基于角色的访问控制入门](../../role-based-access-control/overview.md)。

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>为 Azure AD 管理员角色实施 PIM

将 Privileged Identity Management 与 Azure AD 管理员角色配合使用，对 Azure 资源的访问权限进行管理、控制和监视。 使用 PIM 可通过降低权限的暴露时间并通过报表和警报增加对其使用的可见性，从而进行保护。 有关详细信息，请参阅[使用 Privileged Identity Management 管理对 Azure 资源的 RBAC 访问](../../role-based-access-control/pim-azure-resource.md)。

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>使用 Azure 日志集成将相关的 Azure 日志发送到 SIEM 系统

可以通过 Azure 日志集成将原始日志从 Azure 资源集成到组织的现有安全信息和事件管理 (SIEM) 系统。 [Azure 日志集成](../../security/fundamentals/azure-log-integration-overview.md)从 windows 事件查看器日志和 Azure 资源收集 windows 事件：

* Azure 活动日志
* Azure 安全中心警报
* Azure 资源日志

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>组织通过 Azure AD 管理其他云应用的访问权限时所需的其他步骤

#### <a name="implement-user-provisioning-for-connected-apps"></a>为连接的应用实施用户预配

Azure AD 允许你自动创建和维护云应用中的用户标识，如 Dropbox、Salesforce 和 ServiceNow。 有关详细信息，请参阅[使用 Azure AD 自动预配和取消预配 SaaS 应用程序的用户](../app-provisioning/user-provisioning.md)。

#### <a name="integrate-information-protection"></a>集成信息保护

Microsoft Cloud App Security 允许你调查文件并基于 Azure 信息保护分类标签设置策略，从而更好地查看和控制云数据。 对云中的文件进行扫描和分类，并应用 Azure 信息保护标签。 有关详细信息，请参阅 [Azure 信息保护集成](https://docs.microsoft.com/cloud-app-security/azip-integration)。

#### <a name="configure-conditional-access"></a>配置条件访问

基于组、位置和应用程序敏感性为[SaaS 应用](https://azure.microsoft.com/overview/what-is-saas/)和 Azure AD 连接的应用配置条件访问。 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>监视连接的云应用中的活动

建议使用[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) ，以确保在连接的应用程序中也能保护用户访问权限。 此功能保护了企业对云应用的访问权限，并保护了管理员帐户，使你能够：

* 扩展对云应用的可见性和控制
* 针对访问权限、活动和数据共享创建策略
* 自动标识风险活动、异常行为和威胁
* 防止数据泄漏
* 将风险降至最低并自动实施威胁防护和策略

Cloud App Security SIEM 代理将 Cloud App Security 与 SIEM 服务器集成，以便集中监视 Office 365 警报和活动。 它还可以在服务器上运行，从 Cloud App Security 拉取警报和活动并将其流式传输到 SIEM 服务器中。 有关详细信息，请参阅 [SIEM 集成](https://docs.microsoft.com/cloud-app-security/siem)。

## <a name="stage-4-continue-building-defenses"></a>阶段4：继续构建防御

![阶段4：采用有效的安全状况](./media/directory-admin-roles-secure/stage-four.png)

路线图的阶段4应在六个月内实现。 完成你的路线图，以增强你的特权访问防护，防范目前已知的潜在攻击。 对于明天的安全威胁，我们建议您查看安全，以提高成本并降低以您的环境为目标的攻击者的成功率。

保护特权访问对于为业务资产建立安全保障非常重要。 不过，它应是一个完整的安全程序的一部分，它提供持续的安全保障。 此程序应包含如下元素：

* 策略
* 操作
* 信息安全性
* 服务器
* 应用程序
* 电脑
* 设备
* 云结构

在管理特权访问帐户时，建议采用以下做法：

* 确保管理员将日常业务作为无特权用户进行
* 仅在需要时才授予特权访问权限，并在以后将其删除（实时）
* 保留与特权帐户相关的审核活动日志

若要深入了解如何构建完整的安全路线图，请参阅 [Microsoft 云 IT 体系结构资源](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources)。 若要与 Microsoft 服务联系以帮助你实现路线图的任何部分，请联系你的 Microsoft 代表或参阅[构建关键网络防护，以保护你的企业](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx)。

这个最后进行的阶段的安全特权访问路线图包括以下组件。

### <a name="general-preparation"></a>常规准备

#### <a name="review-admin-roles-in-azure-ad"></a>查看 Azure AD 中的管理员角色

确定当前内置 Azure AD 管理员角色是否仍然是最新的，并确保用户只是所需的角色。 通过 Azure AD，您可以分配单独的管理员来提供不同的功能。 有关详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](directory-assign-admin-roles.md)。

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>审核那些可以管理已加入 Azure AD 的设备的用户

有关详细信息，请参阅[如何配置联接到混合 Azure Active Directory 的设备](../device-management-hybrid-azuread-joined-devices-setup.md)。

#### <a name="review-members-of-built-in-office-365-admin-roles"></a>审核[内置 Office 365 管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)的成员
如果不使用 Office 365，请跳过此步骤。
‎
#### <a name="validate-incident-response-plan"></a>验证事件响应计划

若要改进计划，Microsoft 建议你经常验证计划的执行是否复合预期：

* 审核现有的路线图，看是否漏掉了什么
* 根据事后分析，修改现有或定义新的做法
* 确保在整个组织内分发更新的事件响应计划和实践


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>管理 Azure 访问权限的组织所需的其他步骤 

确定是否需要[将 Azure 订阅所有权转让给其他帐户](../../cost-management-billing/manage/billing-subscription-transfer.md)。
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>“不受限”紧急措施：在紧急情况下需要做的事情

![适用于不受限紧急访问的帐户](./media/directory-admin-roles-secure/emergency.jpeg)

1. 向重要经理和安全官员通知有关事件的信息。

2. 审核攻击 playbook。

3. 访问 "中断玻璃" 帐户用户名和密码组合，以便登录到 Azure AD。

4. 通过[提交 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)从 Microsoft 获取帮助。

5. 查看 [Azure AD 登录报告](../reports-monitoring/overview-reports.md)。 事件发生的时间与报表中包含的事件可能有一段时间。

6. 对于混合环境，如果你的本地基础结构联合并且 AD FS 服务器不可用，则可以暂时从联合身份验证切换为使用密码哈希同步。此开关会将域联合身份验证回托管身份验证，直至 AD FS 服务器可用。

7. 监视特权帐户的电子邮件。

8. 确保保存相关日志的备份，以备将来可能会发生的取证和法庭调查之用。

若要详细了解 Microsoft Office 365 如何处理安全事件，请参阅 [Security Incident Management in Microsoft Office 365](https://aka.ms/Office365SIM)（Microsoft Office 365 中的安全事件管理）。

## <a name="faq-answers-for-securing-privileged-access"></a>常见问题解答：保护特权访问的答案  

**问：** 如果还没有实施任何安全访问组件，该怎么办？

**答：** 定义至少两个“不受限”帐户，指定对特权管理员帐户实施 MFA，并将用户帐户与全局管理员帐户分开。

**文：** 在遭到攻击后，首先需要解决的最重要问题是什么？

**答案：** 请确保对高度公开的个人要求最强的身份验证。

**问：** 如果停用了特权管理员，该怎么办？

**答案：** 创建始终保持最新状态的全局管理员帐户。

**问：** 如果只剩下一个全局管理员，并且无法访问，会发生什么情况？

**答：** 使用其中一个“不受限”帐户，立即获取特许访问权限。

**问：** 如何保护组织中的管理员？

**答：** 让管理员始终以标准的“非特权”用户身份执行日常业务操作。

**问：** 在 Azure AD 中创建管理员帐户的最佳做法是什么？

**答：** 针对特定的管理任务保留特许访问权限。

**问：** 可以通过哪些现有工具减少持久进行的管理访问？

**答：** Privileged Identity Management (PIM) 和 Azure AD 管理员角色。

**问：** 对于将管理员帐户同步到 Azure AD，Microsoft 的态度是什么？

**答案：** 第0层管理员帐户仅用于本地 AD 帐户。 此类帐户通常不会与云中 Azure AD 同步。 第0层管理员帐户包括帐户、组以及对本地 Active Directory 林、域、域控制器和资产进行直接或间接管理控制的其他资产。

**问：** 如何防止管理员在门户中分配随机的管理员访问权限？

**答：** 要求所有用户和大多数管理员使用非特权帐户。 一开始确定组织的占用空间，从而确定应该向哪些管理员帐户授予特权。 和监视新创建的管理用户。

## <a name="next-steps"></a>后续步骤

* [Microsoft 信任中心确保产品安全性](https://www.microsoft.com/trustcenter/security) - Microsoft 云产品和服务的安全特性

* [Microsoft 信任中心 - 符合性](https://www.microsoft.com/trustcenter/compliance/complianceofferings) - Microsoft 推出的一组范围广泛的符合性产品/服务，适用于云服务

* 有关[如何执行风险评估的指导](https://www.microsoft.com/trustcenter/guidance/risk-assessment)-管理 Microsoft 云服务的安全性和合规性要求

### <a name="other-microsoft-online-services"></a>其他 Microsoft Online Services

* [Microsoft Intune 安全性](https://www.microsoft.com/trustcenter/security/intune-security) - Intune 从云端提供移动设备管理、移动应用程序管理和电脑管理功能。

* [Microsoft Dynamics 365 安全性](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 是 Microsoft 的基于云的解决方案，该方案将客户关系管理 (CRM) 和企业资源规划 (ERP) 功能统合在一起。
