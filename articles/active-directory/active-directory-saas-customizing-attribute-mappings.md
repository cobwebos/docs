---
title: "自定义 Azure AD 属性映射 | Microsoft 文档"
description: "了解 Azure Active Directory 中有哪些针对 SaaS 应用的属性映射，以及如何修改它们来满足你的业务需求。"
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
ms.date: 05/16/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 18415c92d50a00c14823685857ab7e2624334ec7
ms.openlocfilehash: 19e934895279adb3a32096fffafd567b294c3009
ms.contentlocale: zh-cn
ms.lasthandoff: 03/01/2017


---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>为 Azure Active Directory 中的 SaaS 应用程序自定义用户预配属性映射
Microsoft Azure AD 支持将用户预配到第三方 SaaS 应用程序，例如 Salesforce、Google Apps 等等。 如果已启用了针对第三方 SaaS 应用程序的用户预配，则 Azure 管理门户将以称作“属性映射”的配置形式控制其属性值。

在 Azure AD 用户对象与每个 SaaS 应用的用户对象之间存在预先配置的一组属性映射。 某些应用可管理其他类型的对象，例如“组”或“联系人”。 <br> 
 可以根据业务需求自定义默认的属性映射。 这意味着，你可以更改或删除现有属性映射或者创建新的属性映射。

在 Azure AD 门户中，可以通过在 SaaS 应用程序的工具栏中单击“属性”来访问此功能。

> [!NOTE]
> 只有为 SaaS 应用程序启用了用户预配时，“属性”链接才可用。 
> 
> 

![Salesforce][1] 

在工具栏中单击“属性”时，将显示为 SaaS 应用程序配置的当前映射的列表。

以下屏幕截图显示了一个示例：

![Salesforce][2]  

在上面的示例中，可以发现 Salesforce 中的托管对象的 **firstName** 属性由所链接的 Azure AD 对象的 **givenName** 值填充。

如果希望自定义属性映射或者希望将自定义设置恢复为默认配置，可以通过单击工具栏中或应用程序底部的相关按钮来执行此操作。

![Salesforce][3]  

SaaS 应用程序需要执行一些属性映射才能正确运行。 在属性表中，相关属性映射的 **Required** 属性值为 **Yes**。 如果某个属性是必需的，则无法删除该属性。 在此情况下，**删除**功能不可用。

若要修改现有的属性映射，请选择该映射，然后单击“编辑”。 这将打开一个对话框页，可以在该页上修改所选的属性映射。

![编辑属性映射][4]  

## <a name="understanding-attribute-mapping-types"></a>了解属性映射类型
使用属性映射，可以控制如何在第三方 SaaS 应用程序中填充属性。 支持四种不同的映射类型：

* **直接** – 目标属性由 Azure AD 中的链接对象的属性值填充。
* **常量** – 目标属性由你指定的一个特定字符串填充。
* **表达式** - 目标属性是基于脚本式表达式的结果填充的。 
  有关详细信息，请参阅[在 Azure Active Directory 中编写属性映射的表达式](active-directory-saas-writing-expressions-for-attribute-mappings.md)。
* **无** - 目标属性保持不变。 不过，如果目标属性曾经为空，则它将由你指定的默认值填充。

除了这四个基本属性映射类型之外，自定义属性映射还支持**默认**值赋值概念。 默认值赋值确保当 Azure AD 中和目标对象中都没有值时使用某个值填充目标属性。

Microsoft Azure AD 提供了一个有效的同步进程实现。 在初始化环境中，在同步周期中只会处理需要更新的对象。 更新属性映射会影响同步周期的性能。 对属性映射配置进行更新需要重新评估所有托管对象。 建议采用的最佳做法是尽量少地连续更改属性映射。

## <a name="related-articles"></a>相关文章
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

