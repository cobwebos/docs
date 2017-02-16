---
title: "Azure Active Directory 中的专用组 | Microsoft Docs"
description: "概述专用组在 Azure Active Directory 中的工作原理及其创建方法。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 86158909-083a-41fe-8090-955e96ad1865
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 3cacf9edde4ed2cabf996e390bb1e00113bc47cb


---
# <a name="dedicated-groups-in-azure-active-directory"></a>Azure Active Directory 中的专用组
在 Azure Active Directory (Azure AD) 中，专门的组功能可自动创建并填充 Azure AD 预定义的组的成员身份。 无法使用 Azure 经典门户、Windows PowerShell cmdlet 或以编程方式添加或删除专用组的成员。

> [!NOTE]
> 专用组要求将 Azure AD Premium 许可证分配给
> 
> * 管理组中规则的管理员
> * 规则选择成为组成员的所有用户
> 
> 

**启用专用组**

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，选择“Active Directory”，然后打开你的组织的目录。
2. 选择“组”选项卡，然后打开要编辑的组。
3. 选择“配置”选项卡，然后将“启用专用组”设置为“是”。

一旦将“启用专用组”开关设置为“是”，你就可以通过将“启用‘所有用户’组”开关设置为“是”，来进一步使目录自动创建“所有用户”专用组。 然后，你还可以编辑此专用组的名称，方法是在“‘所有用户’组的显示名称”字段中键入该名称。

“所有用户”组可用于向目录中的所有用户分配相同的权限。 例如，可以通过向“所有用户”专用组分配对某个 SaaS 应用程序的访问权限，来授予目录中所有用户对该应用程序的访问权限。

“所有用户”专用组包含目录中的所有用户，当中有来宾和外部用户。 如果需要排除外部用户的组，则可以使用类似下面的基于属性的动态规则来创建组：

                (user.userPrincipalName -notContains "#EXT#@")

对于排除所有来宾用户的组，可使用和下面类似的规则来创建：

                (user.userType -ne "Guest")

若要了解如何为动态组成员身份创建 *高级* 规则（可包含多个比较条件的规则），请参阅 [使用属性创建高级规则](active-directory-accessmanagement-groups-with-advanced-rules.md)。

这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](active-directory-manage-groups.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [什么是 Azure Active Directory？](active-directory-whatis.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)




<!--HONumber=Dec16_HO5-->


