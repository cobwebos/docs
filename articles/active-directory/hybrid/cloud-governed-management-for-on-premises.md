---
title: 为本地工作负荷 Azure AD 云控制管理-Azure
description: 本主题介绍了本地工作负荷的云控制管理。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9cb101e415499150cd3d825fe5f42ce0dbc766fb
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662514"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Azure AD 如何为本地工作负荷提供云管控管理

Azure Active Directory (Azure AD) 是一个综合性的标识即服务， (由数百万个组织使用的 IDaaS) 解决方案，这些组织涵盖标识、访问管理和安全的各个方面。 Azure AD 拥有超过10亿用户身份，并可帮助用户登录和安全访问两者：

* 外部资源，例如 Microsoft 365、Azure 门户和上千个软件即服务 (SaaS) 应用程序。
* 内部资源，如组织企业网络和 intranet 上的应用程序，以及由该组织开发的任何云应用程序。

如果组织使用的是 "纯云"，则可以使用 Azure AD，如果它们有本地工作负荷，则可以使用 "混合" 部署。 Azure AD 的混合部署可以是组织的策略的一部分，以将其 IT 资产迁移到云，或继续集成现有的本地基础结构和新的云服务。

从历史上看，"混合" 组织已 Azure AD 作为其现有本地基础结构的扩展。 在这些部署中，本地标识管理管理、Windows Server Active Directory 或其他内部目录系统，是控制点，用户和组都从这些系统同步到云目录，如 Azure AD。 一旦这些标识位于云中，就可以 Microsoft 365、Azure 和其他应用程序使用它们。

![标识生命周期](media/cloud-governed-management-for-on-premises//image1.png)

随着组织将其 IT 基础结构和应用程序移动到云，许多组织都正在寻找提高的安全性和简化的标识管理管理功能。 Azure AD 的云提供的 IDaaS 功能通过提供解决方案和功能，使组织能够快速采用并将更多的标识管理从传统的本地系统迁移到 Azure AD，同时还会继续支持现有的和新的应用程序，从而加快了云管理管理的过渡。

本白皮书概述了 Microsoft 的混合 IDaaS 策略，并介绍了组织可以如何使用现有应用程序的 Azure AD。

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>云管理的身份管理的 Azure AD 方法

当组织过渡到云时，他们需要确保他们能够控制其完整环境-更安全、更深入地了解自动化和主动见解所支持的活动。 "**云管理管理**" 说明了组织如何从云中管理和管理其用户、应用程序、组和设备。

在这一新式领域，组织需要能够大规模地进行管理，因为 SaaS 应用程序的激增以及协作和外部标识的不断增长。 云的新风险形势意味着组织必须做出更好的响应性-损害云用户的恶意用户可能会影响云和本地应用程序。

特别是，混合组织需要能够委托和自动执行任务，这些任务在过去是手动完成的。 若要自动执行任务，它们需要 Api 和进程来协调不同标识相关资源的生命周期， (用户、组、应用程序、设备) ，以便他们可以将这些资源的日常管理委托给核心 IT 人员之外的更多个人。 Azure AD 通过用户帐户管理和用户的本机身份验证来解决这些要求，而无需本地标识基础结构。 如果不构建本地基础结构，则可能会使具有新用户团体（如业务合作伙伴）的组织受益于其本地目录以外的用户，但其访问管理对于实现业务成果至关重要。

此外，如果未在此新领域进行管理---和监管，则管理工作不会是标识系统（而不是外接程序）的集成部分。 标识调控使组织能够跨员工、业务合作伙伴和供应商以及服务和应用程序管理标识和访问生命周期。

结合使用标识调控，使组织能够更轻松地过渡到云管控管理，使其能够进行缩放、解决来宾的新挑战，并提供与客户在本地基础结构方面的更深入的见解和自动化。 这一新领域的监管意味着，组织可以在访问组织内的资源时具有透明度、可见性和适当的控制。 在 Azure AD 中，安全操作和审核团队可以了解谁拥有---，哪些用户应该有权访问组织中的哪些资源 () 哪些设备、这些用户使用该访问权限以及是否根据公司或法规策略来删除或限制访问权限。

新的管理模型可为组织提供 SaaS 和业务线 (LOB) 应用程序，因为他们能够更轻松地管理和保护对这些应用程序的访问。 通过将应用程序与 Azure AD 集成，组织将能够以一致的方式跨云和本地发起的标识访问和管理访问。 应用程序生命周期管理变得更加自动，Azure AD 提供了对应用程序使用的丰富深入了解，这在本地标识管理中无法轻松实现。 通过 Azure AD、Microsoft 365 组和团队自助服务功能，组织可以轻松地创建用于访问管理和协作的组，并在云中添加或删除用户，以实现协作和访问管理要求。

为云控制管理选择正确的 Azure AD 功能取决于要使用的应用程序，以及如何将这些应用程序与 Azure AD 集成。 以下部分概述了对于 AD 集成应用程序以及使用联合身份验证协议的应用程序 (例如，SAML、OAuth 或 OpenID Connect) 的方法。

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>AD 集成的应用程序的云管理管理

Azure AD 通过安全远程访问和对这些应用程序的条件性访问，改进了组织的本地 Active Directory 集成应用程序的管理。 此外，Azure AD 还提供用户现有 AD 帐户的帐户生命周期管理和凭据管理，其中包括：

* **适用于本地应用程序的安全远程访问和条件性访问**

对于许多组织而言，管理从云对本地 AD 集成 web 和基于远程桌面的应用程序的访问权限的第一步是将 [应用程序代理](../manage-apps/application-proxy.md) 部署在这些应用程序的前面，以提供安全的远程访问。

单一登录到 Azure AD 后，用户可以通过外部 URL 或内部应用程序门户访问云端和本地的应用程序。 例如，应用程序代理提供远程桌面、SharePoint 和应用（如 Tableau 和 Qlik sense）的远程访问和单一登录，以及业务线 (LOB) 应用程序。 此外，条件性访问策略可能包括显示 [使用条款](../conditional-access/terms-of-use.md) ，并确保用户在访问应用程序之前 [已同意这些](../conditional-access/require-tou.md) 条款。

![应用代理体系结构](media/cloud-governed-management-for-on-premises/image2.png)

* **Active Directory 帐户的自动生命周期管理**

身份监管可帮助组织在 *工作效率* ---如何快速访问他们所需的资源（例如当他们加入组织时），从而实现了平衡？ ---和 *安全* ---其访问如何随时间而变化，例如，当他的受雇状态发生更改时？ 标识生命周期管理是标识监管的基石，大规模的有效监管需要将应用程序的标识生命周期管理基础结构现代化。

对于许多组织来说，员工的标识生命周期与用户在人力资本管理 (HCM) 系统中的表示形式相关联。 对于使用 Workday 作为其 HCM 系统的组织，Azure AD 可以确保自动预配 AD 中的用户帐户 [，并为 Workday 中的辅助角色取消预配](../saas-apps/workday-inbound-tutorial.md)。 这样做会导致通过自动化 birthright 帐户提高用户工作效率，并通过确保当用户更改角色或离开组织时自动更新应用程序访问来管理风险。 Workday 驱动的用户预配 [部署计划](https://aka.ms/WorkdayDeploymentPlan) 是一项循序渐进指南，指导组织完成 Workday 的最佳实践实现，以在五个步骤中 Active Directory 用户预配解决方案。

Azure AD Premium 还包括 Microsoft Identity Manager，可以从其他本地 HCM 系统（包括 SAP、Oracle 电子商务和 Oracle PeopleSoft）导入记录。

企业到企业的协作越来越多地要求向组织外部的人员授予访问权限。 [AZURE AD B2B](/azure/active-directory/b2b/) 协作使组织能够安全地与来宾用户和外部合作伙伴共享其应用程序和服务，同时保持对其自己公司数据的控制。

Azure AD 可以根据需要 [自动为来宾用户创建 ad 中的帐户](../external-identities/hybrid-cloud-to-on-premises.md) ，从而使业务来宾无需其他密码即可访问本地 ad 集成应用程序。 组织可以 [为来宾用户设置多重身份验证 (MFA) 策略](../external-identities/conditional-access.md)，以便在应用程序代理身份验证期间完成 mfa 检查。 此外，在云 B2B 用户上完成的任何 [访问评审](../governance/manage-guest-access-with-access-reviews.md) 都适用于本地用户。 例如，如果通过生命周期管理策略删除了云用户，则本地用户也会被删除。

**Active Directory 帐户的凭据管理** Azure AD 的自助密码重置功能允许已忘记密码的用户重新进行身份验证并重置其密码，并将更改后的密码 [写入本地 Active Directory](../authentication/concept-sspr-writeback.md)。 密码重置过程还可以使用本地 Active Directory 密码策略：当用户重置其密码时，将对其进行检查，确保它满足本地 Active Directory 策略，然后将其提交到该目录。 自助服务密码重置 [部署计划](https://aka.ms/deploymentplans/sspr) 概述了通过 Web 和 Windows 集成体验向用户推出自助密码重置的最佳实践。

![Azure AD SSPR 体系结构](media/cloud-governed-management-for-on-premises/image3.png)

最后，对于允许用户在 AD 中更改其密码的组织，可以将 AD 配置为使用与 Azure AD 通过 [Azure AD 密码保护功能](../authentication/concept-password-ban-bad-on-premises.md)（目前为公共预览版）中的组织使用的密码策略。

当组织准备好将 AD 集成应用程序移到云时，可以通过将承载应用程序的操作系统移动到 Azure， [Azure AD 域服务](../../active-directory-domain-services/overview.md) 提供与 ad 兼容的域服务 (例如域加入、组策略、LDAP 和 KERBEROS/NTLM 身份验证) 。 Azure AD 域服务与组织现有 Azure AD 租户集成，使用户能够使用其企业凭据登录。 此外，可以使用现有组和用户帐户来保护对资源的访问，从而确保将本地资源的 "直接迁移" 到 Azure 基础结构服务。

![Azure AD 域服务](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>基于本地联合身份验证的应用程序的云管理管理

对于已使用本地标识提供程序的组织，将应用程序移动到 Azure AD 可实现更安全的访问，并为联合管理提供更轻松的管理体验。 Azure AD 允许通过使用 Azure AD 条件访问来配置精细的按应用程序访问控制，包括 Azure 多重身份验证。 Azure AD 支持更多的功能，包括特定于应用程序的令牌签名证书和可配置的证书过期日期。 这些功能、工具和指导使组织能够停用本地标识提供者。 作为一个例子，microsoft 的 IT 人员已将17987应用程序从 Microsoft 的内部 Active Directory 联合身份验证服务 (AD FS) 到 Azure AD。

![Azure AD 进化](media/cloud-governed-management-for-on-premises/image5.png)

若要开始将联合应用程序迁移到 Azure AD 作为标识提供程序，请参阅 https://aka.ms/migrateapps ，其中包含指向以下内容的链接：

* 这篇文章将 [应用程序迁移到 Azure Active Directory](https://aka.ms/migrateapps/whitepaper)，这带来了迁移的好处，并介绍了如何在四个清晰概括的阶段规划迁移：发现、分类、迁移和持续管理。 你将指导你了解过程，并将项目分解为易于使用的部分。 整个文档为指向重要资源的链接，可全程为你提供帮助。

* 将 [应用程序身份验证从 Active Directory 联合身份验证服务迁移到 Azure Active Directory](https://aka.ms/migrateapps/adfssolutionguide) 中的解决方案指南更详细地探讨了规划和执行应用程序迁移项目的四个阶段。 在本指南中，你将了解如何将这些阶段应用于将应用程序从 Active Directory 联合身份验证服务 (AD FS) 迁移到 Azure AD 的具体目标。

* [Active Directory 联合身份验证服务迁移准备情况脚本](https://aka.ms/migrateapps/adfstools)可以在现有的本地 Active Directory 联合身份验证服务 (AD FS) 服务器上运行，以确定要迁移到 Azure AD 的应用程序的准备情况。

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>跨云和本地应用程序的持续访问管理

组织需要一个过程来管理可缩放的访问。 用户继续积累访问权限，最终超出最初设置的权限。 此外，企业组织需要能够高效地进行扩展，以便不断地开发和强制实施访问策略和控制。

通常，IT 部门会将访问权限审批决策委托给业务决策人。 此外，IT 部门可能涉及到用户本身的事务。 例如，访问公司位于欧洲的市场营销应用程序中的机密客户数据的用户需要知道该公司的策略。 来宾用户还可能不知道对其受邀组织中数据的处理要求。

组织可以通过诸如 [动态组](../users-groups-roles/groups-dynamic-membership.md)这样的技术自动执行访问生命周期过程，结合使用用户预配到 [SaaS 应用程序](../saas-apps/tutorial-list.md)，或者 [使用系统实现跨域标识管理 (SCIM) 标准集成的应用程序](../app-provisioning/use-scim-to-provision-users-and-groups.md) 。 组织还可以控制哪些 [来宾用户有权访问本地应用程序](../external-identities/hybrid-cloud-to-on-premises.md)。 然后，可以使用 [Azure AD 访问评审](../governance/access-reviews-overview.md)定期评审这些访问权限。

## <a name="future-directions"></a>未来发展方向

在混合环境中，Microsoft 的策略是实现部署，其中 **云是标识的控制平面**，本地目录和其他标识系统（如 Active Directory 和其他本地应用程序）是设置具有访问权限的用户的目标。 此策略将继续确保依赖于它们的应用程序和工作负荷中的权限、标识和访问权限。 在此结束状态下，组织将能够完全从云中推动最终用户的工作效率。

![Azure AD 体系结构](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>后续步骤

有关如何开始此旅程的详细信息，请参阅中的 Azure AD 部署计划 <https://aka.ms/deploymentplans> 。 它们提供了有关如何部署 Azure Active Directory (Azure AD) 功能的端到端指导。 每个计划都说明了成功推出常见 Azure AD 功能所需的业务价值、规划注意事项、设计和操作过程。 当我们添加新功能以通过 Azure AD 在云中进行管理时，Microsoft 会持续更新部署计划并提供从客户部署中获得的最佳实践和其他反馈。