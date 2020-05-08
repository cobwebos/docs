---
title: 了解管理员角色委派 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory 中的委托模型、示例和 角色安全性
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87f5b1ec02400c5cd9d26cc324ddbafdf8d76537
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582583"
---
# <a name="delegate-administration-in-azure-active-directory"></a>在 Azure Active Directory 中委托管理权限

组织的发展伴随着复杂性的增加。 针对这种情况，常见的响应措施是降低使用 Azure Active Directory (AD) 管理员角色进行访问管理时的工作负荷。 可向用户分配让他们访问应用和执行任务所需的最低可能的特权。 你可以不将“全局管理员”角色分配给每个应用程序所有者，但这样会将应用程序管理责任施加给现有的全局管理员。 有多种原因导致组织改用更离散式的管理。 本文可帮助你在组织中规划权限委托。

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>集中化权限与委托权限

随着组织的发展，可能很难跟踪哪些用户拥有特定的管理员角色。 如果某个员工拥有本不应拥有的管理员权限，组织可能会更容易出现安全漏洞。 通常情况下，可支持的管理员数量以及他们的权限粒度级别都取决于部署的大小和复杂性。

* 在小型或概念证明部署中，一个或少量几个管理员就能满足一切需求；无需委托权限。 在这种情况下，请创建每个具有“全局管理员”角色的管理员。
* 在包含多个计算机、应用程序和桌面的大型部署中，需要更多的委托。 多个管理员可以承担更多的具体职责（角色）。 例如，有些可以是“特权标识管理员”，还有一些管理员可以是“应用程序管理员”。 此外，管理员只能管理特定的对象组，例如设备。
* 更大的部署可能需要更精细的权限，此外，某些管理员可能需要充当非常规或混合角色。

在 Azure AD 门户中，可以[查看任何角色的所有成员](directory-manage-roles-portal.md)，以帮助快速检查部署和委托权限。

如果想要委托 Azure 资源访问权限而不是 Azure AD 中的管理访问权限，请参阅[分配基于角色的访问控制 (RBAC) 角色](../../role-based-access-control/role-assignments-portal.md)。

## <a name="delegation-planning"></a>委托规划

用于开发符合需求的委托模型。 开发委托模型是一个迭代式设计过程，我们建议遵照以下步骤：

* 定义所需的角色
* 委托应用管理权限
* 授予注册应用程序的能力
* 委托应用所有权
* 制定安全计划
* 建立紧急帐户
* 保护管理员角色
* 将特权提升指定为暂时性的措施

## <a name="define-roles"></a>定义角色

确定由管理员执行的 Active Directory 任务以及将它们映射到角色的方式。 可在 Azure 门户中[查看详细的角色说明](directory-manage-roles-portal.md)。

应该评估每个任务的频率、重要性和难度。 这些标准是任务定义至关重要的方面，因为它们决定了是否要委托某个权限：

* 日常执行的、风险有限的和容易完成的任务非常适合委托权限。
* 至于极少执行但会在整个组织中造成很大影响且需要较高技能的任务，在委托权限之前应慎重考虑。 可以[暂时将某个帐户提升到所需的角色](../active-directory-privileged-identity-management-configure.md)，或重新分配任务。

## <a name="delegate-app-administration"></a>委托应用管理权限

组织中应用的激增可能会给委托模型带来压力。 如果将应用程序访问管理的负担放在全局管理员身上，则随着时间的推移，该模型的开销可能会增大。 如果向某人授予了“全局管理员”角色，让其执行配置企业应用程序等任务，则现在可将管理负担转移到其后面的特权更低的角色。 这样做有助于改善安全局势，并减少低级失误的可能性。 特权最高的应用程序管理员角色包括：

* “应用程序管理员”角色：授予管理目录中所有应用程序的能力，包括注册、单一登录设置、用户和组分配与授权、应用程序代理设置，以及许可。**** 它不能授予管理条件访问的能力。
* “云应用程序管理员”角色：授予“应用程序管理员”的所有能力，但不能授予应用程序代理设置的访问权限（因为该角色没有本地权限）****。

## <a name="delegate-app-registration"></a>委托应用注册

默认情况下，所有用户都可以创建应用程序注册。 若要有选择地授予创建应用程序注册的能力，请执行以下操作：

* 在“用户设置”中，将“用户可以注册应用程序”更改为“否”********
* 将用户分配到应用程序开发人员角色

若要有选择地授予允许应用程序访问数据的能力，请执行以下操作：

* 在“用户设置”中，将“用户可以自行许可应用程序访问公司数据”设置为“否”********
* 将用户分配到应用程序开发人员角色

当应用程序开发人员创建新应用程序注册时，他们将自动添加为第一个所有者。

## <a name="delegate-app-ownership"></a>委托应用所有权

若要进行更精细的应用访问权限委托，可将所有权分配到单个企业应用程序。 这是对分配应用程序注册所有者的现有支持的补充。 所有权在“企业应用程序”边栏选项卡中根据每个企业应用程序进行分配。 优点是所有者只能管理他们拥有的企业应用程序。 例如，可以分配 Salesforce 应用程序的所有者，该所有者可以管理 Salesforce 的访问权限和配置，但不能管理其他任何应用程序的访问权限和配置。 一个企业应用程序可以有多个所有者，一个用户可以是许多企业应用程序的所有者。 有两种应用所有者角色：

* “企业应用程序所有者”角色授予管理用户拥有的企业应用程序的能力，包括单一登录设置、用户和组分配，以及添加其他所有者。**** 它不会授予管理应用程序代理设置或条件性访问的能力。
* “应用程序注册所有者”角色授予管理用户拥有的应用的应用程序注册的能力，包括应用程序清单和添加其他所有者。****

## <a name="develop-a-security-plan"></a>制定安全计划

Azure AD 提供了一篇综合性的指南来帮助你规划和执行 Azure AD 管理员角色的安全计划：[保护混合和云部署的特权访问](directory-admin-roles-secure.md)。

## <a name="establish-emergency-accounts"></a>建立紧急帐户

若要在出现问题时保持对标识管理存储的访问权限，请按照[创建紧急访问管理帐户](directory-emergency-access.md)中的说明准备紧急访问帐户。

## <a name="secure-your-administrator-roles"></a>保护管理员角色

控制特权帐户的攻击者可能会面临巨大的损害，因此，请先使用默认情况下提供给所有 Azure AD 组织（在公共预览版中）的[基线访问策略](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/)来保护这些帐户。 该策略针对 Azure AD 特权帐户强制实施多重身份验证。 Azure AD 基线策略涵盖以下 Azure AD 角色：

* 全局管理员
* SharePoint 管理员
* Exchange 管理员
* 条件访问管理员
* 安全管理员

## <a name="elevate-privilege-temporarily"></a>暂时提升特权

对于大多数日常活动，并非所有用户都需要全局管理员权限，也不应将所有用户永久分配到全局管理员角色。 当用户需要全局管理员权限时，应使用其自己的帐户或备用管理帐户在 Azure AD [Privileged Identity Management](../active-directory-privileged-identity-management-configure.md) 中激活角色分配。

## <a name="next-steps"></a>后续步骤

有关 Azure AD 角色说明的参考，请参阅[在 Azure AD 中分配管理员角色](directory-assign-admin-roles.md)
