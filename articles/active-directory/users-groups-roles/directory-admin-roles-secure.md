---
title: Azure AD 中面向管理员的安全访问做法 | Microsoft Docs
description: 确保组织的管理访问权限和管理员帐户是安全的。 适用于配置 Azure AD、Azure 和 Microsoft Online Services 的系统架构师和 IT 专业人员。
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 04/30/2020
ms.topic: conceptual
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: d303f8a04a9159eeb4dc5e78ef09b57f5a966c72
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691354"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>确保 Azure AD 中混合部署和云部署的特权访问安全性

业务资产的安全性取决于管理 IT 系统的特权帐户的完整性。 网络攻击者会针对管理员帐户和其他特权访问权限发起凭据盗窃攻击，以尝试获取对敏感数据的访问权限。

就云服务来说，预防此类攻击并对其做出响应是云服务提供商和客户的共同责任。 若要详细了解对终结点和云的最新威胁，请参阅 [Microsoft 安全情报报告](https://www.microsoft.com/security/operations/security-intelligence-report)。 本文有助于你制定路线图，消除当前计划和此处所述指南的差距。

> [!NOTE]
> Microsoft 致力于实现最高信任度、透明度、合标性和合规性。 若要详细了解 Microsoft 全球事件响应团队如何减轻云服务攻击影响，以及如何将安全措施内置到 Microsoft 商业产品和云服务中，请参阅 [Microsoft 信任中心 - 安全性](https://www.microsoft.com/trustcenter/security)；若要了解 Microsoft 符合性目标，请参阅 [Microsoft 信任中心 - 符合性](https://www.microsoft.com/trustcenter/compliance)。

在传统上，组织安全性侧重于用作安全外围的网络入口点和出口点。 但是，Internet 上的 SaaS 应用和个人设备降低了这种方法的效率。 在 Azure AD 中，我们会将网络安全外围替代为组织标识层中的身份验证，其中的用户将分配到受控的特权管理角色。 无论使用本地环境、云环境还是混合环境，都必须保护用户的访问。

保护特权访问需要对以下各项进行更改：

* 流程、管理做法和知识管理
* 技术组件，例如主机防御、帐户保护和标识管理

在你关注的 Microsoft 服务中以一种管理加报告的方式保护你的特权访问。 如果你有本地管理员帐户，请参阅[保护特权访问](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)中提供的有关 Active Directory 中的本地和混合特权访问的指导。

> [!NOTE]
> 本文中的指南主要讲述 Azure Active Directory Premium 计划 P1 和 P2 中包括的 Azure Active Directory 功能。 Azure Active Directory Premium P2 包括在 EMS E5 套件和 Microsoft 365 E5 套件中。 本指南假定你的组织已经为你的用户购买 Azure AD Premium P2 许可证。 如果没有这些许可证，某些指南内容可能不适用于你的组织。 此外，在本文中，"全局管理员" (或 "全局管理员" 这一术语) 意味着与 "公司管理员" 或 "租户管理员" 相同。

## <a name="develop-a-roadmap"></a>制定路线图

Microsoft 建议你制定并遵循一个路线图，防止特权访问受到网络攻击者的攻击。 始终可以根据你在组织内的现有职能和具体需求调整自己的路线图。 路线图的每个阶段都应提高攻击者攻击本地资产、云资产和混合资产的特许访问权限的成本和难度。 Microsoft 建议实施以下四个路线图阶段。 首先计划最有效且最快速的实施方案。 本文根据 Microsoft 在处理网络攻击事件和响应实施方面的经验提供指导。 此路线图的时间线为近似值。

![采用时间线的路线图的各个阶段](./media/directory-admin-roles-secure/roadmap-timeline.png)

* 阶段 1（24-48 小时）：建议立即执行的关键项目

* 阶段 2（2-4 周）：减轻最常用攻击手段的危害

* 阶段 3（1-3 个月）：构建管理活动的可见性和完全控制

* 阶段 4（至少六个月）：继续构建防御措施，进一步强化安全平台

设计此路线图框架是为了最大程度地使用可能已部署的 Microsoft 技术。 考虑搭配使用其他供应商提供的、你已部署或正在考虑部署的任何安全工具。

## <a name="stage-1-critical-items-to-do-right-now"></a>阶段 1：要立即完成的关键事项

![阶段 1：首先要完成的关键事项](./media/directory-admin-roles-secure/stage-one.png)

路线图的阶段 1 着重于那些执行速度快且容易的关键任务。 建议在第一个 24-48 小时内快速执行这些项目，确保安全地进行基本级别的特权访问。 此阶段的安全特权访问路线图包括以下操作：

### <a name="general-preparation"></a>常规准备

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>启用 Azure AD Privileged Identity Management

我们建议在 Azure AD 生产环境中启用 Azure AD Privileged Identity Management (PIM)。 启用 PIM 后，将收到有关特权访问角色更改的通知电子邮件。 向高特权角色添加更多用户时，通知可以提供预先警告。

Azure AD Privileged Identity Management 包括在 Azure AD Premium P2 或 EMS E5 中。 请注册[企业移动性 + 安全性 90 天免费试用版](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)，它有助于保护对本地和云中应用程序与资源的访问。 Azure AD Privileged Identity Management 和 Azure AD 标识保护使用 Azure AD 的报告、审核和警报功能来监视安全活动。

启用 Azure AD Privileged Identity Management 之后，请执行以下操作：

1. 使用充当 Azure AD 生产组织全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com/)。

2. 若要选择你要在其中使用 Privileged Identity Management 的 Azure AD 组织，请选择 Azure 门户右上角的用户名。

3. 在 Azure 门户菜单中选择“所有服务”，然后从列表中筛选出“Azure AD Privileged Identity Management”。

4. 打开“所有服务”列表中的 Open Privileged Identity Management，将其固定到仪表板。

确保组织中第一个要使用 PIM 的人员已分配到“安全管理员”和“特权角色管理员”角色 。 只有特权角色管理员才能管理用户的 Azure AD 目录角色分配。 PIM 安全向导会引导你完成初始发现和分配体验。 现在可以退出向导，不做任何其他的更改。

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>对特许权限高的角色中的帐户进行标识和分类

启用 Azure AD Privileged Identity Management 后，查看充当以下 Azure AD 角色的用户：

* 全局管理员
* 特权角色管理员
* Exchange 管理员
* SharePoint 管理员

如果组织中没有 Azure AD Privileged Identity Management，可以使用 [PowerShell API](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)。 请从全局管理员角色开始，因为全局管理员在组织订阅的所有云服务中拥有相同的权限。 无论这些角色是在哪个位置分配的（在 Microsoft 365 管理中心分配、在 Azure 门户分配或者通过 Microsoft PowerShell 的 Azure AD 模块分配），都会授予这些权限。

删除这些角色不再需要的任何帐户。 然后，对分配给管理员角色的剩余帐户进行分类：

* 分配给管理用户，但还用于非管理性目的（例如，个人电子邮件）
* 分配给管理用户，且仅用于管理目的
* 跨多个用户共享
* 适用于不受限紧急访问情况
* 适用于自动化脚本
* 适用于外部用户

#### <a name="define-at-least-two-emergency-access-accounts"></a>定义至少两个紧急访问帐户

用户的角色可能会意外锁定。 例如，如果联合的本地标识提供者不可用，则用户无法登录或激活现有的管理员帐户。 可以通过存储两个或更多个紧急访问帐户来应对意外缺少访问权限的情况。

紧急访问帐户有助于限制 Azure AD 组织中的特权访问。 这些帐户拥有极高的特权，不要将其分配给特定的个人。 紧急访问帐户只能用于“破窗式”紧急情况，即不能使用正常管理帐户的情况。 务必控制和减少紧急帐户的使用，只在必要时使用。

评估已经获得或有资格获得全局管理员角色的帐户。 如果使用 \*.onmicrosoft.com 域（用于“破窗式”紧急访问）看不到任何仅限云的帐户，请创建此类帐户。 有关详细信息，请参阅[在 Azure AD 中管理紧急访问管理帐户](directory-emergency-access.md)。

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>启用多重身份验证，并注册所有其他的特许权限高的单用户非联合管理员帐户

需要 Azure 多重身份验证 (MFA) 在登录时，将永久分配给一个或多个 Azure AD 管理员角色的单个用户：全局管理员、特权角色管理员、Exchange 管理员和 SharePoint 管理员。 根据指南启用[针对管理员帐户的多重身份验证 (MFA)](../authentication/howto-mfa-userstates.md)，确保所有这些用户都已注册到 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)。 有关详细信息，可参阅[保护对 Office 365 中数据和服务的访问权限](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)指南的步骤 2 和步骤 3。 

## <a name="stage-2-mitigate-frequently-used-attacks"></a>阶段 2：缓解经常使用的攻击

![阶段 2：缓解经常使用的攻击](./media/directory-admin-roles-secure/stage-two.png)

路线图的阶段 2 着重于减轻进行凭据窃取和滥用时最常用的攻击手段导致的危害，并且可在约 2-4 周内实现。 此阶段的安全特权访问路线图包括以下操作。

### <a name="general-preparation"></a>常规准备

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>制作一个包括服务、所有者和管理员在内的清单

随着“自带设备”和在家办公策略实施人数的增加以及无线连接人数的增加，对连接到网络的人员进行监视至关重要。 安全审核可以揭示网络中不受组织支持且存在高风险的设备、应用程序和程序。 有关详细信息，请参阅 [Azure 安全管理和监视概述](../../security/fundamentals/management-monitoring-overview.md)。 请确保在清单进程中包括所有下述任务。

* 标识具有管理角色的用户及其可以在其中进行管理的服务。
* 使用 Azure AD PIM 找出组织中的哪些用户对 Azure AD 拥有管理访问权限。
* 除了在 Azure AD 中定义的角色，Office 365 还带有一组可以分配给组织中用户的管理员角色。 每个管理员角色都映射到常用业务功能，为组织中的人员提供在 [Microsoft 365 管理中心](https://admin.microsoft.com)执行特定任务的权限。 通过 Microsoft 365 管理中心找出组织中哪些用户可以通过管理员身份来访问 Office 365，包括通过不在 Azure AD 中管理的角色进行访问。 有关详细信息，请参阅[关于 Office 365 管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)和 [Office 365 安全做法](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)。
* 在组织所依赖的服务（例如 Azure、Intune 或 Dynamics 365）中进行盘点。
* 确保用于管理目的的帐户：

  * 附加了工作电子邮件地址
  * 已注册 Azure 多重身份验证或在本地使用 MFA
* 要求用户提供进行管理访问的业务理由。
* 删除不需要的个人和服务的管理员访问权限。

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>确定管理角色中那些需要切换到工作或学校帐户的 Microsoft 帐户

如果初始全局管理员在开始使用 Azure AD 时重复使用其现有的 Microsoft 帐户凭据，请将 Microsoft 帐户替换为单个基于云的或同步的帐户。

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>对于全局管理员帐户，请确保使用单独的用户帐户和邮件转发功能

个人电子邮件帐户经常会遭到网络攻击者的钓鱼攻击，这种风险使得全局管理员帐户不能使用个人电子邮件地址。 若要将 Internet 风险与管理权限隔离开来，请为每个具有管理权限的用户创建专用的帐户。

* 确保为执行全局管理员任务的用户创建单独的帐户
* 确保全局管理员不会无意中使用其管理员帐户打开电子邮件或运行程序
* 确保这些帐户将其电子邮件转发到工作邮箱

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>确保管理帐户的密码最近更改过

确保所有用户在过去 90 天中至少有一次登录到其管理帐户并更改了密码。 另外，验证最近是否更改了任何共享帐户的密码。

#### <a name="turn-on-password-hash-synchronization"></a>启用密码哈希同步

Azure AD Connect 将用户密码的哈希从本地 Active Directory 同步到基于云的 Azure AD 组织。 如果将联合身份验证与 Active Directory 联合身份验证服务 (AD FS) 结合使用，则可以将密码哈希同步用作备用方案。 如果本地 Active Directory 或 AD FS 服务器暂时不可用，此备用方案可能很有用。

密码哈希同步可让用户使用登录到本地 Active Directory 实例时所用的同一密码登录到服务。 密码哈希同步可让标识保护通过将密码哈希与已知泄露的密码进行比较，来检测已泄露的凭据。 有关详细信息，请参阅[使用 Azure AD Connect 同步实现密码哈希同步](../hybrid/how-to-connect-password-hash-synchronization.md)。

#### <a name="require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users"></a>要求特权角色用户和已公开的用户执行多重身份验证

Azure AD 建议你要求所有用户执行多重身份验证 (MFA)。 请务必考虑到在帐户泄露情况下会造成重大影响的用户（例如财务官）。 MFA 可降低因密码泄露而受到攻击的风险。

启用以下功能：

* 对组织中的所有用户启用[使用条件访问策略的 MFA](../authentication/howto-mfa-getstarted.md)。

如果使用 Windows Hello for Business，则可通过 Windows Hello 登录体验来满足 MFA 要求。 有关详细信息，请参阅 [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport)。

#### <a name="configure-identity-protection"></a>配置“标识保护”

Azure AD Identity Protection 是一种基于算法的监视和报告工具，用于检测影响组织标识的潜在漏洞。 可以配置自动响应，以便对那些检测到的可疑活动进行响应，并采取相应的解决措施。 有关详细信息，请参阅 [Azure Active Directory 标识保护](../active-directory-identityprotection.md)。

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>获取 Office 365 安全评分（如果使用 Office 365）

安全分数查看你正在使用的 Office 365 服务的设置和活动，并将其与 Microsoft 建立的基线进行比较。 你将根据你对安全做法的对齐方式获得评分。 对 Office 365 业务高级版或企业版订阅拥有管理员权限的任何人都可以通过 [https://securescore.office.com](https://securescore.office.com/) 访问安全评分。

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>查看 Office 365 安全性和符合性指南（如果使用 Office 365）

[安全性和符合性计划](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57)概述了 Office 365 客户可以如何配置 Office 365 并启用其他 EMS 功能。 然后查看步骤 3-6，了解如何[保护对 Office 365 中数据和服务的访问权限](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)，并查看指南，了解如何[监视 Office 365 中的安全性和符合性](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6)。

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>配置 Office 365 活动监视（如果使用 Office 365）

可以监视组织中使用 Office 365 的用户，以识别具有管理员帐户但因未登录到这些门户而可能不需要 Office 365 访问权限的人员。 有关详细信息，请参阅 [Microsoft 365 管理中心内的活动报告](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263)。

#### <a name="establish-incidentemergency-response-plan-owners"></a>确定事件/紧急情况响应计划所有者

建立成功的事件响应功能需要严格的计划和大量的资源。 必须持续监视网络攻击并建立事件处理优先级。 收集、分析和报告事件数据以建立关系，并与其他内部组和计划所有者建立通信。 有关详细信息，请参阅 [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717)（Microsoft 安全响应中心）。

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>如果尚未这样做，请确保本地特权管理帐户的安全性

如果 Azure Active Directory 组织与本地 Active Directory 同步，请遵循[安全特权访问路线图](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)中的指导：此阶段包括：

* 为需要执行本地管理任务的用户创建单独的管理员帐户
* 为 Active Directory 管理员部署特权访问工作站
* 为工作站和服务器创建唯一的本地管理员密码

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>管理 Azure 访问权限的组织所需的其他步骤

#### <a name="complete-an-inventory-of-subscriptions"></a>完成订阅清单

通过企业门户和 Azure 门户确定组织中用于托管生产应用程序的订阅。

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>从管理员角色中删除 Microsoft 帐户

不应将其他程序（例如 Xbox、Live、Outlook）的 Microsoft 帐户用作组织订阅的管理员帐户。 从所有 Microsoft 帐户中删除管理员状态，将其替换为 Azure AD 工作或学校帐户（例如，chris@contoso.com）。 若要进行管理，请依赖于已在 Azure AD（而不是其他服务）中进行身份验证的帐户。

#### <a name="monitor-azure-activity"></a>监视 Azure 活动

Azure 活动日志提供 Azure 中订阅级别事件的历史记录。 它提供的信息涉及谁创建、更新和删除了什么资源，以及这些事件的发生时间。 有关详细信息，请参阅[审核和接收关于 Azure 订阅中重要操作的通知](../../azure-monitor/platform/quick-audit-notify-action-subscription.md)。

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>组织通过 Azure AD 管理其他云应用的访问权限时所需的其他步骤

#### <a name="configure-conditional-access-policies"></a>配置条件访问策略

为本地应用程序和托管在云中的应用程序准备条件访问策略。 如果你有加入了用户工作区的设备，请参阅[使用 Azure Active Directory 设备注册设置本地条件访问](../active-directory-device-registration-on-premises-setup.md)中提供的详细信息。

## <a name="stage-3-take-control-of-admin-activity"></a>阶段 3：控制管理活动

![阶段 3：控制管理活动](./media/directory-admin-roles-secure/stage-three.png)

阶段 3 以阶段 2 的缓解措施为基础，应在大约 1-3 个月内实施。 此阶段的安全特权访问路线图包括以下组件。

### <a name="general-preparation"></a>常规准备

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>完成对管理员角色用户的访问权限审核

更多的公司用户是通过云服务获得特许访问权限的，因此可能会导致访问不受管理。 当今的用户可以成为 Office 365 全局管理员、Azure 订阅管理员，或者获取对 VM 或 SaaS 应用的管理访问权限。

组织应让所有员工以非特权用户身份处理日常的业务事务，只在有需要时才向其授予管理权限。 完成访问评审，识别并确认有资格激活管理特权的用户。

建议：

1. 确定哪些用户是 Azure AD 管理员，启用按需恰时管理访问权限以及基于角色的安全控制。
2. 将没有明确理由使用管理员特许访问权限的用户转换为其他角色（如果没有符合条件的角色，则删除这些用户）。

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>针对所有用户持续推出更强的身份验证

要求身份暴露可能性高的用户执行新式强身份验证，例如 Azure MFA 或 Windows Hello。 身份暴露可能性高的用户的示例包括：

* C 套件高管
* 高级经理
* IT 和安全部门的关键人员

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>使用专用工作站进行 Azure AD 管理

攻击者可能会尝试以特权帐户为目标，以破坏数据的完整性和真实性。 他们经常使用恶意代码来更改程序逻辑，或者窥视管理员输入凭据。 特权访问工作站 (PAW) 为敏感任务提供专用操作系统，使其免受 Internet 攻击和威胁向量攻击。 将这些敏感的任务和帐户与日常使用的工作站及设备分离，可针对以下威胁提供有力的保护：

* 网络钓鱼攻击
* 应用程序和操作系统漏洞
* 模拟攻击
* 凭据盗窃攻击，例如按键日志记录、哈希传递和票证传递

部署特权访问工作站可以降低管理员在未经过强化的桌面环境中输入凭据时遇到的风险。 有关详细信息，请参阅[特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)。

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>查看美国国家标准与技术协会提供的有关如何处理事件的建议

美国国家标准与技术研究院 (NIST) 提供事件处理的指导原则，特别是用于分析与事件相关的数据，并确定每个事件的适当响应。 有关详细信息，请参阅 [The (NIST) Computer Security Incident Handling Guide (SP 800-61, Revision 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)（(NIST) 计算机安全事件处理指南（SP 800-61，修订版 2））。

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>对其他管理角色实施适用于 JIT 的 Privileged Identity Management (PIM)

对于 Azure Active Directory，请使用 [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) 功能。 对特权角色进行有时间限制的激活时，可执行以下操作：

* 激活执行特定任务所需的管理特权
* 在激活过程中强制实施 MFA
* 使用警报将所做的带外更改通知给管理员
* 允许用户在预配置的时限内保留其访问特权
* 允许安全管理员执行以下操作：

  * 发现所有特权标识
  * 查看审核报告
  * 创建访问评审，用于识别每个有资格激活管理特权的用户

如果已在使用 Azure AD Privileged Identity Management，请根据需要调整时间限制的时间范围 (例如，维护时段) 。

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>确定是否向基于密码的登录协议公开（如果使用 Exchange Online）

建议你识别每一位在其凭据泄漏的情况下可能会对组织造成灾难性后果的用户。 针对这些用户，请制定严格的身份验证要求，并使用 Azure AD 条件访问来防止这些用户使用用户名和密码登录其电子邮件。 可以[使用条件访问来阻止旧式身份验证](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication)，并可以通过 Exchange Online [阻止基本身份验证](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online)。

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>针对 Office 365 角色完成角色审核评估（如果使用 Office 365）

评估是否所有管理员用户都有正确的角色（根据此评估执行删除和重新分配操作）。

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>审核在 Office 365 中使用的安全事件管理方法，并将其与自己的组织进行比较

可以从 [Microsoft Office 365 中的安全事件管理](https://www.microsoft.com/download/details.aspx?id=54302)下载此报告。

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>继续保护本地特权管理帐户

如果 Azure Active Directory 连接到本地 Active Directory，则请遵循[安全特权访问路线图](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)：阶段 2 中的指南。 在此阶段，你将：

* 为所有管理员部署特权访问工作站
* 要求 MFA
* 使用 Just Enough Admin 进行域控制器维护，缩小域的受攻击面
* 部署高级威胁评估进行攻击检测

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>管理 Azure 访问权限的组织所需的其他步骤

#### <a name="establish-integrated-monitoring"></a>建立集成监视

[Azure 安全中心](../../security-center/security-center-intro.md)：

* 跨 Azure 订阅提供集成的安全监视和策略管理
* 帮助检测可能被忽略的威胁
* 适用于广泛的安全解决方案

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>清点托管的虚拟机中的特权帐户

通常不需要向用户授予对所有 Azure 订阅或资源的无限制权限。 使用 Azure AD 管理员角色可以仅向用户授予他们在完成工作时所需的访问权限。 可以使用 Azure AD 管理员角色，让一个管理员只管理某个订阅中的 VM，让另一个管理员管理同一订阅中的 SQL 数据库。 有关详细信息，请参阅 [Azure 门户中基于角色的访问控制入门](../../role-based-access-control/overview.md)。

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>为 Azure AD 管理员角色实施 PIM

将 Privileged Identity Management 与 Azure AD 管理员角色配合使用，对 Azure 资源的访问权限进行管理、控制和监视。 可以缩短特权的暴露时间并通过报告和警报增加对使用情况的可见性，从而使用 PIM 提供保护。 有关详细信息，请参阅[使用 Privileged Identity Management 管理对 Azure 资源的 RBAC 访问](../../role-based-access-control/pim-azure-resource.md)。

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>使用 Azure 日志集成将相关的 Azure 日志发送到 SIEM 系统

可以通过 azure 日志集成将 Azure 资源中的原始日志集成到组织的现有安全信息和事件管理 (SIEM) 系统。 [Azure 日志集成](../../security/fundamentals/azure-log-integration-overview.md)从 Windows 事件查看器日志收集 Windows 事件，并从以下位置收集 Azure 资源：

* Azure 活动日志
* Azure 安全中心警报
* Azure 资源日志

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>组织通过 Azure AD 管理其他云应用的访问权限时所需的其他步骤

#### <a name="implement-user-provisioning-for-connected-apps"></a>为连接的应用实施用户预配

Azure AD 允许在 Dropbox、Salesforce 和 ServiceNow 等云应用中自动创建和维护用户标识。 有关详细信息，请参阅[使用 Azure AD 自动预配和取消预配 SaaS 应用程序的用户](../app-provisioning/user-provisioning.md)。

#### <a name="integrate-information-protection"></a>集成信息保护

Microsoft Cloud App Security 允许根据 Azure 信息保护分类标签来调查文件并设置策略，从而增加云数据的可见性并加强对此类数据的控制。 对云中的文件进行扫描和分类，并应用 Azure 信息保护标签。 有关详细信息，请参阅 [Azure 信息保护集成](https://docs.microsoft.com/cloud-app-security/azip-integration)。

#### <a name="configure-conditional-access"></a>配置条件访问

根据组、位置和应用程序敏感性为 [SaaS 应用](https://azure.microsoft.com/overview/what-is-saas/)和 Azure AD 连接应用配置条件访问。 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>监视连接的云应用中的活动

建议使用 [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) 来确保用户访问在连接的应用程序中也受保护。 此功能可以保护企业对云应用的访问并保护管理员帐户，使你可以：

* 扩展对云应用的可见性和控制
* 针对访问权限、活动和数据共享创建策略
* 自动标识风险活动、异常行为和威胁
* 防止数据泄漏
* 将风险降至最低并自动实施威胁防护和策略

Cloud App Security SIEM 代理将 Cloud App Security 与 SIEM 服务器集成，以便集中监视 Office 365 警报和活动。 它还可以在服务器上运行，从 Cloud App Security 拉取警报和活动并将其流式传输到 SIEM 服务器中。 有关详细信息，请参阅 [SIEM 集成](https://docs.microsoft.com/cloud-app-security/siem)。

## <a name="stage-4-continue-building-defenses"></a>阶段 4：持续构建防御

![阶段 4：采取主动安全举措](./media/directory-admin-roles-secure/stage-four.png)

路线图的阶段 4 应在 6 个月或更长时间内实施。 完成路线图可以强化特权访问保护措施，以防范那些目前已知存在的可能攻击。 针对未来的安全威胁，我们建议持续观察安全状况，提高以你的环境为目标的攻击者的攻击成本，降低其成功率。

保护特权访问是为企业资产建立安全保障的重要一步。 但在整个安全计划中，它应该是持续提供安全保障的一环。 此计划应包括如下要素：

* 策略
* 操作
* 信息安全性
* 服务器
* 应用程序
* 电脑
* 设备
* 云结构

在管理特权访问帐户时，我们建议采取以下做法：

* 确保管理员以非特权用户的身份执行日常业务操作
* 仅在需要的时候才授予特许访问权限，操作完后即将其删除（适时）
* 保留与特权帐户相关的审核活动日志

若要深入了解如何构建完整的安全路线图，请参阅 [Microsoft 云 IT 体系结构资源](https://almbok.com/office365/microsoft_cloud_it_architecture_resources)。 若要与 Microsoft 服务互动以实施路线图中的任一环节，请联系 Microsoft 代表或参阅[构建关键的网络防御来保护企业](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx)。

这个最后进行的阶段的安全特权访问路线图包括以下组件。

### <a name="general-preparation"></a>常规准备

#### <a name="review-admin-roles-in-azure-ad"></a>评审 Azure AD 中的管理员角色

确定当前的内置 Azure AD 管理员角色是否仍为最新，并确保用户只充当所需的角色。 使用 Azure AD 时，可以分配不同的管理员来履行不同的职责。 有关详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](directory-assign-admin-roles.md)。

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>审核那些可以管理已加入 Azure AD 的设备的用户

有关详细信息，请参阅[如何配置联接到混合 Azure Active Directory 的设备](../device-management-hybrid-azuread-joined-devices-setup.md)。

#### <a name="review-members-of-built-in-office-365-admin-roles"></a>审核[内置 Office 365 管理员角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)的成员
如果不使用 Office 365，请跳过此步骤。
‎
#### <a name="validate-incident-response-plan"></a>验证事件响应计划

若要改进计划，Microsoft 建议你经常验证计划的执行是否复合预期：

* 审核现有的路线图，看是否漏掉了什么
* 根据事后分析修订现有的做法，或者定义新做法
* 确保通过组织分发更新的事件响应计划和做法


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>管理 Azure 访问权限的组织所需的其他步骤 

确定是否需要[将 Azure 订阅所有权转让给其他帐户](../../cost-management-billing/manage/billing-subscription-transfer.md)。
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>“不受限”紧急措施：在紧急情况下需要做的事情

![用于进行破窗式紧急访问的帐户](./media/directory-admin-roles-secure/emergency.jpeg)

1. 将事件相关信息通知给主要管理员和安全官。

2. 审核攻击 playbook。

3. 访问登录 Azure AD 所需的“破窗式”紧急访问帐户用户名和密码组合。

4. 通过[提交 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)从 Microsoft 获取帮助。

5. 查看 [Azure AD 登录报告](../reports-monitoring/overview-reports.md)。 事件发生时间与在报告中包含该事件的时间可能存在一段时差。

6. 对于混合环境，如果你的本地基础结构联合并且 AD FS 服务器不可用，则可以暂时从联合身份验证切换为使用密码哈希同步。此开关会将域联合身份验证回托管身份验证，直至 AD FS 服务器可用。

7. 监视特权帐户的电子邮件。

8. 确保保存相关日志的备份，以备将来可能会发生的取证和法庭调查之用。

若要详细了解 Microsoft Office 365 如何处理安全事件，请参阅 [Security Incident Management in Microsoft Office 365](https://aka.ms/Office365SIM)（Microsoft Office 365 中的安全事件管理）。

## <a name="faq-answers-for-securing-privileged-access"></a>常见问题解答：有关保护特权访问的解答  

**问：** 如果尚未实施任何安全访问组件，该怎么办？

**答：** 定义至少两个“不受限”帐户，指定对特权管理员帐户实施 MFA，并将用户帐户与全局管理员帐户分开。

**问：** 在遭到攻击后，首先需要解决的最重要问题是什么？

**答案：** 请确保对高度公开的个人要求最强的身份验证。

**问：** 如果停用了特权管理员，该怎么办？

**答：** 请创建一个始终保持最新的全局管理员帐户。

**问：** 如果只剩下一个全局管理员，并且无法访问，会发生什么情况？

**答：** 使用其中一个“不受限”帐户，立即获取特许访问权限。

**问：** 如何保护组织中的管理员？

**答案：** 让管理员始终以标准的 "非特权" 用户的形式执行日常业务。

**问：** 在 Azure AD 中创建管理员帐户的最佳做法是什么？

**答：** 针对特定的管理任务保留特许访问权限。

**问：** 可以通过哪些现有工具减少持久进行的管理访问？

**答：** Privileged Identity Management (PIM) 和 Azure AD 管理员角色。

**问：** 对于将管理员帐户同步到 Azure AD，Microsoft 的态度是什么？

**答：** 第 0 层管理员帐户仅用于本地 AD 帐户。 此类帐户通常不会与云中的 Azure AD 同步。 第 0 层管理员帐户包括对本地 Active Directory 林、域、域控制器和资产拥有直接或间接管理控制权的帐户、组和其他资产。

**问：** 如何防止管理员在门户中分配随机的管理员访问权限？

**答：** 要求所有用户和大多数管理员使用非特权帐户。 一开始确定组织的占用空间，从而确定应该向哪些管理员帐户授予特权。 监视新建的管理用户。

## <a name="next-steps"></a>后续步骤

* [Microsoft 信任中心确保产品安全性](https://www.microsoft.com/trustcenter/security) - Microsoft 云产品和服务的安全特性

* [Microsoft 信任中心-合规性](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – microsoft 提供的针对云服务的全面的符合性产品/服务

* [有关如何执行风险评估的指南](https://www.microsoft.com/trustcenter/guidance/risk-assessment) - 管理 Microsoft 云服务的安全性与合规性要求

### <a name="other-microsoft-online-services"></a>其他 Microsoft 在线服务

* [Microsoft Intune 安全性](https://www.microsoft.com/trustcenter/security/intune-security) - Intune 从云端提供移动设备管理、移动应用程序管理和电脑管理功能。

* [Microsoft Dynamics 365 安全性](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 是 Microsoft 的基于云的解决方案，该方案将客户关系管理 (CRM) 和企业资源规划 (ERP) 功能统合在一起。
