---
title: 规划 Azure Active Directory 访问评审部署
description: 成功进行访问评审部署所需的规划指南
services: active-directory
documentationCenter: ''
author: BarbaraSelden
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 08/14/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51983e52643d587fc497a1a67fcb0cd29dbb7f17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91306559"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>规划 Azure Active Directory 访问评审部署

使用 [Azure Active Directory (Azure AD) 访问评审](access-reviews-overview.md)，可以通过管理[资源访问生命周期](identity-governance-overview.md)来帮助组织提高网络安全性。 使用访问评审，你可以：

* 计划定期评审或进行临时评审，看谁有权访问特定资源（例如应用程序和组）

* 跟踪见解、合规性或策略原因的评审

* 将评审委托给特定管理员、业务所有者或可以自证需要持续访问权限的最终用户

* 根据见解来有效确定用户是否应继续拥有访问权限

* 自动生成评审结果，例如删除用户对资源的访问权限

  ![显示访问评审流的关系图。](./media/deploy-access-review/1-planning-review.png)

访问评审是一项 [Azure AD Identity Governance](identity-governance-overview.md) 功能。 其他功能包括： [权利管理](entitlement-management-overview.md)、 [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 和 [使用条款](../conditional-access/terms-of-use.md)。 将它们配合使用，可帮助组织解决以下四个问题：

* 哪些用户应该有权访问哪些资源？

* 这些用户使用该访问权限做些什么？

* 是否存在用于管理访问权限的有效组织控制措施？

* 审核员是否可以验证控制措施是否正常实施？

规划访问评审部署对确保你为组织中的用户实现所需的治理策略至关重要。

### <a name="key-benefits"></a>主要优点

启用访问评审的主要优点包括：

* **控制协作**。 访问评审允许组织管理对其用户所需的所有资源的访问权限。 可以确保当组织的用户进行共享和协作时，只在授权用户之间传输信息。

* **管理风险**。 访问评审为组织提供了一种评审对数据和应用程序的访问权限的方法，降低了数据泄露和数据溢出的风险。 这包括用于定期评审外部合作伙伴对公司资源的访问权限的功能。 

* **解决合规性与治理问题**。 使用访问评审，你可以调控和重新认证对组、应用和站点的访问权限的生命周期。 可以控制和跟踪特定于组织的合规性或风险敏感应用程序的评审。 

* **降低成本**。 访问评审内置于云中，并且原生可与云资源（例如组、应用程序和访问包）配合使用。 与构建你自己的工具或升级本地工具集相比，使用访问评审的成本更低。

### <a name="training-resources"></a>训练资源

可通过以下视频了解有关访问评审的信息：

* [What are Access Reviews in Azure AD?](https://youtu.be/kDRjQQ22Wkk)（Azure AD 中的访问评审是什么？）

* [How to create Access Reviews in Azure AD](https://youtu.be/6KB3TZ8Wi40)（如何在 Azure AD 中创建访问评审）

* [How to enable Access Reviews in Azure AD](https://youtu.be/X1SL2uubx9M)（如何在 Azure AD 中启用访问评审）

* [How to review access using My Access](https://youtu.be/tIKdQhdHLXU)（如何使用“我的访问权限”评审访问权限）

### <a name="licenses"></a>许可证

你需要一个有效的 Azure AD Premium (P2) 许可证，用于将创建或执行访问评审的每个用户（不是全局管理员或用户管理员）。 有关详细信息，请参阅[访问评审许可证要求](access-reviews-overview.md)。

可能还需要其他 Identity Governance 功能，例如[权利生命周期管理](entitlement-management-overview.md)或 Privileged Identity Management。 在这种情况下，可能还需要相关的许可证。 有关详细信息，请参阅 [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="plan-the-access-reviews-deployment-project"></a>规划访问评审部署项目

请考虑组织的需求，以便确定用于在环境中部署访问评审的策略。

### <a name="engage-the-right-stakeholders"></a>让合适的利益干系人参与

如果技术项目失败，它们通常是由于在影响、结果和责任方面不符合预期而导致的。 为避免这些缺陷，请[确保你正在吸引正确的利益干系人](https://aka.ms/deploymentplans)，并确保项目角色明确。

对于访问评审，可能需包括组织中以下团队的代表：

* **It 管理** 管理 it 基础结构，管理云投资和软件即服务 (SaaS) 应用。 该团队将：

   * 评审对基础结构和应用（包括 Microsoft 365 和 Azure AD）的特权访问权限。

   * 在用于维护例外列表或 IT 试点项目的组上计划和运行访问评审，以维护最新的访问权限列表。

   * 确保调控和评审通过服务主体以编程方式（以编写脚本的方式）对资源进行的访问。

* **开发团队**：为组织构建和维护应用程序。 该团队将：

   * 控制谁可以访问和管理 SaaS、PaaS 和 IaaS 资源中构成开发解决方案的组件。

   * 管理可访问应用程序和工具（用于内部应用程序开发）的组。

   * 需要特权标识，这些标识有权访问为客户托管的生产软件或解决方案

* **业务单位**：管理项目和自己的应用程序。 该团队将： 

   * 评审并批准或拒绝授予内部和外部用户对组和应用程序的访问权限。

   * 计划和执行评审，以证明员工和外部标识（如业务合作伙伴）需要持续的访问权限。

* **公司治理**：确保组织遵循内部策略并遵守法规。 该团队将：

   * 请求或计划新的访问评审。

   * 评估用于评审访问权限的流程和过程，包括那些其保存目的是为了确保合规性的文档和记录。

   * 对大多数关键资源过去的评审结果进行评审。

> [!NOTE]
> 对于需要手动评估的评审，请确保做好规划，使审阅者人数足够且评审周期符合策略和合规性需要。 如果评审周期太短，或审阅者人数太少，则可能导致评审质量受损，有权访问的人员过多或过少。 

### <a name="plan-communications"></a>规划沟通

沟通对于任何新业务流程的成功至关重要。 主动与用户交流其体验会如何变化、何时会变化以及在遇到问题时如何获取支持。 

#### <a name="communicate-changes-in-accountability"></a>交流责任变化情况

访问评审支持将评审和操作持续访问权限的职责转移给业务所有者。 将访问决策从 IT 部门分离出来有助于更准确地做出访问决策。 这是资源所有者的责任和职责在文化方面的变化。 主动告知此变化，确保资源所有者经过培训并能够根据见解做出合理的决策。

很显然，IT 部门需要对所有与基础结构相关的访问决策和特权角色分配保持控制。 

#### <a name="customize-email-communication"></a>自定义电子邮件通信

计划某个评审时，可指定将执行该评审的用户。 然后，这些审阅者会收到一封关于已向他们分配的新评审的电子邮件通知，并且会在分配给他们的评审过期之前收到提醒。

管理员可以选择在评审过期时间还剩一半的时候发送该通知，也可以选择在过期之前的一天发送。 

可以自定义发送给审阅者的电子邮件，使其包含一条建议他们对评审进行操作的自定义短消息。 建议使用附加文本来执行以下操作：

* 包括一条要发送给审阅者的个人消息，让他们知道这是由合规部门或 IT 部门发送的。

* 包括有关评审预期的内部信息的超链接或引用，以及其他参考或培训材料。

* 包括一个有关[如何对访问权限进行自我评审](review-your-access.md)的说明的链接。 

  ![审阅者电子邮件](./media/deploy-access-review/2-plan-reviewer-email.png)

选择“开始评审”后，审阅者会被转到 [myAccess 门户](https://myapplications.microsoft.com/)进行组和应用程序访问评审。 审阅者可以通过该门户概览有权访问他们所评审资源的所有用户，以及基于上次登录和访问信息的系统建议。

### <a name="plan-a-pilot"></a>规划试点

我们鼓励客户最初使用一个小组对访问评审进行试点，并将非关键资源作为目标。 试点有助于根据需要调整流程和通信，并增强用户和审阅者满足安全性和合规性要求的能力。

在试点中，我们建议：

* 从那些不自动应用结果并且你可以控制其影响的评审开始。

* 确保所有用户的有效电子邮件地址都在 Azure AD 中列出，并且所有用户都收到执行相应操作所需的电子邮件通信内容。 

* 记录在试点过程中删除的任何访问权限，以备快速还原之需。

* 监视审核日志以确保对所有事件进行适当的审核。

有关详细信息，请参阅 [试验的最佳实践](../fundamentals/active-directory-deployment-plans.md)。

## <a name="introduction-to-access-reviews"></a>访问评审简介

本部分介绍在规划评审之前应了解的访问评审概念。

### <a name="what-resource-types-can-be-reviewed"></a>可以评审哪些资源类型？

将组织的资源（例如用户、应用程序和组）与 Azure AD 集成后，即可对其进行管理和评审。 

典型的评审目标包括：

* [与单一登录 (Azure AD 集成的应用程序](../manage-apps/what-is-application-management.md) ，如 SaaS、业务线) 。

* 组[成员身份](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)（同步到 Azure AD，或者在 Azure AD 或 Microsoft 365 中创建，包括 Microsoft Teams）。

* [访问包](/azure/active-directory/governance/entitlement-management-overview)：用于将资源（组、应用和站点）分组到单个包中以管理访问权限。

* [Azure AD 角色和 Azure 资源角色](../privileged-identity-management/pim-resource-roles-assign-roles.md)：详见 Privileged Identity Management 中的定义。

### <a name="who-will-create-and-manage-access-reviews"></a>谁将创建和管理访问评审？

创建、管理或读取访问评审所需的管理角色取决于要评审的资源的类型。 下表指明了每种资源类型所需的角色：

| 资源类型| 创建和管理访问评审（创建者）| 读取访问评审结果 |
| - | - | -|
| 组或应用程序| 全局管理员 <p>用户管理员| 创建者和安全管理员 |
| Azure AD 中的特权角色| 全局管理员 <p>特权角色管理员| 创建者 <p>安全读取者<p>安全管理员 |
| Azure 中的特权角色（资源）| 全局管理员<p>用户管理员<p>资源所有者| 创建者 |
| 访问包| 全局管理员<p>访问包的创建者| 仅限全局管理员 |


有关详细信息，请参阅 [Azure Active Directory 中的管理员角色权限](../users-groups-roles/directory-assign-admin-roles.md)。

### <a name="who-will-review-the-access-to-the-resource"></a>谁将评审对资源的访问权限？

访问评审的创建者在创建时决定谁将进行评审。 在开始评审后，此设置将无法更改。 审阅者由三个角色代表：

* 资源所有者，即资源的业务所有者。

* 一组单独选择的委托，由访问评审管理员选择。

* 将要分别自证需要持续访问权限的最终用户。

创建访问评审时，管理员可以选择一个或多个审阅者。 所有审阅者都可以启动并执行评审，选择可以持续访问某个资源的用户或将其删除。 

### <a name="components-of-an-access-review"></a>访问评审的组件

在实施访问评审之前，应规划与组织相关的评审类型。 为此，你需要做出有关要评审的内容以及基于这些评审而要采取的措施的业务决策。

若要创建访问评审策略，必须具备以下信息。

* 要评审的资源是什么？

* 要评审谁的访问权限。

* 应该多久评审一次？

* 谁将执行此评审？

   * 如何通知他们进行评审？

   * 要强制执行哪些评审时间线？

* 应根据评审强制执行哪些自动操作？

   * 如果审阅者没有及时响应，会发生什么情况？

* 将会根据评审情况采取哪些手动操作？

* 应根据所采取的操作发送哪些通信内容？


**访问评审计划示例**

| 组件| “值” |
| - | - |
| **要评审的资源**| 对 Microsoft Dynamics 的访问权限 |
| **评审频率**| 每月一次 |
| **谁执行评审**| Dynamics 业务组项目经理 |
| **通知**| 在评审前的 24 小时内向 Dynamics-Pms（别名）发送电子邮件<p>包括向审阅者发出的鼓励性自定义消息以获得其认同 |
| **时间线**| 通知发出后的 48 小时内 |
|**自动操作**| 删除在 90 天内没有交互式登录的任何帐户的访问权限，方法是：从安全组 dynamics access 中删除用户。 <p>如果未在时间线内进行评审，则执行相关操作。 |
| **手动操作**| 审阅者可以根据需要在自动操作之前执行删除审批。 |
| **通信**| 向被删除的内部（成员）用户发送一封电子邮件，将他们已被删除的事实告知他们，并说明如何重新获得访问权限。 |


 

### <a name="automate-actions-based-on-access-reviews"></a>基于访问评审自动执行操作

可以选择自动删除访问权限，方法是：将“将结果自动应用到资源”选项设置为“启用”。

  ![规划访问评审](./media/deploy-access-review/3-automate-actions-settings.png)

完成评审并结束后，审阅者未批准的用户将自动从资源中删除，或者保留为持续访问。 这可能意味着删除其组成员身份或其应用程序分配，或者撤消其提升到特权角色所需的权限。

采用建议

建议会在审阅者体验过程中显示给审阅者，并会指示用户上次登录到租户的时间，或上次访问应用程序的时间。 此信息有助于审阅者做出正确的访问决策。 如果你选择“采用建议”，系统将遵循访问评审的建议。 在访问评审结束时，系统会自动为审阅者未对其进行响应的用户应用这些建议。

建议基于访问评审中的条件。 例如，如果你将评审配置为在 90 天内没有交互式登录时删除访问权限，它会建议你删除符合该条件的所有用户。 Microsoft 始终致力于改进建议。 

### <a name="review-guest-user-access"></a>评审来宾用户访问权限

使用访问评审可评审和清理来自外部组织的协作伙伴的标识。 配置按合作伙伴进行的评审即可满足合规性要求。

可通过以下操作之一向外部标识授予对公司资源的访问权限：

* 添加到组 

* 邀请到 Teams 

* 分配到企业应用程序或访问包

* 在 Azure AD 或 Azure 订阅中分配一个特权角色

请参阅[示例脚本](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)。 此脚本会显示在何处使用受邀加入租户的外部标识。 你可以在 Azure AD 中查看外部用户的组成员身份、角色分配和应用程序分配。 此脚本不会显示 Azure AD 之外的任何分配，例如无需使用组即可针对 Sharepoint 资源完成的直接权限分配。

创建组或应用程序的访问评审时，可以选择让审阅者专注于具有访问权限的“所有人”或“仅来宾用户”。 如果选择“仅来宾用户”，系统会为审阅者提供一个集中列表，其中包含 Azure AD B2B 中有权访问资源的外部标识。

 ![评审来宾用户](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> 这不会包括其 userType 为“成员”的外部成员。 这也不会包括在 Azure AD B2B 协作范围外邀请的用户，例如那些直接通过 SharePoint 访问共享内容的用户。

## <a name="plan-access-reviews-for-access-packages"></a>规划访问包的访问评审

[访问包](entitlement-management-overview.md)可以极大地简化治理和访问评审策略。 访问包是一个捆绑包，其中包含的所有资源所具有的访问权限是用户在处理项目或执行其任务时需要的。 例如，你可能希望创建一个访问包，其中包含组织中的开发人员所需的所有应用程序，或外部用户应该有权访问的所有应用程序。 然后，管理员或受委托的访问包管理员会将资源（组或应用）以及用户需要将其用于这些资源的角色分组。

[创建访问包](entitlement-management-access-package-create.md)时，可以创建一个或多个访问策略，用于设置用户请求访问包的条件、审批流程的外观，以及人员需要重新请求访问权限的频率。 访问评审在创建或编辑访问包策略时配置。

打开“生命周期”选项卡即可向下滚动到“访问评审”。

 ![屏幕截图，显示 "生命周期" 选项卡中的 "编辑策略"。](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>为组规划访问评审

除访问包外，评审组成员身份是管理访问权限的最有效方法。 建议通过[安全组或 Microsoft 365 组](../fundamentals/active-directory-manage-groups.md)分配资源访问权限，并将用户添加到这些组以获取访问权限。

可以为单个组授予对所有相应资源的访问权限。 可以为该组分配对各个资源的访问权限，或者为其分配对某个已将应用程序和其他资源组合在一起的访问包的权限。 使用此方法，你可以评审单个用户对组的访问权限，而不是评审其对每个应用程序的访问权限。 

可由以下人员评审组成员身份： 

* Administrators

* 组所有者

* 选定用户，在创建评审时委托了评审权限

* 组的成员，允许自证

### <a name="group-ownership"></a>组所有权

建议组所有者对成员身份进行评审，因为他们最了解谁需要访问权限。 组的所有权因组的类型而异：

在 Microsoft 365 和 Azure AD 中创建的组有一个或多个已明确定义的所有者。 大多数情况下，这些所有者是他们自己的组的理想的审阅者，因为他们知道谁应该有访问权限。 

例如，Microsoft Teams 使用 Microsoft 365 组作为基础授权模型，为用户授予对 SharePoint、Exchange、OneNote 或其他 Microsoft 365 服务中的资源的访问权限。 团队的创建者自动成为所有者，并应负责证明该组的成员身份。 

在 Azure AD 门户中手动创建的组或通过 Microsoft Graph 使用脚本创建的组可能不必定义所有者。 建议你通过 Azure AD 门户在组的“所有者”部分中定义它们，或通过 Graph 来这样做。

从本地 Active Directory 同步的组在 Azure AD 中不能有所有者。 为他们创建访问评审时，应选择最适合决定其成员身份的个人。

> [!NOTE]
> 建议在定义业务策略时定义组的创建方式，务必明确组所有权和责任，以便对成员身份进行定期评审。 

### <a name="review-membership-of-exclusion-groups-in-ca-policies"></a>评审 CA 策略中排除组的成员身份 

有时，不应将旨在确保网络安全的条件访问 (CA) 策略应用到所有用户。 例如，仅允许用户在公司网络上登录的 CA 策略可能不适用于需要到各处出差的销售团队。 在这种情况下，需将销售团队成员置于一个从 CA 策略中排除的组中。 

请定期评审此类组成员身份，因为如果从要求中排除了错误的成员，则排除意味着潜在风险。

可以[使用 Azure AD 访问评审管理从条件访问策略中排除的用户](conditional-access-exclusion.md)。

### <a name="review-external-users-group-memberships"></a>评审外部用户的组成员身份

若要最大程度地减少手动工作和相关的潜在错误，请考虑使用 [动态组](../users-groups-roles/groups-create-rule.md) 基于用户的属性分配组成员身份。 可能需要为外部用户创建一个或多个动态组。 内部赞助商可以充当组中成员身份的审阅者。 

注意：由于访问评审而从组中删除的外部用户不会从租户中删除。 

可以通过手动方式或脚本方式将他们从租户中删除。

### <a name="review-access-to-on-premises-groups"></a>评审对本地组的访问权限

访问评审无法更改通过 [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) 从本地同步的组的组成员身份。 这是因为授权来源在本地。

你仍然可以使用访问评审来计划和维护本地组的定期评审。 然后，审阅者会在本地组中执行操作。 此策略始终将访问评审作为所有评审的工具。

可以在本地组上使用访问评审的结果，并通过以下方式之一进一步处理它们：

* 从访问评审下载 CSV 报表并手动执行操作。

* 使用 Microsoft Graph 以编程方式访问已完成的访问评审中的结果和决策。

例如，若要访问 Windows AD 托管组的结果，请使用此 [PowerShell 示例脚本](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)。 此脚本概述了所需的 Graph 调用，并导出了 Windows AD-PowerShell 命令来执行更改。

## <a name="plan-access-reviews-for-applications"></a>为应用程序规划访问评审 

评审对应用程序的访问权限时，需评审员工和外部标识对应用程序中的信息和数据的访问权限。 如果需要知道谁有权访问特定的应用程序，请选择评审应用程序，而不是评审访问包或组。 

在以下情况下，建议为应用程序规划评审：

* 为用户授予对应用程序（在组或访问包的外部）的直接访问权限。

* 应用程序公开了关键或敏感信息。

* 应用程序具有特定的合规性要求，对于是否符合该要求，你必须进行证明。

* 你怀疑存在访问权限不当的问题。

若要为应用程序创建访问评审，请将“需要进行用户分配?”属性设置为“是”。 如果将其设置为“否”，则目录中的所有用户（包括外部标识）都可以访问应用程序，你无法评审对应用程序的访问权限。 

 ![规划应用分配](./media/deploy-access-review/6-plan-applications-assignment-required.png)

然后，你必须根据需要[分配用户和组](../manage-apps/assign-user-or-group-access-portal.md)，使之具有访问权限。 

### <a name="reviewers-for-an-application"></a>应用程序的审阅者

访问评审的对象可以是组成员，或者是已分配到应用程序的用户。 Azure AD 中的应用程序不一定具有所有者，这就是无法选择应用程序所有者作为审阅者的原因。 可将评审范围进一步限定为仅评审分配到应用程序的来宾用户，而不是评审所有访问权限。

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>规划 Azure AD 和 Azure 资源角色的评审

[Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) 简化了企业对 Azure AD 中的资源的特权访问管理方式。 这样可以大大缩小 [Azure AD](../users-groups-roles/directory-assign-admin-roles.md) 和 [Azure 资源](../../role-based-access-control/built-in-roles.md)中的特权角色的列表，提高目录的总体安全性。

访问评审允许审阅者证实用户是否仍需担任某个角色。 与访问包的访问评审一样，Azure AD 角色和 Azure 资源的评审已集成到 PIM 管理员用户体验中。 建议定期评审下列角色分配：

* 全局管理员

* 用户管理员

* 特权身份验证管理员

* 条件访问管理员

* 安全管理员

* 所有 Microsoft 365 和 Dynamics 服务管理角色

此处选择的角色包括永久角色和符合条件的角色。 

在“审阅者”部分选择一人或多人来评审所有用户。 也可以选择让成员评审自己的访问权限。

 ![编辑策略](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>部署访问评审

准备好策略和计划来评审与 Azure AD 集成的资源的访问权限后，请使用以下资源来部署和管理评审。

### <a name="review-access-packages"></a>评审访问包

为了降低访问权限过时的风险，管理员可以为那些针对访问包进行了有效分配的用户启用定期评审。 请按以下链接中的说明进行操作：

| 操作方法文章| 描述 |
| - | - |
| [创建访问评审](entitlement-management-access-reviews-create.md)| 启用对访问包的评审。 |
| [执行访问评审](entitlement-management-access-reviews-review-access.md)| 针对分配到访问包的其他用户执行访问评审。 |
| [自我评审分配的访问包](entitlement-management-access-reviews-self-review.md)| 自我评审分配的访问包 |


> [!NOTE]
> 系统不会立即将进行自我评审并声明不再需要访问权限的最终用户从访问包中删除。 系统会在评审结束或管理员停止评审时将其从访问包中删除。

### <a name="review-groups-and-apps"></a>评审组和应用

员工和来宾对组和应用程序的访问需求可能会随时间而变化。 为了降低与过期访问权限分配相关的风险，管理员可以针对组成员或应用程序访问权限创建访问评审。 请按以下链接中的说明进行操作：

| 操作方法文章| 描述 |
| - | - |
| [创建访问评审](create-access-review.md)| 针对组成员或应用程序访问权限创建一个或多个访问评审。 |
| [执行访问评审](perform-access-review.md)| 针对有权访问应用程序的组成员或用户执行访问评审。 |
| [自我评审你的访问权限](review-your-access.md)| 成员可以评审自己对组或应用程序的访问权限 |
| [完成访问评审](complete-access-review.md)| 查看访问评审并应用结果 |
| [对本地组执行操作](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| 用于对本地组的访问评审执行操作的示例 PowerShell 脚本。 |


### <a name="review-azure-ad-roles"></a>评审 Azure AD 角色

为了降低与过时角色分配相关的风险，应定期评审特权 Azure AD 角色的访问权限。

![屏幕截图，显示 Azure A D 角色的 "查看成员身份" 列表。](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

请按以下链接中的说明进行操作：

| 操作方法文章 | 描述 |
| - | - |
 [创建访问评审](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 在 PIM 中创建特权 Azure AD 角色的访问评审 |
| [自我评审你的访问权限](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 如果已为你分配了管理角色，请批准或拒绝对角色的访问权限 |
| [完成访问评审](../privileged-identity-management/pim-how-to-complete-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 查看访问评审并应用结果 |


### <a name="review-azure-resource-roles"></a>评审 Azure 资源角色

为了降低与过时角色分配相关的风险，应定期评审特权 Azure 资源角色的访问权限。 

![评审 Azure AD 角色](./media/deploy-access-review/9-review-azure-roles-picker.png)

请按以下链接中的说明进行操作：

| 操作方法文章| 描述 |
| - | -|
| [创建访问评审](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 在 PIM 中创建特权 Azure 资源角色的访问评审 |
| [自我评审你的访问权限](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 如果已为你分配了管理角色，请批准或拒绝对角色的访问权限 |
| [完成访问评审](../privileged-identity-management/pim-resource-roles-complete-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 查看访问评审并应用结果 |


## <a name="use-the-access-reviews-api"></a>使用访问评审 API

请参阅 [Graph API 方法](/graph/api/resources/accessreviews-root?view=graph-rest-beta)和[角色和应用程序授权检查](/graph/api/resources/accessreviews-root?view=graph-rest-beta)，了解如何与可评审资源交互并对其进行管理。 Microsoft Graph API 中的访问评审方法可用于应用程序和用户上下文。 在应用程序上下文中运行脚本时，必须为用于运行 API 的帐户（服务主体）授予查询访问评审信息所需的“AccessReview.Read.All”权限。

可自动通过 Graph API 进行访问评审的常见访问评审任务包括：

* 创建和启动访问评审

* 在按计划结束之前手动结束访问评审

* 列出所有正在运行的访问评审及其状态

* 查看评审系列的历史记录，以及在每项评审中执行的决策和操作。

* 从访问评审中收集决策

* 收集已完成评审中的决策，在这些评审中，审阅者做出的决策与系统建议的决策不同。

为自动化创建新的 Graph API 查询时，建议使用 [Graph 浏览器](https://developer.microsoft.com/en-us/graph/graph-explorer)。 可以先生成并浏览 Graph 查询，然后将其放入脚本和代码中。 这可以帮助你快速地循环访问查询，使你能够准确获得所需的结果，而无需更改脚本的代码。

## <a name="monitor-access-reviews"></a>监视访问评审

访问评审活动会记录下来，在 [Azure AD 的审核日志](../reports-monitoring/concept-audit-logs.md)中提供。 可以按类别、活动类型和日期范围筛选审核数据。 下面是一个示例查询：

| 类别| 策略 |
| - | - |
| 活动类型| 创建访问评审 |
| | 更新访问评审 |
| | 访问评审结束 |
| | 删除访问评审 |
| | 批准决策 |
| | 拒绝决策 |
| | 重置决策 |
| | 应用决策 |
| 日期范围| 七天 |


若要对访问评审进行更高级的查询和分析，以及跟踪评审的更改和完成情况，建议将 Azure AD 审核日志导出到 [Azure Log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) 或 Azure 事件中心。 在 Azure Log Analytics 中存储时，可以使用 [功能强大的分析语言](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) ，并构建你自己的仪表板。

## <a name="next-steps"></a>后续步骤

了解以下相关技术。

* [什么是 Azure AD 权利管理？](entitlement-management-overview.md)

* [什么是 Azure AD Privileged Identity Management？](../privileged-identity-management/pim-configure.md)

