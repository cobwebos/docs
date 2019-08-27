---
title: 标识监管 - Azure Active Directory | Microsoft Docs
description: 使用 Azure Active Directory 标识监管可以在组织的安全性和员工工作效率与适当的流程和可见性需求之间实现平衡。
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 08/25/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 014a2c3cff3804657e4e2bf624b97eceef4bf4b2
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70033325"
---
# <a name="what-is-azure-ad-identity-governance"></a>什么是 Azure AD Identity Governance？

使用 Azure Active Directory (Azure AD) 标识监管可以在组织的安全性和员工工作效率与适当的流程和可见性需求之间实现平衡。 标识监管提供的功能可以确保适当的用户对适当的资源拥有适当的访问权限，它还可用于保护、监视和审核对关键资产的访问 - 同时确保员工的工作效率。  

标识监管使组织能够跨员工、业务合作伙伴和供应商以及服务和应用程序执行以下任务：

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

![标识生命周期](./media/identity-governance-overview/identity-lifecycle.png)

对于许多组织而言，员工的标识生命周期与该用户在 HCM（人力资本管理）系统中的表示形式密切相关。  Azure AD Premium 在 Active Directory 和 Azure Active Directory 中自动维护 Workday 中表示的人员的用户标识，请参阅 [Workday 入站预配（预览版）教程](../saas-apps/workday-inbound-tutorial.md)。  Azure AD Premium 还包含可从 SAP、Oracle eBusiness 和 Oracle PeopleSoft 等本地 HCM 系统导入记录的 [Microsoft Identity Manager](/microsoft-identity-manager/)。

越来越多的方案需要与组织外部的人员协作。 使用 [Azure AD B2B](/azure/active-directory/b2b/) 协作可以安全地将组织的应用程序和服务与来自任何组织的来宾用户和外部合作伙伴共享，同时保持对自己公司数据的控制。

## <a name="access-lifecycle"></a>访问生命周期

组织需要使用一个流程来管理最初在创建用户标识时未为用户预配的访问权限。  此外，企业组织需要能够有效缩放，以便持续制定和实施访问策略与控制措施。

![访问生命周期](./media/identity-governance-overview/access-lifecycle.png)

通常，IT 部门会将访问权限审批决策委托给业务决策人。  此外，IT 部门可能涉及到用户本身的事务。  例如，访问公司位于欧洲的市场营销应用程序中的机密客户数据的用户需要知道该公司的策略。 来宾用户可能不知道他们受邀访问的组织的数据处理要求。

组织可以通过[动态组](../users-groups-roles/groups-dynamic-membership.md)等技术，并结合 [SaaS 应用](../saas-apps/tutorial-list.md)的用户预配或者[与 SCIM集成的应用](../manage-apps/use-scim-to-provision-users-and-groups.md)，将访问生命周期过程自动化。  组织还可以控制哪些[来宾用户有权访问本地应用程序](../b2b/hybrid-cloud-to-on-premises.md)。  然后，可以使用 [Azure AD 访问评审](access-reviews-overview.md)定期评审这些访问权限。

当用户尝试访问应用程序时，Azure AD 会实施[条件访问](/azure/active-directory/conditional-access/)策略。 例如，条件访问策略可以包括显示[使用条款](../conditional-access/terms-of-use.md)，并[确保用户已同意这些条款](../conditional-access/require-tou.md)，然后才能访问应用程序。

## <a name="privileged-access-lifecycle"></a>特权访问权限生命周期

一直以来，其他供应商将特权访问权限描述为标识监管中的一项独立功能。 但是，Microsoft 将特权访问权限的监管视为标识监管的重要组成部分 - 特别是考虑到与这些管理员权限相关的滥用可能会对组织造成的影响。 拥有管理权限的员工、供应商与合同工需要接受监管。

![特权访问权限生命周期](./media/identity-governance-overview/privileged-access-lifecycle.png)

Azure AD Privileged Identity Management (PIM) 提供用于保护 Azure AD、Azure 和其他 Microsoft Online Services 中的资源访问权限的其他定制控制措施。  Azure AD PIM 提供的实时访问和角色更改警报功能，以及多重身份验证和条件访问，共同提供了一套综合性的监管控制措施，可帮助保护公司的资源（目录、Office 365 和 Azure 资源角色）。 与处理其他形式的访问权限一样，组织可以使用访问评审来针对充当管理员角色的所有用户配置定期的访问权限重新认证。

## <a name="getting-started"></a>入门

尽管我们不能为每个客户提供完美的解决方案或建议，但以下配置提供了 Microsoft 建议你遵循的基准策略指南，以确保提高安全性和员工工作效率。

- [标识和设备访问权限配置](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [保护特权访问](../users-groups-roles/directory-admin-roles-secure.md)

还可以在 Azure 门户中查看**标识监管**的“入门”选项卡，以开始使用权利管理、访问评审、Privileged Identity Management 和使用条款。

![标识监管入门](./media/identity-governance-overview/getting-started.png)

如果你对标识调控功能有任何反馈, 请单击 "Azure 门户中的 **" 获取反馈 "** , 以提交你的反馈。 团队定期查看你的反馈。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure AD 权利管理？（预览版）？](entitlement-management-overview.md)
- [Azure AD 访问评审是什么？](access-reviews-overview.md)
- [什么是 Azure AD Privileged Identity Management？](../privileged-identity-management/pim-configure.md)
- [“使用条款”可以用来做什么？](active-directory-tou.md)
