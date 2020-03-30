---
title: 条件访问策略中的用户和组 - Azure 活动目录
description: Azure AD 条件访问策略中的用户和组是谁
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192124"
---
# <a name="conditional-access-users-and-groups"></a>条件访问：用户和组

条件访问策略必须将用户分配作为决策过程中的信号之一。 用户可以包含在条件访问策略中或排除。 

![条件访问决策中用户作为信号](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>包括用户

此用户列表通常包括组织在条件访问策略中定位的所有用户。 

创建条件访问策略时，可以使用以下选项进行包括。

- 无
   - 未选择任何用户
- 所有用户
   - 目录中存在的所有用户，包括 B2B 来宾。
- 选择“用户和组”
   - 所有访客和外部用户（预览版）
      - 此选项包括任何 B2B 来宾和外部用户，包括`user type`属性设置为`guest`的任何用户。 此选项也适用于来自其他组织（如云解决方案提供商 （CSP） 的任何外部用户登录。 
   - 目录角色（预览）
      - 允许管理员选择用于确定分配的特定 Azure AD 目录角色。 例如，组织可能会对分配全局管理员角色的用户创建更严格的策略。
   - 用户和组
      - 允许定位特定用户集。 例如，当将 HR 应用选择为云应用时，组织可以选择包含人力资源部门所有成员的组。 某个组可以是 Azure AD 中任何类型的组，包括动态组，或分配的安全组和通讯组。

## <a name="exclude-users"></a>排除用户

排除通常用于紧急访问或碎玻璃帐户。 有关紧急访问帐户及其重要原因的详细信息，请参阅以下文章： 

* [在 Azure AD 中管理紧急访问帐户](../users-groups-roles/directory-emergency-access.md)
* [使用 Azure Active Directory 创建可复原的访问控制管理策略](../authentication/concept-resilient-controls.md)

创建条件访问策略时，可以使用以下选项进行排除。

- 所有访客和外部用户（预览版）
   - 此选项包括任何 B2B 来宾和外部用户，包括`user type`属性设置为`guest`的任何用户。 此选项也适用于来自其他组织（如云解决方案提供商 （CSP） 的任何外部用户登录。 
- 目录角色（预览）
   - 允许管理员选择用于确定分配的特定 Azure AD 目录角色。 例如，组织可能会对分配全局管理员角色的用户创建更严格的策略。
- 用户和组
   - 允许定位特定用户集。 例如，当将 HR 应用选择为云应用时，组织可以选择包含人力资源部门所有成员的组。 某个组可以是 Azure AD 中任何类型的组，包括动态组，或分配的安全组和通讯组。

## <a name="next-steps"></a>后续步骤

- [条件访问：云应用或操作](concept-conditional-access-cloud-apps.md)

- [条件访问常见策略](concept-conditional-access-policy-common.md)
