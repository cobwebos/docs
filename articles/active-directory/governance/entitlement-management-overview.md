---
title: Azure AD 权利管理是什么？ （预览版）- Azure Active Directory
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
ms.date: 09/03/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c99ee1987b592a2e8314d529b118fb26945271e
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241700"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Azure AD 权利管理是什么？ （预览版）

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

组织中的员工在履行其职责时需要访问各种组、应用程序和站点。 管理这种访问颇有难度。 在大多数情况下，用户需要在项目中用到的所有资源并没有一个组织有序的列表。 项目经理非常了解所需的资源、所涉及的人员以及项目持续时间。 但是，项目经理通常无权向他人批准或授予访问权限。 尝试与外部的个人或公司协作时，这种情况会变得更加复杂。

Azure Active Directory (Azure AD) 权利管理可帮助你管理组织内部与外部用户对组、应用程序和 SharePoint Online 站点的访问。

此视频概述了权利管理及其业务价值：

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="why-use-entitlement-management"></a>为何使用权利管理？

企业组织在管理对资源的访问时往往会遇到难题，例如：

- 用户可能不知道他们应该拥有哪些访问权限
- 用户可能难以找到适当的个人或适当的资源
- 一旦用户确定并获得对资源的访问权限，他们可能会一直拥有这种访问权限，以致超过了业务需求

对于需要从其他目录进行访问的用户（例如，来自供应链组织的外部用户，或其他业务合作伙伴），这些问题更加复杂。 例如：

- 组织可能不知道其他目录中的所有具体人员，以致无法邀请他们
- 即使组织能够邀请这些用户，也可能不记得以一致的方式管理用户的所有访问权限

Azure AD 权利管理可帮助解决这些难题。

## <a name="what-can-i-do-with-entitlement-management"></a>权利管理有哪些作用？

下面是权利管理的一些功能：

- 创建可供用户请求的相关资源包
- 定义有关如何请求资源以及访问权限何时过期的规则
- 控制内部和外部用户的访问权限生命周期
- 委托资源管理权
- 指定审批者来审批请求
- 创建用于跟踪历史记录的报告

有关标识管理和授权管理的概述，请观看 Ignite 2018 会议中的以下视频：

>[!VIDEO https://www.youtube.com/embed/aY7A0Br8u5M]

## <a name="what-resources-can-i-manage"></a>可以管理哪些资源？

下面是可以使用权利管理来管理其访问权限的资源类型：

- Azure AD 安全组
- Office 365 组
- Azure AD 企业应用程序，包括支持联合或预配的 SaaS 应用程序和自定义集成应用程序
- SharePoint Online 站点集合与站点

还可以控制对依赖于 Azure AD 安全组或 Office 365 组的其他资源的访问权限。  例如：

- 可以通过在访问包中使用 Azure AD 安全组并为该组配置[基于组的许可](../users-groups-roles/licensing-groups-assign.md)，来为用户提供 Microsoft Office 365 许可证。
- 可以通过在访问包中使用 Azure AD 安全组并为该组创建 [Azure 角色分配](../../role-based-access-control/role-assignments-portal.md)，来为用户提供管理 Azure 资源的访问权限

## <a name="what-are-access-packages-and-policies"></a>什么是访问包和策略？

权利管理引入了“访问包”的概念。 访问包是用户在处理项目或履行其职责时所需的所有资源的捆绑包。 这些资源包括对组、应用程序或站点的访问权限。 访问包用于监管内部员工以及组织外部用户的访问权限。 在名为 *catalogs* 的容器中定义访问包。

访问包还包含一个或多个策略。 策略定义有关访问某个访问包的规则或准则。 启用策略可以强制要求只在适当的时间内，为适当的用户授予对适当资源的访问权限。

![访问包和策略](./media/entitlement-management-overview/elm-overview-access-package.png)

访问包管理者可以使用访问包及其策略来定义：

- 资源
- 用户需要对资源拥有的角色
- 有资格请求访问权限的内部用户和外部用户的合作伙伴组织
- 审批过程，以及可以批准或拒绝访问权限的用户
- 用户访问权限的持续时间

下图显示了权利管理中不同要素的示例。 其中显示了两个示例访问包。

- “访问包 1”包含单个组作为资源。 访问权限是使用一个策略定义的，该策略允许目录中的一组用户请求访问权限。
- “访问包 2”包含组、应用程序和 SharePoint Online 站点作为资源。 访问权限是使用两个不同的策略定义的。 第一个策略允许目录中的一组用户请求访问权限。 第二个策略允许外部目录中的用户请求访问权限。

![权利管理概述](./media/entitlement-management-overview/elm-overview.png)

## <a name="external-users"></a>外部用户

使用[Azure AD 企业到企业（B2B）](../b2b/what-is-b2b.md)邀请体验时，必须知道要引入资源目录并使用的外部来宾用户的电子邮件地址。 当您处理的项目较小或短期，而且您已经了解了所有参与者时，这非常有用，但如果您有很多用户要使用，或者参与者随着时间推移而发生变化，则很难管理这种情况。  例如，你可能与其他组织合作，与该组织有一个联系点，但随着时间的推移，该组织中的其他用户也需要访问权限。

使用权限管理，你可以定义一个策略，该策略允许你指定的组织中的用户（也使用 Azure AD）能够请求访问包。 你可以指定是否需要批准和访问的到期日期。 如果需要审批，你还可以将之前邀请的外部组织中的一个或多个用户指定为审批者，因为他们可能知道组织中的哪些外部用户需要访问权限。 配置访问包后，可以将访问包的链接发送给外部组织的联系人。 该联系人可以与外部组织中的其他用户共享，他们可以使用此链接请求访问包。  该组织中已邀请到你的目录的用户也可以使用该链接。

批准请求后，授权管理会为用户预配必要的访问权限，如果用户尚未加入你的目录，则可能包括邀请用户。 Azure AD 会自动为他们创建 B2B 帐户。  请注意，管理员可能已通过设置[B2B 允许或拒绝列表](../b2b/allow-deny-list.md)来允许或阻止其他组织的邀请，因而限制了允许协作的组织。  如果允许或阻止列表不允许用户，则不会邀请用户。

由于你不希望外部用户访问最后一次，因此请在策略中指定一个过期日期，如180天。 180天后，如果未续订其访问权限，则权限管理将删除与该访问包关联的所有访问权限。  如果通过授权管理的用户不具有其他访问包分配，则当他们失去最后一次分配时，将阻止其 B2B 帐户登录30天，并随后将其删除。  这可以防止不必要帐户的增加。  

## <a name="terminology"></a>术语

为了更好地理解权利管理及其文档，应查看以下术语。

| 术语或概念 | 描述 |
| --- | --- |
| 权利管理 | 一个可以分配、撤消和管理访问包的服务。 |
| 访问包 | 用户可以请求的资源权限和策略集合。 访问包始终包含在目录中。 |
| 访问请求 | 请求访问访问包。 请求通常会经历某个工作流。 |
| 策略 | 一组规则，定义访问权限的生命周期，例如，用户如何获取访问权限、谁可以审批请求，以及用户可以拥有访问权限多长时间。 示例策略包括员工访问权限和外部访问权限。 |
| catalog | 相关资源和访问包的容器。 |
| 常规目录 | 始终可用的内置目录。 若要将资源添加到常规目录，需要特定的权限。 |
| 资源 | 用户可以被授予对其的权限的资产或服务（如 Office 组、安全组、应用程序或 SharePoint Online 站点）。 |
| 资源类型 | 资源的类型，包括组、应用程序和 SharePoint Online 站点。 |
| 资源角色 | 与资源关联的权限集合。 |
| 资源目录 | 包含一个或多个可共享的资源的目录。 |
| 分配的用户 | 将访问包分配给用户，以便用户具有该访问包的所有资源角色。 |
| 启用 | 使用户能够请求某个访问包的过程。 |

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
