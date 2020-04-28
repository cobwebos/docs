---
title: 适用于你的组织的 Azure 多重身份验证-Azure Active Directory
description: 了解你的组织基于你的许可证型号的 Azure 多重身份验证的可用功能
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
ms.openlocfilehash: febdb708c637ac322c0ca884eae627da9bd5904c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79530387"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>组织的 Azure 多重身份验证概述

可以通过多种方式根据你的组织拥有的许可证为你的 Azure Active Directory （AD）用户启用 Azure 多重身份验证。 

![调查信号并在需要时强制执行 MFA](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

如果你使用多重身份验证（MFA），你的帐户将基于我们的研究，降低99.9% 的概率。

那么，在对统计信息进行统计之前，组织甚至如何免费启用 MFA？

## <a name="free-option"></a>免费选项

利用 Azure AD 的免费权益的客户可以使用[安全默认值](../fundamentals/concept-fundamentals-security-defaults.md)在其环境中启用多重身份验证。

## <a name="office-365-business-premium-e3-or-e5"></a>Office 365 商业高级版、E3 版或 E5 版

对于具有 Office 365 的客户，有两个选项：

* 针对所有用户的所有登录事件启用或禁用 Azure 多重身份验证。 不能只为一部分用户或者只是在特定的方案中启用多重身份验证。 通过 Office 365 门户进行管理。 
* 若要改进用户体验，请升级到 Azure AD Premium P1 或 P2 并使用条件访问。 有关详细信息，请参阅使用多重身份验证保护 Office 365 资源。

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

对于具有 Azure AD Premium P1 或类似许可证的客户，包括此功能企业移动性 + 安全性 E3、Microsoft 365 F1 或 Microsoft 365 E3： 

使用[Azure AD 条件性访问](../conditional-access/overview.md)，在特定方案或事件期间提示用户进行多重身份验证，以满足你的业务要求。

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

对于具有 Azure AD Premium P2 或类似许可证的客户，其中包含此功能，如企业移动性 + 安全性 E5 或 Microsoft 365 E5： 

提供最强的安全形势和改进的用户体验。 将[基于风险的条件访问](../conditional-access/howto-conditional-access-policy-risk.md)添加到可适应用户模式的 Azure AD Premium P1 功能，并将多因素身份验证提示降到最低。

## <a name="authentication-methods"></a>身份验证方法

|   | 安全性默认设置 | 所有其他方法 |
| --- | --- | --- |
| 通过移动应用发送通知 | X | X |
| 移动应用或硬件标志提供的验证码 |   | X |
| 向手机发送短信 |   | X |
| 拨打电话 |   | X |

## <a name="next-steps"></a>后续步骤

若要开始，请参阅教程，了解如何[通过 Azure 多重身份验证保护用户登录事件](../authentication/tutorial-enable-azure-mfa.md)。

有关授权的详细信息，请参阅[Azure 多重身份验证的功能和许可证](../authentication/concept-mfa-licensing.md)。
