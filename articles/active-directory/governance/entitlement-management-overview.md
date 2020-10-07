---
title: 权利管理是什么？ - Azure AD
description: 大致了解 Azure Active Directory 权利管理，以及如何使用它来管理内部和外部用户对组、应用程序和 SharePoint Online 站点的访问权限。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 09/30/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.custom: contperfq1
ms.openlocfilehash: f8bf2a1bb4892637ac504d5920754fc412691a4a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91597374"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Azure AD 权利管理是什么？

Azure Active Directory (Azure AD) 权利管理是一种[标识治理](identity-governance-overview.md)功能，通过自动执行访问请求工作流、访问分配、审核和过期，使组织能够大规模管理标识和访问生命周期。

组织中的员工需要访问各种组、应用程序和站点以执行其作业。 由于要求发生了更改（添加了新应用程序或用户需要额外的访问权限），因此管理此访问具有难度。  在与外部组织协作时，此场景会更为复杂 - 你可能不知道另一组织中的哪些人需要访问你的组织的资源，他们可能也不知道你的组织正在使用哪些应用程序、组或站点。

Azure AD 权利管理可帮助你更加高效地管理内部用户及需要访问这些资源的组织外部的用户对组、应用程序和 SharePoint Online 站点的访问权限。

## <a name="why-use-entitlement-management"></a>为什么要使用权利管理？

企业组织在管理员工对资源的访问权限时经常会遇到困难，比如：

- 用户可能不知道他们应该具有哪些访问权限，即使知道，他们也可能很难找到相应的人员来审批他们的访问权限
- 用户设法获得对某一资源的所需访问权限后，他们可能会将该访问权限保留很长一段时间，从而超出业务目的所需的时长

对于需要从其他组织进行访问的用户（例如来自供应链组织或其他业务合作伙伴的外部用户）而言，问题会变得更加复杂。 例如：

- 没有人能够知晓其他组织目录中的所有特定个人，因此有可能无法邀请到他们
- 即使他们能够邀请这些用户，但该组织中可能没有人会记得以一致地方式管理用户的全部访问权限

Azure AD 权利管理可以帮助解决这些难题。  若要了解有关客户如何使用 Azure AD 权利管理的详细信息，可以参阅 [Avanade 案例研究](https://customers.microsoft.com/story/avanade-professional-services-azure-canada)和 [Centrica 案例研究](https://customers.microsoft.com/story/757467-centrica-energy-azure)。  此视频概述了权利管理及其价值：

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>可以使用权利管理做什么？

以下是权利管理的一些功能：

- 将创建访问包的功能委托给非管理员。 这些访问包包含用户可以请求的资源，并且接受委托的访问包管理员可以使用用户可以请求的规则来定义策略、谁必须审批其访问权限以及访问权限到期时间。
- 选择哪些已连接的组织的用户可以请求访问权限。  当一个不属于你目录的用户请求了访问权限并获得批准后，系统将自动邀请他们进入你的目录并为其分配访问权限。  当他们的访问权限到期时，如果他们没有收到其他访问包分配，可以自动删除他们在你的目录中的 B2B 帐户。

>[!NOTE]
>如果你已准备好试用权利管理，可以从[关于创建你的第一个访问包的教程](entitlement-management-access-package-first.md)开始。

还可以阅读[常见方案](entitlement-management-scenarios.md)或观看视频，包括

- [如何在组织中部署 Azure AD 权利管理](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [如何监视和缩放 Azure AD 权利管理的使用](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [如何在权利管理中委托](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>什么是访问包，可使用它们管理哪些资源？

权利管理向 Azure AD 引入了“访问包”概念。 访问包是包含用户在处理项目或执行其任务时所需的访问权限的所有资源的捆绑包。 访问包用于管理内部员工以及组织外部用户的访问权限。

 以下是可以使用权利管理来管理用户对其访问权限的资源类型：

- Azure AD 安全组的成员身份
- Microsoft 365 组和团队的成员身份
- 分配到 Azure AD 企业应用程序的内容，包括 SaaS 应用程序和支持联合/单一登录和/或预配的自定义集成应用程序
- SharePoint Online 站点的成员身份

你还可以控制对依赖于 Azure AD 安全组或 Microsoft 365 组的其他资源的访问权限。  例如：

- 可以通过在访问包中使用 Azure AD 安全组，并为该组配置[基于组的许可](../users-groups-roles/licensing-groups-assign.md)，为用户提供 Microsoft 365 许可证。
- 可以通过在访问包中使用 Azure AD 安全组，并为该组创建 [Azure 角色分配](../../role-based-access-control/role-assignments-portal.md)，为用户提供管理 Azure 资源的权限。
- 可以使用可分配到访问包中 Azure AD 角色的组并向[该组分配 Azure AD 角色](../users-groups-roles/roles-groups-assign-role.md)，从而授予用户管理 Azure AD 角色的访问权限。

## <a name="how-do-i-control-who-gets-access"></a>如何控制谁获得访问权限？

使用访问包时，管理员或受委托的访问包管理员会列出资源（组、应用和站点）以及用户需要用于这些资源的角色。

访问包还包括一个或多个策略。 策略定义有关访问包分配的规则或准则。 每个策略都可用于确保只有适当的用户才能请求访问权限、有审批者审批他们的请求，并且他们对这些资源的访问权限受时间限制（如果不续订，将会过期）。

![访问包和策略](./media/entitlement-management-overview/elm-overview-access-package.png)

在每个策略中，管理员或访问包管理员会定义

- 已有的用户（通常为员工或已邀请的来宾）或可以请求访问权限的来自合作伙伴组织的外部用户
- 审批流程以及可以批准或拒绝访问的用户
- 用户的访问权限分配在获得批准后、分配到期之前的持续时间

下图显示了权利管理中不同元素的示例。 其中显示了一个包含两个示例访问包的目录。

- 访问包 1 中只有一个组，并充当资源。 它通过一个策略定义访问权限，该策略允许目录中的一组用户请求访问权限。
- 访问包 2 包含组、应用程序和 SharePoint Online 站点作为资源。 它通过两个不同策略定义访问权限。 第一个策略允许目录中的一组用户请求访问权限。 第二个策略允许外部目录中的用户请求访问权限。

![权利管理概述](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>应在何时使用访问包？

访问包并不替代其他访问权限分配机制。  它们最适用于类似如下的情况：

- 员工需要用于特定任务的受时间限制的访问权限。  例如，你可以使用基于组的许可和动态组来确保所有员工都有 Exchange Online 邮箱，然后在员工需要其他访问权限的情况（例如从其他部门读取部门资源）下使用访问包。
- 需要员工经理或其他指定人员批准的访问。
- 部门希望自己管理自己的资源访问策略，不希望 IT 参与。  
- 两个或多个组织在一个项目上进行协作，因此，需要通过 Azure AD B2B 引入一个组织中的多个用户，使其能够访问其他组织的资源。

## <a name="how-do-i-delegate-access"></a>如何委托访问权限？

 在名为 *catalogs* 的容器中定义访问包。  可以为所有访问包使用单个目录，也可以指定某些个人来创建并拥有其自己的目录。 管理员可以将资源添加到任何目录，但非管理员只能将自己拥有的资源添加到目录。 目录所有者可以将其他用户添加为目录共同所有者或访问包管理员。  可通过 [Azure AD 权利管理中的委托和角色](entitlement-management-delegate.md)一文进一步了解这些场景。

## <a name="summary-of-terminology"></a>术语摘要

为了更好地理解权利管理及其文档，可以参考以下术语列表。

| 术语 | 说明 |
| --- | --- |
| 访问包 | 团队或项目所需的且受策略约束的资源的捆绑包。 访问包始终包含在目录中。 对于用户需要请求访问权限的场景，需要创建一个新的访问包。  |
| 访问请求 | 请求访问访问包中的资源的请求。 通常会为请求执行审批工作流。  如果获得批准，请求的用户将收到访问包分配。 |
| 分配 | 向用户分配访问包可确保该用户具有该访问包的所有资源角色。  访问包分配通常具有时间限制，也即会过期。 |
| 目录 | 相关资源和访问包的容器。  目录用于委托，以便非管理员可以创建自己的访问包。 目录所有者可以将其拥有的资源添加到目录。 |
| 目录创建者 | 有权创建新目录的用户的集合。  当已获授权成为目录创建者的非管理员用户创建新目录时，他们将自动成为该目录的所有者。 |
| 连接的组织 | 你与之有关联的外部 Azure AD 目录或域。 可以在策略中将连接的组织中的用户指定为有权请求访问权限。 |
| policy | 定义访问生命周期的一组规则，例如用户获取访问权限的方式、可以审批的人员以及用户通过分配具有的访问权限时长。 策略会链接到访问包。 例如，访问包可以有两个策略，一个用于员工请求访问权限，另一个用于外部用户请求访问权限。 |
| resource | 一种资产（例如 Office 组、安全组、应用程序或 SharePoint Online 站点），包含可向用户授予（相应角色权限）的角色。 |
| 资源目录 | 包含一个或多个可共享的资源的目录。 |
| 资源角色 | 与资源关联并由资源定义的权限的集合。 组具有两种角色 - 成员和所有者。 SharePoint 站点通常具有 3 种角色，但也可能具有其他自定义角色。 应用程序可以具有自定义角色。 |


## <a name="license-requirements"></a>许可要求

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

专用云（例如 Azure 德国和 Azure 中国世纪互联）当前不可用。

### <a name="how-many-licenses-must-you-have"></a>必须拥有多少个许可证？

请确保目录中至少具有与以下项数量相同的 Azure AD Premium P2 许可证：

- 可以请求访问包的成员用户数。
- 请求访问包的成员和来宾用户数。
- 审批访问包请求的成员和来宾用户数。
- 具有直接访问包分配的成员和来宾用户数。

以下任务无需 Azure AD Premium P2 许可证：

- 设置初始目录、访问包和策略并将管理任务委托给其他用户的、具有全局管理员角色的用户无需任何许可证。
- 被委托了管理任务的用户（例如目录创建者、目录所有者和访问包管理员）无需任何许可证。
- 有权请求访问包但并不请求访问包的来宾无需任何许可证 。

Azure AD External Identities（来宾用户）定价基于月度活动用户数 (MAU)，这是对一个日历月内具有身份验证活动的独立用户的计数。 此模型将替换 1:5 比率计费模型，该模型允许租户中的每个 Azure AD Premium 许可证最多 5 个来宾用户。 当你的租户链接到订阅时，如果使用 External Identities 功能与来宾用户协作，则将使用基于 MAU 的计费模型自动计费。 有关详细信息，请参阅 [Azure AD External Identities](../external-identities/external-identities-pricing.md) 的计费模型。

有关许可证的详细信息，请参阅[使用 Azure Active Directory 门户分配或删除许可证](../fundamentals/license-users-groups.md)。

### <a name="example-license-scenarios"></a>许可证场景示例

下面是一些许可证场景示例，可帮助你确定必须拥有的许可证数量。

| 方案 | 计算 | 许可证数量 |
| --- | --- | --- |
| Woodgrove Bank 的全局管理员创建了初始目录，并将管理任务委托给了 6 个其他用户。 其中一个策略指定，所有员工（2000 名员工）都可以请求一组特定的访问包。 150 名员工请求了访问包。 | 可以请求访问包的 2000 名员工 | 2,000 |
| Woodgrove Bank 的全局管理员创建了初始目录，并将管理任务委托给了 6 个其他用户。 其中一个策略指定，所有员工（2000 名员工）都可以请求一组特定的访问包。 另一个策略指定，来自“合作伙伴 Contoso 用户”（来宾）的某些用户可以经批准请求相同的访问包。 Contoso 有 30000 名用户。 150 名员工请求访问包，来自 Contoso 的 10500 名用户请求访问权限。 | 2000 名员工 + 比率超过 1:5 的来自 Contoso 的 500 名来宾用户 (10,500 - (2,000 * 5)) | 2,500 |

## <a name="next-steps"></a>后续步骤

- [教程：创建第一个访问包](entitlement-management-access-package-first.md)
- [常见方案](entitlement-management-scenarios.md)