---
title: "使用范围筛选器预配应用 | Microsoft 文档"
description: "了解如何使用范围筛选器阻止应用中支持自动用户预配的对象实际进行预配（如果对象不满足业务要求）。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: bcfbda74-e4d4-4859-83bc-06b104df3918
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: c06c089fb08c19b55246122201c378917a560e14
ms.openlocfilehash: 3e4458f70afce9ebd9477b00afc39b6e84e49319
ms.contentlocale: zh-cn
ms.lasthandoff: 03/01/2017

---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>使用范围筛选器进行基于属性的应用程序预配
本部分的目的是说明如何使用范围筛选器定义基于属性的规则，用于确定哪些用户将预配到应用程序。

## <a name="clauses-and-scope-groups"></a>子句和范围组
![范围筛选器][1] 

范围筛选器由一个或多个**范围组**定义，每个范围组拥有一个或多个**子句**。 若要查看特定范围组的子句，请通过单击组名称左侧的箭头展开它。

**子句**通过评估每个用户的属性来确定允许哪些用户通过范围筛选器。 例如，你可能有一个子句要求用户的“state”属性等于“New York”，因此只有纽约用户将预配到应用程序。

![范围组名称][2] 

每个**范围组**都以一个必需的**子句**开头，如上面的屏幕截图中所示。 此子句只是声明，必须先将用户分配到应用程序，然后范围筛选器才会对用户进行评估。 无法删除或修改此子句。

可以通过按相应按钮添加新子句或新的范围组。 可以编辑范围组的“范围组名称”属性为每个范围组提供名称。

## <a name="how-scoping-filters-are-evaluated"></a>如何评估范围筛选器
在预配期间，我们将针对范围筛选器测试每个已分配用户来确定该用户是否应该访问此应用程序。 可以将每个子句想成一个测试，用户必须通过该测试以避免被筛选出去。 

如果已定义多个范围组，每个用户必须至少通过其中一个范围组才能访问应用程序。 但是，在每个范围组中，用户必须通过每个子句才能通过该特定的范围组。 

换而言之，可以将范围组想成通过 OR 连接在一起，将其中的子句想成通过 AND 连接在一起。 例如，考虑以下范围筛选器：

![范围组名称][3]  

按照此范围筛选器，用户必须满足以下条件，才能进行预配：

1. 必须将用户分配到应用程序。
2. 用户必须在工程部工作
3. 用户必须在旧金山或加拿大工作。

## <a name="related-articles"></a>相关文章
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [在 SaaS 应用程序中自动预配和取消预配用户](active-directory-saas-app-provisioning.md)
* [为用户预配自定义属性映射](active-directory-saas-customizing-attribute-mappings.md)
* [为属性映射编写表达式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [帐户预配通知](active-directory-saas-account-provisioning-notifications.md)
* [使用 SCIM 启用从 Azure Active Directory 到应用程序的用户和组自动预配](active-directory-scim-provisioning.md)
* [有关如何集成 SaaS 应用的教程列表](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-scoping-filters/ic782811.png
[2]: ./media/active-directory-saas-scoping-filters/ic782812.png
[3]: ./media/active-directory-saas-scoping-filters/ic782813.png

