---
title: 提供 Azure AD Identity Protection-Azure Active Directory 中风险事件的反馈
description: 如何以及为何应提供有关 Identity Protection 风险事件的反馈。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bd0984a78860192f507323491952e895c8de8bf
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370201"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>如何：Azure AD Identity Protection 中提供风险反馈

Azure AD Identity Protection 允许你提供有关其风险评估的反馈。 以下文档列出了有关 Azure AD Identity Protection 的风险评估以及我们如何合并此类情况的反馈。

## <a name="what-is-a-detection"></a>什么是检测？

标识保护检测是从标识风险角度来看可疑活动的指标。 这些可疑活动称为风险事件。 这些基于标识的检测可以基于试探法、机器学习或来自合作伙伴产品。 这些检测用于确定登录风险和用户风险,

* 用户风险表示标识被泄露的概率。
* 登录风险表示登录被泄露的概率 (例如, 登录不是由标识所有者授权)。

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>为什么应向 Azure AD 的风险评估提供风险反馈？ 

提供 Azure AD 风险反馈的原因有以下几个:

- **发现 Azure AD 的用户或登录风险评估不正确**。 例如, 在 "有风险的登录" 报告中显示的登录是良性的, 而且对该登录的所有检测均为误报。
- **你验证 Azure AD 的用户或登录风险评估是正确**的。 例如, 在 "有风险的登录" 报告中显示的登录确实是恶意的, 你希望 Azure AD 了解该登录上的所有检测结果是否为 true。
- **你在 Azure AD Identity Protection 之外修正了该用户的风险**, 并且你希望更新用户的风险级别。

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Azure AD 如何使用我的风险反馈？

Azure AD 使用你的反馈来更新基础用户和/或登录的风险以及这些事件的准确性。 此反馈有助于保护最终用户的安全。 例如, 确认登录受到威胁后, Azure AD 会立即提高用户的风险, 并将登录的聚合风险 (而不是实时风险) 提升到高。 如果你的用户风险策略中包括此用户以强制高风险用户安全重置其密码, 则用户在下次登录时将自动对其进行修正。

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>我应该如何在这种情况下提供风险反馈？

下面是向 Azure AD 提供风险反馈的方案和机制。

| 应用场景 | 如何提供反馈？ | 这种情况下会发生什么情况？ | 说明 |
| --- | --- | --- | --- |
| **登录未泄露 (误报)** <br> "有风险的登录" 报表显示存在风险的登录 [风险状态 = 有风险], 但该登录不会泄露。 | 选择 "登录", 然后单击 "确认登录安全"。 | Azure AD 会将登录的聚合风险转移到 "无" [风险状态 = "已确认安全";风险级别 (聚合) =-], 将会反转其对用户风险的影响。 | 目前, "确认登录安全" 选项仅在 "有风险的登录" 报表中可用。 |
| **已泄露登录 (True)** <br> "有风险的登录" 报表显示风险很低的登录 [风险状态 = 有风险], 风险较低 [风险级别 (聚合) = Low], 而该登录确实已泄露。 | 选择 "登录", 然后单击 "确认登录已泄露"。 | Azure AD 会将登录的聚合风险和用户风险转移到高 [风险状态 = 已确认泄露;风险级别 = 高。 | 目前, "确认登录已泄露" 选项仅在 "有风险的登录" 报告中可用。 |
| **用户已泄露 (真正值)** <br> "有风险的用户" 报告显示 "风险状态 = 有风险" 的 "有风险的用户", 风险低 [风险级别 = Low], 该用户确实已泄露。 | 选择用户, 然后单击 "确认用户已泄露"。 | Azure AD 会将用户风险转移到高 [风险状态 = 已确认泄露;风险级别 = 高], 将添加新的检测 "管理员已确认用户已泄露"。 | 目前, "确认用户已泄露" 选项仅在 "有风险的用户" 报告中可用。 <br> 检测到 "管理员已确认用户已泄露" 会显示在 "有风险的用户" 报表中的 "未链接到登录的风险事件" 选项卡中。 |
| **用户在 Azure AD Identity Protection 之外修正 (真正值 + 已修正)** <br> "有风险的用户" 报表显示有风险的用户, 并随后在 Azure AD Identity Protection 之外修正了该用户。 | 1.选择用户, 然后单击 "确认用户已泄露"。 (此过程确认 Azure AD 用户确实已泄露。) <br> 2.等待用户的 "风险级别" 转为 "高"。 (这一次给出了 Azure AD 所需的时间来向风险引擎进行上述反馈。) <br> 3.选择用户, 然后单击 "消除用户风险"。 (此过程确认 Azure AD 不再危及用户的安全。) |  Azure AD 将用户风险转移到无 [风险状态 = 已消除;风险级别 =-], 并关闭所有有活动风险的现有登录的风险。 | 单击 "消除用户风险" 将会关闭用户和过去登录的所有风险。此操作无法撤消。 |
| **用户未泄露 (误报)** <br> "有风险的用户" 报告以风险用户显示, 但用户不会受到侵害。 | 选择用户, 然后单击 "消除用户风险"。 (此过程确认 Azure AD 用户是否不安全。) | Azure AD 将用户风险转移到无 [风险状态 = 已消除;风险级别 =-]。 | 单击 "消除用户风险" 将会关闭用户和过去登录的所有风险。此操作无法撤消。 |
| 我想要关闭用户风险, 但不确定用户是否已泄露/安全。 | 选择用户, 然后单击 "消除用户风险"。 (此过程确认 Azure AD 不再危及用户的安全。) | Azure AD 将用户风险转移到无 [风险状态 = 已消除;风险级别 =-]。 | 单击 "消除用户风险" 将会关闭用户和过去登录的所有风险。此操作无法撤消。 建议你通过单击 "重置密码" 来修正用户, 或者请求用户安全地重置/更改其凭据。 |

对标识保护中的用户风险事件的反馈会脱机处理, 并可能需要一些时间来更新。 风险处理状态列将提供反馈处理的当前状态。

![风险用户报表的风险处理状态](./media/howto-provide-risk-event-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>后续步骤

[Azure Active Directory Identity Protection 风险事件参考](risk-events-reference.md)