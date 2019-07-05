---
title: 部署 Privileged 的 Identity Management (PIM)-Azure Active Directory |Microsoft Docs
description: 介绍如何规划 Azure AD Privileged Identity Management (PIM) 的部署。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/08/2019
ms.author: rolyon
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7413fcf7992195753cba86a50b7d53a144b36023
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476426"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>部署 Azure AD Privileged Identity Management (PIM)

本循序渐进指南介绍如何规划部署的 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 中，你的组织中。

> [!TIP]
> 本文档中有许多带有以下标记的项：
> 
> :heavy_check_mark:**Microsoft 建议**
> 
> 这些是常规建议，应仅当其适用于企业需求时才实施。

## <a name="step-1-learn-about-pim"></a>步骤 1。 了解 PIM

Azure AD Privileged Identity Management (PIM) 可帮助跨 Azure AD、Azure 资源和其他 Microsoft 联机服务管理特权管理角色。 在一个分配了特权标识但容易将标识遗忘的环境里，PIM 提供实时访问、请求审批工作流和完全集成的访问评审等解决方案，让用户可以实时识别、发现并防止特权角色的恶意活动。 通过部署 PIM 来管理整个组织中的特权角色可极大降低风险，同时获取有关特权角色活动的有用见解。

### <a name="business-value-of-pim"></a>PIM 的商业价值

管理风险 - 通过强制实施[最低访问权限](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)和实时访问权限原则来保护组织  。 通过最大限度地减少永久分配有特权角色的用户数量并强制执行针对提升目的的批准操作和 MFA，可以极大降低与组织中特权访问相关的安全风险。 通过强制实施最低权限和实时访问权限，还能够查看对特权角色的访问历史记录以及在发生安全问题时进行跟踪。

合规性和监管解决方案 - 部署 PIM 可创建一个持续实施标识管理的环境  。 实时特权标识提升为 PIM 提供了一种跟踪组织中特权访问活动的方法。 还可以查看和接收有关组织内所有永久和有效角色分配的通知。 通过访问评审，可以定期审核并删除不必要的特权标识并确保组织符合最严苛的标识、访问和安全标准。

降低成本 - 通过正确部署 PIM 来消除低效率、人为错误和安全问题，从而降低成本  。 最终结果是减少与特权标识相关的网络犯罪，这些犯罪往往导致组织遭受极大损失且久难恢复。 在遵守法规和标准的同时，PIM 还可帮助组织降低访问信息审核相关成本。

有关详细信息，请参阅[什么是 Azure AD Privileged Identity Management？](pim-configure.md)。

### <a name="licensing-requirements"></a>许可要求

若要使用 PIM，目录必须具有以下付费或试用许可证之一：

- Azure AD Premium P2
- 企业移动性 + 安全性 (EMS) E5
- Microsoft 365 M5

有关详细信息，请参阅[使用 PIM 所要满足的许可要求](subscription-requirements.md)。

### <a name="key-pim-terminology"></a>关键 PIM 术语

| 术语或概念 | 描述 |
| --- | --- |
| 符合条件 | 要求用户在使用角色之前执行一项或多项操作的角色分配。 如果用户符合某个角色的条件，则意味着他们在需要执行特权任务时可以激活该角色。 用户无论具有永久角色分配还是合格角色分配，获得的访问权限并无差异。 唯一的差异在于，有些用户并不是一直需要该访问权限。 |
| 激活 | 合格用户在使用角色之前执行一项或多项操作的过程。 操作可能包括执行多重身份验证 (MFA) 检查、提供业务理由或请求获得指定审批者的批准。 |
| 实时 (JIT) 访问 | 一种访问模式。在此模式下，用户会收到执行特权任务的临时权限，防止恶意用户或未授权用户在权限过期后获得访问权限。 只有在用户需要的情况下，才会授予访问权限。 |
| 最低访问权限原则 | 一种建议的安全做法，仅为每个用户提供所需的最低权限，以便完成有权执行的任务。 此做法会尽量减少全局管理员的数目，并使用适合特定方案的特定管理员角色。 |

有关详细信息，请参见[术语](pim-configure.md#terminology)。

### <a name="high-level-overview-of-how-pim-works"></a>PIM 工作原理的综合概述

1. 已设置 PIM，以便用户有资格使用特权角色。
1. 当符合条件的用户需要使用特权角色时，可在 PIM 中激活角色。
1. 根据为角色配置的 PIM 设置，用户须完成某些步骤（例如执行多重身份验证、获得批准或指定原因。）
1. 用户成功激活角色后，会在预配时间段内获得相应角色。
1. 管理员可以查看审核日志中所有 PIM 活动的历史记录。 他们还可以进一步可以保护其租户并使用 PIM 功能（如访问评审和警报）来满足符合性要求。

有关详细信息，请参阅[什么是 Azure AD Privileged Identity Management？](pim-configure.md)。

### <a name="roles-that-can-be-managed-by-pim"></a>可以使用 PIM 管理的角色

**Azure AD 角色**– 这些角色都在 Azure Active Directory （如全局管理员、 Exchange 管理员和安全管理员）。 可通过 [Azure Active Directory 中的管理员角色权限](../users-groups-roles/directory-assign-admin-roles.md)，了解更多有关角色及其功能的信息。 如需帮助来确定向管理员分配哪个角色，请参阅[按任务划分的最小特权角色](../users-groups-roles/roles-delegate-by-task.md)。

Azure 资源角色 - 这些角色链接到 Azure 资源、资源组、订阅或管理组  。 PIM 同时提供对内置角色（如所有者、用户访问管理员和参与者）和[自定义角色](../../role-based-access-control/custom-roles.md)的实时访问。 有关 Azure 资源角色的详细信息，请阅读[基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md)。

有关详细信息，请参阅[无法在 PIM 中管理的角色](pim-roles.md)。

## <a name="step-2-plan-your-deployment"></a>步骤 2. 规划部署

本部分重点介绍在组织中部署 PIM 之前需要完成的操作。 请务必按照说明操作并理解本部分中的概念，这有助于为组织的特权标识制定最合适、最好的计划。

### <a name="identify-your-stakeholders"></a>标识利益干系人

以下部分可帮助标识与项目相关、需要签字、审核或随时了解情况的所有利益相干系人。 包括用于为 Azure AD 角色部署 PIM 和为 Azure 资源角色部署 PIM 的独立表格。 根据组织情况，将利益干系者添加到下表中。

- SO = 就此项目签字表示同意
- R = 审查此项目并提供输入
- I = 了解此项目的新情况

#### <a name="stakeholders-pim-for-azure-ad-roles"></a>利益干系人：适用于 Azure AD 角色的 PIM

| 名称 | 角色 | 操作 |
| --- | --- | --- |
| 姓名和电子邮件 | **标识架构师或 Azure 全局管理员**<br/>标识管理团队的一位代表，负责定义如何根据组织中的核心标识管理基础结构实施此更改。 | SO/R/I |
| 姓名和电子邮件 | **服务所有者/线路管理器**<br/>某项服务或一组服务的 IT 所有者的代表。 他们在制定决策和帮助推出团队 PIM 中发挥关键作用。 | SO/R/I |
| 姓名和电子邮件 | **安全所有者**<br/>安全团队的代表，可以签署确认计划符合组织的安全要求。 | SO/R |
| 姓名和电子邮件 | **IT 支持管理员/支持人员**<br/>IT 支持组织的代表，可以从服务支持的角度提供有关更改的可支持性的信息。 | R/I |
| 试点用户的姓名和电子邮件 | **特权角色用户**<br/>要为其实施特权标识管理的用户组。 他们需要知道 PIM 实施后如何激活角色。 | I |

#### <a name="stakeholders-pim-for-azure-resource-roles"></a>利益干系人：用于 Azure 资源角色 的 PIM

| 名称 | 角色 | 操作 |
| --- | --- | --- |
| 姓名和电子邮件 | **订阅/资源所有者**<br/>要为其部署 PIM 的每个订阅或资源的 IT 所有者的代表 | SO/R/I |
| 姓名和电子邮件 | **安全所有者**<br/>安全团队的代表，可以签署确认计划符合组织的安全要求。 | SO/R |
| 姓名和电子邮件 | **IT 支持管理员/支持人员**<br/>IT 支持组织的代表，可以从服务支持的角度提供有关更改的可支持性的信息。 | R/I |
| 试点用户的姓名和电子邮件 | **RBAC 角色用户**<br/>要为其实施特权标识管理的用户组。 他们需要知道 PIM 实施后如何激活角色。 | I |

### <a name="enable-pim"></a>启用 PIM

作为规划过程的一部分，须按照[开始使用 PIM 文档](pim-getting-started.md)中所述，先同意并启用 PIM。 通过启用 PIM，可以访问专为帮助部署而设计的某些功能。

如果目标是为 Azure 资源部署 PIM，则应按照[发现要在 PIM 文档中管理的 Azure 资源](pim-resource-roles-discover-resources.md)中所述操作。 只有每个资源、资源组和订阅的所有者才能够在 PIM 中发现它们。 如果你是全局管理员尝试部署的 Azure 资源的 PIM，则可[提升访问权限来管理所有 Azure 订阅](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)以允许您自己的目录中的所有 Azure 资源的访问发现。 但是，建议在使用 PIM 管理其资源之前先获取每个订阅所有者的批准。

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

1. 列出组织中有特权角色的人员。 可以使用 [PIM 向导](pim-security-wizard.md#run-the-wizard)来转到类似以下的页。

    ![发现特权的角色窗格，其中显示谁具有特权角色](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. 对于组织中的所有全局管理员，找出他们需要该角色的原因。 基于之前的文档，如果一个人员的工作可由一个或多个权限更为细化的管理员角色来执行，则应删除该人员的全局管理员角色，并在 Azure Active Directory 中实施相应更为细化的角色分配（参考信息：Microsoft 目前只有 10 个管理员拥有全局管理员角色。 通过 [Microsoft 如何使用 PIM](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)，了解详细信息）。

1. 对于所有其他 Azure AD 角色，查看分配列表，确定不再需要该角色的管理员，并在分配中将其删除。

若要自动执行步骤 3 和 4，可以利用 PIM 中的访问评审函数。 按照[为 PIM 中的 Azure AD 启动访问评审](pim-how-to-start-security-review.md)中的步骤操作，可以为每一个拥有一个或多个成员的 Azure AD 角色设置访问评审。

![创建 Azure AD 角色的访问评审窗格](./media/pim-deployment-plan/create-access-review.png)

应将审阅者设置为“成员(自我)”  。 执行此操作后，系统会向该角色中的所有成员发送一封电子邮件，确认其是否需要相关访问权限。 还应在高级设置中启用“需提供批准理由”，以便用户可以描述其需要该角色的原因  。 基于此信息，可将用户从不必要的角色中删除，如果该角色是全局管理员，则可以委派更细化的管理员角色。

访问评审依赖电子邮件来通知人员查看其角色访问权限。 如果拥有未链接电子邮件的特权帐户，请务必填写帐户中的备用电子邮件字段。 有关详细信息，请参阅 [Azure AD 中的 proxyAddresses 属性](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)。

#### <a name="azure-resource-roles"></a>Azure 资源角色

对于 Azure 订阅和资源，可以设置类似的访问评审流程，用于评审每个订阅或资源中的角色。 此过程的目标是最大程度地减少附加到每个订阅或资源的所有者和用户访问管理员分配量，以及删除不必要的分配。 但是，组织通常会将此类任务委派给每个订阅或资源的所有者，因为他们对特定角色（尤其是自定义角色）有更深入的了解。

如果你是具有全局管理员角色的 IT 管理员尝试部署你的组织中的 Azure 资源的 PIM，则可[提升访问权限来管理所有 Azure 订阅](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)以获取每个订阅的访问权限。 然后可以找到每个订阅的所有者，与其协作，删除不必要的分配，最大程度减少所有者角色分配量。

具有 Azure 订阅的所有者角色的用户还可以利用 [Azure 资源的访问评审](pim-resource-roles-start-access-review.md)来审核和删除不必要的角色分配，此过程类似于之前用于 Azure AD 角色的过程。

### <a name="decide-which-role-assignments-should-be-protected-by-pim"></a>确定 PIM 应保护哪些角色分配

在组织中清理特权角色分配后，需要决定使用 PIM 保护哪些角色。

如果某个角色受 PIM 保护，则分配给它的符合条件的用户必须提升权限才能使用该角色授予的权限。 权限提升过程还可能包括获取批准、执行多因素身份验证和/或提供相应的激活原因。 PIM 还可以通过通知、PIM 和 Azure AD 事件日志来跟踪提升进程。

要选择使用 PIM 保护哪些角色可能存在一定困难，而且每个组织的情况都不一样。 本部分提供 Azure AD 和 Azure资源角色的最佳做法建议。

#### <a name="azure-ad-roles"></a>Azure AD 角色

应侧重保护具有最多权限的 Azure AD 角色，这一点很重要。 基于 PIM 客户的使用模式，排在前 10 位的 PIM 管理的 Azure AD 角色为：

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
> :heavy_check_mark:Microsoft 建议首先使用 PIM 管理所有全局管理员和安全管理员，因为它们在受到攻击后造成的损失最大  。

请务必考虑哪些数据和权限对组织而言最为敏感。 例如，某些组织可能希望使用 PIM 保护其 Power BI 管理员角色或其团队管理员角色，因为这些角色能够访问数据和/或更改核心工作流。

如果有任何角色的成员中包含来宾用户，这种角色就特别容易受到攻击。

> [!TIP]
> :heavy_check_mark:Microsoft 建议使用 PIM 管理含有来宾用户的所有角色，以降低与来宾用户帐户相关的攻击风险  。

有时人们会认为目录读取者、消息中心读取者和安全读取者等读取者角色相比其他角色来说不那么重要，因为这些角色没有写入权限。 但我们已经了解到，某些客户也会保护这些角色，因为获得这些帐户访问权限的攻击者可能读取敏感数据，例如个人身份信息 (PII)。 在决定是否需要使用 PIM 管理组织中的读者角色时，应考虑到这一点。

#### <a name="azure-resource-roles"></a>Azure 资源角色

在决定应使用 PIM 为 Azure 资源管理哪些角色分配时，必须首先确定对组织而言最重要的订阅/资源。 此类订阅/资源的示例有：

- 托管最敏感数据的资源
- 核心的、面向客户的应用程序所依赖的资源

如果你是全局管理员，无法确定哪些订阅/资源最重要，应该与组织中的订阅所有者联系，收集每个订阅管理的资源的列表。 然后应与订阅所有者合作，基于其所面临风险的严重性级别（低，中，高）对资源进行分组。 应根据此严重性级别确定优先使用 PIM 管理的资源。

> [!TIP]
> :heavy_check_mark:Microsoft 建议与关键服务的订阅/资源所有者合作，为敏感订阅/资源中的所有角色设置 PIM 工作流  。

Azure 资源的 PIM 支持时限服务帐户。 应以对待常规用户帐户的方式来对待服务帐户。

对于非关键的订阅/资源，不需要为所有角色设置 PIM。 但是，仍应使用 PIM 保护所有者和用户访问管理员角色。

> [!TIP]
> :heavy_check_mark:Microsoft 建议使用 PIM 管理所有订阅/资源的所有者角色和用户访问管理员角色  。

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>确定哪些角色分配应为永久分配或合格分配

要使用 PIM 管理的角色的列表一经确定，须决定应将合格角色与永久活动角色分别分配给哪些用户。 永久活动角色是通过 Azure Active Directory 和 Azure 资源分配的常规角色，而合格角色只能在 PIM 中分配。

> [!TIP]
> :heavy_check_mark:Microsoft 建议不向 Azure AD 角色和 Azure 资源角色分配永久活动角色，但推荐的[两个紧急访问帐户](../users-groups-roles/directory-emergency-access.md)除外，这两个帐户应具有永久的全局管理员角色  。

虽然我们建议不设立长期管理员，但组织有时很难立即实现这一目标。 下面是做出此决定时要考虑的事项：

- 提升频率 - 如果用户只需要一次特权分配，他们不应拥有永久分配。 另一方面，如果用户需要某个角色来处理日常工作且使用 PIM 会极大降低其工作效率，则可考虑向其分配永久角色。
- 特定于组织的案例 - 如果被授予合格角色的人员来自远程团队或为高级管理人员，不便沟通和执行提升过程，则可考虑向其分配永久角色。

> [!TIP]
> :heavy_check_mark:Microsoft建议为分配了永久角色的用户（如果有）设置定期访问评审  。 可通过此部署计划的最后一部分了解有关定期访问评审的详细信息

### <a name="draft-your-pim-settings"></a>草拟 PIM 设置

实现 PIM 解决方案之前，一个很好的做法是为组织使用的每一个特权角色草拟 PIM 设置。 本部分包含一些用于特定角色的 PIM 设置的示例（仅供参考，与组织所需可能不同）。 表格详细说明了前面提及的每一个设置并在表格后面提供了相关 Microsoft 建议。

#### <a name="pim-settings-for-azure-ad-roles"></a>Azure AD 角色的 PIM 设置

| 角色 | 要求 MFA | 通知 | 事件工单 | 需要审批 | 审批者 | 激活持续时间 | 永久管理员 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 全局管理员角色 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 其他全局管理员 | 1 小时 | 紧急访问帐户 |
| Exchange 管理员 | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | 无 | 2 小时 | 无 |
| 支持管理员 | :x: | :x: | :heavy_check_mark: | :x: | 无 | 8 小时 | 无 |

#### <a name="pim-settings-for-azure-resource-roles"></a>Azure 资源角色的 PIM 设置

| 角色 | 要求 MFA | 通知 | 需要审批 | 审批者 | 激活持续时间 | 活动管理员 | 活动期限 | 合格期限 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 关键订阅的所有者 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 订阅的其他所有者 | 1 小时 | 无 | 不适用 | 3 个月 |
| 次要订阅的用户访问管理员 | :heavy_check_mark: | :heavy_check_mark: | :x: | 无 | 1 小时 | 无 | 不适用 | 3 个月 |
| 虚拟机参与者 | :x: | :heavy_check_mark: | :x: | 无 | 3 个小时 | 无 | 不适用 | 6 个月 |

下表说明了每个设置。

| 设置 | 描述 |
| --- | --- |
| 角色 | 要为其定义设置的角色的名称。 |
| 要求 MFA | 符合条件的用户是否需要在激活角色之前执行 MFA。<br/><br/> :heavy_check_mark:Microsoft 建议为所有管理员角色强制执行 MFA，尤其是在角色含有来宾用户的情况下  。 |
| 通知 | 如果设置为 true，当符合条件的用户激活角色时，组织中的全局管理员、特权角色管理员和安全管理员会收到电子邮件通知。<br/><br/>**注意：** 某些组织没有向管理员帐户绑定电子邮件地址来获取这些电子邮件通知，应该设置一个备用电子邮件地址，以便管理员收到这些电子邮件。 |
| 事件工单 | 激活角色时符合条件的用户需要记录事件工单号。 此设置可帮助组织使用内部事件编号识别每次激活，以减少无效激活的次数。<br/><br/> :heavy_check_mark:Microsoft 建议利用事件工单号将 PIM 绑定到内部系统  。 这对于需要激活操作上下文的审批者而言特别有用。 |
| 需要审批 | 符合条件的用户是否需要获取批准才能激活角色。<br/><br/> :heavy_check_mark:Microsoft 建议为具有最多权限的角色设置审批操作  。 基于全体 PIM 客户的使用模式，全局管理员、用户管理员、Exchange 管理员、安全管理员和密码管理员是最常应用审批操作的角色。 |
| 审批者 | 如果符合条件的角色需要执行审批操作来进行激活，应列出批准请求的人员。 默认情况下，PIM 将审批者设置为身份是特权角色管理员的所有用户，无论他们是永久性角色还是合格角色。<br/><br/>**注意：** 如果用户同时有资格拥有 Azure AD 角色和该角色的审批者身份，则无法为自己执行审批操作。<br/><br/> :heavy_check_mark:Microsoft 建议所选择的审批者应为最了解特定角色及其常规用户的人员，而非拥有全局管理员角色的人员  。 |
| 激活持续时间 | 在角色到期之前，用户拥有该角色的有效期。 |
| 永久管理员 | 将成为某个角色的永久管理员的用户的列表（永远不必激活）。<br/><br/> :heavy_check_mark:Microsoft 建议所有角色（全局管理员除外）均不设立长期管理员  。 请参阅本计划的“应向谁分配合格角色”和“应向谁分配永久活动角色”部分，了解更多相关信息。 |
| 活动管理员 | 对于 Azure 资源，活动管理员是永远不必激活角色便可使用角色的用户的列表。 与 Azure AD 角色中的不同，该角色不称为永久管理员，因为可以设置此角色的失效时间。 |
| 活动期限 | 此配置中的时限到期后，Azure 资源角色的活动角色分配将失效。 可以选择 15 天、1 个月、3 个月、6 个月、1 年或永久活动。 |
| 合格期限 | 此配置中的时限到期后，Azure 资源角色的合格角色分配将失效。 可以选择 15 天、1 个月、3 个月、6 个月、1 年或永久符合条件。 |

## <a name="step-3-implement-your-solution"></a>步骤 3. 实施解决方案

正确规划是使用 Azure Active Directory 成功部署应用程序的基础。  它提供智能安全性和集成，简化载入流程，同时减少成功部署的时间。  此特性可确保轻松集成应用程序，同时减少最终用户的停机时间。

### <a name="identify-test-users"></a>标识测试用户

借助此部分标识一组用户和/或用户组以验证实现。 基于在规划部分中选择的设置，确定要为每个角色测试的用户。

> [!TIP]
> :heavy_check_mark:Microsoft 建议将每个 Azure AD 角色的服务所有者设为测试用户，以便其能够熟悉该流程并成为解决方案的内部支持者  。

在此表中，标识测试用户，这些用户将验证每个角色的设置是否有效。

| 角色名称 | 测试用户 |
| --- | --- |
| &lt;角色名称&gt; | &lt;要测试角色的用户&gt; |
| &lt;角色名称&gt; | &lt;要测试角色的用户&gt; |

### <a name="test-implementation"></a>测试实现

现已标识测试用户，请使用此步骤为测试用户配置 PIM。 如果组织要将 PIM 工作流并入内部的应用程序中，而不是使用 Azure 门户内的 PIM 用户界面，[图形 API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root) 也支持 PIM 中的所有操作。

#### <a name="configure-pim-for-azure-ad-roles"></a>为 Azure AD 角色配置 PIM

1. [配置 Azure AD 角色设置](pim-how-to-change-default-settings.md)基于您的计划。

1. 导航到“Azure AD 角色”，单击“角色”，然后选择刚配置的角色   。

1. 对于测试用户组，如果他们已经是永久管理员，可以将其转换为合格管理员，方法是单击其行上的三个点搜索他们并将其从永久性角色转换为合格角色。 如果他们还未分配有角色，可以[实施新的合格角色分配](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role)。

1. 为所有想要测试的角色重复步骤 1-3。

1. 测试用户一经设置，应向其发送有关如何[激活 Azure AD 角色](pim-how-to-activate-role.md)的链接。

#### <a name="configure-pim-for-azure-resource-roles"></a>配置 Azure 资源角色 的 PIM

1. 为想要测试的订阅或资源中的角色[配置 Azure 资源角色设置](pim-resource-roles-configure-role-settings.md)。

1. 导航到“Azure 资源”查找该订阅并单击“角色”，选择刚配置的角色   。

1. 对于测试用户组，如果他们已是活动管理员，可以通过搜索他们并[更新其角色分配](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment)，将其转变为合格管理员。 如果他们还没有角色，可以[分配新角色](pim-resource-roles-assign-roles.md#assign-a-role)。

1. 为所有想要测试的角色重复步骤 1-3。

1. 测试用户一经设置，应向其发送有关如何[激活 Azure 资源角色](pim-resource-roles-activate-your-roles.md)的链接。

应运用此阶段来验证为角色设置的所有配置是否均正常工作。 使用下表来记录测试。 应运用此阶段来优化与受影响用户之间的通信。

| 角色 | 激活期间的预期行为 | 实际结果 |
| --- | --- | --- |
| 全局管理员角色 | (1) 要求 MFA<br/>(2) 需要审批<br/>(3) 审批者收到通知，并且可以批准<br/>(4) 角色在预设时间后过期 |  |
| 订阅所有者 X  | (1) 要求 MFA<br/>(2) 分配的合格角色将在配置的时间段后过期 |  |

### <a name="communicate-pim-to-affected-stakeholders"></a>与受影响的利益干系人就 PIM 进行沟通

部署 PIM 需要特权角色用户执行额外操作步骤。 虽然 PIM 极大地减少了与特权标识相关的安全问题，但在实施租户范围内的部署之前，需要有效地传达相关变更。 根据受影响的管理员数量，组织通常会选择创建有关更改的内部文档、视频或电子邮件。 这些通信内容中通常包含：

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
> :heavy_check_mark:Microsoft 建议你与支持团队确定时间，让其完整了解 PIM 工作流（如果组织有内部 IT 支持团队）  。 为他们提供合适的文档和联系信息。

### <a name="move-to-production"></a>移到生产环境

一旦测试完成并成功，将 PIM 移至生产，方法是为 PIM 配置中定义的每个角色的所有用户重复测试阶段的所有步骤。 对于面向 Azure AD 角色的 PIM，组织通常会先为全局管理员测试和推出 PIM，然后再为其他角色测试和推出 PIM。 同时，对于 Azure 资源，组织通常一次为一个 Azure 订阅测试和推出 PIM。

### <a name="in-the-case-a-rollback-is-needed"></a>需要回滚的情况

如果 PIM 在生产环境中未按预期运行，可借助以下回滚步骤，恢复到设置 PIM 之前时的某个已知的良好状态：

#### <a name="azure-ad-roles"></a>Azure AD 角色

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 打开“Azure AD Privileged Identity Management”。 
1. 单击“Azure AD 角色”，然后单击“角色”   。
1. 对于每个已配置的角色，为所有拥有合格角色的用户单击省略号 (…)  。
1. 单击“指定永久”选项，将角色分配转换为永久分配  。

#### <a name="azure-resource-roles"></a>Azure 资源角色

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 打开“Azure AD Privileged Identity Management”。 
1. 单击“Azure 资源”，然后单击想要回滚的订阅或资源  。
1. 单击“角色”。 
1. 对于每个已配置的角色，为所有拥有合格角色的用户单击省略号 (…)  。
1. 单击“指定永久”选项，将角色分配转换为永久分配  。

## <a name="step-4-next-steps-after-deploying-pim"></a>步骤 4. 部署 PIM 后的后续步骤

在生产中成功部署 PIM 意味着在保护组织特权标识方面迈出了重要一步。 通过部署 PIM，可额外获取用于实现安全性和符合性的 PIM 功能。

### <a name="use-pim-alerts-to-safeguard-your-privileged-access"></a>可使用 PIM 警报来保护特权访问

应利用 PIM 的内置警报功能更好地保护租户。 有关详细信息，请参阅[安全警报](pim-how-to-configure-security-alerts.md#security-alerts)。 这些警报包括：管理员不使用特权角色、角色被分配到 PIM 之外、角色激活过于频繁等等。 若要充分保护组织，应定期查看警报列表，并解决问题。 可以使用以下方法查看和解决警报问题：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 打开“Azure AD Privileged Identity Management”。 
1. 单击“Azure AD 角色”，然后单击“警报”   。

> [!TIP]
> :heavy_check_mark:Microsoft 建议应立即处理所有标记为高严重级别的警报  。 对于严重性为中等和低级别的警报，应随时获取最新相关信息并在认为存在安全威胁时采取措施。

如果某个警报无有用信息或不适用于组织，可以在通知页上将其消除。 且之后可以在 Azure AD 设置页中撤销此消除操作。

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>设置定期访问评审以定期审核组织的特权标识

访问评审是向分配有特权角色的用户或特定审阅者询问每个用户是否需要特权身份的最佳方式。 如果想要减少攻击面并保持符合性，访问评审非常有用。 有关启动访问评审的详细信息，请参阅 [Azure AD 角色访问评审](pim-how-to-start-security-review.md)和 [Azure 资源角色访问评审](pim-resource-roles-start-access-review.md)。 某些组织为遵守法律和法规，需要执行定期访问评审，而对于其他组织，访问评审是在整个组织中实施最低权限原则的最佳方式。

> [!TIP]
> :heavy_check_mark:Microsoft 建议为所有 Azure AD 和 Azure 资源角色设置季度访问评审  。

在大多数情况下，Azure AD 角色的评审者是用户自己，而 Azure 资源角色的评审者是该角色所在订阅的所有者。 但是，通常情况下公司会拥有未与任何人员的电子邮件地址关联的特权帐户。 在这种情况下，无人审阅访问权限。

> [!TIP]
> :heavy_check_mark:Microsoft 建议为所有拥有特权角色但未与定期检查的电子邮件地址相关联的帐户添加备用电子邮件地址 

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>充分利用审核日志，以提升安全性和符合性

借助审核日志，可始终掌握最新动态和遵守相关法规。 PIM 目前在其审核日志中存储 30 天的完整组织历史记录，包括：

- 合格角色的激活/停用
- PIM 内部和外部的角色分配活动
- 角色设置更改
- 通过批准设置，请求/批准/拒绝角色激活活动
- 警报更新

全局管理员或特权角色管理员可以访问这些审核日志。 有关详细信息，请参阅 [Azure AD 角色的审核历史记录](pim-how-to-use-audit-log.md)和 [Azure 资源角色的审核历史记录](azure-pim-resource-rbac.md)。

> [!TIP]
> :heavy_check_mark:Microsoft 建议至少让一名管理员每周阅读所有审核事件，并按月导出审核事件  。

如果要在较长时间内自动存储审核事件，PIM 的审核日志将自动同步到 [Azure AD 审核日志](../reports-monitoring/concept-audit-logs.md)中。

> [!TIP]
> :heavy_check_mark:Microsoft 建议设置 [Azure 日志监视](../reports-monitoring/concept-activity-logs-azure-monitor.md)以在 Azure 存储帐户中存档审核事件，以满足安全性和符合性方面的需要  。
