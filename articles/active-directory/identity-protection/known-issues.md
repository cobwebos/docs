---
title: Azure Active Directory 中有关“标识保护”（已刷新）的常见问题解答和已知问题 | Microsoft Docs
description: Azure Active Directory 中有关“标识保护”（已刷新）的常见问题解答和已知问题。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 01/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe7125174129752e6d6dbe0e00d01d4f32755333
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126096"
---
# <a name="faqs-and-known-issues-with-identity-protection-refreshed-in-azure-active-directory"></a>Azure Active Directory 中有关“标识保护”（已刷新）的常见问题解答和已知问题

## <a name="dismiss-user-risk-known-issues"></a>消除用户风险已知问题

经典“标识保护”中的“消除用户风险”将“标识保护”（已刷新）的用户风险历史记录中的参与者设置为 Azure AD。

“标识保护”（已刷新）中的“消除用户风险”将“标识保护”（已刷新）的用户风险历史记录中的参与者设置为\<管理员名称，并提供指向用户边栏选项卡的超链接\>。

当前存在一个已知问题，该问题导致用户风险消除流中发生延迟。 如果具有“用户风险策略”，则该策略将在单击“消除用户风险”后的几分钟内阻止应用于已消除的用户。 但是，UX 刷新已消除用户的“风险状态”时，存在已知延迟。 要解决此问题，请在浏览器级刷新页面以查看最新的用户“风险状态”。

## <a name="risky-users-report-known-issues"></a>“有风险用户”报告已知问题

username 字段上的查询区分大小写，而 Name 字段上的查询不区分大小写。

切换“日期显示形式”隐藏“风险上次更新时间”列。 要读取该列，请单击“有风险的用户”边栏选项卡顶部的“列”。

消除经典标识保护中的**所有事件**将风险检测的状态设置为 "**已关闭 (已解决)** "。

## <a name="risky-sign-ins-report-known-issues"></a>“有风险登录”报告已知问题

**解决**风险检测会将状态设置为**通过基于风险的策略驱动的用户**。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>为什么不能为每个风险检测设置自己的风险级别？

“标识保护”中的风险级别依赖于检测的精度，并由我们的监督式机器学习提供支持。 若要自定义用户体验，管理员可以在用户风险和登录风险策略中包含/排除某些用户/组。

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>为什么登录位置与用户真正登录的位置不匹配？

IP 地理位置映射是整个行业面临的挑战。 如果认为登录报告中列出的位置与实际位置不匹配，请联系支持人员。 

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>“标识保护”中的反馈机制是如何工作的？

**确认泄漏**（在登录时） - 通知 Azure AD 标识保护，该登录不是由标识所有者执行的，并指示存在泄漏。

- 在收到此反馈后，我们将登录和用户风险状态改为“已确认泄漏”，并将风险级别改为“高”。

- 此外，我们还为我们的机器学习系统提供信息，以便今后改进风险评估。

    > [!NOTE]
    > 如果已对用户采取修正措施，不要单击“确认泄漏”，因为这会将登录和用户风险状态改为“已确认泄漏”，并将风险级别改为“高”。

**确认安全**（在登录时） - 通知 Azure AD 标识保护，登录是由标识所有者执行的，不指示存在泄漏。

- 收到此反馈后，我们将登录（而非用户）风险状态改为“已确认安全”，将风险级别改为 -。

- 此外，我们还为我们的机器学习系统提供信息，以便今后改进风险评估。

    > [!NOTE]
    > 如果认为用户没有遭到泄漏，请使用用户级别的“消除用户风险”，而不是登录级别的“已确认安全”。 用户级别的**消除用户风险**会关闭用户风险以及所有过去有风险的登录和风险检测。

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>为什么当身份保护中没有显示有风险的登录或风险检测时, 为什么会看到一个用户的风险评分低 (或更高)？

如果用户风险本质上是累积性的, 并且不会过期, 即使在标识保护中没有出现最新的有风险登录或风险检测, 用户也可能面临低或更高的风险。 如果用户的唯一恶意活动超出了时间范围, 则会发生这种情况, 我们会在该时间段内存储风险登录和风险检测的详细信息。 我们不会终止用户风险，因为众所周知，恶意参与者会在客户环境的遭到泄漏的标识后停留超过 140 天，然后加大攻击力度。 客户可以通过访问 `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab` 来查看用户的风险时间线，以了解为什么用户处于风险之中。

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>为什么登录的“登录风险(总计)”分数为高，而与之相关的检测结果为低风险或中等风险？

高总计风险评分可能基于登录的其他特性，或者针对该登录触发了多个检测的事实。 反之，登录也可能为中等“登录风险(总计)”，即使与登录相关的检测为高风险。 
