---
title: 提供有关在 Azure AD Identity Protection-Azure Active Directory 风险事件的反馈
description: 如何以及为何应您提供反馈 Identity Protection 风险事件。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66d53590e89afb1a903b22ff60e32871a1502ada
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827900"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>如何：在 Azure AD Identity Protection 提供风险反馈

Azure AD Identity Protection，可提供有关其风险评估的反馈。 下面的文档列出了你想要提供对 Azure AD Identity Protection 风险评估和我们是如何融入其反馈的方案。

## <a name="what-is-a-detection"></a>什么是检测？

Identity Protection 检测是从标识风险角度来看的可疑活动的指示器。 这些可疑活动称为风险事件。 这些基于标识检测可以基于启发、 机器学习或可以来自合作伙伴产品。 这些检测用来确定登录风险和用户风险

* 用户风险表示标识入侵的概率。
* 登录风险表示登录遭到破坏的概率 （例如，登录未经授权的标识所有者）。

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>为什么要向 Azure AD 的风险评估提供风险反馈？ 

为什么应提供 Azure AD 风险反馈的几个原因有：

1. **找到 Azure AD 的用户或登录风险评估不正确**。 例如，有风险登录报告中显示已登录良性并在该登录的所有检测都的误报。
1. **验证 Azure AD 的用户或登录风险评估已正确**。 例如，有风险登录报告中显示已登录确实恶意和想要 Azure AD 知道在该登录的所有检测都是真警报。
1. **修正 Azure AD Identity Protection 外部用户的风险**并且你想要更新的用户的风险级别。

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Azure AD 如何使用我风险的反馈？

Azure AD 使用你的反馈来更新的基础用户和/或单一登录的风险。 此反馈有助于保护最终用户。 例如，一旦确认遭到破坏的登录，Azure AD 立即增加用户的风险和登录的聚合风险 （不是实时风险） 到高。 如果此用户包含在您的用户风险策略来强制高风险用户安全地重置其密码，用户会自动修正本身在下次登录时。

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>如何应提供风险反馈和实质上会发生什么情况？

以下是方案和机制，以向 Azure AD 的风险的反馈。

| 场景 | 如何提供反馈？ | 实质上会发生什么情况？ | 说明 |
| --- | --- | --- | --- |
| **登录不会泄露 （误报）** <br> 有风险的登录报表显示有危险单一登录 [风险状态 = 面临风险]，但登录不损害。 | 选择单一登录，然后单击确认登录安全。 | Azure AD 将登录-聚合风险切换到无模式 [风险状态 = 已确认安全;风险级别 （聚合） =-] 和将反转其影响的用户风险。 | 目前，确认登录安全选项才可用有风险登录报告中。 |
| **在登录泄露 （真警报）** <br> 有风险的登录报表显示有危险单一登录 [风险状态面临风险 =] 使用低风险 [风险级别 （聚合） = 低] 与该登录确实入侵。 | 选择单一登录，然后单击确认登录泄露。 | Azure AD 将移到高登录-聚合风险和用户风险 [风险状态 = 已确认破坏;风险级别 = 高]。 | 目前，确认登录泄露选项才可用有风险登录报告中。 |
| **用户入侵 （真警报）** <br> 有风险的用户报表显示存在风险的用户 [风险状态有风险 =] 使用低风险 [风险级别 = 低] 和该用户确实已受到危害。 | 选择用户，然后单击确认用户泄露。 | Azure AD 将为高移动用户风险 [风险状态 = 已确认破坏;风险级别 = 高] 和将添加新的检测管理员确认用户受威胁的。 | 目前，确认用户泄漏选项才可用有风险的用户报表中。 <br> 检测管理员确认用户受威胁的有风险的用户报表中显示未链接到登录风险事件选项卡中。 |
| **Azure AD Identity Protection （真警报 + Remediated） 之外的用户进行修正** <br> 有风险的用户报表显示存在风险的用户以及我随后进行修正在 Azure AD Identity Protection 外部用户。 | 1.选择用户并单击确认用户泄露。 （此过程将确认到 Azure AD 用户确实已受到危害。） <br> 2.等待用户的风险级别以转到高。 （此时间向提供了 Azure AD 所需的时间，以更高版本的反馈到风险引擎。） <br> 3.选择用户并单击关闭用户风险。 （此过程将确认到 Azure AD，用户不再受到。） |  Azure AD 将用户风险移动为无 [风险状态 = 已忽略;风险级别 =-] 并关闭所有现有登录名具有活动风险的风险。 | 单击解除用户风险，将关闭所有风险的用户和过去的登录名。此操作无法撤消。 |
| **用户不会泄露 （误报）** <br> 有风险的用户报表显示在存在风险的用户，但用户不会泄露。 | 选择用户并单击关闭用户风险。 （此过程将确认到 Azure AD 用户不会泄露。） | Azure AD 将用户风险移动为无 [风险状态 = 已忽略;风险级别 =-]。 | 单击解除用户风险，将关闭所有风险的用户和过去的登录名。此操作无法撤消。 |
| 我想要关闭用户风险，但我不确定用户是否遭到入侵 / 安全。 | 选择用户并单击关闭用户风险。 （此过程将确认到 Azure AD，用户不再受到。） | Azure AD 将用户风险移动为无 [风险状态 = 已忽略;风险级别 =-]。 | 单击解除用户风险，将关闭所有风险的用户和过去的登录名。此操作无法撤消。 我们建议你通过单击重置密码上的修正用户或请求其凭据的用户可以安全地重置/更改。 |

Identity Protection 中的用户风险事件的反馈进行脱机处理，可能需要一些时间来更新。 风险处理状态列将提供反馈处理的当前状态。

![风险有风险的用户的报告处理状态](./media/howto-provide-risk-event-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>后续步骤

[Azure Active Directory Identity Protection 风险事件参考](risk-events-reference.md)