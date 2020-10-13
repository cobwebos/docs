---
title: 部署 Privileged Identity Management (PIM) - Azure AD | Microsoft Docs
description: 介绍如何规划 Azure AD Privileged Identity Management (PIM) 的部署。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/27/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bfe0fee14ed463e265dc4e7e4177c702b051c81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89050193"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>部署 Azure AD Privileged Identity Management (PIM)

本文是一个分步指南，介绍了如何在 Azure Active Directory (Azure AD) 组织中规划 Privileged Identity Management (PIM) 的部署。 你将尽可能将具有高特权角色的用户重新分配给权限较低的内置角色或自定义角色，并为最高特权角色规划实时角色分配。 本文提供了有关部署规划和实现的建议。

> [!TIP]
> 本文中有许多带有以下标记的项：
>
> :heavy_check_mark:**Microsoft 建议**
>
> 这些是常规建议，仅应在其适用于特定企业需求时才实现。

## <a name="licensing-requirements"></a>许可要求

要使用 Privileged Identity Management，目录中必须具有以下付费或试用许可证之一。 有关详细信息，请参阅[使用 Privileged Identity Management 的许可要求](subscription-requirements.md)。

- Azure AD Premium P2
- 企业移动性 + 安全性 (EMS) E5
- Microsoft 365 Education A5
- Microsoft 365 企业版 E5

## <a name="how-pim-works"></a>PIM 的工作原理

本部分回顾了 Privileged Identity Management 过程的相关部分的规划目的。 有关详细信息，请参阅[什么是 Azure AD Privileged Identity Management？](pim-configure.md)

1. 开始使用 Privileged Identity Management，以便用户有资格使用特权角色。
1. 当符合条件的用户需要使用特权角色时，可在 Privileged Identity Management 中激活角色。
1. 用户可能需要在设置中：

    - 使用多因素身份验证
    - 请求批准激活
    - 提供激活的业务原因

1. 用户成功激活其角色后，可在设置的持续时间内拥有角色权限。
1. 管理员可以查看审核日志中所有 Privileged Identity Management 活动的历史记录。 他们还可以进一步保护其 Azure AD 组织，并使用 Privileged Identity Management 功能（如访问评审和警报）来满足合规性需求。

## <a name="roles-that-can-be-managed-by-pim"></a>可以使用 PIM 管理的角色

“Azure AD 角色”都在 Azure Active Directory 中（例如全局管理员、Exchange 管理员和安全管理员）。 可通过 [Azure Active Directory 中的管理员角色权限](../users-groups-roles/directory-assign-admin-roles.md)，了解更多有关角色及其功能的信息。 如需帮助来确定向管理员分配哪个角色，请参阅[按任务划分的最小特权角色](../users-groups-roles/roles-delegate-by-task.md)。

“Azure 角色”是指链接到 Azure 资源、资源组、订阅或管理组的角色。 可以使用 PIM 提供对内置 Azure 角色（如所有者、用户访问管理员和参与者）以及[自定义角色](../../role-based-access-control/custom-roles.md)的实时访问。 有关 Azure 角色的详细信息，请参阅 [Azure 基于角色的访问控制](../../role-based-access-control/overview.md)。

有关详细信息，请参阅[无法在 Privileged Identity Management 中管理的角色](pim-roles.md)。

## <a name="deployment-plan"></a>部署计划

在组织中部署 Privileged Identity Management 之前，请按照说明操作并理解本部分中的概念，这有助于根据组织的特权标识要求创建计划。

### <a name="identify-your-stakeholders"></a>标识利益干系人

以下部分可帮助标识与项目相关、需要签字、审核或随时了解情况的所有利益干系人。 包括用于为 Azure AD 角色部署 PIM 和为 Azure 角色部署 PIM 的独立表格。 根据组织情况，将利益干系者添加到下表中。

- SO = 就此项目签字表示同意
- R = 审查此项目并提供输入
- I = 了解此项目的新情况

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>利益干系人：针对 Azure AD 角色的 Privileged Identity Management

| 名称 | 角色 | 操作 |
| --- | --- | --- |
| 姓名和电子邮件 | **标识架构师或 Azure 全局管理员**<br/>标识管理团队的一位代表，负责定义如何将此更改与组织中的核心标识管理基础结构保持一致。 | SO/R/I |
| 姓名和电子邮件 | **服务所有者/线路管理器**<br/>某项服务或一组服务的 IT 所有者的代表。 他们在制定决策和帮助推出团队 Privileged Identity Management 中发挥关键作用。 | SO/R/I |
| 姓名和电子邮件 | **安全所有者**<br/>安全团队的代表，可以签署确认计划符合组织的安全要求。 | SO/R |
| 姓名和电子邮件 | **IT 支持管理员/支持人员**<br/>IT 支持组织的代表，可以从服务支持的角度提供有关更改的可支持性的反馈。 | R/I |
| 试点用户的姓名和电子邮件 | **特权角色用户**<br/>要为其实施特权标识管理的用户组。 他们需要知道如何在实现 Privileged Identity Management 后激活角色。 | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-roles"></a>利益干系人：针对 Azure 角色的 Privileged Identity Management

| 名称 | 角色 | 操作 |
| --- | --- | --- |
| 姓名和电子邮件 | **订阅/资源所有者**<br/>要为其部署 Privileged Identity Management 的每个订阅或资源的 IT 所有者的代表 | SO/R/I |
| 姓名和电子邮件 | **安全所有者**<br/>安全团队的代表，可以签署确认计划符合组织的安全要求。 | SO/R |
| 姓名和电子邮件 | **IT 支持管理员/支持人员**<br/>IT 支持组织的代表，可以从服务支持的角度提供有关更改的可支持性的反馈。 | R/I |
| 试点用户的姓名和电子邮件 | **Azure 角色用户**<br/>要为其实施特权标识管理的用户组。 他们需要知道如何在实现 Privileged Identity Management 后激活角色。 | I |

### <a name="start-using-privileged-identity-management"></a>开始使用 Privileged Identity Management

作为规划过程的一部分，应该按照[开始使用 Privileged Identity Management](pim-getting-started.md) 一文来准备 Privileged Identity Management。 Privileged Identity Management 允许访问专为帮助部署而设计的某些功能。

如果目标是为 Azure 资源部署 Privileged Identity Management，则应按照[发现要在 Privileged Identity Management 中管理的 Azure 资源](pim-resource-roles-discover-resources.md)一文中所述操作。 只有订阅和管理组的所有者才能使这些资源受 Privileged Identity Management 管理。 资源受管理之后，PIM 功能可用于所有级别（包括管理组、订阅、资源组和资源）的所有者。 如果你是全局管理员并尝试为 Azure 资源部署 Privileged Identity Management，可以[提升访问权限以管理所有 Azure 订阅](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)，从而让自己可以访问目录中的所有 Azure 资源以进行发现。 但是，建议在使用 Privileged Identity Management 管理其资源之前先获取每个订阅所有者的批准。

### <a name="enforce-principle-of-least-privilege"></a>强制执行最低权限原则

务必确保已在组织中为 Azure AD 和 Azure 角色强制执行最低权限原则。

#### <a name="plan-least-privilege-delegation"></a>规划最低权限委派

对于 Azure AD 角色，当大多数管理员只需要一个或两个权限较低的特定管理员角色时，组织通常会将全局管理员角色分配给多个管理员。 如果有大量全局管理员或其他高权限角色，很难对特权角色分配进行足够密切的跟踪。

按照以下步骤为 Azure AD 角色实现最低权限原则。

1. 通过阅读和理解[可用的 Azure AD 管理员角色](../users-groups-roles/directory-assign-admin-roles.md#available-roles)来了解角色粒度。 你和你的团队还应参阅 [Azure AD 中按标识任务划分的管理员角色](../users-groups-roles/roles-delegate-by-task.md)，其中解释了特定任务的最低权限角色。

1. 列出组织中有特权角色的人员。 可以使用 Privileged Identity Management [发现和见解（预览版）](pim-security-wizard.md)来降低曝光度。

    ![“发现和见解(预览版)”页，可通过特权角色降低曝光度](./media/pim-deployment-plan/new-preview-page.png)

1. 对于组织中的所有全局管理员，找出他们需要该角色的原因。 然后，将其从全局管理员角色中删除，并在 Azure Active Directory 中分配内置角色或权限较低的自定义角色。 仅供参考：Microsoft 目前只有 10 个管理员拥有全局管理员角色。 在 [Microsoft 如何使用 Privileged Identity Management](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access) 中了解详细信息。

1. 对于所有其他 Azure AD 角色，查看分配列表，确定不再需要该角色的管理员，并在分配中将其删除。

若要自动执行最后两个步骤，可以在 Privileged Identity Management 中使用访问评审。 按照[为 Privileged Identity Management 中的 Azure AD 启动访问评审](pim-how-to-start-security-review.md)中的步骤操作，可以为每一个拥有一个或多个成员的 Azure AD 角色设置访问评审。

![“为 Azure AD 角色创建访问评审”窗格](./media/pim-deployment-plan/create-access-review.png)

将审阅者设置为“成员(自评审)”。 具有该角色的所有用户都将收到一封电子邮件，要求他们确认自己是否需要访问权限。 另外，在高级设置中启用“需提供批准理由”，要求用户必须描述其需要该角色的原因。 基于此信息，你可以将用户从不必要的角色中删除，或将其委派给更精细的管理员角色。

访问评审依赖电子邮件来通知人员查看其角色访问权限。 如果拥有未链接电子邮件的特权帐户，请务必填写帐户中的备用电子邮件字段。 有关详细信息，请参阅 [Azure AD 中的 proxyAddresses 属性](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)。

#### <a name="plan-azure-resource-role-delegation"></a>规划 Azure 资源角色委派

对于 Azure 订阅和资源，可以设置类似的访问评审流程，用于评审每个订阅或资源中的角色。 此过程的目标是最大程度地减少附加到每个订阅或资源的所有者和用户访问管理员分配量，以及删除不必要的分配。 但是，组织通常会将此类任务委派给每个订阅或资源的所有者，因为他们对特定角色（尤其是自定义角色）有更深入的了解。

如果你以全局管理员角色尝试为组织中的 Azure 角色部署 PIM，则可以[提升访问权限以管理所有 Azure 订阅](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)，从而获取每个订阅的访问权限。 然后可以找到每个订阅的所有者，与其协作，删除不必要的分配，最大程度减少所有者角色分配量。

具有 Azure 订阅的所有者角色的用户还可以使用 [Azure 资源的访问评审](pim-resource-roles-start-access-review.md)来审核和删除不必要的角色分配，此过程类似于之前用于 Azure AD 角色的过程。

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>确定 Privileged Identity Management 应保护哪些角色分配

在组织中清理特权角色分配后，需要决定使用 Privileged Identity Management 保护哪些角色。

如果某个角色受 Privileged Identity Management 保护，则分配给它的符合条件的用户必须提升权限才能使用该角色授予的权限。 权限提升过程还可能包括获取批准、使用多重身份验证和提供相应的激活原因。 Privileged Identity Management 还可以通过通知、Privileged Identity Management 和 Azure AD 审核事件日志来跟踪权限提升。

要选择使用 Privileged Identity Management 保护哪些角色可能存在一定困难，而且每个组织的情况都不一样。 本部分提供 Azure AD 和 Azure 角色的最佳做法建议。

#### <a name="azure-ad-roles"></a>Azure AD 角色

必须侧重于保护具有最多权限的 Azure AD 角色。 基于 Privileged Identity Management 客户的使用模式，排在前 10 位的 Privileged Identity Management 管理的 Azure AD 角色为：

1. 全局管理员
1. 安全管理员
1. 用户管理员
1. Exchange 管理员
1. SharePoint 管理员
1. Intune 管理员
1. 安全读取者
1. 服务管理员
1. 计费管理员
1. Skype for Business 管理员

> [!TIP]
> :heavy_check_mark:Microsoft 建议首先使用 Privileged Identity Management 管理所有全局管理员和安全管理员，因为它们是受到攻击后造成损失最大的用户。

请务必考虑哪些数据和权限对组织而言最为敏感。 例如，某些组织可能希望使用 Privileged Identity Management 保护其 Power BI 管理员角色或其团队管理员角色，因为这些角色能够访问数据和更改核心工作流。

如果有任何角色的成员中包含来宾用户，则这些角色很容易受到攻击。

> [!TIP]
> :heavy_check_mark:**Microsoft 建议**使用 Privileged Identity Management 管理含有来宾用户的所有角色，以降低与来宾用户帐户相关的攻击风险。

诸如目录读取者、消息中心读取者和安全读取者等读取者角色有时被认为不如其他角色重要，因为这些角色没有写入权限。 但我们的某些客户也会保护这些角色，因为获得这些帐户访问权限的攻击者可能读取敏感数据，例如个人数据。 在决定是否需要使用 Privileged Identity Management 管理组织中的读取者角色时，应考虑到这一点。

#### <a name="azure-roles"></a>Azure 角色

在决定应使用 Privileged Identity Management 为 Azure 资源管理哪些角色分配时，必须首先确定对组织而言最重要的订阅/资源。 此类订阅/资源的示例有：

- 托管最敏感数据的资源
- 核心的、面向客户的应用程序所依赖的资源

如果你是全局管理员，且无法确定哪些订阅和资源最重要，则应与组织中的订阅所有者联系，收集每个订阅管理的资源的列表。 然后与订阅所有者合作，基于其所面临风险的严重性级别（低，中，高）对资源进行分组。 根据此严重性级别确定优先使用 Privileged Identity Management 管理的资源。

> [!TIP]
> :heavy_check_mark:**Microsoft 建议**与关键服务的订阅/资源所有者合作，为敏感订阅/资源中的所有角色设置 Privileged Identity Management 工作流。

Azure 资源的 Privileged Identity Management 支持时限服务帐户。 应以对待常规用户帐户的方式来对待服务帐户。

对于非关键的订阅/资源，不需要为所有角色设置 Privileged Identity Management。 但是，仍应使用 Privileged Identity Management 保护所有者和用户访问管理员角色。

> [!TIP]
> :heavy_check_mark:Microsoft 建议使用 Privileged Identity Management 管理所有订阅/资源的所有者角色和用户访问管理员角色。

### <a name="decide-whether-to-use-a-group-to-assign-roles"></a>决定是否使用组来分配角色

是否将角色分配给组（而不是单个用户）是一个战略决策。 规划时，在以下情况下考虑将角色分配给组，以便管理角色分配：

- 向一个角色分配了多个用户
- 想要委托分配角色

#### <a name="many-users-are-assigned-to-a-role"></a>向一个角色分配了多个用户

如果手动完成跟踪向角色分配的用户以及根据用户需要管理其角色分配这一过程，可能会花费一些时间。 若要为某个角色分配一个角色，请首先 [创建一个角色可分配组](../users-groups-roles/roles-groups-create-eligible.md) ，然后为该组分配适用于角色的组。 此操作可将组中的每个人与有资格提升为角色的单个用户进行相同的激活过程。 组成员使用 Privileged Identity Management 的激活请求和审批过程，分别激活其对组的分配。 组未激活，只是用户的组成员身份。

#### <a name="you-want-to-delegate-assigning-the-role"></a>想要委托分配角色

组所有者可以管理组的成员身份。 对于 Azure AD 可分配角色的组，只有特权角色管理员、全局管理员和组所有者可以管理组成员身份。 通过将新成员添加到组中，无论分配是“合格”分配还是“活动”分配，该成员都可以访问为其分配了该组的角色。 使用组所有者来委托管理所分配角色的组成员身份，可减少所需的权限范围。 若要详细了解如何在创建组时为组分配所有者，请参阅 [在 Azure AD 中创建可分配角色的组](../users-groups-roles/roles-groups-create-eligible.md)。

> [!TIP]
> :heavy_check_mark:Microsoft 建议使用 Privileged Identity Management 来管理 Azure AD 可分配角色的组。 在使用 PIM 管理可分配角色的组后，该组被称为特权访问组。 使用 PIM 要求组所有者先激活其所有者角色分配，然后再管理组成员身份。 有关使用 PIM 管理组的详细信息，请参阅[将特权访问组（预览版）引入 Privileged Identity Management](groups-discover-groups.md)。

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>确定哪些角色分配应为永久分配或合格分配

要使用 Privileged Identity Management 管理的角色的列表一经确定，须决定应将合格角色与永久活动角色分别分配给哪些用户。 永久活动角色是通过 Azure Active Directory 和 Azure 资源分配的常规角色，而合格角色只能在 Privileged Identity Management 中分配。

> [!TIP]
> :heavy_check_mark:Microsoft 建议不向 Azure AD 角色和 Azure 角色分配永久活动角色，但推荐的[两个紧急访问帐户](../users-groups-roles/directory-emergency-access.md)除外，这两个帐户应具有永久的全局管理员角色。

虽然我们建议不设立长期管理员，但组织有时很难立即实现这一目标。 下面是做出此决定时要考虑的事项：

- 提升频率 - 如果用户只需要一次特权分配，他们不应拥有永久分配。 另一方面，如果用户需要某个角色来处理日常工作且使用 Privileged Identity Management 会极大降低其工作效率，则可考虑向其分配永久角色。
- 特定于你的组织的案例–如果被授予了合格角色的人员来自遥远团队或高排名的主管，则与进行通信并强制实施提升过程的点比较困难，则可将其视为永久角色。

> [!TIP]
> :heavy_check_mark:Microsoft建议为分配了永久角色的用户（如果有）设置定期访问评审。 可通过此部署计划的最后一部分了解有关定期访问评审的详细信息

### <a name="draft-your-privileged-identity-management-settings"></a>草拟 Privileged Identity Management 设置

实现 Privileged Identity Management 解决方案之前，一个很好的做法是为组织使用的每一个特权角色草拟 Privileged Identity Management 设置。 本部分包含一些用于特定角色的 Privileged Identity Management 设置的示例（仅供参考，与组织所需可能不同）。 表格详细说明了前面提及的每一个设置并在表格后面提供了相关 Microsoft 建议。

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>针对 Azure AD 角色的 Privileged Identity Management 设置

| 角色 | 要求 MFA | 通知 | 事件工单 | 需要审批 | 审批者 | 激活持续时间 | 永久管理员 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 全局管理员角色 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 其他全局管理员 | 1 小时 | 紧急访问帐户 |
| Exchange 管理员 | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | 无 | 2 小时 | 无 |
| 支持管理员 | :x: | :x: | :heavy_check_mark: | :x: | 无 | 8 小时 | 无 |

#### <a name="privileged-identity-management-settings-for-azure-roles"></a>针对 Azure 角色的 Privileged Identity Management 设置

| 角色 | 要求 MFA | 通知 | 需要审批 | 审批者 | 激活持续时间 | 活动管理员 | 活动期限 | 合格期限 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 关键订阅的所有者 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 订阅的其他所有者 | 1 小时 | 无 | 不适用 | 3 个月 |
| 次要订阅的用户访问管理员 | :heavy_check_mark: | :heavy_check_mark: | :x: | 无 | 1 小时 | 无 | 不适用 | 3 个月 |
| 虚拟机参与者 | :x: | :heavy_check_mark: | :x: | 无 | 3 个小时 | 无 | 不适用 | 6 个月 |

下表说明了每个设置。

| 设置 | 说明 |
| --- | --- |
| 角色 | 要为其定义设置的角色的名称。 |
| 要求 MFA | 符合条件的用户是否需要在激活角色之前执行 MFA。<br/><br/> :heavy_check_mark:Microsoft 建议为所有管理员角色强制执行 MFA，尤其是在角色含有来宾用户的情况下。 |
| 通知 | 如果设置为 true，当符合条件的用户激活角色时，组织中的全局管理员、特权角色管理员和安全管理员会收到电子邮件通知。<br/><br/>**注意：** 某些组织没有向管理员帐户绑定电子邮件地址来获取这些电子邮件通知，应该设置一个备用电子邮件地址，以便管理员收到这些电子邮件。 |
| 事件工单 | 激活角色时符合条件的用户需要记录事件工单号。 此设置可帮助组织使用内部事件编号识别每次激活，以减少无效激活的次数。<br/><br/> :heavy_check_mark:**Microsoft 建议**利用事件工单号将 Privileged Identity Management 绑定到内部系统。 此方法对于需要激活上下文的审批者非常有用。 |
| 需要审批 | 符合条件的用户是否需要获取批准才能激活角色。<br/><br/> :heavy_check_mark:Microsoft 建议为具有最多权限的角色设置审批操作。 基于全体 Privileged Identity Management 客户的使用模式，全局管理员、用户管理员、Exchange 管理员、安全管理员和密码管理员是最常应用审批操作的角色。 |
| 审批者 | 如果符合条件的角色需要执行审批操作来进行激活，应列出批准请求的人员。 默认情况下，Privileged Identity Management 将审批者设置为身份是特权角色管理员的所有用户，无论他们是永久性角色还是合格角色。<br/><br/>**注意：** 如果用户同时有资格拥有 Azure AD 角色和该角色的审批者身份，则无法为自己执行审批操作。<br/><br/> :heavy_check_mark:Microsoft 建议所选择的审批者应为最了解角色及其惯常用户的人员，而非全局管理员。 |
| 激活持续时间 | 在角色到期之前，用户拥有该角色的有效期。 |
| 永久管理员 | 将成为某个角色的永久管理员的用户的列表（永远不必激活）。<br/><br/> :heavy_check_mark:Microsoft 建议所有角色（全局管理员除外）均不设立长期管理员。 请参阅本计划的“应向谁分配合格角色”和“应向谁分配永久活动角色”部分，了解更多相关信息。 |
| 活动管理员 | 对于 Azure 资源，活动管理员是永远不必激活角色便可使用角色的用户的列表。 与 Azure AD 角色中不同，该列表不称为永久管理员，因为可以设置此角色的失效时间。 |
| 活动期限 | Azure 角色的活动角色分配在配置的期限后过期。 可以选择 15 天、1 个月、3 个月、6 个月、1 年或永久活动。 |
| 合格期限 | Azure 角色的合格角色分配在此期限后过期。 可以选择 15 天、1 个月、3 个月、6 个月、1 年或永久符合条件。 |

## <a name="implementation-plan"></a>实现规划

正确规划是使用 Azure Active Directory 成功部署应用程序的基础。  它提供智能安全性和集成，简化载入流程，同时减少成功部署的时间。  此特性可确保轻松集成应用程序，同时减少最终用户的停机时间。

### <a name="identify-test-users"></a>标识测试用户

借助此部分标识一组用户和/或用户组以验证实现。 基于在规划部分中选择的设置，确定要为每个角色测试的用户。

> [!TIP]
> :heavy_check_mark:Microsoft 建议将每个 Azure AD 角色的服务所有者设为测试用户，以便其能够熟悉该流程并成为解决方案的内部支持者。

在此表中，标识将验证角色设置是否有效的测试用户。

| 角色名称 | 测试用户 |
| --- | --- |
| &lt;角色名称&gt; | &lt;要测试角色的用户&gt; |
| &lt;角色名称&gt; | &lt;要测试角色的用户&gt; |

### <a name="test-implementation"></a>测试实现

现已标识测试用户，请使用此步骤为测试用户配置 Privileged Identity Management。 如果组织要将 Privileged Identity Management 工作流并入内部的应用程序中，而不是使用 Azure 门户内的 Privileged Identity Management，[图形 API](/graph/api/resources/privilegedidentitymanagement-root) 也支持 Privileged Identity Management 中的所有操作。

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>针对 Azure AD 角色配置 Privileged Identity Management

1. 基于计划[配置 Azure AD 角色设置](pim-how-to-change-default-settings.md)。

1. 导航到“Azure AD 角色”，选择“角色”，然后选择配置的角色 。

1. 对于测试用户组，如果他们已经是永久管理员，可以将其转换为合格管理员，方法是搜索他们，并通过选择其所在行中的三个点将其从永久性角色转换为合格角色。 如果他们还未分配有角色，可以[实施新的合格角色分配](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role)。

1. 为所有想要测试的角色重复步骤 1-3。

1. 测试用户一经设置，应向其发送有关如何[激活 Azure AD 角色](pim-how-to-activate-role.md)的链接。

#### <a name="configure-privileged-identity-management-for-azure-roles"></a>针对 Azure 角色配置 Privileged Identity Management

1. 为想要测试的订阅或资源中的角色[配置 Azure 资源角色设置](pim-resource-roles-configure-role-settings.md)。

1. 导航到“Azure 资源”查找该订阅，选择“角色”，然后选择配置的角色 。

1. 对于测试用户组，如果他们已是活动管理员，可以通过搜索他们并[更新其角色分配](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment)，将其转变为合格管理员。 如果他们还没有角色，可以[分配新角色](pim-resource-roles-assign-roles.md#assign-a-role)。

1. 为所有想要测试的角色重复步骤 1-3。

1. 测试用户一经设置，应向其发送有关如何[激活 Azure 资源角色](pim-resource-roles-activate-your-roles.md)的链接。

应运用此阶段来验证为角色设置的所有配置是否均正常工作。 使用下表来记录测试。 应运用此阶段来优化与受影响用户之间的通信。

| 角色 | 激活期间的预期行为 | 实际结果 |
| --- | --- | --- |
| 全局管理员角色 | (1) 要求 MFA<br/>(2) 需要审批<br/>(3) 审批者收到通知，并且可以批准<br/>(4) 角色在预设时间后过期 |  |
| 订阅所有者 X | (1) 要求 MFA<br/>(2) 分配的合格角色将在配置的时间段后过期 |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>与受影响的利益干系人就 Privileged Identity Management 进行沟通

部署 Privileged Identity Management 需要特权角色用户执行额外操作步骤。 虽然 Privileged Identity Management 极大地减少了与特权标识相关的安全问题，但在组织范围内进行部署之前，需要有效地传达相关变更。 根据受影响的管理员数量，组织通常会选择创建有关更改的内部文档、视频或电子邮件。 这些通信内容中通常包含：

- 什么是 PIM
- 组织有什么好处
- 谁会受到影响
- PIM 何时推出
- 用户需要执行哪些额外步骤才能激活其角色
    - 应发送以下文档的链接：
    - [激活 Azure AD 角色](pim-how-to-activate-role.md)
    - [激活 Azure 角色](pim-resource-roles-activate-your-roles.md)
- 用于咨询 PIM 问题的联系信息或支持链接

> [!TIP]
> :heavy_check_mark:**Microsoft 建议**你与支持团队确定时间，让其完整了解 Privileged Identity Management 工作流（如果组织有内部 IT 支持团队）。 为他们提供合适的文档和联系信息。

### <a name="move-to-production"></a>移到生产环境

一旦测试完成并成功，将 Privileged Identity Management 移至生产，方法是为 Privileged Identity Management 配置中定义的每个角色的所有用户重复测试阶段的所有步骤。 对于针对 Azure AD 角色的 Privileged Identity Management，组织通常会先为全局管理员测试和推出 Privileged Identity Management，然后再为其他角色测试和推出 Privileged Identity Management。 同时，对于 Azure 资源，组织通常一次为一个 Azure 订阅测试和推出 Privileged Identity Management。

### <a name="if-a-rollback-is-needed"></a>如果需要回滚

如果 Privileged Identity Management 在生产环境中未按预期运行，可借助以下回滚步骤，恢复到设置 Privileged Identity Management 之前时的某个已知的良好状态：

#### <a name="azure-ad-roles"></a>Azure AD 角色

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 打开“Azure AD Privileged Identity Management”。
1. 选择“Azure AD 角色”，然后选择“角色” 。
1. 对于每个已配置的角色，为所有拥有合格分配的用户选择省略号 (…)。
1. 选择“指定永久”选项，将角色分配转换为永久分配。

#### <a name="azure-roles"></a>Azure 角色

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 打开“Azure AD Privileged Identity Management”。
1. 选择“Azure 资源”，然后选择想要回滚的订阅或资源。
1. 选择“角色”  。
1. 对于每个已配置的角色，为所有拥有合格分配的用户选择省略号 (…)。
1. 选择“指定永久”选项，将角色分配转换为永久分配。

## <a name="next-steps-after-deploying"></a>部署后的后续步骤

在生产中成功部署 Privileged Identity Management 意味着在保护组织特权标识方面迈出了重要一步。 通过部署 Privileged Identity Management，可额外获取用于实现安全性与合规性的 Privileged Identity Management 功能。

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>使用 Privileged Identity Management 警报来保护特权访问

有关使用 Privileged Identity Management 的内置警报功能来保护组织的详细信息，请参阅[安全警报](pim-how-to-configure-security-alerts.md#security-alerts)。 这些警报包括：管理员不使用特权角色、角色被分配到 Privileged Identity Management 之外、角色激活过于频繁等等。 若要充分保护组织，应定期查看警报列表，并解决问题。 可以使用以下方法查看和解决警报问题：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 打开“Azure AD Privileged Identity Management”。
1. 选择“Azure AD 角色”，然后选择“警报” 。

> [!TIP]
> :heavy_check_mark:Microsoft 建议应立即处理所有标记为高严重级别的警报。 对于严重性为中等和低级别的警报，应随时获取最新相关信息并在认为存在安全威胁时采取措施。

如果某个警报无有用信息或不适用于组织，可以在通知页上将其消除。 且之后可以在 Azure AD 设置页中撤销此消除操作。

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>设置定期访问评审以定期审核组织的特权标识

访问评审是向分配有特权角色的用户或特定审阅者询问每个用户是否需要特权身份的最佳方式。 如果想要减少攻击面并保持符合性，访问评审非常有用。 有关启动访问评审的详细信息，请参阅 [Azure AD 角色访问评审](pim-how-to-start-security-review.md)和 [Azure 角色访问评审](pim-resource-roles-start-access-review.md)。 某些组织为遵守法律和法规，需要执行定期访问评审，而对于其他组织，访问评审是在整个组织中实施最低权限原则的最佳方式。

> [!TIP]
> :heavy_check_mark:Microsoft 建议为所有 Azure AD 和 Azure 角色设置季度访问评审。

在大多数情况下，Azure AD 角色的评审者是用户自己，而 Azure 角色的评审者是该角色所在订阅的所有者。 但是，通常情况下公司会拥有未与任何人员的电子邮件地址关联的特权帐户。 在这种情况下，无人审阅访问权限。

> [!TIP]
> :heavy_check_mark:Microsoft 建议为所有拥有特权角色但未与定期检查的电子邮件地址相关联的帐户添加备用电子邮件地址

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>充分利用审核日志，以提升安全性和符合性

借助审核日志，可始终掌握最新动态和遵守相关法规。 Privileged Identity Management 目前在其审核日志中存储 30 天的完整组织历史记录，包括：

- 合格角色的激活/停用
- Privileged Identity Management 内部和外部的角色分配活动
- 角色设置更改
- 通过批准设置，请求/批准/拒绝角色激活活动
- 警报更新

全局管理员或特权角色管理员可以访问审核日志。 有关详细信息，请参阅 [Azure AD 角色的审核历史记录](pim-how-to-use-audit-log.md)和 [Azure 角色的审核历史记录](azure-pim-resource-rbac.md)。

> [!TIP]
> :heavy_check_mark:Microsoft 建议至少让一名管理员每周阅读所有审核事件，并按月导出审核事件。

如果要在较长时间内自动存储审核事件，Privileged Identity Management 的审核日志将自动同步到 [Azure AD 审核日志](../reports-monitoring/concept-audit-logs.md)中。

> [!TIP]
> :heavy_check_mark:Microsoft 建议设置 [Azure 日志监视](../reports-monitoring/concept-activity-logs-azure-monitor.md)以在 Azure 存储帐户中存档审核事件，从而提高安全性和符合性。
