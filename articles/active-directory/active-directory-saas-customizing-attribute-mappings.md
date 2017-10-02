---
title: "自定义 Azure AD 属性映射 | Microsoft Docs"
description: "了解 Azure Active Directory 中有哪些针对 SaaS 应用的属性映射，以及如何修改它们来满足业务需求。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 6ca2fdc9c68ea0030d938eeaebd57aafa0e2790f
ms.contentlocale: zh-cn
ms.lasthandoff: 06/20/2017

---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>为 Azure Active Directory 中的 SaaS 应用程序自定义用户预配属性映射
Microsoft Azure AD 支持将用户预配到第三方 SaaS 应用程序，例如 Salesforce、Google Apps 等等。 如果已启用了针对第三方 SaaS 应用程序的用户预配，则 Azure 管理门户以称作“属性映射”的配置形式控制其属性值。

在 Azure AD 用户对象与每个 SaaS 应用的用户对象之间存在预先配置的一组属性映射。 某些应用可管理其他类型的对象，例如“组”或“联系人”。 <br> 
 可以根据业务需求自定义默认的属性映射。 这意味着，可以更改或删除现有属性映射或者创建新的属性映射。

在 Azure AD 门户中，可在“企业应用程序”的“管理”部分，通过单击“预配”下的“映射”配置来访问此功能。


![Salesforce][5] 

单击“映射”配置，将打开相关的“属性映射”边栏选项卡。  
SaaS 应用程序需要执行一些属性映射才能正确运行。 对于必需属性，“删除”功能将不可用。


![Salesforce][6]  

在上面的示例中，可以发现 Salesforce 中托管对象的“用户名”属性由所链接的 Azure Active Directory 对象的“userPrincipalName”值填充。

可通过单击映射自定义现有“属性映射”。 这将打开“编辑属性”边栏选项卡。

![Salesforce][7]  


  

## <a name="understanding-attribute-mapping-types"></a>了解属性映射类型
使用属性映射，可以控制如何在第三方 SaaS 应用程序中填充属性。 支持四种不同的映射类型：

* **直接** – 目标属性由 Azure AD 中的链接对象的属性值填充。
* **常量** - 目标属性使用已指定的特定字符串填充。
* **表达式** - 目标属性是基于脚本式表达式的结果填充的。 
  有关详细信息，请参阅[在 Azure Active Directory 中编写属性映射的表达式](active-directory-saas-writing-expressions-for-attribute-mappings.md)。
* **无** - 目标属性保持不变。 不过，如果目标属性曾经为空，则它由你指定的默认值填充。

除了这四个基本属性映射类型之外，自定义属性映射还支持可选的默认值赋值概念。 默认值赋值确保当 Azure AD 中和目标对象中都没有值时使用某个值填充目标属性。 最常见的配置是将此项留空。


## <a name="understanding-attribute-mapping-properties"></a>了解属性映射的属性

在前面的部分中，已介绍了属性映射类型属性。
除此属性外，属性映射还支持以下属性：

- 源属性 - 来自源系统的用户属性（例如：Azure Active Directory）。
- 目标属性 – 目标系统中的用户属性（例如：ServiceNow）。
- 使用此属性匹配对象 – 是否应使用此映射唯一标识源系统和目标系统之间的用户。 这通常在 Azure AD 中的 userPrincipalName 或邮件属性上设置，其通常映射到目标应用程序中的用户名字段。
- 匹配优先级 – 可设置多个匹配属性。 当存在匹配时，会按照此字段定义的顺序进行评估。 一旦找到匹配，就不会进一步评估其他匹配属性。
- **应用此映射**
    - 始终 – 对用户创建和更新操作均应用此映射
    - 仅创建期间 - 仅对用户创建操作应用此映射


## <a name="what-you-should-know"></a>要点

Microsoft Azure AD 提供了一个有效的同步进程实现。 在初始化环境中，在同步周期中只会处理需要更新的对象。 更新属性映射会影响同步周期的性能。 对属性映射配置进行更新需要重新评估所有托管对象。 建议采用的最佳做法是尽量少地连续更改属性映射。

## <a name="next-steps"></a>后续步骤

* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [在 SaaS 应用中自动预配和取消预配用户](active-directory-saas-app-provisioning.md)
* [为属性映射编写表达式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [用于用户预配的作用域筛选器](active-directory-saas-scoping-filters.md)
* [使用 SCIM 启用从 Azure Active Directory 到应用程序的用户和组自动预配](active-directory-scim-provisioning.md)
* [帐户预配通知](active-directory-saas-account-provisioning-notifications.md)
* [有关如何集成 SaaS 应用的教程列表](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png


