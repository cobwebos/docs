---
title: Azure AD 云受管理的本地工作负荷-Azure
description: 本主题介绍适用于在本地工作负荷管理的云管理。
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109524"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>云如何提供 Azure AD 管理本地工作负荷的管理

Azure Active Directory (Azure AD) 是综合性的标识即服务 (IDaaS) 解决方案数以百万的 s p a n 标识、 访问管理和安全的方方面的组织使用。 Azure AD 包含十亿次以上的用户标识，可帮助用户登录，并同时安全地访问：

* 外部资源，例如 Microsoft Office 365、 Azure 门户中和数以千计的其他软件作为-服务 (SaaS) 应用程序。
* 内部资源，例如组织的企业网络和 intranet，以及由该组织开发的任何云应用程序上的应用程序。

如果它们是纯云，或混合为如果它们具有部署在本地工作负荷，组织可以使用 Azure AD。 Azure AD 的混合部署可以将其 IT 资产迁移到云，或继续将现有的本地基础结构，以及新的云服务集成的组织策略的一部分。

从历史上看，混合组织已了解 Azure AD 作为其现有的扩展本地基础结构。 在这些部署中，在本地标识管理管理，Windows Server Active Directory 或其他内部目录系统，是的控点，以及用户和组从这些系统同步到 Azure AD 之类的云目录。 一旦这些标识在云中，它们可为 Office 365、 Azure 及其他应用程序。

![标识生命周期](media/cloud-governed-management-for-on-premises//image1.png)

随着组织迁移到云应用程序的同时其 IT 基础结构的详细信息，很多要查找的改进的安全性和标识管理即服务的简化的管理功能。 Azure AD 中的云提供 IDaaS 功能加快转换到云的解决方案和功能，组织可快速采用，并将其标识管理的详细信息从传统的本地移动，从而控制的管理到 Azure AD，同时继续支持现有以及新应用程序的系统。

本白皮书概述了 Microsoft 的混合 IDaaS 的策略，并介绍组织如何为其现有的应用程序使用 Azure AD。

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>实现云控制的标识管理的 Azure AD 方法

作为组织过渡到云，他们需要保证它们具有更高的安全性和更多可见性支持的自动化和主动 insights 活动对其完整的环境中的控件。 "**云控制管理**"介绍了组织管理和控制其用户、 应用程序、 组和从云设备的方式。

在我们这个现代社会，组织需要能够有效地管理大规模情况下，由于 SaaS 应用程序的发展壮大和增加协作和外部标识的角色。 在云中的新风险前景意味着，组织必须响应速度更快-恶意行动者威胁云用户可能会影响在本地和云应用程序。

具体而言，混合组织需要能够委托和自动执行任务，其中从历史上看 IT 未手动。 若要自动执行任务，他们需要的 Api 并安排不同标识相关的资源 （用户、 组、 应用程序、 设备） 的生命周期的进程使它们可以将这些资源的日常管理委托给外部的更多个人核心 IT 人员。 Azure AD 满足这些要求通过用户帐户管理，而无需用户的本机身份验证的本地标识基础结构。 不会生成的本地基础结构可以受益具有新的社区的用户，如业务伙伴，这不是发起的在其本地目录中，但其访问管理实现业务成果的关键的组织。

此外，管理未完成而无需管理---和这个新世界中的监管是标识系统，而不是一个外接程序的集成的部分。 标识管理，使组织能够管理标识和访问跨员工、 业务合作伙伴和供应商和服务和应用程序生命周期。

合并标识管理可以更轻松地启用迁移，以便云管控的管理组织，允许 IT 部门能够缩放，与来宾应对新挑战，并提供更深层次见解和实现比客户已经有了使用自动化在本地基础结构。 这个新世界中的监管意味着组织很透明度、 可见性和适当的控制，在对组织内的资源的访问权限的能力。 与 Azure AD 安全操作和审核团队能够有人员---和谁应具有的可见性中 （在哪些设备） 组织、 这些用户具有相关访问权限，执行的操作和组织是否拥有和使用适当的哪些资源访问若要删除或限制访问公司或法规策略根据的控件。

新的管理模型受益组织与 SaaS 和业务线 (LOB) 应用程序，因为他们可更轻松地管理和保护对这些应用程序的访问。 通过与 Azure AD 集成的应用程序，组织将能够使用和管理跨这两个云访问和内部部署一致地发起的标识。 应用程序生命周期管理变得更加自动化，以及 Azure AD 提供丰富的见解并不能轻松地实现中的本地标识管理的应用程序使用情况。 通过 Azure AD，Office 365 组和团队的自助服务功能，组织可以轻松地创建用于访问管理和协作的组和添加或删除用户在云中以实现协作和访问管理要求。

选择控制的管理取决于要使用的应用程序的云连接和这些应用程序与 Azure AD 的集成方式的右 Azure AD 功能。 以下部分概述的方法，才能为 AD 集成的应用程序，并使用联合身份验证协议 （例如，SAML、 OAuth 或 OpenID Connect） 的应用程序。

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>AD 集成的应用程序的控制的云管理

Azure AD 可提高组织的本地 Active Directory 集成的应用程序的安全远程访问和条件性访问通过对这些应用程序的管理。 此外，Azure AD 还提供帐户生命周期管理和凭据管理对于用户的现有的 AD 帐户，包括：

* **安全远程访问和本地应用程序的条件性访问**

对于许多组织而言，从云中的本地 AD 集成的 web 和远程基于桌面的应用程序管理访问权限的第一步是部署[应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)前面这些应用程序提供保护远程访问。

单一登录到 Azure AD 后，用户可以通过外部 URL 或内部应用程序门户访问云端和本地的应用程序。 例如，应用程序代理提供远程访问和单一登录到远程桌面、 SharePoint，以及应用程序，例如 Tableau 和 Qlik 和业务线 (LOB) 应用程序的行。 此外，条件性访问策略可以包括显示[使用条款](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou)并[确保用户已同意这些](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou)之前无法访问某个应用程序。

![应用代理体系结构](media/cloud-governed-management-for-on-premises/image2.png)

* **Active Directory 帐户的自动生命周期管理**

标识管理可帮助组织实现之间的平衡*生产力*---一个人可以多快的速度有访问的资源所需，例如何时加入组织？ ---和*安全*---如何应他们的访问权限随时间变化，例如当这个人的就业状态的更改？ 标识生命周期管理是标识监管的基石，大规模的有效监管需要将应用程序的标识生命周期管理基础结构现代化。

许多组织而言，员工的标识生命周期的人力资本管理 (HCM) 系统中绑定到该用户的表示形式。 对于将 Workday 用作其 HCM 系统的组织，Azure AD 可以确保在 AD 中的用户帐户都[自动预配和取消预配为工作人员在 Workday 中](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)。 执行与通过自动化 birthright 改进了的用户工作效率，潜在客户的帐户和风险管理方法是确保应用程序访问时将自动更新用户更改角色或离开组织。 Workday 驱动的用户预配[部署计划](https://aka.ms/WorkdayDeploymentPlan)是将在五步进程中的 Active Directory 用户预配解决方案指导通过 Workday 的最佳实践实现组织的分步指南。

Azure AD 高级版还包括 Microsoft 标识管理器，它可以记录导入从其他本地 HCM 系统，包括 SAP、 Oracle eBusiness、 和 Oracle PeopleSoft。

企业到企业协作越来越多地需要授予对组织外部人员访问权限。 [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/)协作可让组织可以安全地与来宾用户和外部合作伙伴同时维护控制其自己的公司数据共享其应用程序和服务。

Azure AD 可以[自动在 AD 中的来宾用户创建帐户](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises)根据需要启用商业客户访问的本地 AD 集成的应用程序而无需另一个密码。 组织可以设置[来宾用户的多重身份验证 (MFA) 策略](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)s 因此 MFA 检查在应用程序代理身份验证过程中完成。 此外，任何[访问评审](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)云 B2B 用户的本地用户对应用上完成。 例如，如果通过生命周期管理策略删除云用户，则在本地用户也会删除。

**凭据管理 Active Directory 帐户**Azure AD 的自助服务密码重置允许用户忘记其密码重新进行身份验证和重置其密码，但有更改的密码[写入到在本地 Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)。 密码重置过程还可以使用本地 Active Directory 密码策略：当用户重置其密码时，它将检查以确保其满足之前提交到该目录的本地 Active Directory 策略。 自助服务密码重置[部署计划](https://aka.ms/deploymentplans/sspr)启用自助服务密码重置为用户通过 web 和 Windows 集成体验的最佳实践。

![Azure AD SSPR 体系结构](media/cloud-governed-management-for-on-premises/image3.png)

最后，对于允许用户在 AD 中更改其密码的组织，AD 可配置为使用相同的密码策略，因为在通过 Azure AD 中使用组织[Azure AD 密码保护功能](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)，当前在公共预览版。

如果组织已准备好移到云的 AD 集成应用程序移动到 Azure，应用程序承载的操作系统[Azure AD 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)提供 AD 兼容 （例如域加入的域服务组策略、 LDAP 和 Kerberos/NTLM 身份验证)。 Azure AD 域服务与组织的现有 Azure AD 租户，从而让用户使用其公司凭据进行登录。 此外，现有组和用户帐户可以用于安全访问资源，确保更流畅提起并移动到 Azure 基础结构服务的本地资源。

![Azure AD 域服务](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>在本地基于联合身份验证的应用程序控制的云管理

已使用的本地标识提供程序的组织，移动到 Azure AD 应用程序实现更安全的访问和联合身份验证管理变得更容易管理体验。 Azure AD 允许配置细致的每个应用程序访问控制，包括 Azure 多重身份验证，使用 Azure AD 条件访问。 Azure AD 支持多个功能，包括特定于应用程序的令牌签名证书和可配置的证书到期日期。 这些功能、 工具和指南，组织可以停用其在本地标识提供程序。 Microsoft 的 IT，一个示例中，已移动 17,987 应用程序从 Microsoft 的内部 Active Directory 联合身份验证服务 (AD FS) 到 Azure AD。

![Azure AD 演变](media/cloud-governed-management-for-on-premises/image5.png)

若要开始迁移联合应用程序到 Azure AD 作为标识提供者，请参阅 https://aka.ms/migrateapps 包含链接：

* 白皮书[您的应用程序迁移到 Azure Active Directory](https://aka.ms/migrateapps/whitepaper)，该显示迁移的好处，并介绍了如何在四个明确列出阶段中进行迁移规划： 发现、 分类、 迁移和正在进行管理。 本教程介绍如何在过程的看法和分解成易于使用的部分项目。 整个文档为指向重要资源的链接，可全程为你提供帮助。

* 解决方案指南[迁移应用程序从 Active Directory 联合身份验证服务向 Azure Active Directory 验证](https://aka.ms/migrateapps/adfssolutionguide)探讨了更多详细信息中相同的计划和执行应用程序迁移项目的四个阶段. 在本指南中，将了解如何将这些阶段应用于从 Active Directory 联合身份验证服务 (AD FS) 的应用程序移动到 Azure AD 的特定目标。

* [Active Directory 联合身份验证服务迁移准备情况脚本](https://aka.ms/migrateapps/adfstools)可以在现有的本地 Active Directory 联合身份验证服务 (AD FS) 服务器，以确认迁移到 Azure AD 的应用程序的安装上运行。

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>跨云和本地应用程序正在进行的访问管理

组织需要一个过程来管理可缩放的访问。 用户不断地累积的访问权限并最终超出最初为其预配什么。 此外，企业组织需要能够扩展有效地开发和实施访问策略和持续的控件。

通常，IT 部门会将访问权限审批决策委托给业务决策人。 此外，IT 部门可能涉及到用户本身的事务。 例如，访问公司位于欧洲的市场营销应用程序中的机密客户数据的用户需要知道该公司的策略。 来宾用户还可能会察觉到它们已受邀组织中的数据的处理要求。

组织可以如自动化技术访问生命周期流程[动态组](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)，并为用户预配[SaaS 应用程序](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)，或[应用程序集成使用用于跨域标识管理系统 (SCIM](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)) 标准。 组织还可以控制哪些[来宾用户有权访问的本地应用程序](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises)。 然后，可以使用 [Azure AD 访问评审](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)定期评审这些访问权限。

## <a name="future-directions"></a>未来发展方向

Microsoft 的策略是启用部署在混合环境中，其中**云是标识控制平面**，并在本地目录和其他标识系统，例如 Active Directory 和其他本地应用程序，都将具有访问权限的用户预配的目标。 此策略将继续确保权限、 标识和访问这些应用程序和依赖于它们的工作负荷中。 在此最终状态下，组织将无法将驱动器最终用户工作效率完全从云中。

![Azure AD 体系结构](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>后续步骤

有关如何开始使用这一过程的详细信息，请参阅 Azure AD 部署计划，位于<https://aka.ms/deploymentplans>。 它们提供有关如何部署 Azure Active Directory (Azure AD) 功能的端到端指南。 每个计划介绍规划注意事项、 设计和操作过程成功地将常见的 Azure AD 功能所需的业务价值。 Microsoft 不断更新部署计划的从客户部署和其他反馈中了解到，当我们将新功能添加到从云中使用 Azure AD 管理的最佳做法。
