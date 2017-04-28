---
title: "在 Azure Active Directory 中管理组 | Microsoft Docs"
description: "如何使用 Azure Active Directory 创建和管理组，以管理 Azure 用户。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: d1f5451c-3807-423c-8bac-2822d27b893f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/26/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 8f171456f74925eba16ebd6f56ced558a11f9d99
ms.openlocfilehash: 7d2cc99925e01f8135f04f5863f798e13d7413e3
ms.lasthandoff: 02/11/2017


---
# <a name="managing-groups-in-azure-active-directory"></a>在 Azure Active Directory 中管理组
> [!div class="op_single_selector"]
> * [Azure 门户](active-directory-groups-create-azure-portal.md)
> * [Azure 经典门户](active-directory-accessmanagement-manage-groups.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

Azure Active Directory (Azure AD) 用户管理的其中一项功能是能够创建用户组。 你可以使用组来执行管理任务，例如一次向多个用户分配许可证或权限。 也可以使用组将访问权限分配给

* 资源，例如目录中的对象
* 目录外部的资源，例如 SaaS 应用程序、Azure 服务、SharePoint 站点或本地资源

此外，资源所有者还可以将资源访问权限分配给其他人拥有的 Azure AD 组。 此分配操作将向该组的成员授予资源访问权限。 然后，组所有者可以管理组的成员身份。 实际上，资源所有者是将其资源的用户访问权限委派给了组的所有者。

## <a name="how-do-i-create-a-group"></a>如何创建组？
根据组织已订阅的服务，你可以使用下列其中一项来创建组：

* Azure 经典门户
* Office 365 帐户门户
* Windows Intune 帐户门户

我们将介绍在 Azure 经典门户中执行的任务。 有关使用非 Azure 门户管理 Azure AD 目录的详细信息，请参阅 [管理 Azure AD 目录](active-directory-administer.md)。

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，选择“Active Directory”，然后选择你所在组织的目录名称。
2. 选择“组”选项卡。
3. 选择“添加组”。
4. 在“添加组”窗口中，指定组的名称和说明。

## <a name="how-do-i-add-or-remove-individual-users-in-a-security-group"></a>如何在安全组中添加或删除单个用户？
**将单个用户添加到组**

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，选择“Active Directory”，然后选择你所在组织的目录名称。
2. 选择“组”选项卡。
3. 打开你要添加成员的组。 打开所选组的“成员”选项卡（如果尚未显示）。
4. 选择“添加成员” 。
5. 在“添加成员”页上，选择要添加为该组成员的用户或组的名称。 确保该名称已添加到“选定”窗格中。

**从组中删除单个用户**

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，选择“Active Directory”，然后选择你所在组织的目录名称。
2. 选择“组”选项卡。
3. 打开你要从中删除成员的组。
4. 选择“成员”选项卡，选择要从该组中删除的成员的名称，然后单击“删除”。
5. 在要从组中删除此成员的提示中确认。

## <a name="how-can-i-manage-the-membership-of-a-group-dynamically"></a>如何动态管理组的成员身份？
在 Azure AD 中，你可以非常轻松地设置一个简单的规则来判断哪些用户将成为组成员。 简单的规则是指仅进行一项比较的规则。 例如，如果某个组分配到 SaaS 应用程序，则可设置规则来添加职务为“销售代表”的用户。 此规则接着会将对此 SaaS 应用程序的访问权限授予目录中所有具有该职务的用户。

当用户的任何属性发生更改时，系统将评估目录中的所有动态组规则，以查看用户的属性更改是否会触发任何组添加或删除。 如果用户满足组中的规则，他们将作为成员添加到该组。 如果他们不再满足所属组的规则，则会从该组中删除他们的身份身份。

> [!NOTE]
> 你可以为安全组或 Office 365 组中的动态成员身份设置规则。 目前不支持使用嵌套的组成员身份来对应用程序进行基于组的分配。
>
> 动态组成员身份要求将 Azure AD Premium 许可证分配到
>
> * 管理组中规则的管理员
> * 组的所有成员
>
>

**启用组的动态成员身份**

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，选择“Active Directory”，然后选择你所在组织的目录名称。
2. 选择“组”选项卡，然后打开要编辑的组。
3. 选择“配置”选项卡，然后将“启用动态成员身份”设置为“是”。
4. 为该组设置一个简单的规则，以控制该组的动态成员身份的运作方式。 请确保已选中“添加符合以下条件的用户”选项，然后从列表中选择用户属性（例如部门、职务等）。
5. 接下来选择条件（“不等于”、“等于”、“开头不为”、“开头为”、“不包含”、“包含”、“不匹配”、“匹配”）。
6. 指定所选用户属性的比较值。

若要了解如何为动态组成员身份创建 *高级* 规则（可包含多个比较条件的规则），请参阅 [使用属性创建高级规则](active-directory-accessmanagement-groups-with-advanced-rules.md)。

## <a name="additional-information"></a>其他信息
这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](active-directory-manage-groups.md)
* [用于配置组设置的 Azure Active Directory cmdlet](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [什么是 Azure Active Directory？](active-directory-whatis.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)

