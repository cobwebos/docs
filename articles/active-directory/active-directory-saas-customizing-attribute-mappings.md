---
title: 自定义 Azure AD 属性映射 | Microsoft Docs
description: 了解 Azure Active Directory 中有哪些针对 SaaS 应用的属性映射，以及如何修改它们来满足业务需求。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7dad9f3e688c43de3eabd430bf5618ad4632ca3d
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034360"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>为 Azure Active Directory 中的 SaaS 应用程序自定义用户预配属性映射
Microsoft Azure AD 支持将用户预配到第三方 SaaS 应用程序，例如 Salesforce、Google Apps 等等。 如果已为第三方 SaaS 应用程序启用用户预配，Azure 门户将以属性映射的形式控制其属性值。

Azure AD 用户对象与每个 SaaS 应用的用户对象之间存在预先配置的一组属性和属性映射。 除了“用户”以外，某些应用还可以管理其他类型的对象，例如“组”。 <br> 
 可以根据业务需求自定义默认的属性映射。 这意味着，可以更改或删除现有属性映射或者创建新的属性映射。
 
## <a name="editing-user-attribute-mappings"></a>编辑用户属性映射

在 Azure AD 门户中，可在“企业应用程序”的“管理”部分，通过单击“预配”下的“映射”配置来访问此功能。


![Salesforce][5] 

单击“映射”配置，打开相关的“属性映射”屏幕。 SaaS 应用程序需要执行一些属性映射才能正确运行。 对于必需属性，“删除”功能将不可用。


![Salesforce][6]  

在上面的示例中，可以发现 Salesforce 中托管对象的“用户名”属性由所链接的 Azure Active Directory 对象的“userPrincipalName”值填充。

可通过单击映射自定义现有“属性映射”。 此时会打开“编辑属性”屏幕。

![Salesforce][7]  


### <a name="understanding-attribute-mapping-types"></a>了解属性映射类型
使用属性映射可以控制属性在第三方 SaaS 应用程序中的填充方式。 支持四种不同的映射类型：

* **直接** – 目标属性由 Azure AD 中的链接对象的属性值填充。
* **常量** - 目标属性使用已指定的特定字符串填充。
* **表达式** - 目标属性是基于脚本式表达式的结果填充的。 
  有关详细信息，请参阅[在 Azure Active Directory 中编写属性映射的表达式](active-directory-saas-writing-expressions-for-attribute-mappings.md)。
* **无** - 目标属性保持不变。 不过，如果目标属性曾经为空，则它由你指定的默认值填充。

除了这四个基本类型之外，自定义属性映射还支持可选的默认值赋值概念。 默认值赋值确保当 Azure AD 中和目标对象中都没有值时使用某个值填充目标属性。 最常见的配置是将此项留空。


### <a name="understanding-attribute-mapping-properties"></a>了解属性映射的属性

前面的部分介绍了属性映射类型属性。
除此属性外，属性映射还支持以下属性：

- **源属性** - 来自源系统的用户属性（例如 Azure Active Directory）。
- **目标属性** – 目标系统中的用户属性（例如 ServiceNow）。
- 使用此属性匹配对象 – 是否应使用此映射唯一标识源系统和目标系统之间的用户。 这通常在 Azure AD 中的 userPrincipalName 或邮件属性上设置，其通常映射到目标应用程序中的用户名字段。
- 匹配优先级 – 可设置多个匹配属性。 当存在匹配时，会按照此字段定义的顺序进行评估。 一旦找到匹配，就不会进一步评估其他匹配属性。
- **应用此映射**
    - 始终 – 对用户创建和更新操作均应用此映射
    - 仅创建期间 - 仅对用户创建操作应用此映射


## <a name="editing-group-attribute-mappings"></a>编辑组属性映射

一些特定的应用程序（例如 ServiceNow、Box 和 Google Apps）除了支持预配“用户”对象以外，还支持预配“组”对象。 “组”对象可以包含组属性（例如显示名称和电子邮件别名）以及组成员。

![ServiceNow][8]  

可以通过在“映射”下面选择组映射，并在“属性映射”屏幕中将“已启用”设置为所需的选项，来有选择性地启用或禁用组预配。

可以自定义预配为“组”对象的一部分的属性，就像前面所述的预配“用户”对象一样。 

>[!TIP]
>预配组对象（属性和成员）的过程完全不同于[将组分配](manage-apps/assign-user-or-group-access-portal.md)到应用程序。 可以将组分配到应用程序，但只能预配组中包含的用户对象。 预配整个组对象不需要使用分配中的组。


## <a name="editing-the-list-of-supported-attributes"></a>编辑受支持属性的列表

给定应用程序支持的用户属性是预先配置的。 大多数应用程序的用户管理 API 不支持架构发现，因此，Azure AD 预配服务无法通过调用应用程序来动态生成受支持属性的列表。 

但是，某些应用程序支持自定义属性。 要使 Azure AD 预配服务能够读取和写入自定义属性，必须使用“属性映射”屏幕底部的“显示高级选项”复选框，将这些属性的定义输入 Azure 门户。

支持属性列表自定义的应用程序和系统包括：

* Salesforce
* ServiceNow
* Workday
* Azure Active Directory（支持 [Azure AD 图形 API 默认属性](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity)和自定义目录扩展）
* 支持 [SCIM 2.0](https://tools.ietf.org/html/rfc7643) 的应用（需要添加[核心架构](https://tools.ietf.org/html/rfc7643)中定义的属性）

>[!NOTE]
>建议只让自定义了其应用程序和系统的架构，并且原本就知道自定义属性定义方式的管理员来编辑受支持属性的列表。 有时，需要熟悉应用程序或系统提供的 API 和开发人员工具才能执行此操作。 

![编辑器][9]  

编辑受支持属性的列表时，将提供以下属性：

* **名称** - 属性的系统名称，在目标对象的架构中定义。 
* **类型** - 属性存储的数据类型，在目标对象的架构中定义。 可以是以下值之一：
   * *二进制* - 属性包含二进制数据。
   * *布尔值* - 属性包含 True 或 False 值。
   * *日期时间* - 属性包含日期字符串。
   * *整数* - 属性包含整数。
   * *引用* - 属性包含 ID，该 ID 引用目标应用程序中的另一个表存储的值。
   * *字符串* - 属性包含文本字符串。 
* **主键?** - 属性是否已定义为目标对象架构中的主键字段。
* **必需？** - 是否需要在目标应用程序或系统中填充该属性。
* **多值?** - 属性是否支持多个值。
* **区分大小写?** - 是否以区分大小写的方式计算属性值。
* **API 表达式** - 除非特定预配连接器（例如 Workday）的文档要求使用，否则请不要使用。
* **引用的对象属性** - 如果这是一个引用类型的属性，则此菜单允许在目标应用程序中选择包含与该属性关联的值的表和属性。 例如，如果名为“Department”的属性的存储值引用了独立“Departments”表中的对象，则需要选择“Departments.Name”。 请注意，给定应用程序支持的引用表和主要 ID 字段是预先配置的，目前无法使用 Azure 门户进行编辑，但可以使用[图形 API](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes) 进行编辑。

若要添加新属性，请滚动到受支持属性列表的末尾，使用提供的输入填充上述字段，然后选择“添加属性”。 添加完属性后选择“保存”。 然后需要重新加载“预配”选项卡，使新属性显示在属性映射编辑器中。

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>还原默认属性和属性映射

如果需要从头开始并将现有映射重置为默认状态，可以选中“还原默认映射”复选框并保存配置。 这样就会设置所有映射，就如同刚刚将应用程序从应用程序库添加到 Azure AD 租户一样。 

在运行预配服务时，选择此选项可以有效地强制重新同步所有用户。 

>[!IMPORTANT]
>调用此选项之前，我们强烈建议将“预配状态”设置为“关闭”。


## <a name="what-you-should-know"></a>要点

* Microsoft Azure AD 提供了一个有效的同步进程实现。 在初始化环境中，在同步周期中只会处理需要更新的对象。 

* 更新属性映射会影响同步周期的性能。 对属性映射配置进行更新需要重新评估所有托管对象。 

* 建议采用的最佳做法是尽量少地连续更改属性映射。


## <a name="next-steps"></a>后续步骤

* [在 SaaS 应用中自动预配和取消预配用户](active-directory-saas-app-provisioning.md)
* [为属性映射编写表达式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [用于用户预配的作用域筛选器](active-directory-saas-scoping-filters.md)
* [使用 SCIM 启用从 Azure Active Directory 到应用程序的用户和组自动预配](manage-apps/use-scim-to-provision-users-and-groups.md)
* [有关如何集成 SaaS 应用的教程列表](saas-apps/tutorial-list.md)

<!--Image references-->
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png
[8]: ./media/active-directory-saas-customizing-attribute-mappings/24.png
[9]: ./media/active-directory-saas-customizing-attribute-mappings/25.PNG

