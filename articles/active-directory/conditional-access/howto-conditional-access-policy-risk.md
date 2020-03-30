---
title: 条件访问 - 基于风险的条件访问 - Azure Active Directory
description: 创建条件访问策略，以便启用对策略的标识保护增强功能
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8054d8985596095db32d9262322d7fb0f4aab8c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295157"
---
# <a name="conditional-access-risk-based-conditional-access"></a>条件访问：基于风险的条件访问

使用 Azure AD Premium P2 许可证的组织可以创建纳入 Azure AD 标识保护风险检测的条件访问策略。 有三个可以立即启用的默认策略。 

* 要求所有用户注册 Azure 多重身份验证。
* 要求高风险用户更改密码。
* 要求登录风险为中到高的用户执行多重身份验证。

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>要求所有用户注册 Azure 多重身份验证

启用此策略后，所有用户必须在 14 天内注册 Azure 多重身份验证。 

1. 登录到 Azure**门户**。
1. 单击“所有服务”，然后浏览到“Azure AD 标识保护”。********
1. 单击“MFA 注册”。****
1. 在 **"分配"** 下，选择 **"用户**"。
   1. 在 **"包括**"下，选择**所有用户**。
   1. 在“排除”下**** 选择“选择排除的用户”****，接着选择组织的紧急访问帐户或不受限帐户，然后选择“选择”。**** 
   1. 选择 **"完成**"。
1. 将 **"强制策略**"设置为 **"打开**"。
1. 单击“保存”。****

## <a name="require-a-password-change-high-risk-users"></a>要求高风险用户更改密码

Microsoft 会与研究人员、执法机构、Microsoft 安全团队以及其他受信任的源合作，以查找用户名和密码对。 当这些对中的某一对与你环境中的某个帐户匹配时，则可通过以下策略触发基于风险的密码更改。

1. 登录到 Azure**门户**。
1. 单击“所有服务”，然后浏览到“Azure AD 标识保护”。********
1. 单击**用户风险策略**。
1. 在“分配”**** 下，选择“用户”****
   1. 在 **"包括**"下，选择**所有用户**。
   1. 在“排除”下**** 选择“选择排除的用户”****，接着选择组织的紧急访问帐户或不受限帐户，然后选择“选择”。****
   1. 选择 **"完成**"。
1. 在“条件”下选择“用户风险”，然后选择“高”。************
   1. 单击 **"选择**"然后**完成**。
1. 在 **"控制** > **访问**"下，选择 **"允许访问**"，然后选择 **"需要密码更改**"。
   1. 单击“选择”。
1. 将 **"强制策略**"设置为 **"打开**"。
1. 单击“保存”。****

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>要求中或高登录风险用户进行 MFA

大多数用户的正常行为是可以跟踪的，如果其行为超出规范，则允许他们登录可能很危险。 可能需要阻止该用户，或者直接要求他们执行多重身份验证，证明自己真的是所宣称的用户。 若要启用一项在检测到风险登录时会要求用户执行 MFA 的策略，请启用以下策略。

1. 登录到 Azure**门户**。
1. 单击“所有服务”，然后浏览到“Azure AD 标识保护”。********
1. 单击**登录风险策略**
1. 在“分配”**** 下，选择“用户”****
   1. 在 **"包括**"下，选择**所有用户**。
   1. 在“排除”下**** 选择“选择排除的用户”****，接着选择组织的紧急访问帐户或不受限帐户，然后选择“选择”。****
   1. 选择 **"完成**"。
1. 在“条件”下选择“登录风险”，然后选择“中等及以上”。************
   1. 单击 **"选择**"然后**完成**。
1. 在 **"控制** > **访问**"下，选择 **"允许访问**"，然后选择 **"需要多重身份验证**"。
   1. 单击“选择”。
1. 将 **"强制策略**"设置为 **"打开**"。
1. 单击“保存”。****

## <a name="next-steps"></a>后续步骤

[条件访问常见策略](concept-conditional-access-policy-common.md)

[使用仅条件访问报告模式确定影响](howto-conditional-access-report-only.md)

[使用条件访问"如果"工具模拟登录行为](troubleshoot-conditional-access-what-if.md)

[工作原理：Azure 多重身份验证](../authentication/concept-mfa-howitworks.md)

[Azure Active Directory 标识保护是什么？](../identity-protection/overview.md)
