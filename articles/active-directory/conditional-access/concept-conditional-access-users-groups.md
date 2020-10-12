---
title: 条件访问策略中的用户和组 - Azure Active Directory
description: Azure AD 条件访问策略中的用户和组包括谁
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba1fc856ee9093b628bd86b9847f8fc70b7189c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87552894"
---
# <a name="conditional-access-users-and-groups"></a>条件访问：用户和组

条件访问策略必须包括用户分配，作为决策过程中的信号之一。 可以在条件访问策略中包括或排除用户。 

![用户作为条件访问所做决策中的信号](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>包括用户

此用户列表通常包括组织在条件访问策略中设为目标的所有用户。 

创建条件访问策略时，可以包括以下选项。

- 无
   - 不选择任何用户
- 所有用户
   - 目录中存在的所有用户，包括 B2B 来宾。
- 选择“用户和组”
   - 所有来宾用户和外部用户
      - 此选择包括所有 B2B 来宾和外部用户，包括将 `user type` 特性设置为 `guest` 的任何用户。 此选择也适用于从其他组织（例如云解决方案提供商 (CSP)）登录的任何外部用户。 
   - 目录角色
      - 允许管理员选择用于确定分配的特定 Azure AD 目录角色。 例如，组织可以对分配有全局管理员角色的用户创建更严格的策略。
   - 用户和组
      - 允许以特定用户集为目标。 例如，将某个人力资源应用选作云应用时，组织可以选择包含人力资源部所有成员的组。 某个组可以是 Azure AD 中任何类型的组，包括动态组，或分配的安全组和通讯组。 策略将应用于嵌套的用户和组。

> [!WARNING]
> 如果用户或组是 2048 个以上的组的成员，则其访问可能会被阻止。 此限制适用于直接组和嵌套组成员身份。

> [!WARNING]
> 条件访问策略不支持为用户分配[范围为管理单元](../users-groups-roles/roles-admin-units-assign-roles.md)的目录角色或范围直接为对象的目录角色（例如，通过[自定义角色](../users-groups-roles/roles-create-custom.md)进行角色分配时就是如此）。

## <a name="exclude-users"></a>排除用户

如果组织同时包括并排除某个用户或组，则会从策略中排除该用户或组，因为排除操作会替代策略中的包括操作。 排除通常用于紧急访问或不受限帐户。 有关紧急访问帐户及其重要原因的详细信息，请参阅以下文章： 

* [在 Azure AD 中管理紧急访问帐户](../users-groups-roles/directory-emergency-access.md)
* [使用 Azure Active Directory 创建可复原的访问控制管理策略](../authentication/concept-resilient-controls.md)

创建条件访问策略时，可以排除以下选项。

- 所有来宾用户和外部用户
   - 此选择包括所有 B2B 来宾和外部用户，包括将 `user type` 特性设置为 `guest` 的任何用户。 此选择也适用于从其他组织（例如云解决方案提供商 (CSP)）登录的任何外部用户。 
- 目录角色
   - 允许管理员选择用于确定分配的特定 Azure AD 目录角色。 例如，组织可以对分配有全局管理员角色的用户创建更严格的策略。
- 用户和组
   - 允许以特定用户集为目标。 例如，将某个人力资源应用选作云应用时，组织可以选择包含人力资源部所有成员的组。 某个组可以是 Azure AD 中任何类型的组，包括动态组，或分配的安全组和通讯组。

### <a name="preventing-administrator-lockout"></a>防止管理员锁定

若要防止管理员在创建应用于所有用户  和所有应用  的策略时将自身锁定在其目录外面，可向他们显示以下警告。

> 别把自己锁在外面！ 我们建议先将策略应用于一小部分用户，以验证其行为是否符合预期。 我们还建议至少从此策略中排除一个管理员。 这可确保在需要更改时，你仍然可以访问策略并对其进行更新。 请查看受影响的用户和应用。

默认情况下，此策略会提供一个从策略中排除当前用户的选项，但管理员可以重写此默认设置，如下图所示。 

![警告：别把自己锁在外面！](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

## <a name="next-steps"></a>后续步骤

- [条件访问：云应用或操作](concept-conditional-access-cloud-apps.md)

- [条件访问常见策略](concept-conditional-access-policy-common.md)
