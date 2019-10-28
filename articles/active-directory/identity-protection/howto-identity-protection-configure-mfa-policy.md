---
title: 如何在 Azure Active Directory Identity Protection 中配置多重身份验证注册策略
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
ms.openlocfilehash: bc98f645c6b24069e090560a049ccb4fcd03dfec
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887567"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>如何：配置 Azure 多重身份验证注册策略

Azure AD Identity Protection 通过配置条件访问策略来管理 Azure 多重身份验证（MFA）注册的推出，无论你登录到哪个新式身份验证应用程序，都需要进行 MFA 注册。

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Azure 多重身份验证注册策略是什么？

Azure 多重身份验证提供了一种方法，用于验证你使用的不只是用户名和密码。 它为用户登录提供了另一层安全措施。为了使用户能够响应 MFA 提示，他们必须首先注册 Azure 多重身份验证。

建议你对用户登录要求进行 Azure 多重身份验证，因为它：

- 通过一系列验证选项提供强身份验证。
- 在准备您的组织从 Identity Protection 中的风险检测中进行自我修复时，扮演着重要的角色。

有关 Azure 多重身份验证的详细信息，请参阅[什么是 Azure 多重身份验证？](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>策略配置

1. 导航到 [Azure 门户](https://portal.azure.com)。
1. 浏览到**Azure Active Directory** > **Security** > **Identity Protection** > **MFA 注册策略**。
   1. 在**分配**下
      1. **用户**-选择 "**所有用户**" **，或选择 "个人和组**" （如果限制你的部署）。
         1. 还可以选择从策略中排除用户。
   1. **控件**下面
      1. 确保选中 "**需要 AZURE MFA 注册**" 复选框，然后选择 "**选择**"。
   1. **强制执行策略** ** - **
   1. **保存**

## <a name="user-experience"></a>用户体验

Azure Active Directory Identity Protection 将提示用户在下次以交互方式登录时注册，并将在14天内完成注册。 在此14天内，他们可以绕过注册，但在结束时间结束时，他们将需要注册，然后才能完成登录过程。

如需相关用户体验的概述，请参阅：

- [Azure AD 标识保护中的登录体验](concept-identity-protection-user-experience.md)。  

## <a name="next-steps"></a>后续步骤

- [启用登录和用户风险策略](howto-identity-protection-configure-risk-policies.md)

- [启用 Azure AD 自助服务密码重置](../authentication/howto-sspr-deployment.md)

- [启用 Azure 多重身份验证](../authentication/howto-mfa-getstarted.md)
