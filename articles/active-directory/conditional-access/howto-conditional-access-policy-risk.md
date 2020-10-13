---
title: 登录基于风险的条件性访问-Azure Active Directory
description: 使用 Identity Protection 登录风险创建条件性访问策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a09c4513206bea3462577ecba49b5d77b655b0e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91628258"
---
# <a name="conditional-access-sign-in-risk-based-conditional-access"></a>条件性访问：登录基于风险的条件性访问

大多数用户的正常行为是可以跟踪的，如果其行为超出规范，则允许他们登录可能很危险。 可能需要阻止该用户，或者直接要求他们执行多重身份验证，证明自己真的是所宣称的用户。 

登录风险表示给定的身份验证请求未由标识所有者授权的概率。 具有 Azure AD Premium P2 许可证的组织可以创建包含 [Azure AD Identity Protection 登录风险检测](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)的条件性访问策略。

可在两个位置分配此策略。 组织应选择以下选项之一，以启用需要安全密码更改的基于风险的条件访问策略。

## <a name="enable-with-conditional-access-policy"></a>启用条件性访问策略

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”    。
1. 选择“新策略”  。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组”  。
   1. 在“包括”下，选择“所有用户”。  
   1. 在“排除”下选择“用户和组”，然后选择组织的紧急访问帐户或不受限帐户。 
   1. 选择“完成”  。
1. 在“云应用或操作”   >   “包括”下，选择“所有云应用”。 
1. 在 "**条件**  >  **登录风险**" 下，将 "**配置**" 设置为 **"是"**。 在 **"选择将应用此策略的登录风险级别"** 下 
   1. 选择 " **高** " 和 " **中**"。
   1. 选择“完成”。
1. 在“访问控制” > “授予”下，依次选择“授予访问权限”、“需要多重身份验证”、“选择”。  
1. 确认设置，然后将“启用策略”设置为“打开”。 
1. 选择“创建”，以便创建启用策略所需的项目。

## <a name="enable-through-identity-protection"></a>通过 Identity Protection 启用

1. 登录 **Azure 门户**。
1. 选择 " **所有服务**"，然后浏览到 **Azure AD Identity Protection**。
1. 选择 **"登录风险策略"**。
1. 在 " **分配**" 下，选择 " **用户**"。
   1. 在“包括”下，选择“所有用户”。 
   1. 在“排除”下**** 选择“选择排除的用户”****，接着选择组织的紧急访问帐户或不受限帐户，然后选择“选择”。****
   1. 选择“完成”。
1. 在“条件”下选择“登录风险”，然后选择“中等及以上”。************
   1. 选择 " **选择**"，然后选择 " **完成**"。
1. 在 "**控制**  >  **访问**" 下，选择 "**允许访问**"，然后选择 "**需要多重身份验证**"。
   1. 选择“选择”  。
1. 将 " **强制实施策略** " 设置为 **"开"**。
1. 选择“保存”。

## <a name="next-steps"></a>后续步骤

[条件访问常见策略](concept-conditional-access-policy-common.md)

[基于用户风险的条件访问](howto-conditional-access-policy-risk-user.md)

[使用条件访问仅限报告模式确定影响](howto-conditional-access-insights-reporting.md)

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)

[Azure Active Directory 标识保护是什么？](../identity-protection/overview-identity-protection.md)
