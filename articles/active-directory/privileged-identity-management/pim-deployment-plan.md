---
title: 部署 Privileged Identity Management （PIM）-Azure AD |Microsoft Docs
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
ms.date: 11/08/2019
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: eef096322c8a8cfbf1618447529d46f6fbfd13b1
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021852"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>部署 Azure AD Privileged Identity Management (PIM)

此循序渐进指南介绍了如何规划 Azure Active Directory （Azure AD）组织中 Privileged Identity Management （PIM）的部署。

> [!TIP]
> 在本文中，你将看到标记为的项：
> 
> ： heavy_check_mark： **Microsoft 建议**
> 
> 这些是常规建议，应仅当其适用于企业需求时才实施。

## <a name="learn-about-privileged-identity-management"></a>了解 Privileged Identity Management

Azure AD Privileged Identity Management 可帮助你跨 Azure AD、Azure 资源和其他 Microsoft 联机服务管理特权管理角色。 在分配和遗忘特权标识的环境中，Privileged Identity Management 提供实时访问、请求批准工作流和完全集成的访问评审等解决方案，以便识别、发现和防止恶意攻击特权角色的实时活动。 部署 Privileged Identity Management 来管理组织中的特权角色将极大地降低风险，同时为特权角色的活动呈现有价值的见解。

### <a name="business-value-of-privileged-identity-management"></a>Privileged Identity Management 的业务价值

管理风险 - 通过强制实施**最低访问权限**和实时访问权限原则来保护组织[](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)。 通过最大限度地减少永久分配有特权角色的用户数量并强制执行针对提升目的的批准操作和 MFA，可以极大降低与组织中特权访问相关的安全风险。 通过强制实施最低权限和实时访问权限，还能够查看对特权角色的访问历史记录以及在发生安全问题时进行跟踪。

**解决法规遵从性和监管**-部署 Privileged Identity Management 为日常标识治理创建环境。 特权标识的实时提升为 Privileged Identity Management 提供了一种方法，用于跟踪组织中的特权访问活动。 还可以查看和接收有关组织内所有永久和有效角色分配的通知。 通过访问评审，可以定期审核并删除不必要的特权标识并确保组织符合最严苛的标识、访问和安全标准。

**降低成本**-通过正确部署 Privileged Identity Management 来消除效率低下、人为错误和安全问题，从而降低成本。 最终结果是减少与特权标识相关的网络犯罪，这些犯罪往往导致组织遭受极大损失且久难恢复。 当遵守法规和标准时，Privileged Identity Management 还将帮助你的组织降低与审核访问信息相关的成本。

有关详细信息，请参阅[什么是 Azure AD Privileged Identity Management？](pim-configure.md)。

### <a name="licensing-requirements"></a>许可要求

若要使用 Privileged Identity Management，你的目录必须具有以下付费或试用许可证之一：

- Azure AD Premium P2
- 企业移动性 + 安全性 (EMS) E5
- Microsoft 365 M5

有关详细信息，请参阅[使用 Privileged Identity Management 的许可要求](subscription-requirements.md)。

### <a name="key-terminology"></a>关键术语

| 术语或概念 | 说明 |
| --- | --- |
| 符合条件 | 要求用户在使用角色之前执行一项或多项操作的角色分配。 如果用户符合某个角色的条件，则意味着他们在需要执行特权任务时可以激活该角色。 用户无论具有永久角色分配还是合格角色分配，获得的访问权限并无差异。 唯一的差异在于，有些用户并不是一直需要该访问权限。 |
| 激活 | 合格用户在使用角色之前执行一项或多项操作的过程。 操作可能包括执行多重身份验证 (MFA) 检查、提供业务理由或请求获得指定审批者的批准。 |
| 实时 (JIT) 访问 | 一种访问模式。在此模式下，用户会收到执行特权任务的临时权限，防止恶意用户或未授权用户在权限过期后获得访问权限。 只有在用户需要的情况下，才会授予访问权限。 |
| 最低访问权限原则 | 一种建议的安全做法，仅为每个用户提供所需的最低权限，以便完成有权执行的任务。 此做法会尽量减少全局管理员的数目，并使用适合特定方案的特定管理员角色。 |

有关详细信息，请参见[术语](pim-configure.md#terminology)。

### <a name="high-level-overview-of-how-privileged-identity-management-works"></a>Privileged Identity Management 工作原理的简要概述

1. 设置 Privileged Identity Management 以便用户有资格获得特权角色。
1. 当合格用户需要使用其特权角色时，它们会在 Privileged Identity Management 中激活角色。
1. 根据为角色配置的 Privileged Identity Management 设置，用户必须完成某些步骤（如执行多重身份验证、获得批准或指定原因）。
1. 用户成功激活角色后，会在预配时间段内获得相应角色。
1. 管理员可以查看审核日志中所有 Privileged Identity Management 活动的历史记录。 他们还可以使用访问评审和警报等 Privileged Identity Management 功能进一步保护其 Azure AD 组织并满足合规性。

有关详细信息，请参阅[什么是 Azure AD Privileged Identity Management？](pim-configure.md)。

### <a name="roles-that-can-be-managed-by-privileged-identity-management"></a>可以通过管理的角色 Privileged Identity Management

**Azure AD 角色**–这些角色都在 Azure Active Directory （例如全局管理员、Exchange 管理员和安全管理员）中。 可通过 [Azure Active Directory 中的管理员角色权限](../users-groups-roles/directory-assign-admin-roles.md)，了解更多有关角色及其功能的信息。 如需帮助来确定向管理员分配哪个角色，请参阅[按任务划分的最小特权角色](../users-groups-roles/roles-delegate-by-task.md)。

Azure 资源角色 - 这些角色链接到 Azure 资源、资源组、订阅或管理组。 Privileged Identity Management 提供内置角色（如所有者、用户访问管理员、参与者以及[自定义角色](../../role-based-access-control/custom-roles.md)）的实时访问权限。 有关 Azure 资源角色的详细信息，请阅读[基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md)。

有关详细信息，请参阅[Privileged Identity Management 中无法管理的角色](pim-roles.md)。

## <a name="plan-your-deployment"></a>规划部署

本部分重点介绍在组织中部署 Privileged Identity Management 之前需要执行的操作。 请务必按照说明操作并理解本部分中的概念，这有助于为组织的特权标识制定最合适、最好的计划。

### <a name="identify-your-stakeholders"></a>标识利益干系人

以下部分可帮助标识与项目相关、需要签字、审核或随时了解情况的所有利益相干系人。 它包含单独的表，用于为 Azure 资源角色的 Azure AD 角色和 Privileged Identity Management 部署 Privileged Identity Management。 根据组织情况，将利益干系者添加到下表中。

- SO = 就此项目签字表示同意
- R = 审查此项目并提供输入
- I = 了解此项目的新情况

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>利益干系人： Azure AD 角色的 Privileged Identity Management

| 名称 | 角色 | 操作 |
| --- | --- | --- |
| 姓名和电子邮件 | **标识架构师或 Azure 全局管理员**<br/>标识管理团队的一位代表，负责定义如何根据组织中的核心标识管理基础结构实施此更改。 | SO/R/I |
| 姓名和电子邮件 | **服务所有者/线路管理器**<br/>某项服务或一组服务的 IT 所有者的代表。 它们是制定决策和帮助为其团队推出 Privileged Identity Management 的关键所在。 | SO/R/I |
| 姓名和电子邮件 | **安全所有者**<br/>安全团队的代表，可以签署确认计划符合组织的安全要求。 | SO/R |
| 姓名和电子邮件 | **IT 支持管理员/支持人员**<br/>IT 支持组织的代表，可以从服务支持的角度提供有关更改的可支持性的信息。 | R/I |
| 试点用户的姓名和电子邮件 | **特权角色用户**<br/>要为其实施特权标识管理的用户组。 在实施 Privileged Identity Management 后，他们将需要知道如何激活其角色。 | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-resource-roles"></a>利益干系人： Azure 资源角色的 Privileged Identity Management

| 名称 | 角色 | 操作 |
| --- | --- | --- |
| 姓名和电子邮件 | **订阅/资源所有者**<br/>要为其部署 Privileged Identity Management 的每个订阅或资源的 IT 所有者的代表 | SO/R/I |
| 姓名和电子邮件 | **安全所有者**<br/>安全团队的代表，可以签署确认计划符合组织的安全要求。 | SO/R |
| 姓名和电子邮件 | **IT 支持管理员/支持人员**<br/>IT 支持组织的代表，可以从服务支持的角度提供有关更改的可支持性的信息。 | R/I |
| 试点用户的姓名和电子邮件 | **RBAC 角色用户**<br/>要为其实施特权标识管理的用户组。 在实施 Privileged Identity Management 后，他们将需要知道如何激活其角色。 | I |

### <a name="enable-privileged-identity-management"></a>启用 Privileged Identity Management

在规划过程中，必须首先同意并启用 Privileged Identity Management，方法是[使用 Privileged Identity Management](pim-getting-started.md)文章。 启用 Privileged Identity Management 使你能够访问专门设计用于帮助你的部署的某些功能。

如果你的目标是部署适用于 Azure 资源的 Privileged Identity Management，则应遵循我们的[发现 azure 资源来管理 Privileged Identity Management](pim-resource-roles-discover-resources.md)文章。 只有每个资源、资源组和订阅的所有者才能在 Privileged Identity Management 中发现这些资源。 如果你是尝试为 Azure 资源部署 Privileged Identity Management 的全局管理员，则可以[提升访问权限，以管理所有 azure 订阅](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)，使你能够在目录中访问所有 azure 资源，以便发现。 但是，我们建议你先从每个订阅所有者处获得批准，然后才能 Privileged Identity Management 管理其资源。

### <a name="enforce-principle-of-least-privilege"></a>强制执行最低权限原则

务必确保已在组织中为 Azure AD 和 Azure 资源角色强制执行最低权限原则。

#### <a name="azure-ad-roles"></a>Azure AD 角色

对于 Azure AD 角色，当大多数管理员只需要一个或两个特定管理员角色时，组织通常会将全局管理员角色分配给大量管理员。 特权角色分配也往往不受管理。

> [!NOTE]
> 角色委派中的常见错误：
>
> - 向负责 Exchange 的管理员分配全局管理员角色，即使他们只需要 Exchange 管理员角色来执行其日常工作。
> - 向 Office 管理员分配全局管理员角色，因为管理员同时需要安全管理员和 SharePoint 管理员角色，委派一个角色更容易操作。
> - 很久以前给某个管理员分配了安全管理员角色去执行任务，但一直未删除该角色。

请按照下列步骤为 Azure AD 角色强制执行最低权限原则。

1. 通过阅读和理解[可用的 Azure AD 管理员角色](../users-groups-roles/directory-assign-admin-roles.md#available-roles)来了解角色粒度。 你和你的团队还应参阅 [Azure AD 中按标识任务划分的管理员角色](../users-groups-roles/roles-delegate-by-task.md)，其中解释了特定任务的最低权限角色。

1. 列出组织中有特权角色的人员。 您可以使用[Privileged Identity Management 向导](pim-security-wizard.md#run-the-wizard)来访问类似于下面的页面。

    ![“发现特权角色”窗格，其中显示具有特权角色的人员](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. 对于组织中的所有全局管理员，找出他们需要该角色的原因。 根据阅读前面的文档，如果用户的作业可以通过一个或多个粒度的管理员角色执行，则应将其从全局管理员角色中删除，并在 Azure Active Directory 中做出相应的分配（作为参考：Microsoft 目前只有大约10个具有全局管理员角色的管理员。 了解[Microsoft 如何使用 Privileged Identity Management](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)）的详细信息。

1. 对于所有其他 Azure AD 角色，查看分配列表，确定不再需要该角色的管理员，并在分配中将其删除。

若要自动执行最后两个步骤，可以在 Privileged Identity Management 中使用访问评审。 按照在 Privileged Identity Management 中[开始 Azure AD 角色的访问评审](pim-how-to-start-security-review.md)中的步骤进行操作，可以为具有一个或多个成员的每个 Azure AD 角色设置访问评审。

![“为 Azure AD 角色创建访问评审”窗格](./media/pim-deployment-plan/create-access-review.png)

应将审阅者设置为“成员(自我)”。 执行此操作后，系统会向该角色中的所有成员发送一封电子邮件，确认其是否需要相关访问权限。 还应在高级设置中启用“需提供批准理由”，以便用户可以描述其需要该角色的原因。 基于此信息，可将用户从不必要的角色中删除，如果该角色是全局管理员，则可以委派更细化的管理员角色。

访问评审依赖电子邮件来通知人员查看其角色访问权限。 如果拥有未链接电子邮件的特权帐户，请务必填写帐户中的备用电子邮件字段。 有关详细信息，请参阅 [Azure AD 中的 proxyAddresses 属性](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)。

#### <a name="azure-resource-roles"></a>Azure 资源角色

对于 Azure 订阅和资源，可以设置类似的访问评审流程，用于评审每个订阅或资源中的角色。 此过程的目标是最大程度地减少附加到每个订阅或资源的所有者和用户访问管理员分配量，以及删除不必要的分配。 但是，组织通常会将此类任务委派给每个订阅或资源的所有者，因为他们对特定角色（尤其是自定义角色）有更深入的了解。

如果你是使用全局管理员角色尝试为你的组织中的 Azure 资源部署 Privileged Identity Management 的 IT 管理员，你可以[提升访问权限来管理所有 azure 订阅](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)，以获取对每个订阅的访问权限。 然后可以找到每个订阅的所有者，与其协作，删除不必要的分配，最大程度减少所有者角色分配量。

具有 Azure 订阅的所有者角色的用户还可以利用 [Azure 资源的访问评审](pim-resource-roles-start-access-review.md)来审核和删除不必要的角色分配，此过程类似于之前用于 Azure AD 角色的过程。

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>确定应对哪些角色分配进行保护 Privileged Identity Management

在组织中清除了特权角色分配后，需要确定哪些角色要 Privileged Identity Management 保护。

如果某个角色受 Privileged Identity Management 保护，则分配给该角色的合格用户必须提升为使用该角色授予的特权。 权限提升过程还可能包括获取批准、执行多因素身份验证和/或提供相应的激活原因。 Privileged Identity Management 还可以通过通知和 Privileged Identity Management 并 Azure AD 审核事件日志来跟踪提升。

选择要用 Privileged Identity Management 保护的角色可能比较困难，每个组织都将有所不同。 本部分提供 Azure AD 和 Azure资源角色的最佳做法建议。

#### <a name="azure-ad-roles"></a>Azure AD 角色

应侧重保护具有最多权限的 Azure AD 角色，这一点很重要。 根据所有 Privileged Identity Management 客户的使用模式，Privileged Identity Management 管理的前 10 Azure AD 角色如下：

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
> ： heavy_check_mark： **Microsoft 建议**你使用 Privileged Identity Management 作为第一步来管理所有全局管理员和安全管理员，因为他们是在受到威胁时可以采取最大损害的步骤。

请务必考虑哪些数据和权限对组织而言最为敏感。 例如，某些组织可能希望使用 Privileged Identity Management 保护其 Power BI 管理员角色或其团队管理员角色，因为他们能够访问数据和/或更改核心工作流。

如果有任何角色的成员中包含来宾用户，这种角色就特别容易受到攻击。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建议**你使用 Privileged Identity Management 管理具有来宾用户的所有角色，以降低与受攻击的来宾用户帐户关联的风险。

有时人们会认为目录读取者、消息中心读取者和安全读取者等读取者角色相比其他角色来说不那么重要，因为这些角色没有写入权限。 但我们已经了解到，某些客户也会保护这些角色，因为获得这些帐户访问权限的攻击者可能读取敏感数据，例如个人身份信息 (PII)。 确定组织中的读者角色是否需要使用 Privileged Identity Management 进行管理时，应考虑到这一点。

#### <a name="azure-resource-roles"></a>Azure 资源角色

确定应该使用 Azure 资源 Privileged Identity Management 管理哪些角色分配时，必须首先确定对组织最重要的订阅/资源。 此类订阅/资源的示例有：

- 托管最敏感数据的资源
- 核心的、面向客户的应用程序所依赖的资源

如果你是全局管理员，无法确定哪些订阅/资源最重要，应该与组织中的订阅所有者联系，收集每个订阅管理的资源的列表。 然后应与订阅所有者合作，基于其所面临风险的严重性级别（低，中，高）对资源进行分组。 你应根据此严重级别 Privileged Identity Management 管理资源的优先级。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建议**你使用关键服务的订阅/资源所有者，为敏感订阅/资源中的所有角色设置 Privileged Identity Management 工作流。

Azure 资源 Privileged Identity Management 支持时间限制的服务帐户。 应以对待常规用户帐户的方式来对待服务帐户。

对于不重要的订阅/资源，无需为所有角色设置 Privileged Identity Management。 但是，你仍应使用 Privileged Identity Management 保护所有者和用户访问管理员角色。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建议**使用 Privileged Identity Management 管理所有订阅/资源的所有者角色和用户访问管理员角色。

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>确定哪些角色分配应为永久分配或合格分配

确定要通过 Privileged Identity Management 管理的角色列表后，必须确定哪些用户应该获得符合条件的角色，以及永久活动角色。 永久活动角色是通过 Azure Active Directory 和 Azure 资源分配的正常角色，而只有符合条件的角色才能在 Privileged Identity Management 中分配。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建议**你为 Azure AD 角色和 Azure 资源角色（除了建议的[两个中断玻璃紧急访问帐户](../users-groups-roles/directory-emergency-access.md)，该帐户应具有永久全局管理员角色）提供零个永久活动分配。

虽然我们建议不设立长期管理员，但组织有时很难立即实现这一目标。 下面是做出此决定时要考虑的事项：

- 提升频率 - 如果用户只需要一次特权分配，他们不应拥有永久分配。 另一方面，如果用户需要其日常工作的角色，并且使用 Privileged Identity Management 会极大地降低工作效率，则可将其视为永久角色。
- 特定于组织的案例 - 如果被授予合格角色的人员来自远程团队或为高级管理人员，不便沟通和执行提升过程，则可考虑向其分配永久角色。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建议**你为具有永久角色分配（如果有）的用户设置定期访问评审。 可通过此部署计划的最后一部分了解有关定期访问评审的详细信息

### <a name="draft-your-privileged-identity-management-settings"></a>草拟 Privileged Identity Management 设置

在实现 Privileged Identity Management 解决方案之前，最好为组织使用的每个特权角色草拟 Privileged Identity Management 设置。 本部分提供了一些有关特定角色 Privileged Identity Management 设置的示例（它们仅用于引用，并且对于你的组织可能有所不同）。 表格详细说明了前面提及的每一个设置并在表格后面提供了相关 Microsoft 建议。

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Azure AD 角色 Privileged Identity Management 设置

| 角色 | 要求 MFA | 通知 | 事件工单 | 需要审批 | 审批者 | 激活持续时间 | 永久管理员 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 全局管理员角色 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 其他全局管理员 | 1 小时 | 紧急访问帐户 |
| Exchange 管理员 | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | 无 | 2 小时 | 无 |
| 支持管理员 | :x: | :x: | :heavy_check_mark: | :x: | 无 | 8 小时 | 无 |

#### <a name="privileged-identity-management-settings-for-azure-resource-roles"></a>Azure 资源角色的 Privileged Identity Management 设置

| 角色 | 要求 MFA | 通知 | 需要审批 | 审批者 | 激活持续时间 | 活动管理员 | 活动期限 | 合格期限 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 关键订阅的所有者 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 订阅的其他所有者 | 1 小时 | 无 | 不适用 | 3 个月 |
| 次要订阅的用户访问管理员 | :heavy_check_mark: | :heavy_check_mark: | :x: | 无 | 1 小时 | 无 | 不适用 | 3 个月 |
| 虚拟机参与者 | :x: | :heavy_check_mark: | :x: | 无 | 3 个小时 | 无 | 不适用 | 6 个月 |

下表说明了每个设置。

| 设置 | 说明 |
| --- | --- |
| 角色 | 要为其定义设置的角色的名称。 |
| 要求 MFA | 符合条件的用户是否需要在激活角色之前执行 MFA。<br/><br/> ： heavy_check_mark： **Microsoft 建议**你为所有管理员角色强制实施 MFA，尤其是在角色具有来宾用户的情况下。 |
| 通知 | 如果设置为 true，当符合条件的用户激活角色时，组织中的全局管理员、特权角色管理员和安全管理员会收到电子邮件通知。<br/><br/>**注意：** 某些组织没有与管理员帐户关联的电子邮件地址，若要获取这些电子邮件通知，你应该设置一个备选电子邮件地址，以便管理员能够收到这些电子邮件。 |
| 事件工单 | 激活角色时符合条件的用户需要记录事件工单号。 此设置可帮助组织使用内部事件编号识别每次激活，以减少无效激活的次数。<br/><br/> ： heavy_check_mark： **Microsoft 建议**使用事件票证号，将 Privileged Identity Management 绑定到内部系统。 这对于需要激活操作上下文的审批者而言特别有用。 |
| 需要审批 | 符合条件的用户是否需要获取批准才能激活角色。<br/><br/> ： heavy_check_mark： **Microsoft 建议**你为具有最大权限的角色设置审批。 基于所有 Privileged Identity Management 客户的使用模式，全局管理员、用户管理员、Exchange 管理员、安全管理员和密码管理员是使用批准设置的最常见角色。 |
| 审批者 | 如果符合条件的角色需要执行审批操作来进行激活，应列出批准请求的人员。 默认情况下，Privileged Identity Management 会将审批者设置为作为特权角色管理员的所有用户，无论这些用户是永久性还是符合条件。<br/><br/>**注意：** 如果用户既有资格使用 Azure AD 角色又有权使用角色的审批者，他们将不能审批自己的角色。<br/><br/> ： heavy_check_mark： **Microsoft 建议**你选择 "审批者"，使其成为最有针对性的特定角色的用户，而不是全局管理员。 |
| 激活持续时间 | 在角色到期之前，用户拥有该角色的有效期。 |
| 永久管理员 | 将成为某个角色的永久管理员的用户的列表（永远不必激活）。<br/><br/> ： heavy_check_mark： **Microsoft 建议**你为除全局管理员之外的所有角色提供0个 "管理员"。 请参阅本计划的“应向谁分配合格角色”和“应向谁分配永久活动角色”部分，了解更多相关信息。 |
| 活动管理员 | 对于 Azure 资源，活动管理员是永远不必激活角色便可使用角色的用户的列表。 与 Azure AD 角色中的不同，该角色不称为永久管理员，因为可以设置此角色的失效时间。 |
| 活动期限 | 此配置中的时限到期后，Azure 资源角色的活动角色分配将失效。 可以选择 15 天、1 个月、3 个月、6 个月、1 年或永久活动。 |
| 合格期限 | 此配置中的时限到期后，Azure 资源角色的合格角色分配将失效。 可以选择 15 天、1 个月、3 个月、6 个月、1 年或永久符合条件。 |

## <a name="implement-your-solution"></a>实施解决方案

正确规划是使用 Azure Active Directory 成功部署应用程序的基础。  它提供智能安全性和集成，简化载入流程，同时减少成功部署的时间。  此特性可确保轻松集成应用程序，同时减少最终用户的停机时间。

### <a name="identify-test-users"></a>标识测试用户

借助此部分标识一组用户和/或用户组以验证实现。 基于在规划部分中选择的设置，确定要为每个角色测试的用户。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建议**使每个 Azure AD 角色的服务所有者成为测试用户，以便他们可以熟悉该过程，并成为推出的内部 advocator。

在此表中，标识测试用户，这些用户将验证每个角色的设置是否有效。

| 角色名称 | 测试用户 |
| --- | --- |
| &lt;角色名称&gt; | &lt;要测试角色的用户&gt; |
| &lt;角色名称&gt; | &lt;要测试角色的用户&gt; |

### <a name="test-implementation"></a>测试实现

确定测试用户后，请使用此步骤为测试用户配置 Privileged Identity Management。 如果你的组织想要将 Privileged Identity Management 工作流合并到你自己的内部应用程序中，而不是使用 Azure 门户中的 Privileged Identity Management，则 Privileged Identity Management 中的所有操作都是通过我们的[图形 API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root)来支持的。

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>为 Azure AD 角色配置 Privileged Identity Management

1. 基于计划[配置 Azure AD 角色设置](pim-how-to-change-default-settings.md)。

1. 导航到“Azure AD 角色”，单击“角色”，然后选择刚配置的角色。

1. 对于测试用户组，如果他们已经是永久管理员，可以将其转换为合格管理员，方法是单击其行上的三个点搜索他们并将其从永久性角色转换为合格角色。 如果他们还未分配有角色，可以[实施新的合格角色分配](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role)。

1. 为所有想要测试的角色重复步骤 1-3。

1. 测试用户一经设置，应向其发送有关如何[激活 Azure AD 角色](pim-how-to-activate-role.md)的链接。

#### <a name="configure-privileged-identity-management-for-azure-resource-roles"></a>为 Azure 资源角色配置 Privileged Identity Management

1. 为想要测试的订阅或资源中的角色[配置 Azure 资源角色设置](pim-resource-roles-configure-role-settings.md)。

1. 导航到“Azure 资源”查找该订阅并单击“角色”，选择刚配置的角色。

1. 对于测试用户组，如果他们已是活动管理员，可以通过搜索他们并[更新其角色分配](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment)，将其转变为合格管理员。 如果他们还没有角色，可以[分配新角色](pim-resource-roles-assign-roles.md#assign-a-role)。

1. 为所有想要测试的角色重复步骤 1-3。

1. 测试用户一经设置，应向其发送有关如何[激活 Azure 资源角色](pim-resource-roles-activate-your-roles.md)的链接。

应运用此阶段来验证为角色设置的所有配置是否均正常工作。 使用下表来记录测试。 应运用此阶段来优化与受影响用户之间的通信。

| 角色 | 激活期间的预期行为 | 实际结果 |
| --- | --- | --- |
| 全局管理员角色 | (1) 要求 MFA<br/>(2) 需要审批<br/>(3) 审批者收到通知，并且可以批准<br/>(4) 角色在预设时间后过期 |  |
| 订阅所有者 X | (1) 要求 MFA<br/>(2) 分配的合格角色将在配置的时间段后过期 |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>向受影响的利益干系人传达 Privileged Identity Management

部署 Privileged Identity Management 将为特权角色的用户引入额外的步骤。 尽管 Privileged Identity Management 大大减少了与特权标识相关的安全问题，但需要在租户范围的部署之前有效地传达更改。 根据受影响的管理员数量，组织通常会选择创建有关更改的内部文档、视频或电子邮件。 这些通信内容中通常包含：

- 什么是 PIM
- 组织有什么好处
- 谁会受到影响
- PIM 何时推出
- 用户需要执行哪些额外步骤才能激活其角色
    - 应发送以下文档的链接：
    - [激活 Azure AD 角色](pim-how-to-activate-role.md)
    - [激活 Azure 资源角色](pim-resource-roles-activate-your-roles.md)
- 用于咨询 PIM 问题的联系信息或支持链接

> [!TIP]
> ： heavy_check_mark： **Microsoft 建议**你为帮助台/支持团队设置时间，以帮助他们完成 Privileged Identity Management 工作流（如果你的组织有内部 IT 支持团队）。 为他们提供合适的文档和联系信息。

### <a name="move-to-production"></a>移到生产环境

测试完成并成功后，请通过重复在 Privileged Identity Management 配置中定义的每个角色的所有用户的测试阶段中的所有步骤，将 Privileged Identity Management 转移到生产。 对于 Azure AD 角色的 Privileged Identity Management，组织通常会在测试和推出其他角色 Privileged Identity Management 之前，测试和推出全局管理员的 Privileged Identity Management。 对于 Azure 资源，组织通常会一次测试和推出 Privileged Identity Management 一个 Azure 订阅。

### <a name="in-the-case-a-rollback-is-needed"></a>需要回滚的情况

如果 Privileged Identity Management 无法在生产环境中按预期工作，则在设置 Privileged Identity Management 之前，以下回滚步骤可帮助你恢复到已知的良好状态：

#### <a name="azure-ad-roles"></a>Azure AD 角色

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 打开“Azure AD Privileged Identity Management”。
1. 单击“Azure AD 角色”，然后单击“角色”。
1. 对于每个已配置的角色，为所有拥有合格角色的用户单击省略号 (…)。
1. 单击“指定永久”选项，将角色分配转换为永久分配。

#### <a name="azure-resource-roles"></a>Azure 资源角色

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 打开“Azure AD Privileged Identity Management”。
1. 单击“Azure 资源”，然后单击想要回滚的订阅或资源。
1. 单击“角色”。
1. 对于每个已配置的角色，为所有拥有合格角色的用户单击省略号 (…)。
1. 单击“指定永久”选项，将角色分配转换为永久分配。

## <a name="next-steps-after-deploying"></a>部署后的后续步骤

在保护组织的特权标识方面，在生产中成功部署 Privileged Identity Management 是一项重大进步。 随着 Privileged Identity Management 的部署，还提供了其他 Privileged Identity Management 的功能，你应该使用这些功能来确保安全性和符合性。

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>使用 Privileged Identity Management 警报来保护特权访问

你应利用 Privileged Identity Management 的内置警报功能来更好地保护你的租户。 有关详细信息，请参阅[安全警报](pim-how-to-configure-security-alerts.md#security-alerts)。 这些警报包括：管理员未使用特权角色、在 Privileged Identity Management 之外分配角色、角色激活过于频繁等。 若要充分保护组织，应定期查看警报列表，并解决问题。 可以使用以下方法查看和解决警报问题：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 打开“Azure AD Privileged Identity Management”。
1. 单击“Azure AD 角色”，然后单击“警报”。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建议**你立即处理标记为高严重性的所有警报。 对于严重性为中等和低级别的警报，应随时获取最新相关信息并在认为存在安全威胁时采取措施。

如果某个警报无有用信息或不适用于组织，可以在通知页上将其消除。 且之后可以在 Azure AD 设置页中撤销此消除操作。

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>设置定期访问评审以定期审核组织的特权标识

访问评审是向分配有特权角色的用户或特定审阅者询问每个用户是否需要特权身份的最佳方式。 如果想要减少攻击面并保持符合性，访问评审非常有用。 有关启动访问评审的详细信息，请参阅 [Azure AD 角色访问评审](pim-how-to-start-security-review.md)和 [Azure 资源角色访问评审](pim-resource-roles-start-access-review.md)。 某些组织为遵守法律和法规，需要执行定期访问评审，而对于其他组织，访问评审是在整个组织中实施最低权限原则的最佳方式。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建议**为所有 Azure AD 和 Azure 资源角色设置季度访问评审。

在大多数情况下，Azure AD 角色的评审者是用户自己，而 Azure 资源角色的评审者是该角色所在订阅的所有者。 但是，通常情况下公司会拥有未与任何人员的电子邮件地址关联的特权帐户。 在这种情况下，无人审阅访问权限。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建议**使用未链接到定期检查电子邮件地址的特权角色分配的所有帐户添加辅助电子邮件地址。

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>充分利用审核日志，以提升安全性和符合性

借助审核日志，可始终掌握最新动态和遵守相关法规。 Privileged Identity Management 当前在其审核日志内存储所有组织历史记录的30天历史记录，其中包括：

- 合格角色的激活/停用
- Privileged Identity Management 的内部和外部的角色分配活动
- 角色设置更改
- 通过批准设置，请求/批准/拒绝角色激活活动
- 警报更新

全局管理员或特权角色管理员可以访问这些审核日志。 有关详细信息，请参阅 [Azure AD 角色的审核历史记录](pim-how-to-use-audit-log.md)和 [Azure 资源角色的审核历史记录](azure-pim-resource-rbac.md)。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建议**你每周至少有一个管理员通读所有审核事件，并按月导出审核事件。

如果要将审核事件自动存储较长时间，Privileged Identity Management 的审核日志会自动同步到[Azure AD 审核日志](../reports-monitoring/concept-audit-logs.md)。

> [!TIP]
> ： heavy_check_mark： **Microsoft 建议**将[azure 日志监视](../reports-monitoring/concept-activity-logs-azure-monitor.md)设置为在 azure 存储帐户中存档审核事件，以满足安全性和符合性要求。
