---
title: 为组织启用多重身份验证-Azure Active Directory
description: 根据你的许可证为你的组织启用 Azure MFA
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98bf62dff3e6c642f5de775f2d5bf8682ecea169
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164921"
---
# <a name="enable-multi-factor-authentication-for-your-organization"></a>为组织启用多重身份验证

可以通过多种方式根据你的组织拥有的许可证为你的 Azure Active Directory （AD）用户启用 Azure 多重身份验证（MFA）。 

![调查信号并在需要时强制执行 MFA](./media/concept-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

根据我们的调查，如果你使用 MFA，你的帐户将不会受到超过99.9% 的可能性。

那么，在对统计信息进行统计之前，组织如何免费启用多重身份验证？

## <a name="free-option"></a>免费选项

利用 Azure AD 的免费权益的客户可以使用[安全默认值](../conditional-access/concept-conditional-access-security-defaults.md)在其环境中启用多重身份验证。

## <a name="office-365"></a>PowerApps

对于具有 Office 365 的客户，有两个选项：

- 可以通过 Azure AD 启用[安全默认设置](../conditional-access/concept-conditional-access-security-defaults.md)以使用 Azure 多重身份验证保护所有用户。
- 如果你的组织需要更多粒度来提供多重身份验证，则你的 Office 许可证包括[每用户 MFA](../authentication/howto-mfa-userstates.md)功能。 每个用户的 MFA 分别在每个用户上启用并强制执行。

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

对于具有 Azure AD Premium P1 或类似许可证的客户，包括此功能企业移动性 + 安全性 E3、Microsoft 365 F1 或 Microsoft 365 E3： 

建议使用[条件性访问策略](../conditional-access/concept-conditional-access-policy-common.md)以获得最佳用户体验。

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

对于具有 Azure AD Premium P2 或类似许可证的客户，其中包含此功能，如企业移动性 + 安全性 E5 或 Microsoft 365 E5： 

建议将[条件访问策略](../conditional-access/concept-conditional-access-policy-common.md)与[Identity Protection](../identity-protection/overview-v2.md)风险策略结合使用，以获得最佳用户体验和强制实施灵活性。

## <a name="authentication-methods"></a>身份验证方法

|   | 安全默认值 | 所有其他方法 |
| --- | --- | --- |
| 通过移动应用发送通知 | X | X |
| 移动应用或硬件标志提供的验证码 |   | X |
| 向手机发送短信 |   | X |
| 拨打电话 |   | X |
| 应用密码 |   | X * * |

\* * 只有在管理员启用了旧身份验证方案的情况下，才能在每用户 MFA 中使用应用密码。

## <a name="next-steps"></a>后续步骤

[Azure AD 定价页](https://azure.microsoft.com/pricing/details/active-directory/)
