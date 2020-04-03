---
title: 权利管理是什么？ - Azure AD
description: 获取 Azure Active Directory 权利管理的概述，以及如何使用它来管理内部与外部用户对组、应用程序和 SharePoint Online 站点的访问权限。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65100e8584eba5c31edc2f9fd8c57ad8bd14c0d1
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582546"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Azure AD 权利管理是什么？

Azure 活动目录 （Azure AD） 授权管理是一种[标识治理](identity-governance-overview.md)功能，通过自动访问请求工作流、访问分配、审核和过期，使组织能够大规模管理标识和访问生命周期。

组织中的员工在履行其职责时需要访问各种组、应用程序和站点。 随着需求的变化，管理此访问具有挑战性 - 添加新应用程序或用户需要额外的访问权限。  当您与外部组织协作时，此方案变得更加复杂 - 您可能不知道其他组织中谁需要访问组织的资源，并且他们不知道您的组织正在使用哪些应用程序、组或网站。

Azure AD 授权管理可帮助您更有效地管理对内部用户以及组织外部需要访问这些资源的用户的组、应用程序和 SharePoint Online 网站的访问。

## <a name="why-use-entitlement-management"></a>为何使用权利管理？

企业组织在管理员工访问资源时经常面临挑战，例如：

- 用户可能不知道他们应拥有哪些访问权限，即使他们拥有访问权限，也可能难以找到合适的个人来批准其访问权限
- 一旦用户确定并获得对资源的访问权限，他们可能会一直拥有这种访问权限，以致超过了业务需求

对于需要从其他组织进行访问的用户（例如来自供应链组织或其他业务合作伙伴的外部用户）而言，这些问题会加剧。 例如：

- 任何人都不得知道其他组织目录中的所有特定个人能够邀请他们
- 即使他们能够邀请这些用户，该组织中任何人都不会记得始终如一地管理用户的所有访问权限

Azure AD 权利管理可帮助解决这些难题。  要详细了解客户如何使用 Azure AD 授权管理，请阅读[埃维诺案例研究](https://customers.microsoft.com/story/avanade-professional-services-azure-canada)和[Centrica 案例研究](https://customers.microsoft.com/story/757467-centrica-energy-azure)。  本视频概述了授权管理及其价值：

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>权利管理有哪些作用？

下面是权利管理的一些功能：

- 将创建访问包的能力委派给非管理员。 这些访问包包含用户可以请求的资源，委派的访问包管理员可以使用用户可以请求的规则定义策略，用户必须批准其访问，以及访问何时过期。
- 选择用户可以请求访问的已连接组织。  当尚未在目录中的用户请求访问并获得批准时，将自动邀请他们进入您的目录并分配访问权限。  当他们的访问权限过期时，如果他们没有其他访问包分配，则可以自动删除目录中的 B2B 帐户。

你可以开始我们的[教程，创建你的第一个访问包](entitlement-management-access-package-first.md)。 您还可以阅读[常见方案](entitlement-management-scenarios.md)，或观看视频，包括

- [如何在组织中部署 Azure AD 授权管理](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [如何监视和扩展 Azure AD 授权管理的使用](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [如何在授权管理中委派](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>什么是访问包，可以使用这些资源管理哪些资源？

授权管理向 Azure AD 引入了*访问包*的概念。 访问包是用户处理项目或执行任务所需的所有资源的包。 访问包用于监管内部员工以及组织外部用户的访问权限。

 以下是您可以使用授权管理管理用户访问权限的资源类型：

- Azure AD 安全组的成员身份
- 办公室 365 个组和团队的成员身份
- 分配给 Azure AD 企业应用程序，包括支持联合/单一登录和/或预配的 SaaS 应用程序和自定义集成应用程序
- 共享点在线网站的成员身份

您还可以控制对依赖于 Azure AD 安全组或 Office 365 组的其他资源的访问。  例如：

- 可以通过在访问包中使用 Azure AD 安全组并为该组配置[基于组的许可](../users-groups-roles/licensing-groups-assign.md)，来为用户提供 Microsoft Office 365 许可证。
- 可以通过在访问包中使用 Azure AD 安全组并为该组创建 [Azure 角色分配](../../role-based-access-control/role-assignments-portal.md)，来为用户提供管理 Azure 资源的访问权限

## <a name="how-do-i-control-who-gets-access"></a>如何控制谁获得访问权限？

使用访问包，管理员或委派的访问包管理器列出资源（组、应用和网站）以及用户需要这些资源的角色。

访问包还包含一个或多个策略。** 策略定义用于分配访问包的规则或护栏。 每个策略都可用于确保只有适当的用户才能请求访问，有审批者为其请求，并且他们对这些资源的访问是有时间限制的，如果不续订，他们将过期。

![访问包和策略](./media/entitlement-management-overview/elm-overview-access-package.png)

在每个策略中，管理员或访问包管理器定义

- 已存在的用户（通常是员工或已邀请的来宾）或有资格请求访问权限的外部用户的合作伙伴组织
- 审批流程和可以批准或拒绝访问权限的用户
- 用户的访问分配的持续时间，一旦获得批准，在分配到期之前

下图显示了权利管理中不同要素的示例。 它显示一个目录，其中有两个示例访问包。

- “访问包 1”包含单个组作为资源。**** 访问权限是使用一个策略定义的，该策略允许目录中的一组用户请求访问权限。
- “访问包 2”包含组、应用程序和 SharePoint Online 站点作为资源。**** 访问权限是使用两个不同的策略定义的。 第一个策略允许目录中的一组用户请求访问权限。 第二个策略允许外部目录中的用户请求访问权限。

![权利管理概述](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>何时应该使用访问包？

访问包不会替换用于访问分配的其他机制。  它们最适合以下情况：

- 员工需要对特定任务进行限时访问。  例如，可以使用基于组的许可和动态组来确保所有员工都有 Exchange Online 邮箱，然后针对员工需要额外访问权限的情况（例如从其他部门读取部门资源）使用访问包。
- 访问权限需要获得员工经理或其他指定人员的批准。
- 各部门希望在没有 IT 参与的情况下管理自己的资源访问策略。  
- 两个或多个组织正在协作一个项目，因此，需要通过 Azure AD B2B 引入一个组织的多个用户来访问另一个组织的资源。

## <a name="how-do-i-delegate-access"></a>如何委派访问权限？

 在名为 *catalogs* 的容器中定义访问包。  您可以为所有访问包提供单个目录，也可以指定个人创建和拥有自己的目录。 管理员可以将资源添加到任何目录，但非管理员只能向目录中添加他们拥有的资源。 目录所有者可以将其他用户添加为目录共同所有者，也可以添加访问包管理器。  这些方案在[Azure AD 授权管理中的文章委派和角色中](entitlement-management-delegate.md)进一步描述。

## <a name="summary-of-terminology"></a>术语摘要

为了更好地理解授权管理及其文档，您可以参考以下术语列表。

| 术语 | 描述 |
| --- | --- |
| 访问包 | 团队或项目需要并受策略管理的一组资源。 访问包始终包含在目录中。 您将为用户需要请求访问的方案创建新的访问包。  |
| 访问请求 | 访问访问包中资源的请求。 请求通常通过审批工作流。  如果获得批准，请求用户将收到访问包分配。 |
| 分配 | 向用户分配访问包可确保用户具有该访问包的所有资源角色。  访问包分配通常有过期前的时间限制。 |
| 目录 | 相关资源和访问包的容器。  目录用于委派，以便非管理员可以创建自己的访问包。 目录所有者可以将他们拥有的资源添加到目录中。 |
| 目录创建者 | 授权创建新目录的用户的集合。  当被授权为目录创建者的非管理员用户创建新目录时，他们将自动成为该目录的所有者。 |
| 互联组织 | 与具有关系的外部 Azure AD 目录或域。 在策略中可以指定来自已连接组织的用户请求访问。 |
| policy | 定义访问生命周期的一组规则，例如用户如何获取访问权限、谁可以批准以及用户通过分配访问的时间。 策略链接到访问包。 例如，访问包可以有两个策略 - 一个策略供员工请求访问，另一个策略用于外部用户请求访问。 |
| resource | 具有用户可以授予权限的资产，如 Office 组、安全组、应用程序或 SharePoint Online 网站。 |
| 资源目录 | 包含一个或多个可共享的资源的目录。 |
| 资源角色 | 与资源关联并由资源定义的权限的集合。 组有两个角色 - 成员和所有者。 SharePoint 网站通常有 3 个角色，但可能具有其他自定义角色。 应用程序可以具有自定义角色。 |


## <a name="license-requirements"></a>许可要求

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

专用云（如 Azure 德国和 Azure 中国 21Vianet）当前不可用。

### <a name="how-many-licenses-must-you-have"></a>必须拥有多少个许可证？

确保目录至少具有与具有的 Azure AD 高级 P2 许可证数量一样：

- **可以**请求访问包的成员用户。
- 请求访问包的成员和来宾用户。
- 批准访问包请求的成员和来宾用户。
- 直接分配访问包的成员和来宾用户。

对于以下任务，不需要**** 使用 Azure AD Premium P2 许可证：

- 具有全局管理员角色的用户无需许可证，这些用户设置了初始目录、访问包和策略，并将管理任务委派给其他用户。
- 已委派管理任务的用户（如目录创建者、目录所有者和访问包管理器）不需要许可证。
- **可以**请求访问包**但不请求访问**包的客人无需许可证。

对于为会员用户（员工）购买的每个付费 Azure AD 高级 P2 许可证，可以使用 Azure AD B2B 邀请最多 5 个来宾用户。 这些来宾用户也可以使用 Azure AD Premium P2 功能。 有关详细信息，请参阅[Azure AD B2B 协作许可指南](../b2b/licensing-guidance.md)。

有关许可证的详细信息，请参阅[使用 Azure Active Directory 门户分配或删除许可证](../fundamentals/license-users-groups.md)。

### <a name="example-license-scenarios"></a>示例许可证方案

下面是一些示例许可证方案，可帮助你确定必须拥有的许可证数量。

| 方案 | 计算 | 许可证数量 |
| --- | --- | --- |
| 伍德格罗夫银行的全球管理员创建初始目录，并将管理任务委派给 6 个其他用户。 其中一个策略指定**所有员工**（2，000 名员工）可以请求一组特定的访问包。 150 名员工请求访问包。 | 2，000 名员工**可以**请求访问包 | 2,000 |
| 伍德格罗夫银行的全球管理员创建初始目录，并将管理任务委派给 6 个其他用户。 其中一个策略指定**所有员工**（2，000 名员工）可以请求一组特定的访问包。 另一个策略指定来自**合作伙伴 Contoso（** 来宾）的用户的某些用户可以请求相同的访问包，但须经批准。 Contoso 有 30，000 个用户。 150 名员工请求访问包，Contoso 的 10，500 名用户请求访问。 | 2，000 名员工 + 来自 Contoso 的 500 名来宾用户，比例超过 1：5（10，500 - （2，000 + 5）） | 2,500 |

## <a name="next-steps"></a>后续步骤

- [教程：创建第一个访问包](entitlement-management-access-package-first.md)
- [常见方案](entitlement-management-scenarios.md)
