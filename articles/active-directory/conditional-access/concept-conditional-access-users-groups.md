---
title: 条件访问策略中的用户和组-Azure Active Directory
description: 谁是 Azure AD 条件访问策略中的用户和组
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36898e75680771a9cb084fa142bb635ddbf51c70
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192124"
---
# <a name="conditional-access-users-and-groups"></a>条件性访问：用户和组

条件性访问策略必须包括用户分配作为决策过程中的一个信号。 可以在条件访问策略中包括或排除用户。 

![用户作为条件访问做出的决策中的信号](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>包括用户

此用户列表通常包含组织作为条件性访问策略的目标的所有用户。 

创建条件性访问策略时，可以使用以下选项。

- 无
   - 未选择任何用户
- 所有用户
   - 包含 B2B 来宾的目录中存在的所有用户。
- 选择“用户和组”
   - 所有来宾和外部用户（预览）
      - 此选择包括任何 B2B 来宾和外部用户，包括 `user type` 属性设置为 `guest`的任何用户。 此选项也适用于从不同组织（如云解决方案提供商（CSP））登录的任何外部用户。 
   - 目录角色（预览）
      - 允许管理员选择用于确定分配的特定 Azure AD 目录角色。 例如，组织可能会在分配了全局管理员角色的用户上创建限制性更强的策略。
   - 用户和组
      - 允许特定用户集的目标。 例如，当将 HR 应用选作云应用时，组织可以选择包含 HR 部门所有成员的组。 某个组可以是 Azure AD 中任何类型的组，包括动态组，或分配的安全组和通讯组。

## <a name="exclude-users"></a>排除用户

排除项通常用于紧急访问或破解玻璃帐户。 有关紧急访问帐户及其重要性的详细信息，请参阅以下文章： 

* [管理 Azure AD 中的紧急访问帐户](../users-groups-roles/directory-emergency-access.md)
* [使用 Azure Active Directory 创建弹性访问控制管理策略](../authentication/concept-resilient-controls.md)

创建条件性访问策略时，可以使用以下选项。

- 所有来宾和外部用户（预览）
   - 此选择包括任何 B2B 来宾和外部用户，包括 `user type` 属性设置为 `guest`的任何用户。 此选项也适用于从不同组织（如云解决方案提供商（CSP））登录的任何外部用户。 
- 目录角色（预览）
   - 允许管理员选择用于确定分配的特定 Azure AD 目录角色。 例如，组织可能会在分配了全局管理员角色的用户上创建限制性更强的策略。
- 用户和组
   - 允许特定用户集的目标。 例如，当将 HR 应用选作云应用时，组织可以选择包含 HR 部门所有成员的组。 某个组可以是 Azure AD 中任何类型的组，包括动态组，或分配的安全组和通讯组。

## <a name="next-steps"></a>后续步骤

- [条件性访问：云应用或操作](concept-conditional-access-cloud-apps.md)

- [条件访问公用策略](concept-conditional-access-policy-common.md)
