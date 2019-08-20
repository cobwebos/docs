---
title: 条件访问-基于风险的条件性访问-Azure Active Directory
description: 创建条件性访问策略以启用策略的标识保护增强
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64d1b3e2f36256164420ae6b2e699f0ef48e2e78
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576545"
---
# <a name="conditional-access-risk-based-conditional-access"></a>条件性访问:基于风险的条件性访问

具有 Azure AD Premium P2 许可证的组织可以创建包含 Azure AD Identity Protection 风险事件的条件性访问策略。 有三个可用的默认策略。 

* 要求所有用户注册 Azure 多重身份验证。
* 对于具有高风险的用户, 需要更改密码。
* 对于具有中等或高登录风险的用户, 需要多重身份验证。

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>要求所有用户注册 Azure 多重身份验证

启用此策略将要求所有用户在14天内注册 Azure 多重身份验证。 

1. 登录到 **Azure 门户**。
1. 单击“所有服务”，然后浏览到“Azure AD 标识保护”。
1. 单击“MFA 注册”。
1. 在 "**分配**" 下, 选择 "**用户**"。
   1. 在 "**包括**" 下, 选择 "**所有用户**"。
   1. 在 "**排除**" 下, 选择 "**选择排除的用户**", 选择你的组织的紧急访问或中断玻璃帐户, 然后选择 "**选择**"。 
   1. 选择“完成”。
1. 将 "**强制实施策略**" 设置为 **"开"** 。
1. 单击“保存”。

## <a name="require-a-password-change-high-risk-users"></a>要求密码更改高风险用户

Microsoft 会与研究人员、执法机构、Microsoft 安全团队以及其他受信任的源合作，以查找用户名和密码对。 当这些对中的某一对与你环境中的某个帐户匹配时，则可通过以下策略触发基于风险的密码更改。

1. 登录到 **Azure 门户**。
1. 单击“所有服务”，然后浏览到“Azure AD 标识保护”。
1. 单击 "**用户风险策略**"。
1. 在 "**分配**" 下, 选择 "**用户**"
   1. 在 "**包括**" 下, 选择 "**所有用户**"。
   1. 在 "**排除**" 下, 选择 "**选择排除的用户**", 选择你的组织的紧急访问或中断玻璃帐户, 然后选择 "**选择**"。
   1. 选择“完成”。
1. 在 "**条件**" 下, 选择 "**用户风险**", 然后选择 "**高**"。
   1. 单击 "**选择**", 然后单击 "**完成**"。
1. 在 "**控制** > **访问**" 下, 选择 "**允许访问**", 然后选择 "**需要密码更改**"。
   1. 单击“选择”。
1. 将 "**强制实施策略**" 设置为 **"开"** 。
1. 单击“保存”。

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>需要 MFA 中型或高登录风险用户

大多数用户的正常行为是可以跟踪的，如果其行为超出规范，则允许他们登录可能很危险。 你可能想要阻止该用户, 或者只要求他们执行多重身份验证, 以证明他们确实是他们所说的。 若要启用一项在检测到风险登录时会要求用户执行 MFA 的策略，请启用以下策略。

1. 登录到 **Azure 门户**。
1. 单击“所有服务”，然后浏览到“Azure AD 标识保护”。
1. 单击 "**登录风险策略**"
1. 在 "**分配**" 下, 选择 "**用户**"
   1. 在 "**包括**" 下, 选择 "**所有用户**"。
   1. 在 "**排除**" 下, 选择 "**选择排除的用户**", 选择你的组织的紧急访问或中断玻璃帐户, 然后选择 "**选择**"。
   1. 选择“完成”。
1. 在 "**条件**" 下, 选择 "**登录风险**", 然后选择 "**中" 和 "上**"。
   1. 单击 "**选择**", 然后单击 "**完成**"。
1. 在 "**控制** > **访问**" 下, 选择 "**允许访问**", 然后选择 "**需要多重身份验证**"。
   1. 单击“选择”。
1. 将 "**强制实施策略**" 设置为 **"开"** 。
1. 单击“保存”。

## <a name="next-steps"></a>后续步骤

[条件访问公用策略](concept-conditional-access-policy-common.md)

[使用条件性访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)

[工作原理：Azure 多重身份验证](../authentication/concept-mfa-howitworks.md)一文

[什么是 Azure Active Directory Identity Protection？](../identity-protection/overview.md)
