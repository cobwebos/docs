---
title: 常见的条件访问策略-Azure Active Directory
description: 组织常用的条件性访问策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf338ad0c555038cd99c3604d69ab80371d6ea22
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74804968"
---
# <a name="common-conditional-access-policies"></a>常用条件访问策略

基线保护策略非常好，但很多组织需要比其提供的灵活性更高的灵活性。 例如，许多组织都需要能够从需要多重身份验证的条件性访问策略中排除特定帐户，如其紧急访问或中断玻璃管理帐户。 对于这些组织，可以使用本文中提到的常见策略。

![Azure 门户中的条件性访问策略](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>紧急访问帐户

有关紧急访问帐户及其重要性的详细信息，请参阅以下文章： 

* [管理 Azure AD 中的紧急访问帐户](../users-groups-roles/directory-emergency-access.md)
* [使用 Azure Active Directory 创建弹性访问控制管理策略](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>组织部署的典型策略

* [需要对管理员的 MFA](howto-conditional-access-policy-admin-mfa.md)
* [需要 MFA 进行 Azure 管理](howto-conditional-access-policy-azure-management.md)
* [要求对所有用户进行 MFA](howto-conditional-access-policy-all-users-mfa.md)
* [阻止旧身份验证](howto-conditional-access-policy-block-legacy.md)
* [基于风险的条件性访问（需要 Azure AD Premium P2）](howto-conditional-access-policy-risk.md)
* [需要受信任的位置来注册 MFA](howto-conditional-access-policy-registration.md)
* [按位置阻止访问](howto-conditional-access-policy-location.md)
* [需要相容设备](howto-conditional-access-policy-compliant-device.md)

## <a name="next-steps"></a>后续步骤

[使用条件性访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
