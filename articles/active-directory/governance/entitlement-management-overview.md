---
title: Azure AD 权利管理是什么？ -Azure Active Directory
description: 获取 Azure Active Directory 权利管理的概述，以及如何使用它来管理内部与外部用户对组、应用程序和 SharePoint Online 站点的访问权限。
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
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd72bba6e8c01644adebaa333c8fe588c35b8c7
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927318"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Azure AD 权利管理是什么？

Azure Active Directory （Azure AD）权利管理是一项[标识监管](identity-governance-overview.md)功能，使组织能够通过自动执行访问请求工作流、访问分配、审核和过期来大规模管理标识和访问生命周期。

组织中的员工在履行其职责时需要访问各种组、应用程序和站点。 管理此访问具有挑战性，因为需要更改-添加新应用程序或用户需要额外的访问权限。  当你与外部组织协作时，此方案更复杂-你可能不知道另一组织中的谁需要访问你组织的资源，并且他们不知道你的组织正在使用哪些应用程序、组或站点。

Azure AD 权限管理可帮助您更有效地管理对内部用户的组、应用程序和 SharePoint Online 站点的访问权限，并且还可以帮助您组织外部的用户需要访问这些资源。

## <a name="why-use-entitlement-management"></a>为何使用权利管理？

管理员工对资源（例如）的访问权限时，企业组织经常面临挑战：

- 用户可能不知道他们应该具有哪些访问权限，即使他们这样做，他们也可能难以找到合适的个人来批准他们的访问权限
- 一旦用户确定并获得对资源的访问权限，他们可能会一直拥有这种访问权限，以致超过了业务需求

对于需要来自其他组织的访问权限的用户（如供应链组织或其他业务合作伙伴的外部用户），这些问题会变得更加复杂。 例如：

- 任何人都不能知道其他组织的目录中的所有特定人员可以邀请他们
- 即使他们能够邀请这些用户，在该组织中的任何人都不能记住管理所有用户的访问权限

Azure AD 权利管理可帮助解决这些难题。  若要详细了解客户如何使用 Azure AD 的权利管理，你可以阅读[Avanade 案例研究](https://aka.ms/AvanadeELMCase)和[Centrica 案例研究](https://aka.ms/CentricaELMCase)。  此视频概述了权利管理及其价值：

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>权利管理有哪些作用？

下面是权利管理的一些功能：

- 委托给非管理员可以创建访问包。 这些访问包包含用户可以请求的资源，并且委派的访问包管理器可以使用用户可以请求的规则定义策略、谁必须批准其访问权限以及访问到期时间。
- 选择用户可请求访问的已连接组织。  当尚未在你的目录中的用户请求访问并获得批准后，将自动邀请你的目录并为其分配访问权限。  当用户的访问权限过期时，如果它们没有其他访问包分配，则可以自动删除其目录中的 B2B 帐户。

你可以开始使用我们的[教程来创建你的第一个访问包](entitlement-management-access-package-first.md)。 你还可以阅读[常见方案](entitlement-management-scenarios.md)或观看视频，包括

- [如何在组织中部署 Azure AD 的权利管理](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [如何监视和扩展你使用 Azure AD 的权利管理](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [如何在权利管理中委派](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>什么是访问包，哪些资源可使用它们进行管理？

权利管理引入 Azure AD*访问包*的概念。 访问包是所有资源的捆绑，其中包含用户在项目上工作或执行其任务所需的访问权限。 访问包用于监管内部员工以及组织外部用户的访问权限。

 以下是可以使用授权管理管理用户对的访问权限的资源类型：

- Azure AD 安全组的成员身份
- Office 365 组和团队的成员身份
- 分配到 Azure AD 企业应用程序，包括 SaaS 应用程序和支持联合/单一登录和/或预配的自定义集成应用程序
- SharePoint Online 网站的成员身份

你还可以控制对依赖于 Azure AD 安全组或 Office 365 组的其他资源的访问权限。  例如：

- 可以通过在访问包中使用 Azure AD 安全组并为该组配置[基于组的许可](../users-groups-roles/licensing-groups-assign.md)，来为用户提供 Microsoft Office 365 许可证。
- 可以通过在访问包中使用 Azure AD 安全组并为该组创建 [Azure 角色分配](../../role-based-access-control/role-assignments-portal.md)，来为用户提供管理 Azure 资源的访问权限

## <a name="how-do-i-control-who-gets-access"></a>如何实现控制谁可以访问？

使用访问包时，管理员或委托访问包管理器会列出资源（组、应用和站点）以及用户需要用于这些资源的角色。

访问包还包含一个或多个策略。 策略定义分配给 access 包的规则或 guardrails。 每个策略都可用于确保只有适当的用户才能请求访问权限、是否有请求的审批者，以及他们对这些资源的访问权限是受时间限制的，如果不续订，将会过期。

![访问包和策略](./media/entitlement-management-overview/elm-overview-access-package.png)

在每个策略中，管理员或访问包管理器定义

- 已有的用户（通常为员工或已邀请的来宾）或外部用户的合作伙伴组织，可以请求访问
- 审批流程以及可批准或拒绝访问的用户
- 用户的访问权限分配在分配过期之前经过批准后的持续时间

下图显示了权利管理中不同要素的示例。 其中显示了一个具有两个示例访问包的目录。

- “访问包 1”包含单个组作为资源。 访问权限是使用一个策略定义的，该策略允许目录中的一组用户请求访问权限。
- “访问包 2”包含组、应用程序和 SharePoint Online 站点作为资源。 访问权限是使用两个不同的策略定义的。 第一个策略允许目录中的一组用户请求访问权限。 第二个策略允许外部目录中的用户请求访问权限。

![权利管理概述](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>何时应使用访问包？

访问包不会替换其他用于访问分配的机制。  它们最适用于以下情况：

- 员工对于特定任务需要有时间限制的访问权限。  例如，你可以使用基于组的许可和动态组来确保所有员工都有 Exchange Online 邮箱，然后在员工需要其他访问权限的情况下使用访问包，如从另一个部门.
- 访问权限需要由员工的经理或其他指定的个人批准。
- 部门希望为其资源管理自己的访问策略，而无需 IT 人员的参与。  
- 两个或多个组织正在对项目进行协作，因此，一个组织中的多个用户需要通过 Azure AD B2B 来访问其他组织的资源。

## <a name="how-do-i-delegate-access"></a>如何实现委派访问权限？

 在名为 *catalogs* 的容器中定义访问包。  您可以为所有访问包使用单个目录，也可以指定个人创建和拥有其自己的目录。 管理员可以将资源添加到任何目录中，但非管理员只能将他们拥有的资源添加到目录中。 目录所有者可以将其他用户添加为目录共同所有者，或添加为访问包管理器。  本文中进一步介绍了这些方案[，详见 Azure AD 授权管理中的委派和角色](entitlement-management-delegate.md)。

## <a name="summary-of-terminology"></a>术语摘要

为了更好地了解权限管理及其文档，你可以参考以下术语列表。

| 术语 | 说明 |
| --- | --- |
| 访问包 | 团队或项目需要并受策略管辖的资源的捆绑。 访问包始终包含在目录中。 在用户需要请求访问权限的情况下，你将创建新的访问包。  |
| 访问请求 | 访问访问包中的资源的请求。 请求通常通过审批工作流。  如果获得批准，请求用户将收到访问包分配。 |
| #a1 | 向用户分配访问包可确保用户具有该访问包的所有资源角色。  访问包分配在过期之前通常会有时间限制。 |
| 目录 | 相关资源和访问包的容器。  目录用于委派，使非管理员可以创建自己的访问包。 目录所有者可以将他们拥有的资源添加到目录中。 |
| 目录创建者 | 有权创建新目录的用户的集合。  当有权成为目录创建者的非管理员用户创建新目录时，它们将自动成为该目录的所有者。 |
| 连接的组织 | 与之有关系的外部 Azure AD 目录或域。 连接的组织中的用户可以在策略中指定为请求访问。 |
| policy | 定义访问生命周期的一组规则，例如用户获取访问权限的方式、可以批准的用户以及用户通过分配进行访问的时间长短。 策略已链接到访问包。 例如，访问包可以有两个策略，一个用于员工请求访问，另一个用于请求访问。 |
| resource | 一种资产，例如办公室组、安全组、应用程序或 SharePoint Online 站点，以及用户可以被授予权限的角色。 |
| 资源目录 | 包含一个或多个可共享的资源的目录。 |
| 资源角色 | 与资源相关联并由资源定义的权限的集合。 组具有两个角色：成员和所有者。 SharePoint 站点通常具有3个角色，但可能具有其他自定义角色。 应用程序可以具有自定义角色。 |


## <a name="license-requirements"></a>许可要求

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Azure 政府、Azure 德国和 Azure 中国世纪互联的专用云当前不可用。

### <a name="which-users-must-have-licenses"></a>哪些用户必须有许可证？

你的租户必须具有至少 Azure AD Premium P2 许可证，因为你的成员用户在权利管理中处于活动状态。 权利管理中的活动成员用户包括：

- 发起或批准访问包请求的用户。
- 已分配有访问包的用户。
- 管理访问包的用户。

作为成员用户许可证的一部分，你还可以允许多个来宾用户与授权管理交互。 有关如何计算可包含的来宾用户数的信息，请参阅[AZURE ACTIVE DIRECTORY B2B 协作许可指南](../b2b/licensing-guidance.md)。

有关如何将许可证分配给用户的信息，请参阅[使用 Azure Active Directory 门户分配或删除许可证](../fundamentals/license-users-groups.md)。 请注意，"权利管理" 当前不会为用户强制实施许可证分配。

## <a name="next-steps"></a>后续步骤

- [教程：创建第一个访问包](entitlement-management-access-package-first.md)
- [常见方案](entitlement-management-scenarios.md)
