---
title: 使用范围筛选器预配应用 | Microsoft Docs
description: 了解如何使用范围筛选器阻止应用中支持自动用户预配的对象进行预配（如果对象不满足业务要求）。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: barbkess
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9f28c97fed2a5fa4990c1310e8389868c6b7dc20
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368545"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>使用范围筛选器进行基于属性的应用程序预配
本文的目的是说明如何使用范围筛选器定义基于属性的规则，用于确定哪些用户将预配到应用程序。

## <a name="scoping-filter-use-cases"></a>范围筛选器用例

范围筛选器允许 Azure Active Directory (Azure AD) 预配服务包含或排除具有与特定值匹配的属性的任何用户。 例如，将用户从 Azure AD 预配到销售团队使用的 SaaS 应用程序时，可指定预配范围内仅含“部门”属性为“销售”的用户。

范围筛选器的用法因预配连接器的类型而异：

* **从 Azure AD 到 SaaS 应用程序的出站预配**。 当 Azure AD 是源系统时，[用户和组分配](manage-apps/assign-user-or-group-access-portal.md)是确定预配范围内用户的最常用方法。 这些分配也用于启用单一登录，它们提供单一方法来管理访问权限和预配。 除分配外，还可选择性地使用范围筛选器，根据属性值筛选用户。

    >[!TIP]
    > 可将预配设置下的[范围](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application)菜单中的设置更改为“同步所有用户和组”，根据企业应用程序的分配禁用预配。 使用此选项及基于属性的范围筛选器比使用基于组的分配速度更快。  

* **从 HCM 应用程序到 Azure AD 和 Active Directory 的入站预配**。 当 [Workday 等 HCM 应用程序](saas-apps/workday-tutorial.md)是源系统时，范围筛选器是确定应从 HCM 应用程序预配到 Active Directory 或 Azure AD 的用户的主要方法。

默认情况下，Azure AD 预配连接器没有配置任何基于属性的范围筛选器。 

## <a name="scoping-filter-construction"></a>范围筛选器构造

范围筛选器包含一个或多个子句。 子句通过评估每个用户的属性来确定允许哪些用户通过范围筛选器。 例如，可能有一个子句要求用户的“state”属性等于“New York”，因此只有纽约用户将预配到应用程序。 

单个子句定义单个属性值的单个条件。 如果在单个范围筛选器中创建多个子句，则将使用“AND”逻辑评估它们。 这意味着所有子句必须评估为“true”方可预配用户。

最后，可为单个应用程序创建多个范围筛选器。 如果存在多个范围筛选器，则使用“OR”逻辑评估它们。 这意味着，如果配置的任意范围筛选器中所有子句评估为“true”，就会预配用户。

将始终针对每个范围筛选器单独评估由 Azure AD 预配服务处理的每个用户或组。

例如，考虑以下范围筛选器：

![范围筛选器](./media/active-directory-saas-scoping-filters/scoping-filter.PNG) 

按照此范围筛选器，用户必须满足以下条件，才能进行预配：

* 他们必须位于纽约。
* 他们必须在工程部工作。
* 其公司雇员 ID 必须在 1,000,000 和 2,000,000 之间。
* 其职务不能为 null 或为空。

## <a name="create-scoping-filters"></a>创建范围筛选器
在每个 Azure AD 用户预配连接器的属性映射过程中，配置范围筛选器。 以下过程假设已为[受支持的应用程序之一](saas-apps/tutorial-list.md)设置了自动预配，且要向其添加范围筛选器。

### <a name="create-a-scoping-filter"></a>创建范围筛选器
1. 在 [Azure 门户](https://portal.azure.com)中，转到“Azure Active Directory” > “企业应用程序” > “所有应用程序”部分。

2. 选择已为其配置自动预配的应用程序，例如“ServiceNow”。

3. 选择“预配”选项卡。

4. 在“映射”部分，选择要为其预配范围筛选器的映射，例如“将 Azure Active Directory 用户同步到 ServiceNow”。

5. 选择“源对象范围”菜单。

6. 选择“添加范围筛选器”。

7. 选择要匹配的源“属性名称”、“运算符”和“属性值”来定义子句。 支持以下运算符：

   a. **EQUALS**。 如果评估的属性与输入字符串值完全匹配（区分大小写），则子句返回“true”。

   b. **NOT EQUALS**。 如果评估的属性与输入字符串值不匹配（区分大小写），则子句返回“true”。

   c. **IS TRUE**。 如果评估的属性包含为 true 的布尔值，则子句返回“true”。

   d. **IS FALSE**。 如果评估的属性包含为 false 的布尔值，则子句返回“true”。

   e. **IS NULL**。 如果评估的属性为空，则子句返回“true”。

   f. **IS NOT NULL**。 如果评估的属性不为空，则子句返回“true”。

   g. **REGEX MATCH**。 如果评估的属性与正则表达式模式匹配，则子句返回“true”。 示例：([1-9][0-9]) 与介于 10 和 99 之间的任意数字匹配。

   h. **NOT REGEX MATCH**。 如果评估的属性与正则表达式模式不匹配，则子句返回“true”。

8. 选择“添加新的范围子句”。

9. 可重复步骤 7-8 以添加其他范围子句。

10. 在“范围筛选器标题”中，为范围筛选器添加名称。

11. 选择“确定”。

12. 在“范围筛选器”屏幕上再次选择“确定”。 （可选）重复步骤 6-11 添加另一范围筛选器。

13. 在“属性映射”屏幕上选择“保存”。 

>[!IMPORTANT] 
> 保存新的范围筛选器将触发新的应用程序完全同步，其中将针对新的范围筛选器再次对源系统中的所有用户进行评估。 如果应用程序中的用户以前在预配范围内，但现在不在范围内，则会在应用程序中禁用或取消预配其帐户。


## <a name="related-articles"></a>相关文章
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [在 SaaS 应用程序中自动预配和取消预配用户](active-directory-saas-app-provisioning.md)
* [为用户预配自定义属性映射](active-directory-saas-customizing-attribute-mappings.md)
* [为属性映射编写表达式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [帐户预配通知](active-directory-saas-account-provisioning-notifications.md)
* [使用 SCIM 启用从 Azure Active Directory 到应用程序的用户和组自动预配](manage-apps/use-scim-to-provision-users-and-groups.md)
* [有关如何集成 SaaS 应用的教程列表](saas-apps/tutorial-list.md)

