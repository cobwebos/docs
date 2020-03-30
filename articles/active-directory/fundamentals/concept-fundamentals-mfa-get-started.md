---
title: 组织的 Azure 多重身份验证 - Azure 活动目录
description: 根据许可证模型了解组织 Azure 多重身份验证的可用功能
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530387"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>组织 Azure 多重身份验证概述

有多种方法可以根据组织拥有的许可证为 Azure 活动目录 （AD） 用户启用 Azure 多重身份验证。 

![调查信号并在必要时强制实施 MFA](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

根据我们的研究，如果您使用多重身份验证 （MFA），您的帐户被入侵的可能性要降低 99.9%。

那么，在成为统计数据之前，您的组织如何免费打开 MFA？

## <a name="free-option"></a>免费选项

利用 Azure AD 免费优势的客户可以使用[安全默认值](../fundamentals/concept-fundamentals-security-defaults.md)在其环境中启用多重身份验证。

## <a name="office-365-business-premium-e3-or-e5"></a>Office 365 业务高级版、E3 或 E5

对于具有 Office 365 的客户，有两个选项：

* 针对所有用户的所有登录事件启用或禁用 Azure 多重身份验证。 不能只为一部分用户或者只是在特定的方案中启用多重身份验证。 通过 Office 365 门户进行管理。 
* 若要改进用户体验，请升级到 Azure AD Premium P1 或 P2 并使用条件访问。 有关详细信息，请参阅使用多重身份验证保护 Office 365 资源。

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

适用于具有 Azure AD 高级 P1 或类似许可证的客户，包括此功能，如企业移动性 + 安全 E3、Microsoft 365 F1 或 Microsoft 365 E3： 

使用[Azure AD 条件访问](../conditional-access/overview.md)提示用户在某些方案或事件期间进行多重身份验证，以满足您的业务需求。

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

适用于具有 Azure AD 高级 P2 或类似许可证的客户，这些许可证包含此功能，如企业移动性 + 安全 E5 或 Microsoft 365 E5： 

提供最强的安全形势和改进的用户体验。 添加[基于风险的条件访问](../conditional-access/howto-conditional-access-policy-risk.md)Azure AD 高级 P1 功能，这些功能可适应用户的模式并最大限度地减少多重身份验证提示。

## <a name="authentication-methods"></a>身份验证方法

|   | 安全默认值 | 所有其他方法 |
| --- | --- | --- |
| 通过移动应用发送通知 | X | X |
| 移动应用或硬件标志提供的验证码 |   | X |
| 向手机发送短信 |   | X |
| 拨打电话 |   | X |

## <a name="next-steps"></a>后续步骤

要开始，请参阅使用 Azure[多重身份验证保护用户登录事件的](../authentication/tutorial-enable-azure-mfa.md)教程。

有关许可的详细信息，请参阅[Azure 多重身份验证的功能和许可证](../authentication/concept-mfa-licensing.md)。
