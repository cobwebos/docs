---
title: 适用于你的组织的 Azure 多重身份验证 - Azure Active Directory
description: 了解你的组织可以基于其许可模式使用的 Azure 多重身份验证功能
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf05532f81d70d4d7b09de2ccbf9807273a85330
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83799592"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>适用于你的组织的 Azure 多重身份验证概述

可以通过多种方式基于你的组织拥有的许可证为 Azure Active Directory (AD) 用户启用 Azure 多重身份验证。 

![调查信号并根据需要强制实施 MFA](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

根据我们的调查，如果你使用多重身份验证 (MFA)，有 99.9% 以上的概率可以避免你的帐户遭到入侵。

那么，为避免遭到入侵，组织应如何免费启用 MFA 呢？

## <a name="free-option"></a>免费选项

利用 Azure AD 免费权益的客户可以使用[安全默认值](../fundamentals/concept-fundamentals-security-defaults.md)在其环境中启用多重身份验证。

## <a name="microsoft-365-business-e3-or-e5"></a>Microsoft 365 商业版、E3 或 E5

具有 Office 365 的客户可以使用两个选项：

* 针对所有用户的所有登录事件启用或禁用 Azure 多重身份验证。 无法做到仅为一部分用户或者仅在特定的情况下启用多重身份验证。 管理是通过 Office 365 门户进行的。 
* 若要改进用户体验，请升级到 Azure AD Premium P1 或 P2 并使用条件访问。 有关详细信息，请参阅“使用多重身份验证保护 Office 365 资源”。

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

对于具有 Azure AD Premium P1 的客户，或者具有包含此功能的类似许可证（例如企业移动性 + 安全性 E3、Microsoft 365 F1 或 Microsoft 365 E3）的客户： 

在特定的情况下或者发生特定事件期间，根据业务要求使用 [Azure AD 条件访问](../authentication/tutorial-enable-azure-mfa.md)提示用户执行多重身份验证。

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

对于具有 Azure AD Premium P2 的客户，或者具有包含此功能的类似许可证（例如企业移动性 + 安全性 E5 或 Microsoft 365 E5）的客户： 

提供了最强的安全保障和改进的用户体验。 在 Azure AD Premium P1 功能的基础上增加了[基于风险的条件访问](../conditional-access/howto-conditional-access-policy-risk.md)，以适应用户模式并尽量减少出现多重身份验证提示。

## <a name="authentication-methods"></a>身份验证方法

|   | 安全默认值 | 所有其他方法 |
| --- | --- | --- |
| 通过移动应用发送通知 | X | X |
| 移动应用或硬件标志提供的验证码 |   | X |
| 向手机发送短信 |   | X |
| 拨打电话 |   | X |

## <a name="next-steps"></a>后续步骤

若要开始，请参阅有关[使用 Azure 多重身份验证保护用户登录事件](../authentication/tutorial-enable-azure-mfa.md)的教程。

有关许可的详细信息，请参阅 [Azure 多重身份验证的功能和许可证](../authentication/concept-mfa-licensing.md)。
