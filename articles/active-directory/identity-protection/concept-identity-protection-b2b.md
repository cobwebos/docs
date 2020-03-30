---
title: 标识保护和 B2B 用户 - Azure 活动目录
description: 与 B2B 用户使用身份保护的工作原理和已知的限制
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95ea7eb470a5880bc88b3df903d33854f363e974
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72881306"
---
# <a name="identity-protection-and-b2b-users"></a>标识保护和 B2B 用户

借助 Azure AD B2B 协作，组织可以使用身份保护为 B2B 用户强制实施基于风险的策略。 这些策略的配置方式有两种：

- 管理员可以配置适用于所有应用（包括来宾用户）的基于身份验证保护风险的策略。
- 管理员可以使用包含来宾用户的条件配置其条件访问策略，将登录风险作为条件。

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>如何为 B2B 协作用户评估风险

B2B 协作用户的用户风险在其主目录中进行评估。 当这些用户尝试访问资源时，将在资源目录中评估他们的实时登录风险。

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>B2B 协作用户的身份保护限制

资源目录中的 B2B 协作用户的身份保护实施存在限制，因为它们的标识存在于其主目录中。 主要限制如下：

- 如果来宾用户触发身份保护用户风险策略以强制重置密码，**则会阻止它们**。 此块是由于无法重置资源目录中的密码。
- **来宾用户未显示在风险用户报告中**。 这种可见性损失是由于 B2B 用户主目录中发生的风险评估造成的。
- 管理员**无法在其资源目录中关闭或修复风险的 B2B 协作用户**。 这种功能丢失是由于资源目录中的管理员无法访问 B2B 用户主目录造成的。

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>为什么我无法修复目录中有风险的 B2B 协作用户？

B2B 用户的风险评估和补救发生在其主目录中。 由于此事实，来宾用户未出现在资源目录中的风险用户报告中，资源目录中的管理员无法强制对这些用户进行安全密码重置。

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>如果 B2B 协作用户由于组织中基于风险的策略而被阻止，我该怎么办？

如果目录中的风险 B2B 用户被基于风险的策略阻止，则用户将需要在其主目录中修复该风险。 用户可以通过在主目录中执行安全密码重置来修复其风险。 如果他们在主目录中未启用自助服务密码重置，则需要联系自己组织的 IT 员工，让管理员手动消除其风险或重置密码。

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>如何防止 B2B 协作用户受到基于风险的策略的影响？

将 B2B 用户从组织的基于风险的条件访问策略中排除，将防止 B2B 用户受到其风险评估的影响或阻止。 要排除这些 B2B 用户，请在 Azure AD 中创建一个包含组织所有来宾用户的组。 然后，添加此组作为对内置身份保护用户风险和登录风险策略以及使用登录风险作为条件的任何条件访问策略的排除。

## <a name="next-steps"></a>后续步骤

请参阅以下有关 Azure AD B2B 协作的文章：

- [什么是 Azure AD B2B 协作？](../b2b/what-is-b2b.md)
