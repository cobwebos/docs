---
title: 常用条件访问策略 - Azure Active Directory
description: 组织常用的条件访问策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbf4d7b9f622ff590eee401544bf62655fd8d096
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024377"
---
# <a name="common-conditional-access-policies"></a>常用条件访问策略

[安全默认值](../fundamentals/concept-fundamentals-security-defaults.md)非常适用于某些组织，但对于许多组织而言，需要的灵活性比提供的要高。 例如，许多组织都需要能够从需要多重身份验证的条件访问策略中排除特定帐户，如其紧急访问帐户或不受限管理帐户。 对于这些组织，可以使用本文中提到的常用策略。

![Azure 门户中的条件访问策略](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>紧急访问帐户

有关紧急访问帐户及其重要原因的详细信息，请参阅以下文章： 

* [在 Azure AD 中管理紧急访问帐户](../users-groups-roles/directory-emergency-access.md)
* [使用 Azure Active Directory 创建可复原的访问控制管理策略](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>组织部署的典型策略

* [阻止旧式身份验证](howto-conditional-access-policy-block-legacy.md)\*
* [要求对管理员执行 MFA](howto-conditional-access-policy-admin-mfa.md)\*
* [要求将 MFA 用于 Azure 管理](howto-conditional-access-policy-azure-management.md)\*
* [要求对所有用户进行 MFA](howto-conditional-access-policy-all-users-mfa.md)\*

\*这四个策略一起配置时，会模拟[安全默认设置](../fundamentals/concept-fundamentals-security-defaults.md)启用的功能。

## <a name="additional-policies"></a>其他策略

* [基于风险的登录条件访问（需要 Azure AD Premium P2）](howto-conditional-access-policy-risk.md)
* [基于用户风险的条件性访问（需要 Azure AD Premium P2）](howto-conditional-access-policy-risk-user.md)
* [要求在进行 MFA 注册时处于受信任的位置](howto-conditional-access-policy-registration.md)
* [按位置阻止访问](howto-conditional-access-policy-location.md)
* [需要兼容设备](howto-conditional-access-policy-compliant-device.md)
* [阻止访问，但特定应用除外](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>后续步骤

- [使用条件访问 What If 工具模拟登录行为。](troubleshoot-conditional-access-what-if.md)

- [在仅限报告模式下进行条件访问来确定新策略决定的影响。](concept-conditional-access-report-only.md)
