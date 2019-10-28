---
title: Azure AD Identity Protection 的用户体验
description: Azure AD Identity Protection 的用户体验
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
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72886995"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Azure AD Identity Protection 的用户体验

使用 Azure Active Directory 标识保护时，可以：

* 要求用户注册 Azure 多重身份验证（MFA）
* 自动修正有风险的登录和泄露的用户

所有 Identity Protection 策略都会影响用户的登录体验。 允许用户注册和使用 Azure MFA 和自助密码重置等工具可以减少影响。 这些工具连同适当的策略选项一起提供，用户在需要时可提供自我修正选项。

## <a name="multi-factor-authentication-registration"></a>多重身份验证注册

若要启用需要多重身份验证注册并面向所有用户的身份保护策略，将确保他们能够在将来使用 Azure MFA 进行自我修正。 配置此策略后，用户可以选择在14天的期限内进行注册，并强制进行注册。 用户体验如下所述。 有关详细信息，请参阅文章中的最终用户文档、[双因素验证概述以及工作或学校帐户](../user-help/user-help-two-step-verification-overview.md)。

### <a name="registration-interrupt"></a>注册中断

1. 登录到任何 Azure AD 集成的应用程序时，用户将收到有关设置用于多重身份验证的帐户的要求的通知。 使用新设备的新用户的 Windows 10 全新体验中也会触发此策略。
   
    ![需要更多信息](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. 完成注册 Azure 多重身份验证的指导步骤并完成登录。

## <a name="risky-sign-in-remediation"></a>有风险的登录修正

当管理员为登录风险配置策略时，受影响的用户在尝试登录时将收到通知并触发策略风险级别。 

### <a name="risky-sign-in-self-remediation"></a>有风险的登录自我修正

1. 通知用户在其登录时检测到异常情况，例如从新的位置、设备或应用登录。
   
    ![出现异常提示](./media/concept-identity-protection-user-experience/120.png)

1. 用户需要通过使用以前注册的一种方法完成 Azure MFA 来证明其身份。 

### <a name="risky-sign-in-administrator-unblock"></a>有风险的登录管理员取消阻止

管理员可以根据用户的风险级别选择阻止用户登录。 若要解除阻止，最终用户必须联系其 IT 人员，或者他们可以尝试从熟悉的位置或设备登录。 在这种情况下，不能选择是否执行多重身份验证。

![已被登录风险策略阻止](./media/concept-identity-protection-user-experience/200.png)

IT 人员可以按照取消[阻止用户](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk)的部分中的说明操作，以允许用户重新登录。

## <a name="risky-user-remediation"></a>有风险的用户修正

配置了用户风险策略后，在用户登录之前，满足用户风险级别的威胁的用户必须经历用户入侵恢复流。 

### <a name="risky-user-self-remediation"></a>有风险的用户自我修正

1. 通知用户，由于出现可疑活动或凭据泄露，其帐户的安全性面临风险。
   
    ![补救](./media/concept-identity-protection-user-experience/101.png)

1. 用户需要通过使用以前注册的一种方法完成 Azure MFA 来证明其身份。 
1. 最后，用户被迫使用自助密码重置更改其密码，因为其他人可能有权访问自己的帐户。

## <a name="risky-sign-in-administrator-unblock"></a>有风险的登录管理员取消阻止

管理员可以根据用户的风险级别选择阻止用户登录。 若要解除阻止，最终用户必须与 IT 人员联系。 在这种情况下，不能选择是否执行多重身份验证和自助密码重置。

![被用户风险策略阻止](./media/concept-identity-protection-user-experience/104.png)

IT 人员可以按照取消[阻止用户](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk)的部分中的说明操作，以允许用户重新登录。

## <a name="see-also"></a>另请参阅

- [修正风险和解除阻止用户](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory 标识保护](../active-directory-identityprotection.md) 
