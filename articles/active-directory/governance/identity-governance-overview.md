---
title: Azure AD 标识监管 | Microsoft Docs
description: Azure AD 标识监管可以在组织的安全性和员工工作效率与适当的流程和可见性需求之间实现平衡。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 304399905e9dfd37557a342248c829c1f940452b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167562"
---
# <a name="what-is-azure-ad-identity-governance"></a>什么是 Azure AD 标识监管？

Azure Active Directory (Azure AD) 标识监管可以在组织的安全性和员工工作效率与适当的流程和可见性需求之间实现平衡。 标识监管提供的功能可以确保适当的用户对适当的资源拥有适当的访问权限，它还可用于保护、监视和审核对关键资产的访问 - 同时确保员工的工作效率。  

标识管理使组织能够针对员工、业务合作伙伴、供应商、服务和应用程序实现以下目的：

- 监管标识生命周期
- 监管访问生命周期
- 安全管理

具体而言，标识监管旨在帮助组织解决以下四个关键问题：

- 哪些用户应该有权访问哪些资源？
- 这些用户使用该访问权限做些什么？
- 是否存在有效的组织控制措施可用于管理访问权限？
- 审核员是否可以验证控制措施是否正常实施？

## <a name="identity-lifecycle"></a>标识生命周期

标识监管可帮助组织在以下需求之间实现平衡：工作效率 - 用户可以多快地访问所需的资源（例如，在刚入职时）？ 安全性 - 用户的访问权限会不断发生怎样的变化（例如，由于该用户的雇佣状态发生变化）？  标识生命周期管理是标识监管的基石，大规模的有效监管需要将应用程序的标识生命周期管理基础结构现代化。

对于许多组织而言，员工的标识生命周期与该用户在 HCM（人力资本管理）系统中的表示形式密切相关。  Azure AD Premium 在 Active Directory 和 Azure Active Directory 中自动维护 Workday 中表示的人员的用户标识，请参阅 [Workday 入站预配（预览版）教程](../saas-apps/workday-inbound-tutorial.md)。  Azure AD Premium 还包含可从 SAP、Oracle eBusiness 和 Oracle PeopleSoft 等本地 HCM 系统导入记录的 [Microsoft Identity Manager](/microsoft-identity-manager/)。

越来越多的方案需要与组织外部的人员协作。 使用 [Azure AD B2B](/azure/active-directory/b2b/) 协作可以安全地将组织的应用程序和服务与来自任何组织的来宾用户和外部合作伙伴共享，同时保持对自己公司数据的控制。

## <a name="access-lifecycle"></a>访问生命周期

组织需要使用一个流程来管理最初在创建用户标识时未为用户预配的访问权限。  此外，企业组织需要能够有效缩放，以便持续制定和实施访问策略与控制措施。

通常，IT 部门会将访问权限审批决策委托给业务决策人。  此外，IT 部门可能涉及到用户本身的事务。  例如，访问公司位于欧洲的市场营销应用程序中的机密客户数据的用户需要知道该公司的策略。 来宾用户可能不知道他们受邀访问的组织的数据处理要求。

组织可以通过[动态组](../users-groups-roles/groups-dynamic-membership.md)等技术，并结合 [SaaS 应用](../saas-apps/tutorial-list.md)的用户预配或者[与 SCIM集成的应用](../manage-apps/use-scim-to-provision-users-and-groups.md)，将访问生命周期过程自动化。  组织还可以控制哪些[来宾用户有权访问本地应用程序](../b2b/hybrid-cloud-to-on-premises.md)。  然后，可以使用 [Azure AD 访问评审](access-reviews-overview.md)定期评审这些访问权限。

当用户尝试访问应用程序时，Azure AD 会实施[条件访问](/azure/active-directory/conditional-access/)策略。 例如，条件访问策略可以显示[使用条款](active-directory-tou.md)，并确保用户在访问应用程序之前[同意这些条款](../conditional-access/require-tou.md)。

## <a name="privileged-access-lifecycle"></a>特权访问权限生命周期

一直以来，其他供应商将特许访问权限描述为标识监管中的一项独立功能。 但是，Microsoft 将特权访问权限的监管视为标识监管的重要组成部分 - 尤其是组织可能遇到管理员权限的滥用情况时。 拥有管理权限的员工、供应商与合同工需要接受监管。

Azure AD Privileged Identity Management (PIM) 提供用于保护 Azure AD、Azure 和其他 Microsoft Online Services 中的资源访问权限的其他定制控制措施。  Azure AD PIM 提供的实时访问和角色更改警报功能，以及多重身份验证和条件访问，共同提供了一套综合性的监管控制措施，可帮助保护公司的资源（目录、Office 365 和 Azure 资源角色）。 与处理其他形式的访问权限一样，组织可以使用访问评审来针对充当管理员角色的所有用户配置定期的访问权限重新认证。

## <a name="getting-started"></a>入门

尽管我们不能为每个客户提供完美的解决方案或建议，但以下配置提供了有关 Microsoft 建议的基准策略，确保提高安全性和员工工作效率的指导。

- [标识和设备访问权限配置](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [保护特权访问](../users-groups-roles/directory-admin-roles-secure.md)


### <a name="access-reviews"></a>访问评审

- [什么是访问评审？](access-reviews-overview.md)
- [使用访问评审管理用户访问权限](manage-user-access-with-access-reviews.md)
- [使用访问评审管理来宾访问权限](manage-guest-access-with-access-reviews.md)
- [开始目录角色的访问评审](../privileged-identity-management/pim-how-to-start-security-review.md)

### <a name="terms-of-use"></a>使用条款

- [“使用条款”可以用来做什么？](active-directory-tou.md)

### <a name="privileged-identity-management"></a>Privileged Identity Management

- [什么是 Azure AD PIM？](../privileged-identity-management/pim-configure.md)
