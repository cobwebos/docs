---
title: 本地工作负载的 Azure AD 云治理管理 - Azure
description: 本主题介绍本地工作负载的云治理管理。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 510a5562740260eb2946ded074a5c37804c55375
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109524"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Azure AD 如何为本地工作负载提供云治理管理

Azure 活动目录 （Azure AD） 是一种全面的身份标识作为服务 （IDaaS） 解决方案，由数百万组织使用，涵盖标识、访问管理和安全性的所有方面。 Azure AD 包含超过 10 亿个用户身份，可帮助用户登录并安全地访问这两者：

* 外部资源，如 Microsoft Office 365、Azure 门户和数千个其他软件即服务 （SaaS） 应用程序。
* 内部资源（如组织企业网络和 Intranet 上的应用程序）以及该组织开发的任何云应用程序。

如果组织是"纯云"，则可以使用 Azure AD，或者使用"混合"部署（如果他们具有本地工作负荷）。 Azure AD 的混合部署可以是组织将其 IT 资产迁移到云的策略的一部分，或者继续将现有的本地基础结构与新云服务集成。

从历史上看，"混合"组织将 Azure AD 视为其现有本地基础结构的扩展。 在这些部署中，本地标识治理管理、Windows Server 活动目录或其他内部目录系统是控制点，用户和组从这些系统同步到云目录（如 Azure AD）。 一旦这些标识在云中，它们就可以提供给 Office 365、Azure 和其他应用程序。

![标识生命周期](media/cloud-governed-management-for-on-premises//image1.png)

随着组织将更多的 IT 基础架构及其应用程序迁移到云中，许多公司都在寻求改进身份管理作为服务的安全性和简化的管理功能。 Azure AD 中云交付的 IDaaS 功能通过提供解决方案和功能，使组织能够快速采用和移动更多其身份管理，从而加快向云治理管理的过渡系统到 Azure AD，同时继续支持现有应用程序和新应用程序。

本文概述了 Microsoft 的混合 IDaaS 策略，并介绍了组织如何使用 Azure AD 进行现有应用程序。

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Azure AD 云治理标识管理的方法

随着组织向云过渡，他们需要保证自己能够控制其完整的环境 - 更高的安全性和对活动的可见性，并由自动化和主动洞察提供支持。 "**云治理管理**"描述了组织如何管理和管理来自云的用户、应用程序、组和设备。

在这个现代世界中，由于 SaaS 应用程序的激增以及协作和外部身份的作用日益增加，组织需要能够大规模有效地管理。 云的新风险环境意味着组织必须响应更快 - 危害云用户的恶意参与者可能会影响云和本地应用程序。

特别是，混合组织需要能够委派和自动化任务，这在历史上是 IT 手动执行的。 要自动执行任务，他们需要协调不同标识相关资源（用户、组、应用程序、设备）生命周期的 API 和流程，以便他们可以将这些资源的日常管理委派给核心 IT 员工。 Azure AD 通过用户帐户管理和用户本机身份验证来满足这些要求，而无需本地标识基础结构。 不构建本地基础结构可以使具有新用户社区的组织受益，例如业务合作伙伴，这些用户不是源自其本地目录，但其访问管理对于实现业务成果至关重要。

此外，如果没有治理---，管理是不完整的，在这个新世界中，治理是标识系统的集成部分，而不是附加组件。 身份治理使组织能够跨员工、业务合作伙伴和供应商以及服务和应用程序管理身份和访问生命周期。

通过采用身份治理，使组织能够更轻松地过渡到云治理管理，允许 IT 部门扩展、向来宾应对新的挑战，并提供比客户拥有更深入的见解和自动化本地基础结构。 在这个新世界中的治理意味着组织能够对组织内资源的访问具有透明度、可见性和适当的控制。 借助 Azure AD，安全操作和审核团队可以了解谁拥有---以及谁应该拥有 - 访问组织中哪些资源（在哪些设备上）、这些用户使用该访问权限执行的操作，以及组织是否具有和使用适当的资源根据公司或监管政策取消或限制访问的控制措施。

新的管理模式使具有 SaaS 和业务线 （LOB） 应用程序的组织受益，因为它们更易于管理和安全地访问这些应用程序。 通过将应用程序与 Azure AD 集成，组织将能够一致地使用和管理跨云和本地源标识的访问。 应用程序生命周期管理变得更加自动化，Azure AD 提供了对应用程序使用情况的丰富见解，在本地标识管理中不容易实现。 通过 Azure AD、Office 365 组和 Teams 自助服务功能，组织可以轻松地创建用于访问管理和协作的组，并在云中添加或删除用户，以启用协作和访问管理要求。

为云管理选择正确的 Azure AD 功能取决于要使用的应用程序以及这些应用程序将如何与 Azure AD 集成。 以下各节概述了 AD 集成应用程序和使用联合协议的应用程序（例如 SAML、OAuth 或 OpenID 连接）的方法。

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>面向 AD 集成应用程序的云治理管理

Azure AD 通过安全远程访问和对这些应用程序进行条件访问，改进了组织的本地活动目录集成应用程序的管理。 此外，Azure AD 还为用户的现有 AD 帐户提供帐户生命周期管理和凭据管理，包括：

* **为本地应用程序安全远程访问和条件访问**

对于许多组织来说，管理本地 AD 集成 Web 和基于远程桌面的应用程序从云访问的第一步是在这些应用程序前面部署[应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)，以提供安全的远程访问。

单一登录到 Azure AD 后，用户可以通过外部 URL 或内部应用程序门户访问云端和本地的应用程序。 例如，应用程序代理提供远程访问和单一登录远程桌面、SharePoint 以及 Tableau 和 Qlik 等应用程序以及业务线 （LOB） 应用程序。 此外，条件访问策略可以包括显示[使用条款](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou)，并确保用户在能够访问应用程序之前[已同意它们](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou)。

![应用代理体系结构](media/cloud-governed-management-for-on-premises/image2.png)

* **活动目录帐户的自动生命周期管理**

身份治理可帮助组织在*工作效率*---人员访问所需资源（例如何时加入组织）之间实现平衡？ ---*和安全*---，他们的访问权限应该随着时间的推移而变化，例如此人的就业状态何时发生变化？ 标识生命周期管理是标识监管的基石，大规模的有效监管需要将应用程序的标识生命周期管理基础结构现代化。

对于许多组织来说，员工的身份生命周期与该用户在人力资本管理 （HCM） 系统中的表示形式相关联。 对于使用工作日作为 HCM 系统的组织，Azure AD 可确保 AD 中的用户帐户[自动预配和取消预配到工作日中的工作人员](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)。 这样做通过自动实现与生俱来帐户的自动化从而提高了用户工作效率，并通过确保当用户更改角色或离开组织时自动更新应用程序访问来管理风险。 工作日驱动的用户预配[部署计划是](https://aka.ms/WorkdayDeploymentPlan)分步指南，用于引导组织在五步过程中完成"工作日"到"活动目录用户预配"解决方案的最佳实践实现。

Azure AD 高级版还包括 Microsoft 标识管理器，该管理器可以从其他本地 HCM 系统（包括 SAP、Oracle 电子商务和 Oracle PeopleSoft）导入记录。

企业对企业协作越来越需要授予组织外部人员访问权限。 [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/)协作使组织能够安全地与来宾用户和外部合作伙伴共享其应用程序和服务，同时保持对自身公司数据的控制。

Azure AD 可以根据需要[在 AD 中自动为来宾用户创建帐户](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises)，使业务来宾无需另一个密码即可访问本地 AD 集成应用程序。 组织可以为[来宾用户设置多重身份验证 （MFA） 策略](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)，以便在应用程序代理身份验证期间执行 MFA 检查。 此外，对云 B2B 用户执行的任何[访问评论](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)都适用于本地用户。 例如，如果通过生命周期管理策略删除了云用户，则本地用户也会被删除。

**活动目录帐户的凭据管理**Azure AD 的自助服务密码重置允许忘记密码的用户重新验证并重置其密码，更改的密码[将写入本地活动目录](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)。 密码重置过程还可以使用本地活动目录密码策略：当用户重置其密码时，将检查它以确保在将其提交到该目录之前满足本地活动目录策略。 自助服务密码重置[部署计划](https://aka.ms/deploymentplans/sspr)概述了通过 Web 和 Windows 集成体验向用户推出自助服务密码重置的最佳做法。

![Azure AD SSPR 架构](media/cloud-governed-management-for-on-premises/image3.png)

最后，对于允许用户在 AD 中更改其密码的组织，可以通过[Azure AD 密码保护功能](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)（当前处于公共预览版中）将 AD 配置为使用与组织在 Azure AD 中使用的密码策略相同的密码策略。

当组织准备好通过将托管应用程序的操作系统移动到 Azure 将 AD 集成应用程序移动到云中时[，Azure AD 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)提供与 AD 兼容的域服务（如域联接、组策略、LDAP 和 Kerberos/NTLM 身份验证）。 Azure AD 域服务与组织现有的 Azure AD 租户集成，使用户能够使用其公司凭据登录。 此外，现有组和用户帐户可用于保护对资源的访问，确保将本地资源更顺畅地"提升和转移"到 Azure 基础结构服务。

![Azure AD 域服务](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>基于本地联合应用程序的云治理管理

对于已经使用本地标识提供程序的组织，将应用程序移动到 Azure AD 可实现更安全的访问，并简化联合管理管理体验。 Azure AD 支持通过使用 Azure AD 条件访问配置每个应用程序精细的访问控件，包括 Azure 多重身份验证。 Azure AD 支持更多功能，包括特定于应用程序的令牌签名证书和可配置的证书到期日期。 这些功能、工具和指南使组织能够停用其本地标识提供程序。 例如，微软自己的 IT 部门将 17，987 个应用程序从 Microsoft 的内部活动目录联合服务 （AD FS） 迁移到 Azure AD。

![Azure AD 演进](media/cloud-governed-management-for-on-premises/image5.png)

要开始将联合应用程序迁移到 Azure AD 作为标识提供程序，请参阅https://aka.ms/migrateapps包含指向以下链接的链接：

* 白皮书[将应用程序迁移到 Azure 活动目录](https://aka.ms/migrateapps/whitepaper)，其中介绍了迁移的好处，并介绍了如何在四个明确概述的阶段规划迁移：发现、分类、迁移和持续管理。 您将指导您如何思考流程，并将项目分解为易于使用的部分。 整个文档为指向重要资源的链接，可全程为你提供帮助。

* 解决方案指南[将应用程序身份验证从活动目录联合服务迁移到 Azure 活动目录](https://aka.ms/migrateapps/adfssolutionguide)，更详细地探讨了规划和执行应用程序迁移项目的四个阶段。 在本指南中，您将了解如何将这些阶段应用于将应用程序从活动目录联合服务 （AD FS） 移动到 Azure AD 的特定目标。

* [活动目录联合服务迁移就绪脚本](https://aka.ms/migrateapps/adfstools)可以在现有本地活动目录联合服务 （AD FS） 服务器上运行，以确定应用程序迁移到 Azure AD 的准备情况。

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>云和本地应用程序的持续访问管理

组织需要一个流程来管理可扩展的访问。 用户继续累积访问权限，最终超出最初为其预配的权限。 此外，企业组织需要能够有效地扩展，以不断制定和实施访问策略和控制。

通常，IT 部门会将访问权限审批决策委托给业务决策人。 此外，IT 部门可能涉及到用户本身的事务。 例如，访问公司位于欧洲的市场营销应用程序中的机密客户数据的用户需要知道该公司的策略。 来宾用户可能还不知道受邀到的组织中数据的处理方式要求。

组织可以通过[动态组](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)等技术，加上用户预配到[SaaS 应用程序](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)，或使用[跨域身份管理系统 （SCIM） 标准集成的应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)来自动执行访问生命周期过程。 组织还可以控制哪些[访客用户有权访问本地应用程序](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises)。 然后，可以使用 [Azure AD 访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)定期评审这些访问权限。

## <a name="future-directions"></a>未来方向

在混合环境中，Microsoft 的策略是启用部署，其中**云是标识的控制平面**，而本地目录和其他标识系统（如 Active Directory 和其他本地应用程序）是向用户预配访问权限的目标。 此策略将继续确保依赖这些权限和工作负载中的权限、身份和访问权限。 在此结束状态下，组织将能够完全从云中提高最终用户的工作效率。

![Azure AD 体系结构](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>后续步骤

有关如何开始此旅程的详细信息，请参阅位于 的<https://aka.ms/deploymentplans>Azure AD 部署计划。 它们提供有关如何部署 Azure 活动目录 （Azure AD） 功能的端到端指导。 每个计划都解释了成功推出常见 Azure AD 功能所需的业务价值、规划注意事项、设计和操作过程。 当我们使用 Azure AD 从云添加新功能进行管理时，Microsoft 会不断更新部署计划，并获取从客户部署和其他反馈中学到的最佳实践。
