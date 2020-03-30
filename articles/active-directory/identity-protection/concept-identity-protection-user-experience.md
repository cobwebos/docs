---
title: 使用 Azure AD 标识保护的用户体验
description: Azure AD 标识保护的用户体验
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
ms.openlocfilehash: cc10fb4f9894a355c9eed024ae9f87747214999b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72886995"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>使用 Azure AD 标识保护的用户体验

使用 Azure Active Directory 标识保护时，可以：

* 要求用户注册 Azure 多重身份验证 （MFA）
* 自动补救有风险登录和受攻击的用户

所有身份保护策略都对用户的登录体验有影响。 允许用户注册和使用 Azure MFA 和自助服务密码重置等工具可以减轻影响。 这些工具以及适当的策略选择为用户提供了在需要时的自我修复选项。

## <a name="multi-factor-authentication-registration"></a>多重身份验证注册

启用需要多重身份验证注册并面向所有用户的标识保护策略将确保他们将来能够使用 Azure MFA 进行自我修复。 配置此策略可为用户提供 14 天的时间，用户可以选择注册，并在结束时强制注册。 下面概述了用户的体验。 更多信息可在文章"[双因素验证概述"和"工作或学校帐户"](../user-help/user-help-two-step-verification-overview.md)中的最终用户文档中找到。

### <a name="registration-interrupt"></a>注册中断

1. 登录任何 Azure AD 集成应用程序时，用户会收到有关设置帐户以进行多重身份验证的通知。 对于具有新设备的新用户的 Windows 10 开箱即用体验，也会触发此策略。
   
    ![需要更多信息](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. 完成注册 Azure 多重身份验证的引导步骤并完成登录。

## <a name="risky-sign-in-remediation"></a>风险登录补救

当管理员为登录风险配置策略时，受影响的用户在尝试登录并触发策略风险级别时将收到通知。 

### <a name="risky-sign-in-self-remediation"></a>风险登录自我修复

1. 通知用户在其登录时检测到异常情况，例如从新的位置、设备或应用登录。
   
    ![一些不寻常的提示](./media/concept-identity-protection-user-experience/120.png)

1. 用户需要使用以前注册的方法之一完成 Azure MFA 来证明其身份。 

### <a name="risky-sign-in-administrator-unblock"></a>风险登录管理员取消阻止

管理员可以选择在登录时阻止用户，具体取决于其风险级别。 要解除阻止，最终用户必须联系其 IT 人员，或者他们可以尝试从熟悉的位置或设备登录。 在这种情况下，通过执行多重身份验证进行自我修复不是一个选项。

![受登录风险策略阻止](./media/concept-identity-protection-user-experience/200.png)

IT 人员可以按照["取消阻止用户"](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk)部分中的说明操作，以允许用户重新登录。

## <a name="risky-user-remediation"></a>风险用户补救

配置用户风险策略后，满足用户风险级别危害概率的用户必须经过用户危害恢复流，然后才能登录。 

### <a name="risky-user-self-remediation"></a>风险用户自我修复

1. 通知用户，由于出现可疑活动或凭据泄露，其帐户的安全性面临风险。
   
    ![补救](./media/concept-identity-protection-user-experience/101.png)

1. 用户需要使用以前注册的方法之一完成 Azure MFA 来证明其身份。 
1. 最后，用户被迫使用自助服务密码重置更改其密码，因为其他人可能有权访问其帐户。

## <a name="risky-sign-in-administrator-unblock"></a>风险登录管理员取消阻止

管理员可以选择在登录时阻止用户，具体取决于其风险级别。 要解除阻止，最终用户必须联系其 IT 人员。 在这种情况下，通过执行多重身份验证和自助服务密码重置进行自我修复不是一个选项。

![被用户风险策略阻止](./media/concept-identity-protection-user-experience/104.png)

IT 人员可以按照["取消阻止用户"](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk)部分中的说明操作，以允许用户重新登录。

## <a name="see-also"></a>请参阅

- [修复风险并取消阻止用户](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory 标识保护](../active-directory-identityprotection.md) 
