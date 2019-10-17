---
title: Azure AD 权利管理是什么？ （预览）-Azure Active Directory
description: 大致了解 Azure Active Directory 的权利管理，以及如何使用它来管理内部和外部用户对组、应用程序和 SharePoint Online 站点的访问权限。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/03/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a467856550bf2deaab931b3fe2f54b7986f12f8a
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430294"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Azure AD 权利管理是什么？ （预览）

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

组织中的员工需要访问各种组、应用程序和站点才能执行其作业。 管理此访问具有挑战性。 在大多数情况下，没有用户需要的项目的所有资源的列表。 项目经理非常了解所需的资源、所涉及的人员以及项目的持续时间。 但是，项目经理通常无权批准或向他人授予访问权限。 当你尝试使用外部个人或公司时，此方案会变得更加复杂。

Azure Active Directory （Azure AD）权利管理可以帮助您管理对组、应用程序和 SharePoint Online 站点的访问权限，以供内部用户和组织外部的用户使用。

此视频概述了权利管理及其业务价值：

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="why-use-entitlement-management"></a>为什么要使用权利管理？

在管理对资源的访问时，企业组织经常面临挑战，例如：

- 用户可能不知道应该使用哪些访问权限
- 用户可能很难找到正确的个人或正确的资源
- 一旦用户发现并收到对资源的访问权限，他们可能会保留访问权限，而不是商业用途所需的时间

对于需要从其他目录（如供应链组织或其他业务合作伙伴的外部用户）进行访问的用户，这些问题会更加复杂。 例如：

- 组织可能不知道其他目录中的所有特定人员能够邀请他们
- 即使组织能够邀请这些用户，组织也可能不记得一致地管理用户的所有访问

Azure AD 的权利管理可以帮助解决这些难题。

## <a name="what-can-i-do-with-entitlement-management"></a>可以通过权利管理进行哪些操作？

以下是授权管理的一些功能：

- 创建用户可以请求的相关资源包
- 定义有关如何请求资源以及访问何时过期的规则
- 为内部和外部用户控制访问生命周期
- 委托资源管理
- 指定审批者批准请求
- 创建报表以跟踪历史记录

有关标识管理和授权管理的概述，请观看 Ignite 2018 会议中的以下视频：

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>可以管理哪些资源？

以下是可以使用授权管理来管理访问权限的资源类型：

- Azure AD 安全组
- Office 365 组
- Azure AD 企业应用程序，包括 SaaS 应用程序和支持联合或预配的自定义集成应用程序
- SharePoint Online 网站集和网站

你还可以控制对依赖于 Azure AD 安全组或 Office 365 组的其他资源的访问权限。  例如：

- 你可以通过在访问包中使用 Azure AD 安全组，并为该组配置[基于组的许可](../users-groups-roles/licensing-groups-assign.md)，为用户提供 Microsoft Office 365 的许可证。
- 可以通过使用访问包中的 Azure AD 安全组并为该组创建[azure 角色分配](../../role-based-access-control/role-assignments-portal.md)，为用户提供管理 azure 资源的访问权限

## <a name="what-are-access-packages-and-policies"></a>什么是访问包和策略？

权利管理引入了*访问包*的概念。 访问包是用户在项目上工作或执行其作业所需的所有资源的捆绑。 这些资源包括对组、应用程序或站点的访问权限。 访问包用于为内部员工以及组织外部的用户管理访问权限。 在名为 *catalogs* 的容器中定义访问包。

访问包还包括一个或多个*策略*。 策略定义用于访问访问包的规则或 guardrails。 启用策略会强制仅向适当的用户授予访问权限、正确的资源以及适当的时间量。

![访问包和策略](./media/entitlement-management-overview/elm-overview-access-package.png)

使用访问包及其策略，访问包管理器定义：

- 资源
- 用户需要的资源角色
- 可请求访问的外部用户的内部用户和合作伙伴组织
- 审批流程以及可批准或拒绝访问的用户
- 用户访问的持续时间

下图显示了授权管理中不同元素的示例。 其中显示了两个示例访问包。

- **Access package 1**包含单个组作为资源。 访问权限是通过允许目录中的一组用户请求访问权限来定义的。
- **Access package 2**将组、应用程序和 SharePoint Online 站点包含为资源。 使用两个不同的策略定义了访问权限。 第一个策略允许目录中的一组用户请求访问。 第二个策略使外部目录中的用户可以请求访问。

![权利管理概述](./media/entitlement-management-overview/elm-overview.png)

## <a name="terminology"></a>术语

为了更好地了解权限管理及其文档，你应该查看以下术语。

| 术语或概念 | 描述 |
| --- | --- |
| 权利管理 | 分配、撤消和管理访问包的服务。 |
| 访问包 | 团队或项目需要并受策略管辖的资源的捆绑。 访问包始终包含在目录中。 |
| 访问请求 | 访问访问包中的资源的请求。 请求通常通过工作流。 |
| policy | 定义访问生命周期的一组规则，如用户如何获取访问权限、谁可以批准以及用户有权访问的时间长短。 示例策略包括员工访问权限和外部访问权限。 |
| 目录 | 相关资源和访问包的容器。 |
| 常规目录 | 始终可用的内置目录。 若要将资源添加到一般目录，需要特定权限。 |
| resource | 用户可以向其授予权限的资产或服务（如 Office 组、安全组、应用程序或 SharePoint Online 网站）。 |
| 资源类型 | 资源的类型，包括组、应用程序和 SharePoint Online 站点。 |
| 资源角色 | 与资源关联的权限的集合。 |
| 资源目录 | 具有一个或多个要共享的资源的目录。 |
| 分配的用户 | 向用户分配访问包，以便用户拥有该访问包的所有资源角色。 |
| enable | 使访问包可供用户请求的过程。 |

## <a name="license-requirements"></a>许可要求

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Azure 政府、Azure 德国和 Azure 中国世纪互联的专用云当前不可用于此预览版。

### <a name="which-users-must-have-licenses"></a>哪些用户必须有许可证？

你的租户必须具有至少与活动成员用户数量相同的 Azure AD Premium P2 许可证。 授权管理中的活动成员用户包括：

- 发起或批准访问包请求的用户。
- 已分配有访问包的用户。 
- 管理访问包的用户。

作为成员用户许可证的一部分，你还可以允许多个来宾用户与授权管理交互。 有关如何计算可包含的来宾用户数的信息，请参阅[AZURE ACTIVE DIRECTORY B2B 协作许可指南](../b2b/licensing-guidance.md)。

有关如何向用户分配许可证的信息，请参阅[使用 Azure Active Directory 门户分配或删除许可证](../fundamentals/license-users-groups.md)。

## <a name="next-steps"></a>后续步骤

- [教程：创建第一个访问包](entitlement-management-access-package-first.md)
- [常见方案](entitlement-management-scenarios.md)
