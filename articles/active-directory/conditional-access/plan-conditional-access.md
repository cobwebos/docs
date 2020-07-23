---
title: 规划 Azure Active Directory 条件访问部署
description: 了解如何设计条件访问策略，并在组织中有效地部署。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 06/09/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83cc75c1d69ee7232edf0c21643d25027b97f088
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85339731"
---
# <a name="plan-a-conditional-access-deployment"></a>计划条件访问部署

规划条件访问部署对于实现组织的应用和资源访问策略至关重要。

在移动优先、云优先的世界，用户会使用各种设备和应用从任何位置访问组织的资源。 因此，关注谁可以访问资源不再能满足需求。 还需要考虑用户的位置、正在使用的设备、要访问的资源等。 

Azure Active Directory (Azure AD) 条件访问 (CA) 可分析各种信号（例如，用户、设备和位置）以自动做出决策，并强制实施组织的资源访问策略。 你可以使用 CA 策略来应用访问控制，如多重身份验证 (MFA)。 通过 CA 策略，你可以在有安全性需要时提示用户进行 MFA，并在不需要时避免用户进入。

![条件访问概述](./media/plan-conditional-access/conditional-access-overview-how-it-works.png)

Microsoft 提供了标准的条件策略（称为[安全默认值](../fundamentals/concept-fundamentals-security-defaults.md)）可确保基本安全级别。 但是，组织所需要的灵活性可能超出了安全默认值能提供的。 你可以使用条件访问更精细地自定义安全默认值，并配置满足需求的新策略。

## <a name="learn"></a>了解

在开始之前，请确保了解[条件访问](overview.md)的工作原理及其使用时机。

### <a name="benefits"></a>优点

部署条件访问的优点是：

* 提高工作效率。 仅在有一个或多个信号支持时，才会中断具有登录条件（如 MFA）的用户。 通过 CA 策略，你可以控制何时提示用户进行 MFA、何时阻止访问以及何时用户必须使用受信任的设备。

* 管理风险。 通过策略条件自动执行风险评估意味着，一旦识别风险登录便可立即修正或阻止。 将条件访问与[标识保护](../identity-protection/overview-identity-protection.md)耦合在一起，可以检测异常和可疑事件，使您能够在资源访问被阻止或门控时锁定目标。 

* 解决符合性和管理问题。 通过条件访问，你可以审核对应用程序的访问权限，提出使用条款以获得同意，并根据符合性策略限制访问。

* 管理成本。 将访问策略移动到 Azure AD 可以降低对条件访问的自定义或本地解决方案的依赖及其基础结构成本。

### <a name="license-requirements"></a>许可要求

请参阅[条件访问许可证要求](overview.md)。

如果需要附加的功能，则还可能需要相关的许可证。 有关详细信息，请参阅 [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。

### <a name="prerequisites"></a>先决条件

* 一个至少启用了 Azure AD Premium 或试用版许可证的有效 Azure AD 租户。 如果需要，[可免费创建一个](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 一个拥有条件访问管理员特权的帐户。

* 一个你知道其密码的非管理员用户，例如 testuser。 如果需要创建用户，请参阅[快速入门：向 Azure Active Directory 添加新用户](../fundamentals/add-users-azure-active-directory.md)。

* 该非管理员用户所属的组。 如需创建一个组，请参阅[在 Azure Active Directory 中创建组并添加成员](../fundamentals/active-directory-groups-create-azure-portal.md)。

### <a name="training-resources"></a>训练资源

了解条件访问时，以下资源可能会很有用：


#### <a name="videos"></a>视频

* [What is Conditional Access?](https://youtu.be/ffMAw2IVO7A)（什么是条件访问？）
* [How to deploy Conditional Access?](https://youtu.be/c_izIRNJNuk)（如何部署条件访问？）
* [How to roll out CA policies to end users?](https://youtu.be/0_Fze7Zpyvc)（如何向最终用户推出 CA 策略？）
* [Conditional Access with device controls](https://youtu.be/NcONUf-jeS4)（使用设备控件的条件访问）
* [Conditional Access with Azure MFA](https://youtu.be/Tbc-SU97G-w)（使用 Azure MFA 的条件访问）
* [Conditional Access in Enterprise Mobility + Security](https://youtu.be/A7IrxAH87wc)（企业移动性 + 安全性中的条件访问）


#### <a name="online-courses-on-pluralsight"></a>PluralSight 的在线课程

* [Design Identity Management in Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)（在 Microsoft Azure 中设计标识管理）
* [Design Authentication for Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)（设计 Microsoft Azure 身份验证）
* [Design Authorization for Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)（设计 Microsoft Azure 授权）

## <a name="plan-the-deployment-project"></a>计划部署项目

在环境中确定此部署的策略时，请考虑你的组织需求。

### <a name="engage-the-right-stakeholders"></a>吸引适当的利益干系人

如果技术项目失败，它们通常是由于在影响、结果和责任方面不符合预期而导致的。 为避免这些缺陷，请[确保你正在吸引正确的利益干系人](https://aka.ms/deploymentplans)，并确保项目角色明确。

### <a name="plan-communications"></a>规划沟通

沟通对于任何新服务的成功都至关重要。 主动与用户交流他们的体验将如何变化、何时会变化以及在遇到问题时如何获取支持。

### <a name="plan-a-pilot"></a>规划试点

当新策略针对你的环境准备就绪后，在生产环境中分阶段部署它们。 首先在测试环境中将策略应用于一小部分用户，并验证策略是否按预期方式工作。 请参阅[试点的最佳做法](../fundamentals/active-directory-deployment-plans.md)。

> [!NOTE]
> 若要推出非特定于管理员的新策略，请排除所有管理员。 这可确保管理员仍可以访问策略，并在出现重大影响时进行更改或撤销。 在应用到所有用户之前，请始终使用较小的用户组验证策略。

## <a name="understand-ca-policy-components"></a>了解 CA 策略组件
CA 策略是 if-then 语句：如果满足分配，则应用这些访问控制。

配置 CA 策略时，条件称为“分配”。 通过 CA 策略，你可以根据某些分配对组织的应用强制实施访问控制。


有关详细信息，请参阅[生成 CA 策略](concept-conditional-access-policies.md)。

![创建策略屏幕](media/plan-conditional-access/create-policy.png)

[分配](concept-conditional-access-policies.md#assignments)定义

* 受策略影响的[用户和组](concept-conditional-access-users-groups.md)

* 策略将应用到的[云应用或操作](concept-conditional-access-cloud-apps.md) 

* 策略将应用到的[条件](concept-conditional-access-conditions.md)。

[访问控制](concept-conditional-access-policies.md)设置确定如何强制实施策略：

* [授予或阻止](concept-conditional-access-grant.md)对云应用的访问权限。

* 通过[会话控制](concept-conditional-access-session.md)，可以限制特定云应用中的体验。

### <a name="ask-the-right-questions-to-build-your-policies"></a>询问正确的问题以生成策略

策略回答有关谁应访问你的资源、他们应访问哪些资源以及在什么条件下访问资源的问题。 策略可用于授予访问权限或阻止访问。 请务必就策略要实现的目标提出正确的问题。 

在生成策略之前，记录每个策略的问题答案。 

#### <a name="common-questions-about-assignments"></a>有关分配的常见问题

[用户和组](concept-conditional-access-users-groups.md)

* 将在策略中包括或从策略中排除哪些用户和组？

* 此策略是否包括所有用户、特定的用户组、目录角色或外部用户？

[云应用或操作](concept-conditional-access-cloud-apps.md)

* 将策略应用到哪些应用程序？

* 哪些用户操作将受此策略的限制？

[条件](concept-conditional-access-conditions.md)

* 将在策略中包括或从策略中排除哪些设备平台？

* 组织受信任的位置有哪些？

* 将在策略中包括或从策略中排除哪些位置？

* 将在策略中包括或从策略中排除哪些客户端应用类型（浏览器、移动设备、桌面客户端、使用旧式身份验证方法的应用）？

* 你是否有策略会将已建立 Azure AD 联接的设备或已建立混合 Azure AD 联接的设备从策略中排除？ 

* 如果使用[标识保护](../identity-protection/concept-identity-protection-risks.md)，你是否想要合并登录风险保护？

#### <a name="common-questions-about-access-controls"></a>有关访问控制的常见问题

[授予或阻止](concept-conditional-access-grant.md) 

是否要通过以下一项或多项要求来授予对资源的访问权限？

* 要求 MFA

* 要求将设备标记为合规

* 要求使用已建立混合 Azure AD 联接的设备

* 需要批准的客户端应用

* 需要应用保护策略

[会话控制](concept-conditional-access-session.md)

是否要在云应用上强制执行以下任意访问控制？

* 使用应用强制执行的权限

* 使用条件访问应用控制

* 强制登录频率

* 使用持久性浏览器会话

### <a name="access-token-issuance"></a>访问令牌颁发

了解访问令牌的颁发方式很重要。 

![访问令牌颁发示意图](media/plan-conditional-access/CA-policy-token-issuance.png)

> [!NOTE]
> 如果不需要执行任何操作，并且没有有效的 CA 策略，则默认行为是颁发访问令牌。 

例如，请在以下情况下考虑使用策略：

如果用户属于组 1，则强制使用 MFA 访问应用 1。

如果不属于组 1 的用户尝试访问应用，则不满足“if”条件，并颁发令牌。 若要排除不属于组 1 的用户，需要使用单独的策略来阻止所有其他用户。

## <a name="follow-best-practices"></a>遵循最佳做法

条件访问框架提供了极大的配置灵活性。 不过，极大的灵活性也意味着应先仔细检查每个配置策略，然后才能发布，以免产生不良结果。

### <a name="apply-ca-policies-to-every-app"></a>将 CA 策略应用于每个应用

如果 CA 策略条件不触发访问控制，则默认情况下会颁发访问令牌。 确保每个应用至少已应用一个条件访问策略

> [!IMPORTANT]
> 在单个策略中使用“阻止”和所有应用时要非常小心。 这可能会将管理员锁在 Azure 管理门户之外，并且无法为重要终结点（例如 Microsoft Graph）配置排除项。

### <a name="minimize-the-number-of-ca-policies"></a>最大程度地减少 CA 策略的数量

为每个应用创建一个策略并不是很有效，而且会导致难以管理。 条件访问只会对每个用户应用前 195 个策略。 我们建议你对应用进行分析，并按对相同用户具有相同的资源要求将应用分组。 例如，如果所有 Microsoft 365 应用或所有 HR 应用对相同用户具有相同的要求，请创建一个策略，并包括应用到的所有应用。 

### <a name="set-up-emergency-access-accounts"></a>设置紧急访问帐户

如果你错误配置了策略，则该策略会将组织锁在 Azure 门户之外。 通过在组织中创建两个或更多[紧急访问帐户](../users-groups-roles/directory-emergency-access.md)，可缓解意外锁定管理员造成的影响。

* 创建专用于策略管理并从所有策略中排除的用户帐户。

* 混合环境的不受限方案：

  * 创建一个本地安全组，并将其同步到 Azure AD。 安全组应包含专用的策略管理帐户。 

   * 从所有 CA 策略中排除此安全组。

   * 发生服务中断时，请根据需要将其他管理员添加到本地组，并强制执行同步。这会将其例外动态地用于 CA 策略。

### <a name="set-up-report-only-mode"></a>设置“仅限报告”模式

可能很难预测受常见部署计划（示例如下）影响的用户的数量和名称：

* 阻止旧式身份验证
* 要求 MFA
* 实施登录风险策略

[仅限报告模式](concept-conditional-access-report-only.md)允许管理员在其环境中启用 CA 策略之前评估其影响。

了解如何[在 CA 策略上配置仅限报告模式](howto-conditional-access-report-only.md)。

### <a name="plan-for-disruption"></a>为应对中断做好计划

如果你依靠单一访问控制（如 MFA 或网络位置）来保护 IT 系统，那么当该单一访问控制不可用或配置错误时，很容易发生访问失败。 为了降低在发生不可预见的中断时被锁定的风险，应[计划策略](../authentication/concept-resilient-controls.md)以供组织采用。

### <a name="set-naming-standards-for-your-policies"></a>为策略设置命名标准

命名标准有助于查找策略及了解其用途，而无需在 Azure 管理门户中将其打开。 我们建议你对策略进行命名以显示：

* 序列号

* 策略应用到的云应用

* 响应

* 策略对谁应用

* 何时应用（如果适用）

![命名标准](media/plan-conditional-access/11.png)

示例：对于从外部网络访问 Dynamics CRP 应用的营销用户要求 MFA 的策略可能是：

![命名标准](media/plan-conditional-access/naming-example.png)

描述性名称可帮助你大致了解条件访问实现。 如果需要在对话中引用策略，则序列号非常有用。 例如，当你在电话中与管理员进行交谈时，可以要求他们打开策略 CA01 来解决问题。

#### <a name="naming-standards-for-emergency-access-controls"></a>紧急访问控制的命名标准

除了活动策略以外，还应实施已禁用策略，这些策略[在中断或紧急情况下充当辅助性的弹性访问控制措施](../authentication/concept-resilient-controls.md)。 应急策略的命名标准应当包括：
* 以“ENABLE IN EMERGENCY”开头，使名称从其他策略中脱颖而出。

* 它应当应用于的中断的名称。

* 一个排序序列号，可以帮助管理员了解应当以何顺序启用策略。

**示例**

以下名称表明，如果发生 MFA 中断，此策略是要启用的四个策略中的第一个：

EM01 - ENABLE IN EMERGENCY：MFA 中断 [1/4] - Exchange SharePoint：VIP 用户需要混合 Azure AD 联接。

### <a name="exclude-countries-from-which-you-never-expect-a-sign-in"></a>排除你绝不会从那里登录的国家/地区。

Azure Active Directory 允许你创建[命名位置](location-condition.md)。 创建一个命名位置，其中包含绝不会发生登录的所有国家/地区。 然后为阻止从该命名位置登录的所有应用创建策略。 请确保从此策略中排除你的管理员。

### <a name="plan-your-policy-deployment"></a>规划策略部署

当新策略为你的环境准备就绪时，请确保在发布策略之前检查每个策略，以避免产生不良结果。 请参阅以下文档，了解有关如何应用策略以及如何避免问题的重要信息

* [应了解的内容](best-practices.md)

* [应避免的操作](best-practices.md)

## <a name="common-policies"></a>常用策略

计划 CA 策略解决方案时，请评估是否需要创建策略来实现以下结果。

* [要求 MFA](#require-mfa)
* [响应可能已泄密的帐户](#respond-to-potentially-compromised-accounts)
* [需要托管设备](#require-managed-devices)
* [需要批准的客户端应用程序](#require-approved-client-apps)
* [阻止访问](#block-access)

### <a name="require-mfa"></a>要求 MFA

要求 MFA 访问的常见用例包括：

* [管理员的访问](howto-conditional-access-policy-admin-mfa.md)

* [访问特定的应用](app-based-mfa.md)

* [对于所有用户](howto-conditional-access-policy-all-users-mfa.md)

* [从你不信任的网络位置访问](untrusted-networks.md)

* [对于 Azure 管理](howto-conditional-access-policy-azure-management.md)

### <a name="respond-to-potentially-compromised-accounts"></a>响应可能已泄密的帐户

使用 CA 策略，你可以通过可能已泄密的标识对登录实施自动响应。 帐户泄密的可能性以风险级别的形式表示。 “标识保护”会计算两种风险级别：登录风险和用户风险。 以下三个默认策略可以启用。

* [要求所有用户注册 MFA](howto-conditional-access-policy-risk.md)

* [要求高风险用户更改密码](howto-conditional-access-policy-risk.md)

* [要求具有中等或高登录风险的用户执行 MFA](howto-conditional-access-policy-risk.md)

### <a name="require-managed-devices"></a>需要托管设备

扩大用来访问云资源的受支持设备的范围有助于提高用户的工作效率。 你可能不希望具有未知保护级别的设备访问环境中的某些资源。 对于这些资源，[要求用户只能使用受管理设备访问它们](require-managed-devices.md)。

### <a name="require-approved-client-apps"></a>需要已批准的客户端应用

员工使用其移动设备执行个人和工作任务。 对于 BYOD 方案，你必须决定是要管理整个设备还是只管理其中的数据。 如果仅管理数据和访问权限，你可以[要求已批准的云应用](app-based-conditional-access.md)，以保护你的企业数据。 例如，你可以要求仅通过 Outlook Mobile 而不是通过常规邮件程序访问电子邮件。

### <a name="block-access"></a>阻止访问

用于[阻止所有访问](howto-conditional-access-policy-block-access.md)的选项非常强大。 例如，当你将应用迁移到 Azure AD，但尚未准备好让任何人登录该应用时，可以使用此选项。 阻止访问： 

* 覆盖用户的所有其他分配

* 具有阻止整个组织登录到租户的强大权限

> [!IMPORTANT]
> 如果创建一个策略来阻止所有用户的访问，请确保排除紧急访问帐户，并考虑从策略中排除所有管理员。

你可以阻止用户访问的其他常见情况如下：

* [阻止某些网络位置](howto-conditional-access-policy-location.md)访问你的云应用。 你可以使用此策略来阻止你知道不应该有流量输出的某些国家/地区。

* Azure AD 支持旧式身份验证。 但是，旧式身份验证不支持 MFA，而许多环境需要后者来解决标识安全问题。 在这种情况下，你可以[使用旧式身份验证阻止应用](block-legacy-authentication.md)访问你的租户资源。

## <a name="build-and-test-policies"></a>生成和测试策略

在部署的每个阶段，请确保评估结果符合预期。 

当新策略准备就绪后，将其分阶段部署到生产环境中：

* 向最终用户提供内部变更通知。

* 从少量的用户着手，验证策略的行为是否符合预期。

* 将策略的范围扩大，使之包括更多的用户时，继续排除所有管理员。 排除管理员可以确保在需要更改的情况下，仍有人可以访问该策略。

* 仅在进行全面测试后，才能将策略应用到所有用户。 确保你拥有至少一个该策略不适用的管理员帐户。

### <a name="create-test-users"></a>创建测试用户

创建一组反映生产环境中真实用户的测试用户。 创建测试用户可以验证策略的工作方式是否符合预期，避免其影响实际用户并潜在性地干扰他们对应用和资源的访问。

某些组织出于此目的创建了测试租户。 但是，可能很难在测试租户中重新创建所有条件和应用来全面测试策略的结果。

### <a name="create-a-test-plan"></a>创建测试计划

测试计划非常重要，它可以在预期结果与实际结果之间进行比较。 进行测试之前，始终应该持有某种预期。 下表概述了示例测试用例。 根据 CA 策略的配置方式调整方案和预期结果。

| 策略| 场景| 预期结果 |
| - | - | - |
| [在非工作时间要求执行 MFA](untrusted-networks.md)| 经授权的用户在受信任的位置/工作时登录到应用| 不提示用户执行 MFA |
| [在非工作时间要求执行 MFA](untrusted-networks.md)| 经授权的用户不在受信任的位置/工作时登录到应用| 提示用户执行 MFA，他们可以成功登录 |
| [要求执行 MFA（针对管理员）](howto-baseline-protect-administrators.md)| 全局管理员登录到应用| 提示管理员执行 MFA |
| [有风险的登录](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)| 用户使用未经批准的浏览器登录应用| 提示管理员执行 MFA |
| [设备管理](require-managed-devices.md)| 经授权的用户尝试从已授权的设备登录| 授予访问权限 |
| [设备管理](require-managed-devices.md)| 经授权的用户尝试从未授权的设备登录| 阻止访问 |
| [有风险用户的密码更改](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)| 经授权的用户尝试使用已泄密的凭据登录（高风险登录）| 根据策略提示用户更改密码或阻止访问 |


### <a name="configure-the-test-policy"></a>配置测试策略

在 [Azure 门户](https://portal.azure.com/)中，你将在“Azure Active Directory”>“安全性”>“条件访问”下配置 CA 策略。

如果要了解有关如何创建 CA 策略的详细信息，请参阅以下示例：[用户登录到 Azure 门户时提示执行 MFA 的 CA 策略](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)。 此快速入门可帮助你：

* 熟悉用户界面

* 初步认识条件访问的工作原理

### <a name="enable-the-policy-in-report-only-mode"></a>在仅限报告模式下启用策略

若要评估策略的影响，首先在[仅限报告模式](concept-conditional-access-report-only.md)下启用策略。 仅限报告策略在登录期间评估，但不会强制实施授权控制和会话控制。 在仅限报告模式下保存策略后，可以在登录日志中看到对实时登录的影响。 从登录日志中选择一个事件，然后导航到“仅限报告”选项卡，以查看每个仅限报告策略的结果。


![仅限报告模式 ](media/plan-conditional-access/report-only-mode.png)

选择该策略，还可以查看如何使用“策略详细信息”屏幕来评估策略的分配和访问控制。 若要将策略应用到登录，必须满足每个配置的分配。 

### <a name="understand-the-impact-of-your-policies-using-the-insights-and-reporting-workbook"></a>使用见解和报告工作簿了解策略的影响

你可以在见解和报告工作簿中查看条件访问策略的总体影响。 若要访问该工作簿，需要 Azure Monitor 订阅，并且需要[将登录日志流式传输到 Log Analytics 工作区](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)。 

### <a name="simulate-sign-ins-using-the-what-if-tool"></a>使用假设工具模拟登录

验证条件访问策略的另一种方法是使用[假设工具](troubleshoot-conditional-access-what-if.md)，该工具模拟哪些策略将应用于在假设环境下登录的用户。 选择要测试的登录属性（例如，用户、应用程序、设备平台和位置），并查看要应用的策略。

> [!NOTE] 
> 尽管模拟运行可让你很好地了解 CA 策略的影响，但它不能取代实际的测试运行。

### <a name="test-your-policy"></a>测试策略

在测试计划中通过测试用户执行每个测试。

务必测试策略的排除条件。 例如，你可能从要求执行 MFA 的策略中排除了某个用户或组。 测试系统是否会提示已排除的用户执行 MFA，因为其他策略的组合可能会要求这些用户执行 MFA。

### <a name="roll-back-policies"></a>回滚策略

如果需要回滚新实施的策略，请使用以下一个或多个选项：

* 禁用策略。 禁用某个策略可确保当用户尝试登录时不应用该策略。 想要使用该策略时，可以随时重新启用它。

![启用策略图像](media/plan-conditional-access/enable-policy.png)

* 从策略中排除用户或组。 如果某个用户无法访问应用，你可以选择从策略中排除该用户。

![排除用户和组](media/plan-conditional-access/exclude-users-groups.png)

> [!NOTE]
>  应该慎用此选项，仅在用户受信任的情况下才使用。 应该尽快将该用户加回到策略或组中。

* 删除策略。 如果不再需要该策略，请将其[删除](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)。

## <a name="manage-access-to-cloud-apps"></a>管理对云应用的访问权限

使用以下管理选项来控制和管理 CA 策略：

![manage-access](media/plan-conditional-access/manage-access.png)


### <a name="named-locations"></a>命名位置

使用 CA 策略的位置条件可将访问控制设置绑定到用户的网络位置。 使用[命名位置](location-condition.md)可以创建 IP 地址范围、国家和地区的逻辑分组。

### <a name="custom-controls"></a>自定义控件

[自定义控件](controls.md)可将用户重定向到兼容的服务，以满足 Azure AD 之外的身份验证要求。 若要满足此控制要求，用户浏览器将重定向到外部服务，执行任何需要的身份验证，然后重定向回 Azure AD。 Azure AD 将验证响应，如果用户已成功完成身份验证或验证，该用户将继续留在条件访问流中。

### <a name="terms-of-use"></a>使用条款

在访问环境中的某些云应用之前，你可能希望通过使用条款 (ToU) 来获取用户的同意。 请按照以下[快速入门创建使用条款](require-tou.md)。

### <a name="classic-policies"></a>经典策略

在 [Azure 门户](https://portal.azure.com/)中，你可以在“Azure Active Directory”>“安全性”>“条件访问”下找到 CA 策略。 你的组织还可能具有不是使用此页创建的旧 CA 策略。 这些策略称为“经典策略”。 我们建议你[考虑将这些经典策略迁移到 Azure 门户中](best-practices.md)。

## <a name="troubleshoot-conditional-access"></a>排查条件访问问题

如果用户遇到 CA 策略问题，请收集以下信息以便于进行故障排除。

* 用户主体名称

* 用户显示名称

* 操作系统名称

* 时间戳（近似为正常）

* 目标应用程序

* 客户端应用程序类型（浏览器与客户端）

* 相关 ID（这对于登录是唯一的）

如果用户收到了包含“更多详细信息”链接的消息，则可以为你收集大部分此类信息。

![无法访问应用错误消息](media/plan-conditional-access/cant-get-to-app.png)

收集信息后，请参阅以下资源：

* [条件访问导致的登录问题](troubleshoot-conditional-access.md) - 使用错误消息和 Azure AD 登录日志了解与条件访问相关的意外登录结果。

* [使用假设工具](troubleshoot-conditional-access-what-if.md) - 了解为什么在特定情况下将策略应用于用户或未应用于用户，或者策略是否适用于已知状态。

## <a name="next-steps"></a>后续步骤

[详细了解多重身份验证](../authentication/concept-mfa-howitworks.md)

[详细了解标识保护](../identity-protection/overview-identity-protection.md)

[利用 Microsoft Graph API 管理 CA 策略](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta.md)
