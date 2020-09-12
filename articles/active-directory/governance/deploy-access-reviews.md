---
title: 规划 Azure Active Directory 访问评审部署
description: 成功访问评审部署的规划指南
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
ms.openlocfilehash: 7154bc1f033806d359726cff8ed227f2219559ec
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89461028"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>规划 Azure Active Directory 访问评审部署

[Azure Active Directory (Azure AD) 访问评审](access-reviews-overview.md) 可帮助你的组织通过管理其 [资源访问生命周期](identity-governance-overview.md)，使网络更安全。 通过访问评审，你可以：

* 计划定期查看或执行即席评审，以查看谁有权访问特定资源，如应用程序和组

* 跟踪见解、合规性或策略原因评审

* 将评审委托给特定管理员、企业所有者或最终用户，这些用户可以自行证明是否需要继续访问

* 使用见解来有效确定用户是否应继续具有访问权限

* 自动执行评审结果，如删除用户对资源的访问权限

  ![规划访问评审](./media/deploy-access-review/1-planning-review.png)

访问评审是一种 [Azure AD Identity Governance](identity-governance-overview.md) 功能。 其他功能包括： [权利管理](entitlement-management-overview.md)、 [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 和 [使用条款](../conditional-access/terms-of-use.md)。 它们共同帮助组织解决这四个问题：

* 哪些用户应该有权访问哪些资源？

* 这些用户使用该访问权限做些什么？

* 是否有有效的组织控制来管理访问？

* 审核员是否可以验证控制措施是否正常实施？

规划访问评审部署至关重要，可确保为组织中的用户实现所需的管理策略。

### <a name="key-benefits"></a>主要优点

启用访问评审的主要优点是：

* **控件协作**。 访问评审允许组织管理其用户所需的所有资源的访问权限。 当其用户共享和协作时，组织可以确保信息只在授权用户之间。

* **管理风险**。 访问评审为组织提供了一种查看数据和应用程序访问权限的方法，降低了数据泄露和数据溢出的风险。 这包括定期查看外部合作伙伴对公司资源的访问权限的功能。 

* **满足合规性和监管要求**。 通过访问评审，你可以控制和再次验证组、应用和站点的访问生命周期。 您可以控制特定于您的组织的符合性或风险敏感应用程序的跟踪评审。 

* **降低成本**。 访问评审内置于云中，并与云资源（如组、应用程序和访问包）一起工作。 与构建你自己的工具或升级本地工具集相比，使用访问评审的成本更低。

### <a name="training-resources"></a>训练资源

了解访问评审时，可以使用以下视频：

* [Azure AD 中的访问评审是什么？](https://youtu.be/kDRjQQ22Wkk)

* [如何在 Azure AD 中创建访问评审](https://youtu.be/6KB3TZ8Wi40)

* [如何在 Azure AD 中启用访问评审](https://youtu.be/X1SL2uubx9M)

* [如何使用 Access 查看访问权限](https://youtu.be/tIKdQhdHLXU)

### <a name="licenses"></a>许可证

需要为每个人（而非全局管理员或用户管理员）提供有效的 Azure AD Premium (P2) 许可证，他们将创建或执行访问评审。 有关详细信息，请参阅 [访问评审许可证要求](access-reviews-overview.md)。

你可能还需要其他标识治理功能，如 [权利生命周期管理](entitlement-management-overview.md) 或特权标识管理。 在这种情况下，你可能还需要相关的许可证。 有关详细信息，请参阅 [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="plan-the-access-reviews-deployment-project"></a>规划访问评审部署项目

考虑你的组织需要确定在你的环境中部署访问评审的策略。

### <a name="engage-the-right-stakeholders"></a>让合适的利益干系人参与

如果技术项目失败，它们通常是由于在影响、结果和责任方面不符合预期而导致的。 为避免这些缺陷，请[确保你正在吸引正确的利益干系人](https://aka.ms/deploymentplans)，并确保项目角色明确。

对于访问评审，你可能会在你的组织内包含以下团队的代表：

* **It 管理** 管理 it 基础结构，管理云投资和软件即服务 (SaaS) 应用。 此团队将：

   * 查看对基础结构和应用的特权访问，包括 Microsoft 365 和 Azure AD。

   * 计划和运行用于维护异常列表或 IT 试验项目的组的访问评审，以维护最新的访问列表。

   * 确保以编程方式 (通过服务主体访问资源) 对资源进行管理和查看。

* **开发团队** 构建和维护组织的应用程序。 此团队将：

   * 控制谁可以访问和管理 SaaS、PaaS 和 IaaS 资源中构成开发解决方案的组件。

   * 管理可访问应用程序和工具进行内部应用程序开发的组。

   * 需要有权访问为客户托管的生产软件或解决方案的特权标识

* **业务单位** 管理项目和自己的应用程序。 此团队将： 

   * 查看并批准或拒绝内部和外部用户对组和应用程序的访问权限。

   * 计划和执行评审，以证明员工和外部标识（如业务合作伙伴）的持续访问权限。

* **企业治理** 确保组织遵守内部政策，并遵守法规。 此团队将：

   * 请求或计划新的访问评审。

   * 评估用于查看访问权限的过程和过程，包括保存符合性的文档和记录。

   * 查看大多数关键资源过去评审的结果。

> [!NOTE]
> 对于需要手动评估的评论，请确保规划足够的审阅者，并查看满足您的策略和合规性需求的周期。 如果审阅周期过于频繁，或审阅者太少，则质量可能会丢失，并且太多或太少人员可能具有访问权限。 

### <a name="plan-communications"></a>规划沟通

通信对于任何新业务流程的成功至关重要。 主动与用户进行交流的方式和时间，以及如何在遇到问题时获得支持。 

#### <a name="communicate-changes-in-accountability"></a>传达责任变化

访问评审支持对业务所有者进行持续访问的责任转移责任。 从 IT 中分离访问决策，更准确地做出访问决策。 这是对资源所有者的责任和责任的文化改变。 主动传达此更改，确保资源所有者经过培训，并且能够使用见解做出合理的决策。

很显然，它需要控制所有与基础结构相关的访问决策和特权角色分配。 

#### <a name="customize-email-communication"></a>自定义电子邮件通信

当你计划审查时，你将授予执行此审查的用户。 然后，这些审阅者会收到一封电子邮件通知，其中向他们分配了新审阅，并在向他们分配审核之前收到提醒。

管理员可以选择将此通知发送到审阅过期之前的半方，也可以选择在一天过期之前发送。 

可以自定义发送给审阅者的电子邮件，使其包含一条建议他们在评审上执行操作的自定义短信。 建议使用以下附加文本：

* 向审阅者提供个人消息，以便他们了解它是由你的符合性或 IT 部门发送的。

* 包含超链接或引用有关评审的期望的内部信息，以及其他参考或培训材料。

* 提供一个链接，指向有关[如何执行查看访问权限](review-your-access.md)的说明。 

  ![审阅者电子邮件](./media/deploy-access-review/2-plan-reviewer-email.png)

选择开始评审后，审阅者将定向到 [myAccess 门户](https://myapplications.microsoft.com/) 进行组和应用程序访问评审。 门户为用户提供了对要查看的资源具有访问权限的所有用户的概述，并提供基于最后一次登录和访问信息的系统建议。

### <a name="plan-a-pilot"></a>规划试点

我们鼓励客户开始使用一小小组试验访问评审，并以非关键资源为目标。 试验可帮助你根据需要调整流程和通信，并使用户和审阅者能够满足安全和合规性要求。

在试验中，我们建议：

* 从未自动应用结果的评审开始，你可以控制其含义。

* 确保所有用户都具有 Azure AD 中列出的有效电子邮件地址，并收到电子邮件通信以执行相应的操作。 

* 记录作为试验的一部分删除的任何访问权限，以备你需要快速还原。

* 监视审核日志以确保所有事件都得到适当的审核。

有关详细信息，请参阅 [试验的最佳实践](../fundamentals/active-directory-deployment-plans.md)。

## <a name="introduction-to-access-reviews"></a>访问评审简介

本部分介绍在规划评审之前应该了解的访问评审概念。

### <a name="what-resource-types-can-be-reviewed"></a>可以查看哪些资源类型？

将组织的资源与) Azure AD (（如用户、应用程序和组）集成后，即可对其进行管理和查看。 

典型审核目标包括：

* [与单一登录 (Azure AD 集成的应用程序](../manage-apps/what-is-application-management.md) ，如 SaaS、业务线) 。

* 组 [成员身份](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) (同步到 Azure AD，或者在 Azure AD 或 Microsoft 365 中创建，包括 Microsoft 团队) 。

* 将资源 (组、应用和站点分组) 为单个包以管理访问权限的[访问包](/azure/active-directory/governance/entitlement-management-overview)。

* Azure AD 在 Privileged Identity Management 中定义的[角色和 Azure 资源角色](../privileged-identity-management/pim-resource-roles-assign-roles.md)。

### <a name="who-will-create-and-manage-access-reviews"></a>谁将创建和管理访问评审？

创建、管理或读取访问评审所需的管理角色取决于要查看的资源的类型。 下表指明了每种资源类型所需的角色：

| 资源类型|  (创建者创建和管理访问评审) | 读取访问评审结果 |
| - | - | -|
| 组或应用程序| 全局管理员 <p>用户管理员| 创建者和安全管理员 |
| Azure AD 中的特权角色| 全局管理员 <p>特权角色管理员| 创建者 <p>安全读取者<p>安全管理员 |
| Azure 中的特权角色 (资源) | 全局管理员<p>用户管理员<p>资源所有者| 创建者 |
| 访问包| 全局管理员<p>访问包的创建者| 仅限全局管理员 |


有关详细信息，请参阅 [Azure Active Directory 中的管理员角色权限](../users-groups-roles/directory-assign-admin-roles.md)。

### <a name="who-will-review-the-access-to-the-resource"></a>谁将审查对资源的访问权限？

访问评审的创建者在创建时将决定执行检查。 在开始评审后，将无法更改此设置。 审阅者由三个角色表示：

* 资源所有者，是资源的业务所有者。

* 由访问评审管理员选择的一组单独选择的委托。

* 最终用户，他们将自行证明其是否需要继续访问。

创建访问评审时，管理员可以选择一个或多个审阅者。 所有审阅者都可以开始并执行审查，选择用户以继续访问某个资源或将其删除。 

### <a name="components-of-an-access-review"></a>访问评审的组件

在实施访问评审之前，应计划与组织相关的评审类型。 要执行此操作，您需要对您要查看的内容和根据这些评审采取的措施做出业务决策。

若要创建访问评审策略，必须具有以下信息。

* 要查看) 资源 (是什么？

* 正在查看其访问权限。

* 检查发生的频率是多少？

* 谁将执行此审查？

   * 如何通知他们要查看哪些信息？

   * 要检查哪些时间线？

* 应根据评审强制执行哪些自动操作？

   * 如果审阅者没有按时响应，会发生什么情况？

* 根据评审的结果，将采取哪些手动操作？

* 应根据采取的操作发送哪些通信？


**示例访问评审计划**

| 组件| 值 |
| - | - |
| **要查看的资源**| 访问 Microsoft Dynamics |
| **查看频率**| 每月一次 |
| **执行评审的人员**| Dynamics 业务组计划经理 |
| **通知**| 在检查别名 Dynamics 之前的24小时内发送电子邮件-项目经理签字<p>包括向审阅者鼓励自定义消息以保护他们的购买 |
| **时间线**| 从通知48小时 |
|**自动操作**| 通过从安全组 dynamics access 中删除用户，删除在90天内没有交互式登录的任何帐户的访问权限。 <p>*如果未在时间线内检查，请执行操作。* |
| **手动操作**| 如果需要，审阅者可以在自动操作之前执行删除审批。 |
| **通信**| 发送内部 (成员) 删除了电子邮件的用户，说明删除了这些用户，以及如何重新获得访问权限。 |


 

### <a name="automate-actions-based-on-access-reviews"></a>基于访问评审自动执行操作

可以通过将 "自动应用结果" 选项设置为 "启用" 来选择自动删除访问权限。

  ![规划访问评审](./media/deploy-access-review/3-automate-actions-settings.png)

完成评审并结束后，审阅者未批准的用户将自动从资源中删除，或者保留为持续访问。 这可能意味着删除其组成员身份、应用程序分配或撤消权限以提升到特权角色。

采用建议

建议会在审阅者体验中显示为审阅者，并指示用户上次登录到租户的时间，或上次访问应用程序的时间。 此信息可帮助审阅者做出正确的访问决策。 选择 "获取建议" 将遵循访问评审的建议。 在访问评审结束时，系统将自动为审阅者未响应的用户应用这些建议。

建议基于访问评审中的条件。 例如，如果将审阅配置为在90天内没有交互式登录时删除访问权限，则建议删除符合该条件的所有用户。 Microsoft 不断致力于增强建议。 

### <a name="review-guest-user-access"></a>查看来宾用户访问权限

使用访问评审来查看和清理来自外部组织的协作伙伴的标识。 每合作伙伴评审的配置可能满足合规性要求。

可以通过以下操作之一向外部标识授予对公司资源的访问权限：

* 添加到组 

* 已邀请到团队 

* 分配到企业应用程序或访问包

* 在 Azure AD 或 Azure 订阅中分配有特权角色

请参阅 [示例脚本](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)。 此脚本将显示在何处使用受邀加入租户的外部标识。 可以在 Azure AD 中查看外部用户的组成员身份、角色分配和应用程序分配。 此脚本不会显示 Azure AD 之外的任何分配，例如无需使用组即可直接分配给 Sharepoint 资源的权限。

创建组或应用程序的访问评审时，可以选择让审阅者专注于具有访问权限的所有人或仅限来宾用户。 通过选择 "仅来宾用户"，将为审阅者提供 Azure AD B2B 中可访问资源的外部标识的集中列表。

 ![查看来宾用户](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> 这不包括具有 userType 成员的外部成员。 这还不包括在 Azure AD B2B 协作的外部进行邀请的用户，例如那些直接通过 SharePoint 访问共享内容的用户。

## <a name="plan-access-reviews-for-access-packages"></a>计划访问包的访问评审

[访问包](entitlement-management-overview.md) 可以极大地简化管理和访问审核策略。 访问包是所有资源的捆绑，其中包含用户在项目上工作或执行其任务所需的访问权限。 例如，你可能想要创建一个访问包，其中包含组织中的开发人员所需的所有应用程序，或外部用户应该有权访问的所有应用程序。 然后，管理员或委派访问包管理器会将资源 (组或应用) ，以及用户需要用于这些资源的角色。

[创建访问包](entitlement-management-access-package-create.md)时，可以创建一个或多个访问策略，用于设置用户可请求访问包的条件、审批流程的外观，以及人员需要重新请求访问的频率。 在创建或编辑访问包策略时，将配置访问评审。

打开 "生命周期" 选项卡，向下滚动以访问审阅。

 ![编辑策略](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>规划组的访问评审

除访问包外，查看组成员身份是管理访问权限的最有效方法。 建议通过 [安全组或 Microsoft 365 组](../fundamentals/active-directory-manage-groups.md)分配资源访问权限，并将用户添加到这些组以获取访问权限。

单个组可以被授予对所有相应资源的访问权限。 可以将组访问权限分配给单个资源，或者分配给对应用程序和其他资源进行分组的访问包。 使用此方法，你可以查看对组的访问权限，而不是查看单个应用程序的访问权限。 

可以通过以下方式查看组成员身份： 

* 管理员

* 组所有者

* 所选用户，创建评审时的委托审查功能

* 组的成员，证明本身

### <a name="group-ownership"></a>组所有权

建议组所有者查看成员身份，因为它们最好知道需要访问的用户。 组的所有权不同于组的类型：

在 Microsoft 365 和 Azure AD 中创建的组具有一个或多个定义完善的所有者。 在大多数情况下，这些所有者为他们自己的组提供了完美的审阅者，因为他们知道谁有权访问。 

例如，Microsoft 团队使用 Microsoft 365 组作为基础授权模型，以便向用户授予对 SharePoint、Exchange、OneNote 或其他 Microsoft 365 服务中的资源的访问权限。 团队的创建者将自动成为所有者，并应负责证明该组的成员身份。 

在 Azure AD 门户中手动创建的组或通过 Microsoft Graph 的脚本创建的组可能不必定义所有者。 建议你通过组的 "所有者" 部分中的 Azure AD 门户或通过 Graph 来定义它们。

从本地 Active Directory 同步的组在 Azure AD 中不能有所有者。 为他们创建访问评审时，应选择最适合决定其成员身份的个人。

> [!NOTE]
> 建议定义用于定义如何创建组的业务策略，以确保对成员身份进行定期审核的明确的组所有权和责任。 

### <a name="review-membership-of-exclusion-groups-in-ca-policies"></a>查看 CA 策略中排除组的成员身份 

在某些情况中，条件访问 (CA) 策略旨在确保网络安全不应应用到所有用户。 例如，仅允许用户在公司网络上登录的 CA 策略可能不适用于销售团队，后者会广泛传播。 在这种情况下，销售团队成员将被放入一个组中，该组将从 CA 策略中排除。 

如果从要求中排除了错误的成员，则定期查看此类组成员身份，因为排除表示潜在风险。

你可以 [使用 Azure AD 访问评审来管理从条件访问策略中排除的用户](conditional-access-exclusion.md)。

### <a name="review-external-users-group-memberships"></a>查看外部用户的组成员身份

若要最大程度地减少手动工作和相关的潜在错误，请考虑使用 [动态组](../users-groups-roles/groups-create-rule.md) 基于用户的属性分配组成员身份。 可能需要为外部用户创建一个或多个动态组。 内部赞助商可以充当组中成员身份的审阅者。 

注意：不会从租户中删除由于访问评审而从组中删除的外部用户。 

可以从已手动删除的租户或通过脚本删除它们。

### <a name="review-access-to-on-premises-groups"></a>查看对本地组的访问权限

访问评审无法更改从本地同步的组的组成员身份 [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)。 这是因为授权来源在本地。

你仍可以使用访问评审来计划和维护本地组的定期审查。 然后，审阅者将在本地组中执行操作。 此策略将访问评审作为所有评审的工具。

可以在本地组上使用访问评审的结果，并通过以下方式进一步处理它们：

* 从访问评审下载 CSV 报表并手动采取措施。

* 使用 Microsoft Graph 以编程方式访问完成的访问评审中的结果和决策。

例如，若要访问 Windows AD 管理的组的结果，请使用此 [PowerShell 示例脚本](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)。 此脚本概述了所需的图形调用，并导出了 Windows AD PowerShell 命令来执行更改。

## <a name="plan-access-reviews-for-applications"></a>规划应用程序的访问评审 

查看对应用程序的访问权限时，你要查看对应用程序中的信息和数据的员工和外部标识的访问权限。 如果需要知道谁有权访问特定的应用程序，请选择查看应用程序，而不是访问包或组。 

建议在以下情况下为应用程序计划评审：

* 向用户授予对应用程序 (的直接访问权限) 组外或访问包。

* 应用程序公开关键或敏感信息。

* 应用程序具有特定的符合性要求，你必须对其进行证明。

* 你怀疑访问不当。

若要为应用程序创建访问评审，请设置 "需要用户分配"。属性为 Yes。 如果设置为 "否"，则目录中的所有用户（包括外部标识）都可以访问应用程序，无法查看对应用程序的访问权限。 

 ![计划应用分配](./media/deploy-access-review/6-plan-applications-assignment-required.png)

然后，必须分配要访问的 [用户和组](../manage-apps/assign-user-or-group-access-portal.md) 。 

### <a name="reviewers-for-an-application"></a>应用程序的审阅者

访问评审的对象可以是组成员，或者是已分配到应用程序的用户。 Azure AD 中的应用程序不一定具有所有者，这就是为什么无法选择应用程序所有者作为审阅者的原因。 可以进一步确定评审的范围，以仅查看分配给应用程序的来宾用户，而不是查看所有访问权限。

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>规划 Azure AD 和 Azure 资源角色的评审

[Privileged Identity Management (PIM) ](../privileged-identity-management/pim-configure.md) 简化了企业如何管理对 Azure AD 中资源的特权访问。 这会使特权角色的列表在 [Azure AD](../users-groups-roles/directory-assign-admin-roles.md) 和 [Azure 资源](../../role-based-access-control/built-in-roles.md) 中的规模大大降低，并提高目录的总体安全性。

访问评审允许审阅者证明用户是否仍需要处于角色中。 与访问包的访问评审一样，对 Azure AD 角色和 Azure 资源的检查都已集成到 PIM 管理员用户体验中。 建议定期查看下列角色分配：

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

准备好策略和计划以查看与 Azure AD 集成的资源的访问权限后，请使用以下资源来部署和管理评审。

### <a name="review-access-packages"></a>查看访问包

为了降低过时访问的风险，管理员可以对具有访问包的活动分配的用户进行定期检查。 请按照以下链接中的说明进行操作：

| 操作方法文章| 说明 |
| - | - |
| [创建访问评审](entitlement-management-access-reviews-create.md)| 启用访问包的审核。 |
| [执行访问评审](entitlement-management-access-reviews-review-access.md)| 为分配到访问包的其他用户执行访问评审。 |
| [自行查看分配的访问包 (s) ](entitlement-management-access-reviews-self-review.md)| 自动查看分配的访问包 (s)  |


> [!NOTE]
> 直接查看并指出他们不再需要访问权限的最终用户不会立即从访问包中删除。 当评审结束或管理员停止评审时，它们会从访问包中删除。

### <a name="review-groups-and-apps"></a>查看组和应用

需要对员工和来宾的组和应用程序进行访问，这可能会随着时间的推移而变化。 为了降低与过时访问权限分配相关的风险，管理员可以为组成员或应用程序访问创建访问评审。 请按照以下链接中的说明进行操作：

| 操作方法文章| 说明 |
| - | - |
| [创建访问评审](create-access-review.md)| 为组成员或应用程序访问创建一个或多个访问评审。 |
| [执行访问评审](perform-access-review.md)| 对有权访问应用程序的组成员或用户执行访问评审。 |
| [自我审查你的访问权限](review-your-access.md)| 成员查看自己对组或应用程序的访问权限 |
| [完成访问评审](complete-access-review.md)| 查看访问评审并应用结果 |
| [对本地组执行操作](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| 用于对本地组进行访问评审的示例 PowerShell 脚本。 |


### <a name="review-azure-ad-roles"></a>查看 Azure AD 角色

若要降低与过时角色分配相关的风险，应定期查看特权 Azure AD 角色的访问权限。

![查看 azure ad 角色](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

请按照以下链接中的说明进行操作：

| 操作方法文章 | 说明 |
| - | - |
 [创建访问评审](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 在 PIM 中创建特权 Azure AD 角色的访问评审 |
| [自我审查你的访问权限](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 如果分配到管理角色，请批准或拒绝对角色的访问权限 |
| [完成访问评审](../privileged-identity-management/pim-how-to-complete-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 查看访问评审并应用结果 |


### <a name="review-azure-resource-roles"></a>查看 Azure 资源角色

若要降低与过时角色分配相关的风险，应定期查看特权 Azure 资源角色的访问权限。 

![查看 azure ad 角色](./media/deploy-access-review/9-review-azure-roles-picker.png)

请按照以下链接中的说明进行操作：

| 操作方法文章| 说明 |
| - | -|
| [创建访问评审](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 在 PIM 中为特权 Azure 资源角色创建访问评审 |
| [自我审查你的访问权限](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 如果分配到管理角色，请批准或拒绝对角色的访问权限 |
| [完成访问评审](../privileged-identity-management/pim-resource-roles-complete-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 查看访问评审并应用结果 |


## <a name="use-the-access-reviews-api"></a>使用访问评审 API

请参阅 [图形 API 方法](/graph/api/resources/accessreviews-root?view=graph-rest-beta) 和 [角色和应用程序权限授权检查](/graph/api/resources/accessreviews-root?view=graph-rest-beta) 以与 reviewable 资源交互和管理这些资源。 Microsoft Graph API 中的访问评审方法可用于应用程序和用户上下文。 在应用程序上下文中运行脚本时，用于运行 API 的帐户 (服务主体) 必须向其授予 "AccessReview" 权限才能查询访问评审信息。

常用访问审查任务，以使用图形 API 进行访问评审：

* 创建和启动访问评审

* 在计划结束之前手动结束访问评审

* 列出所有正在运行的访问评审及其状态

* 查看评审系列的历史记录，以及在每个评审中执行的决策和操作。

* 从访问评审收集决策

* 收集已完成评审的决策，其中审阅者做出的决策与系统建议的决策不同。

为自动化创建新的图形 API 查询时，建议使用 [图形资源管理器](https://developer.microsoft.com/en-us/graph/graph-explorer)。 可以生成并浏览图形查询，然后将其放入脚本和代码。 这可以帮助你快速地循环访问查询，使你能够准确获得所需的结果，而无需更改脚本代码。

## <a name="monitor-access-reviews"></a>监视访问评审

将从 [Azure AD 的审核日志](../reports-monitoring/concept-audit-logs.md)记录并提供访问评审活动。 你可以在 "类别"、"活动类型" 和 "日期范围" 上筛选审核数据。 下面是一个示例查询：

| 类别| 策略 |
| - | - |
| 活动类型| 创建访问评审 |
| | 更新访问评审 |
| | 访问评审已结束 |
| | 删除访问评审 |
| | 批准决策 |
| | 拒绝决策 |
| | 重置决策 |
| | 应用决策 |
| 日期范围| 七天 |


对于更高级的查询和访问评审分析，以及若要跟踪评审的更改和完成，建议将 Azure AD 审核日志导出到 [azure Log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) 或 Azure 事件中心。 在 Azure Log Analytics 中存储时，可以使用 [功能强大的分析语言](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) ，并构建你自己的仪表板。

## <a name="next-steps"></a>后续步骤

了解以下相关技术。

* [什么是 Azure AD 权限管理？](entitlement-management-overview.md)

* [什么是 Azure AD Privileged Identity Management？](../privileged-identity-management/pim-configure.md)

