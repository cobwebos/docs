---
title: 配置 MFA 注册策略 - Azure 活动目录标识保护
description: 了解如何配置“Azure AD 标识保护”多重身份验证注册策略。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fa6a4cf184b426355f62117ea51642127eee529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382147"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>如何：配置 Azure 多重身份验证注册策略

Azure AD 标识保护通过配置条件访问策略来要求 MFA 注册（无论您登录到什么现代身份验证应用），可帮助您管理 Azure 多重身份验证 （MFA） 注册的推出。

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>什么是 Azure 多重身份验证注册策略？

Azure 多重身份验证提供了一种方法来实现不只使用用户名和密码验证用户的身份。 它为用户登录提供了第二层安全性。为了使用户能够响应 MFA 提示，他们必须首先注册 Azure 多重身份验证。

我们建议你要求用户登录使用 Azure 多重身份验证，因为这种身份验证方法可以：

- 通过一系列验证选项提供强身份验证。
- 在使组织能够自我补救身份保护中的风险检测方面发挥关键作用。

有关 Azure 多重身份验证的详细信息，请参阅什么是[Azure 多重身份验证？](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>策略配置

1. 导航到[Azure 门户](https://portal.azure.com)。
1. 浏览到**Azure 活动目录** > **安全** > **标识保护** > **MFA 注册策略**。
   1. 在**分配**下
      1. **用户**- 如果限制推出，请选择**所有用户**或**选择个人和组**。
         1. 可以选择将用户从策略中排除。
   1. 在**控制下**
      1. 确保选中"**需要 Azure MFA 注册"** 复选框，然后**选择"选择**"。
   1. **执行策略** - **On**
   1. **保存**

## <a name="user-experience"></a>用户体验

Azure 活动目录标识保护将提示用户下次以交互方式登录时进行注册，并且他们有 14 天的时间完成注册。 在这 14 天期间，他们可以绕过注册，但在期限结束时，他们将需要注册，然后才能完成登录过程。

如需相关用户体验的概述，请参阅：

- [使用 Azure AD 标识保护进行登录体验](concept-identity-protection-user-experience.md)。  

## <a name="next-steps"></a>后续步骤

- [启用登录和用户风险策略](howto-identity-protection-configure-risk-policies.md)

- [启用 Azure AD 自助服务密码重置](../authentication/howto-sspr-deployment.md)

- [启用 Azure 多重身份验证](../authentication/howto-mfa-getstarted.md)
