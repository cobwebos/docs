---
title: 如何在 Azure Active Directory Identity Protection 中关闭活动风险事件 | Microsoft Docs
description: 了解用于关闭活动风险事件的选项。
services: active-directory
keywords: Azure Active Directory Identity Protection, Cloud App Discovery, 管理应用程序, 安全, 风险, 风险级别, 漏洞, 安全策略
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e003aec8fa5aeab587fa07acdae3a13b370a535
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60453422"
---
# <a name="how-to-close-active-risk-events"></a>如何：关闭活动风险事件

Azure Active Directory 使用[风险事件](../reports-monitoring/concept-risk-events.md)检测可能表示用户帐户已泄露的迹象。 管理员希望能够关闭所有风险事件，以便受影响的用户不再面临风险。

本文提供用于关闭活动风险事件的附加选项的概述。

## <a name="options-to-close-risk-events"></a>用于关闭风险事件的选项 

风险事件的状态为“活动”或“已关闭”。 计算称作“用户风险级别”的值时，会考虑到所有活动风险事件。 用户风险级别是反映帐户泄露可能性的一个指标（低、中、高）。 

若要关闭活动风险事件，可使用以下选项：

- 要求使用用户风险策略进行密码重置

- 手动重置密码
 
- 消除所有风险事件 

- 手动关闭单个风险事件



## <a name="require-password-reset-with-a-user-risk-policy"></a>要求使用用户风险策略进行密码重置

通过配置[用户风险条件访问策略](howto-user-risk-policy.md)，可以要求在自动检测到指定的用户风险级别时更改密码。 

![重置密码](./media/howto-close-active-risk-events/13.png)

密码重置会关闭相关用户的所有活动风险事件，并使标识恢复安全状态。 使用用户风险策略是关闭活动风险事件的首选方法，因为此方法是自动化的。 受影响的用户与支持人员或管理员之间不需要交互。

但是，不一定总适合使用用户风险策略。 例如，对于以下情况，用户风险策略就不适用：

- 用户尚未注册多重身份验证 (MFA)。
- 用户的活动风险事件已删除。
- 调查发现合法的用户执行了报告的风险事件。


## <a name="manual-password-reset"></a>手动重置密码

如果无法要求使用用户风险策略重置密码，可以使用手动密码重置关闭用户的所有风险事件。

![重置密码](./media/howto-close-active-risk-events/04.png)


相关对话框中提供了重置密码的两种不同方法：

![重置密码](./media/howto-close-active-risk-events/05.png)


**生成临时密码** - 生成临时密码可以立即让标识恢复安全状态。 此方法需要与受影响的用户交互，因为这些用户需要知道临时密码。 例如，可将新的临时密码发送到用户的备用电子邮件地址或用户的经理。 由于该密码是临时性的，因此系统会在用户下次登录时提示更改密码。


**要求用户重置密码** - 要求用户重置密码可以实现自助恢复，而无需联系支持人员或管理员。 与用户风险策略一样，此方法仅适用于已注册 MFA 的用户。 对于尚未注册 MFA 的用户，此选项不可用。


## <a name="dismiss-all-risk-events"></a>消除所有风险事件

如果密码重置不可行，也可以消除所有风险事件。 

![重置密码](./media/howto-close-active-risk-events/03.png)

单击“清除所有事件”时，所有事件都会关闭，受影响的用户不再有风险。 但是，此方法不会对现有密码产生影响，因为它不能将相关的标识恢复安全状态。 如果删除了存在活动风险事件的用户，则此方法是首选方法。 


## <a name="close-individual-risk-events-manually"></a>手动关闭单个风险事件

可以手动关闭单个风险事件。 手动关闭风险事件可以降低用户风险级别。 通常，在响应相关调查时需手动关闭风险事件。 例如，在与用户沟通时发现不再需要某个活动风险事件。 
 
手动关闭风险事件时，可以选择执行以下任一操作来更改风险事件的状态：

![操作](./media/howto-close-active-risk-events/06.png)

- **解决** - 如果在调查风险事件之后在 Identity Protection 外部采取适当的补救措施，并且应该将风险事件视为已关闭，请将事件标记为“已解决”。 解决的事件会将风险事件的状态设置为“已关闭”，此风险事件不再算作用户风险。

- **标记为误报** - 在某些情况下，可以调查某个风险事件，查明该事件是否被错误地标记为有风险。 将风险事件标记为误报，可以减少发生这种情况。 这可以帮助机器学习算法将来改善类似事件的分类。 误报事件的状态为“已关闭”，不再算作用户风险。

- **忽略** - 如果尚未采取任何补救措施，但想要从活动列表中删除风险事件，可以忽略风险事件，在这种情况下，事件状态将变为“已关闭”。 忽略的事件不算作用户风险。 这种做法只能在非常情况下使用。

- **重新激活** - 可以重新激活已手动关闭的风险事件（通过选择“解决”、“误报”或“忽略”），并将事件状态设回“活动”。 重新激活的风险事件包括在用户风险级别计算中。 无法重新激活通过补救（例如重置安全密码）关闭的风险事件。
  

## <a name="next-steps"></a>后续步骤

若要获取 Azure AD Identity Protection 的概述，请参阅 [Azure AD Identity Protection 概述](overview.md)。
