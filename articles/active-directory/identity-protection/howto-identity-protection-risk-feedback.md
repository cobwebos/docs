---
title: 在 Azure Active Directory Identity Protection 中提供风险反馈
description: 如何以及为何要针对标识保护风险检测提供反馈。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4be8290f12d64f0c556100c63ec159bd414c6fcb
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382096"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>如何：在 Azure AD Identity Protection 中提供风险反馈

Azure AD 标识保护允许针对其风险评估提供反馈。 以下文档列出了在哪种情况下需要针对 Azure AD 标识保护的风险评估提供反馈，以及如何整合反馈。

## <a name="what-is-a-detection"></a>什么是检测？

标识保护检测从标识风险的角度来反映可疑活动。 这些可疑活动称为风险检测。 这些基于标识的检测可以基于试探法和机器学习，或者来自于合作伙伴产品。 这些检测用于确定登录风险和用户风险。

* 用户风险表示标识遭到入侵的概率。
* 登录风险表示登录凭据遭到入侵的概率（例如，登录未由标识所有者授权）。

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>为何应针对 Azure AD 的风险评估提供风险反馈？ 

提供 Azure AD 风险反馈的原因有多种：

- **你发现 Azure AD 的用户风险或登录风险评估不正确**。 例如，“有风险的登录”报告中显示的登录是合法的，针对该登录的所有检测结果均为误报。
- **你已验证 Azure AD 的用户风险或登录风险评估是正确的**。 例如，“有风险的登录”报告中显示的登录确实是恶意的，你希望 Azure AD 知道针对该登录的所有检测结果均为漏报。
- **你已在 Azure AD 标识保护的外部修正了该用户的风险**，希望更新用户的风险级别。

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Azure AD 如何使用我的风险反馈？

Azure AD 使用你的反馈来更新底层用户和/或登录的风险以及这些事件的准确性。 此反馈有助于保护最终用户的安全。 例如，在确认登录凭据遭到入侵后，Azure AD 会立即将用户的风险以及登录的聚合风险（而不是实时风险）提升为到“高”。 如果此用户已包含在用户风险策略中，以强制高风险用户以安全方式重置其密码，该用户在下次登录时，将自动地自我修正。

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>如何提供风险反馈，幕后会发生什么情况？

下面是向 Azure AD 提供风险反馈的方案和机制。

| 应用场景 | 如何提供反馈？ | 幕后会发生什么情况？ | 说明 |
| --- | --- | --- | --- |
| **登录凭据未遭到入侵（误报）** <br> “有风险的登录”报告显示登录风险 [风险状态 = 有风险]，但登录凭据未遭到入侵。 | 选择该登录活动，然后单击“确认登录安全”。 | Azure AD 会将登录的聚合风险改为“无”[风险状态 = 确认安全；风险级别(聚合) = -]，并消除它对用户风险造成的影响。 | 目前，“确认登录安全”选项仅在“有风险的登录”报告中可用。 |
| **登录凭据已遭到入侵（漏报）** <br> “有风险的登录”报告显示登录有风险 [风险状态 = 有风险]，其风险较低 [风险级别(聚合) = 低]，而且登录凭据确实已遭到入侵。 | 选择该登录活动，然后单击“确认登录凭据已遭到入侵”。 | Azure AD 会将登录的聚合风险和用户风险改为“高”[风险状态 = 确认已遭到入侵；风险级别 = 高]。 | 目前，“确认登录凭据已遭到入侵”选项仅在“有风险的登录”报告中可用。 |
| **用户已遭到入侵（漏报）** <br> “有风险的用户”报告显示用户有风险 [风险状态 = 有风险]，其风险较低 [风险级别 = 低]，而且该用户确实已遭到入侵。 | 选择该用户，然后单击“确认用户已遭到入侵”。 | Azure AD 会将用户风险改为“高”[风险状态 = 确认已遭到入侵；风险级别 = 高]，并将添加新的检测“管理员确认用户已遭到入侵”。 | 目前，“确认用户遭到入侵”选项仅在“有风险的用户”报告中可用。 <br> 检测“管理员确认用户已遭到入侵”显示在“有风险的用户”报告中的“未链接到登录的风险检测”选项卡上。 |
| **用户已在 Azure AD 标识保护外部修正（漏报 + 已修正）** <br> “有风险的用户”报告显示用户有风险，以及“我随后已在 Azure AD 标识保护外部修正该用户”。 | 1. 选择用户，然后单击 "确认用户已泄露"。 （此过程向 Azure AD 确认该用户确实已遭到入侵。） <br> 2. 等待用户的 "风险级别" 转为 "高"。 （Azure AD 可以利用这段时间向风险引擎引入上述反馈。） <br> 3. 选择用户，然后单击 "消除用户风险"。 （此过程向 Azure AD 确认该用户不再遭到入侵。） |  Azure AD 会将用户风险改为“无”[风险状态 = 已消除；风险级别 = -]，并关闭所有具有活动风险的现有登录的风险。 | 单击 "消除用户风险" 将会关闭用户和过去登录的所有风险。此操作无法撤消。 |
| **用户未遭到入侵（误报）** <br> “有风险的用户”报告显示用户有风险，但该用户并未遭到入侵。 | 选择该用户，然后单击“消除用户风险”。 （此过程向 Azure AD 确认该用户未遭到入侵。） | Azure AD 会将用户风险改为“无”[风险状态 = 已消除；风险级别 = -]。 | 单击 "消除用户风险" 将会关闭用户和过去登录的所有风险。此操作无法撤消。 |
| 我想要关闭用户风险，但不确定该用户是否遭到入侵/安全。 | 选择该用户，然后单击“消除用户风险”。 （此过程向 Azure AD 确认该用户不再遭到入侵。） | Azure AD 会将用户风险改为“无”[风险状态 = 已消除；风险级别 = -]。 | 单击 "消除用户风险" 将会关闭用户和过去登录的所有风险。此操作无法撤消。 建议通过单击“重置密码”来修正用户，或者请求用户以安全方式重置/更改其凭据。 |

针对标识保护中用户风险检测的反馈将会脱机处理，可能需要一段时间才能更新。 风险处理状态列将提供当前的反馈处理状态。

![有风险用户报告的风险处理状态](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>后续步骤

- [Azure Active Directory 标识保护风险检测参考](risk-events-reference.md)
