---
title: 规划 Azure Active Directory 中的条件性访问策略 |Microsoft Docs
description: 本文介绍如何计划 Azure Active Directory 的条件性访问策略。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1c75d5022432a9a57b30aabec4dd2c4f76f2f29
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671826"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>如何：在 Azure Active Directory 中规划条件访问部署

若要确保为组织中的应用和资源实现所需的访问策略，规划条件访问部署非常重要。 在部署规划阶段花费很长时间来设计所需的各种策略，以便在你选择的条件下授予或阻止对用户的访问权限。 本文档介绍了实现安全且有效的条件性访问策略时应采取的步骤。 在开始之前，请确保了解[条件访问](overview.md)的工作原理以及何时使用它。

## <a name="what-you-should-know"></a>要点

将条件访问视为一个框架，可用于控制对组织的应用和资源的访问，而不是独立的功能。 因此，某些条件访问设置需要配置其他功能。 例如，可以配置一个用于响应特定[登录风险级别](../identity-protection/howto-identity-protection-configure-risk-policies.md)的策略。 但是，基于登录风险级别的策略需要启用 [Azure Active Directory 标识保护](../identity-protection/overview-identity-protection.md)。

如果需要附加的功能，则还可能需要获取相关的许可证。 例如，虽然条件访问是 Azure AD Premium P1 功能，但 identity protection 要求 Azure AD Premium P2 许可证。

有两种类型的条件性访问策略：基线和标准。 [基准策略](baseline-protection.md)是预定义的条件性访问策略。 这些策略的目标是确保至少启用了基线安全级别。 基线策略。 基线策略适用于所有 Azure AD 版本，只提供有限的自定义选项。 如果方案需要更大的灵活性，请禁用基线策略，并在自定义标准策略中实现要求。

在标准条件访问策略中，你可以自定义所有设置以根据你的业务需求调整策略。 标准策略需要 Azure AD Premium P1 许可证。

>[!NOTE]
> 建议使用基于 Azure AD 设备的条件性访问策略，以便在初始设备身份验证后获得最佳强制。 这包括在设备不符合和设备代码流时关闭会话。

## <a name="draft-policies"></a>草拟策略

Azure Active Directory 条件性访问使你能够将云应用的保护带入新级别。 在此新级别中，云应用的访问方式取决于动态策略评估而不是静态访问配置。 使用条件性访问策略，可定义访问条件（**如果发生这种**情况）的响应（**执行此操作**）。

![原因和响应](./media/plan-conditional-access/10.png)

使用此计划模型定义要实现的每个条件访问策略。 规划练习：

- 帮助你概括每个策略的响应和条件。
- 为组织生成一个记录良好的条件性访问策略目录。 

可以使用目录来评估策略实施是否反映了组织的业务要求。 

使用以下示例模板为你的组织创建条件性访问策略：

|发生这种情况时：|这样做：|
|-|-|
|尝试访问：<br>- 访问云应用<br>- 由用户和组<br>使用：<br>- 条件 1（例如，外部企业网络）<br>- 条件 2（例如，设备平台）|阻止访问应用程序|
|尝试访问：<br>- 访问云应用<br>- 由用户和组<br>使用：<br>- 条件 1（例如，外部企业网络）<br>- 条件 2（例如，设备平台）|使用 (AND) 授予访问权限：<br>- 要求 1（例如，MFA）<br>- 要求 2（例如，设备合规性）|
|尝试访问：<br>- 访问云应用<br>- 由用户和组<br>使用：<br>- 条件 1（例如，外部企业网络）<br>- 条件 2（例如，设备平台）|使用 (OR) 授予访问权限：<br>- 要求 1（例如，MFA）<br>- 要求 2（例如，设备合规性）|

“发生这种情况时”最起码要定义尝试访问云应用（“什么”）的主体（“谁”）。 如果需要，还可以包含访问尝试是“如何”执行的。 在条件性访问中，定义了谁、什么和如何称为条件的元素。 有关详细信息，请参阅[什么是 Azure Active Directory 条件访问中的条件？](concept-conditional-access-conditions.md) 

对于“这样做”，请定义策略对访问条件做出的响应。 在响应中，可以使用附加的要求（例如，多重身份验证 (MFA)）阻止或授予访问权限。 有关完整概述，请参阅[什么是 Azure Active Directory 条件访问中的访问控制？](controls.md)  

条件与访问控制的组合表示一种条件访问策略。

![原因和响应](./media/plan-conditional-access/51.png)

有关详细信息，请参阅[需要满足哪些要求才能让策略起作用](best-practices.md#whats-required-to-make-a-policy-work)。

现在，可以决定策略的命名标准。 命名标准有助于查找策略及了解其用途，而无需在 Azure 管理门户中将其打开。 为要显示的策略命名：

- 序列号
- 策略应用到的云应用
- 响应
- 策略对谁应用
- 何时应用（如果适用）
 
![命名标准](./media/plan-conditional-access/11.png)

虽然描述性名称可帮助您概述条件访问实现的概述，但如果您需要引用会话中的策略，则序列号会很有帮助。 例如，如果在手机上交谈了其他管理员，则可以要求他们打开策略 EM063 以解决问题。

例如，以下名称指出，策略要求外部网络中使用 Dynamics CRP 应用的营销用户执行 MFA：

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

除了活动策略以外，还建议实现[在服务中断/紧急情况下充当辅助性的弹性访问控制措施](../authentication/concept-resilient-controls.md)的已禁用策略。 应急策略的命名标准应当包括更多的一些项： 

- `ENABLE IN EMERGENCY` 在开始时使名称在其他策略中脱颖而出。
- 它应当应用于的中断的名称。
- 一个排序序列号，可以帮助管理员了解应当以何顺序启用策略。 

例如，以下名称表明，如果发生 MFA 中断，此策略是第四个策略中的第四个：

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>规划策略

规划条件访问策略解决方案时，请评估是否需要创建策略来实现以下结果。 

### <a name="block-access"></a>阻止访问

用于阻止访问的选项非常强大，因为它可以：

- 优先于用户的其他所有分配
- 具有阻止整个组织登录到租户的强大权限
 
若要阻止所有用户的访问，至少应该从策略中排除一个用户（通常是紧急访问帐户）。 有关详细信息，请参阅[选择用户和组](block-legacy-authentication.md#select-users-and-cloud-apps)。  

### <a name="require-mfa"></a>要求 MFA

为了简化用户的登录体验，你可能希望允许他们使用用户名和密码登录你的云应用。 但是，一般情况下，我们至少建议在某些方案中要求采用较强形式的帐户验证。 使用条件性访问策略，你可以限制对某些情况的 MFA 要求。 

要求执行 MFA 的常见用例包括：

- [管理员的访问](howto-baseline-protect-administrators.md)
- [访问特定的应用](app-based-mfa.md) 
- [从你不信任的网络位置访问](untrusted-networks.md)。

### <a name="respond-to-potentially-compromised-accounts"></a>响应可能已泄密的帐户

使用条件性访问策略，你可以实现自动响应，使其免受可能泄露的标识的登录。 帐户泄密的可能性以风险级别的形式表示。 “标识保护”计算两种风险级别：登录风险和用户风险。 若要对登录风险实施响应，可以使用两个选项：

- 条件访问策略中[的登录风险条件](concept-conditional-access-conditions.md#sign-in-risk)
- “标识保护”中的[登录风险策略](../identity-protection/howto-sign-in-risk-policy.md) 

解决条件形式的登录风险是首选方法，因为它提供更多的自定义选项。

在“标识保护”中，用户风险级别仅用作[用户风险策略](../identity-protection/howto-user-risk-policy.md)。 

有关详细信息，请参阅[什么是 Azure Active Directory 标识保护？](../identity-protection/overview.md) 

### <a name="require-managed-devices"></a>需要托管设备

扩大用来访问云资源的受支持设备的范围有助于提高用户的工作效率。 另一方面，你可能不希望具有未知保护级别的设备访问你的环境中的某些资源。 对于受影响的资源，你应当要求用户只能使用受管理设备访问它们。 有关详细信息，请参阅[如何要求使用条件访问的云应用访问托管设备](require-managed-devices.md)。 

### <a name="require-approved-client-apps"></a>需要已批准的客户端应用

对于自带设备 (BYOD) 方案，需要做出的首要决策之一是，是需要管理整个设备，还是只管理其中的数据。 员工使用移动设备执行个人和工作任务。 既要确保提高员工的工作效率，也要防止数据丢失。 使用 Azure Active Directory （Azure AD）条件性访问，你可以将云应用的访问权限限制为可保护你的公司数据的批准的客户端应用。 有关详细信息，请参阅[如何使用条件性访问要求批准的适用于云应用访问的客户端应用](app-based-conditional-access.md)。

### <a name="block-legacy-authentication"></a>阻止传统身份验证

Azure AD 支持多个最广泛使用的身份验证和授权协议，包括旧身份验证。 如何阻止使用旧身份验证的应用访问租户的资源？ 建议仅使用条件性访问策略来阻止它们。 如有必要，只允许某些用户和特定网络位置使用基于旧身份验证的应用程序。 有关详细信息，请参阅[如何使用条件性访问阻止旧身份验证 Azure AD](block-legacy-authentication.md)。

## <a name="test-your-policy"></a>测试策略

在生产环境中实施策略之前，应通过测试来验证其行为是否符合预期。

1. 创建测试用户
1. 创建测试计划
1. 配置策略
1. 评估模拟登录
1. 测试策略
1. 清理

### <a name="create-test-users"></a>创建测试用户

若要测试策略，请创建一组与环境中的用户类似的用户。 创建测试用户可以验证策略的工作方式是否符合预期，避免其影响实际用户并潜在性地中断他们对应用和资源的访问。 

某些组织出于此目的创建了测试租户。 但是，可能很难在测试租户中重新创建所有条件和应用来全面测试策略的结果。 

### <a name="create-a-test-plan"></a>创建测试计划

测试计划非常重要，它可以在预期结果与实际结果之间进行比较。 进行测试之前，始终应该持有某种预期。 下表概述了示例测试用例。 根据 CA 策略的配置方式调整方案和预期结果。

|策略 |应用场景 |预期结果 | 结果 |
|---|---|---|---|
|[在非工作时间要求执行 MFA](/azure/active-directory/conditional-access/untrusted-networks)|经授权的用户在受信任的位置/工作时登录到应用|不提示用户执行 MFA| |
|[在非工作时间要求执行 MFA](/azure/active-directory/conditional-access/untrusted-networks)|经授权的用户不在受信任的位置/工作时登录到应用|提示用户执行 MFA，他们可以成功登录| |
|[要求执行 MFA（针对管理员）](/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|全局管理员登录到应用|提示管理员执行 MFA| |
|[有风险的登录](/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|用户使用 [Tor 浏览器](/azure/active-directory/active-directory-identityprotection-playbook)登录到应用|提示管理员执行 MFA| |
|[设备管理](/azure/active-directory/conditional-access/require-managed-devices)|经授权的用户尝试从已授权的设备登录|授予访问权限| |
|[设备管理](/azure/active-directory/conditional-access/require-managed-devices)|经授权的用户尝试从未授权的设备登录|阻止访问| |
|[有风险用户的密码更改](/azure/active-directory/identity-protection/howto-user-risk-policy)|经授权的用户尝试使用已泄密的凭据登录（高风险登录）|根据策略提示用户更改密码或阻止访问| |

### <a name="configure-the-policy"></a>配置策略

管理条件访问策略是一种手动任务。 在 Azure 门户中，你可以在一个中心位置（"条件性访问" 页）管理条件访问策略。 "条件访问" 页的一个入口点是**Active Directory**导航窗格中的 "**安全性**" 部分。 

![条件访问](media/plan-conditional-access/03.png)

如果要了解有关如何创建条件性访问策略的详细信息，请参阅[需要对具有 Azure Active Directory 条件性访问的特定应用的 MFA](app-based-mfa.md)。 此快速入门可帮助你：

- 熟悉用户界面。
- 首先了解条件访问的工作原理。 

### <a name="evaluate-a-simulated-sign-in"></a>评估模拟登录

你已经配置了条件访问策略，现在可能想知道它是否按预期工作。 第一步，使用条件访问 [what if 策略工具](what-if-tool.md)模拟测试用户登录。 该模拟会估计此登录对策略的影响并生成模拟报表。

>[!NOTE]
> 尽管模拟运行为你反映了条件性访问策略的影响，但它不会取代实际的测试运行。

### <a name="test-your-policy"></a>测试策略

根据测试计划运行测试用例。 在此步骤中，针对测试用户的每个策略运行端到端的测试，以确保每个策略的行为正确。 使用上面创建的方案执行每个测试。

必须确保测试策略的排除条件。 例如，你可能从要求执行 MFA 的策略中排除了某个用户或组。 测试是否提示已排除的用户进行 MFA，因为其他策略的组合可能需要对这些用户进行 MFA。

### <a name="cleanup"></a>清理

清理过程包括以下步骤：

1. 禁用策略。
1. 删除已分配的用户和组。
1. 删除测试用户。  

## <a name="move-to-production"></a>移到生产环境

当新策略针对你的环境准备就绪后，分阶段部署它们：

- 向最终用户提供内部变更通知。
- 从少量的用户着手，验证策略的行为是否符合预期。
- 将策略的范围扩大，使之包括更多的用户时，继续排除所有管理员。 排除管理员可以确保在需要更改的情况下，仍有人可以访问该策略。
- 仅当有必要时，才将策略应用到所有用户。

最佳做法是至少创建一个符合以下条件的用户帐户：

- 专用于策略管理
- 已从所有策略中排除

## <a name="rollback-steps"></a>回滚步骤

如果需要回滚新实施的策略，请使用以下一个或多个选项：

1. **禁用策略** - 禁用某个策略可确保当用户尝试登录时不应用该策略。 想要使用该策略时，始终可以重新启用它。

   ![禁用策略](media/plan-conditional-access/07.png)

1. **从策略中排除用户/组** - 如果某个用户无法访问应用，你可以选择从策略中排除该用户

   ![排除用户](media/plan-conditional-access/08.png)

   > [!NOTE]
   > 应该慎用此选项，仅在用户受信任的情况下才使用。 应该尽快将该用户加回到策略或组中。

1. **删除策略** - 如果不再需要该策略，请将其删除。

## <a name="next-steps"></a>后续步骤

查看 [Azure AD 条件访问文档](index.yml)以获取可用的概述信息。
