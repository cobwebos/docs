---
title: 标识保护和 B2B 用户-Azure Active Directory
description: 对 B2B 用户使用 Identity Protection 其工作原理和已知限制
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5d374f4ddac2e4d013bab1c5e88a87af1da2c1f
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667136"
---
# <a name="identity-protection-and-b2b-users"></a>标识保护和 B2B 用户

使用 Azure AD B2B 协作, 组织可以使用 Identity Protection 为 B2B 用户强制实施基于风险的策略。 这些策略的配置方式有两种:

- 管理员可以配置适用于所有应用的内置 Identity Protection 基于风险的策略, 包括来宾用户。
- 管理员可以配置其条件性访问策略, 并使用包括来宾用户的条件登录风险。

## <a name="how-is-risk-evaluated-for-b2b-collaboration-users"></a>如何为 B2B 协作用户评估风险

B2B 协作用户的用户风险在其主目录中进行评估。 用户尝试访问资源时, 会在资源目录中评估这些用户的实时登录风险。

## <a name="limitations-of-identity-protection-for-b2b-collaboration-users"></a>B2B 协作用户的标识保护限制

由于资源目录中的 B2B 协作用户的标识已存在, 因此在此实现中存在一些限制。 主要限制如下:

- 如果来宾用户触发 Identity Protection 用户风险策略来强制重置密码, 则将阻止用户进行密码重置。 此块的原因是无法在资源目录中重置密码。
- 来宾用户不会出现在有风险的用户报表中。 此可见性损失是因为 B2B 用户的主目录中发生了风险评估。
- 管理员无法在其资源目录中消除或修正有风险的 B2B 协作用户。 此功能损失是因为资源目录中的管理员无法访问 B2B 用户的主目录。

### <a name="why-cant-i-remediate-risky-b2b-collaboration-users-in-my-directory"></a>为什么无法在目录中修正有风险的 B2B 协作用户？

B2B 用户的风险评估和修正发生在其主目录中。 由于此事实, 来宾用户不会出现在资源目录中的 "有风险的用户" 报表中, 并且资源目录中的管理员不能为这些用户强制进行安全密码重置。

### <a name="what-do-i-do-if-a-b2b-collaboration-user-was-blocked-due-to-a-risk-based-policy-in-my-organization"></a>如果 B2B 协作用户由于组织中基于风险的策略而被阻止, 我该怎么办？

如果你的基于风险的策略阻止了你的目录中有风险的 B2B 用户, 则用户需要在其主目录中修正此风险。 用户可以通过在其主目录中执行安全的密码重置来修正他们的风险。 如果用户的主目录中没有启用自助密码重置, 则他们需要联系其自己的组织的 IT 人员, 以使管理员能够手动解除其风险或重置其密码。

### <a name="how-do-i-prevent-b2b-collaboration-users-from-being-impacted-by-risk-based-policies"></a>如何实现阻止 B2B 协作用户受到基于风险的策略的影响吗？

从组织的基于风险的条件性访问策略中排除 B2B 用户会阻止 B2B 用户受到风险评估的影响或阻止。 若要排除这些 B2B 用户, 请在 Azure AD 中创建一个包含组织的所有来宾用户的组。 然后, 将此组添加为内置身份保护用户风险和登录风险策略的排除项, 以及将登录风险作为条件的任何条件性访问策略。

## <a name="next-steps"></a>后续步骤

请参阅以下有关 Azure AD B2B 协作的文章：

- [什么是 Azure AD B2B 协作？](../b2b/what-is-b2b.md)
- [什么是条件性访问？](../conditional-access/overview.md)
