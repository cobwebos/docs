---
title: 如何在 Azure Active Directory 标识保护中配置多重身份验证注册策略| Microsoft Docs
description: 了解如何配置“Azure AD 标识保护”多重身份验证注册策略。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27ff7512bb3f9422ed4c8edd7ab50fce23f0ed07
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499567"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>如何：配置 Azure 多重身份验证注册策略

Azure AD Identity Protection 通过将条件访问策略配置为要求进行 MFA 注册, 而不考虑登录到的现代身份验证应用, 可帮助你管理多因素身份验证 (MFA) 注册的推出。 本文介绍了可以使用的策略, 以及如何对其进行配置。



## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Azure 多重身份验证注册策略是什么？

Azure 多重身份验证提供了一种方法, 用于验证你使用的不只是用户名和密码。 它为用户登录提供了另一层安全措施。为了使用户能够响应 MFA 提示, 他们必须首先注册 Azure 多重身份验证。

建议你对用户登录要求进行 Azure 多重身份验证, 因为它:

- 提供强式身份验证和一系列简单的验证选项
- 在准备你的组织以保护和从 Identity Protection 中的风险事件中恢复时, 扮演着重要的角色

有关 MFA 的详细信息, 请参阅[什么是 Azure 多重身份验证？](../authentication/howto-mfa-getstarted.md)

## <a name="how-do-i-access-the-registration-policy"></a>如何实现访问注册策略？

MFA 注册策略位于[“Azure AD 标识保护”页](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy)上的“配置”部分中。

![MFA 策略](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>策略设置

配置 MFA 注册策略时, 需要进行以下配置更改:

- 应用策略的用户和组。 请记住排除组织的[紧急访问帐户](../users-groups-roles/directory-emergency-access.md)。

    ![用户和组](./media/howto-mfa-policy/11.png)

- 要强制实施的控件-**需要 AZURE MFA 注册**

    ![访问权限](./media/howto-mfa-policy/12.png)

- 应将 "强制策略" 设置为 **"开"** 。

    ![强制实施策略](./media/howto-mfa-policy/14.png)

- **保存**策略

## <a name="user-experience"></a>用户体验

Azure Active Directory Identity Protection 将提示用户在下次以交互方式登录时注册。

如需相关用户体验的概述，请参阅：

- [多重身份验证注册流程](flows.md#multi-factor-authentication-registration)。  
- [Azure AD 标识保护中的登录体验](flows.md)。  

## <a name="next-steps"></a>后续步骤

若要获取“Azure AD 标识保护”的概述，请参阅 [Azure AD 标识保护概述](overview.md)。
