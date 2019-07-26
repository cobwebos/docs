---
title: 如何在 Azure Active Directory 标识保护（已刷新）中提高检测精度 | Microsoft Docs
description: 如何在 Azure Active Directory 标识保护（已刷新）中提高检测精度。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32bb8de7970fc167a6a95e9d9c3c71e4e1dc0150
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333949"
---
# <a name="how-to-improve-the-detection-accuracy"></a>如何：提高检测精度 

“标识保护”提供了可将环境中的风险检测反馈给 Azure AD 的机制。 若要提供反馈，可以确认检测到的有风险的用户或登录事件的状态。 Microsoft 用户利用此反馈对当前的风险检测采取措施，并提高未来检测精度。 

## <a name="what-is-detection"></a>什么是检测？

检测是识别用户帐户可疑活动的过程。 Azure AD 可以检测到的可疑活动称为[风险事件](../reports-monitoring/concept-risk-events.md)。 该检测过程基于自适应机器学习算法和启发来检测用户的风险事件。

检测结果用于确定用户和登录是否面临风险。 

## <a name="how-can-i-improve-the-detection-accuracy"></a>如何才能提高检测精度？

由于检测是一个自动化过程, 因此 Azure AD 报告误报。 你可以通过向 Azure AD 提供关于检测结果的反馈来提高检测精度。

有三种方法可以提高检测准确性: 确认泄露登录、确认安全登录和消除用户风险。 可以从下列报告执行操作：

- 有风险的登录报告 - 在“有风险的登录报告”中，你可以确认登录是安全还是有泄露风险
- 有风险的用户报告 - 在“有风险的用户报告”中，可以消除用户风险 

你的反馈将由 Azure AD 处理，以提高检测结果的准确性。 通常情况下，可在用户风险或登录风险调查中提供反馈。 有关详细信息，请参阅[如何调查有风险的用户和登录](howto-investigate-risky-users-signins.md)。

## <a name="confirm-compromised"></a>确认泄露

确认登录事件向 Azure AD 发出泄露信号，表明该登录未经标识所有者的授权。 如果选择“确认泄露”，Azure AD 会

- 将受影响用户的风险提高至高级别。
- 帮助优化检测风险事件的机器学习
- 执行其他措施来进一步保护组织

要确认已泄露的登录：

- 有风险的登录报告 - 此选项使你能够确认一个或多个遭泄露的登录事件。

   ![消除用户风险](./media/howto-improve-detection-accuracy/07.png)

- 有风险的登录报告的详细信息视图 - 此选项使你能够确认有风险的登录报告中所选登录事件的帐户是否遭泄露。 

   ![消除用户风险](./media/howto-improve-detection-accuracy/04.png)
 
## <a name="confirm-safe"></a>确认安全

确认登录事件向 Azure AD 发出安全信号，表明该登录已得到相应标识所有者的授权。 在选择“确认安全”时，Azure AD 会：

- 将所选登录的用户风险恢复原样
- 关闭潜在风险事件
- 帮助优化检测风险事件的机器学习
- 执行其他措施来进一步保护组织
 
要确认安全登录：

- 有风险的登录报告 - 此选项使你能够确认一个或多个安全登录事件。

   ![消除用户风险](./media/howto-improve-detection-accuracy/08.png)

- 有风险的登录报告的详细信息视图 - 此选项使你能够确认有风险的登录报告中所选登录事件是否为安全登录。 

   ![消除用户风险](./media/howto-improve-detection-accuracy/05.png)

## <a name="dismiss-user-risk"></a>消除用户风险

如果已经为风险用户采取了补救措施，或者认为它们被误报为有风险，则可以消除用户风险。 消除用户风险将使用户恢复到无风险状态。 所选用户过去所有有风险的登录和风险事件都将被消除。

例如，可以通过以下选项消除报告的用户风险：

- 有风险的用户报告 - 此选项可以消除一个或多个所选用户的用户风险。

   ![消除用户风险](./media/howto-improve-detection-accuracy/02.png)

- 详细信息视图 - 此选项可以消除用户风险报告中所选用户的用户风险。 

   ![消除用户风险](./media/howto-improve-detection-accuracy/01.png)

**应了解的内容：**

- 无法还原此操作。
- 完成此操作可能需要几分钟时间, 这就是不应重新提交请求的原因。
- 你只能在 AD 管理用户凭据的情况下执行此操作。 

## <a name="best-practices"></a>最佳实践

消除用户风险是取消阻止用户风险的一种方法, 如果这些风险已被用户风险策略阻止并且由于未启用密码重置和/或 MFA 而无法进行自我修复。 在此情况下，最好确保用户随后注册了密码重置和 MFA，以便在将来能够自行修复任何风险事件。

## <a name="next-steps"></a>后续步骤

若要获取“Azure AD 标识保护”的概述，请参阅 [Azure AD 标识保护概述](overview-v2.md)。
