---
title: " (预览版的用户管理增强功能) -Azure Active Directory |Microsoft Docs"
description: 介绍 Azure Active Directory 如何启用用户搜索、筛选以及有关用户的详细信息。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 10/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: be0d428120f53a4edb9763199a78b0e50409b19a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708725"
---
# <a name="user-management-enhancements-preview-in-azure-active-directory"></a>Azure Active Directory 中 (预览) 的用户管理增强功能

本文介绍如何在 Azure Active Directory (Azure AD) 门户中使用用户管理增强功能预览。 " **所有用户** " 和 " **已删除用户** " 页已更新，以提供详细信息并使查找用户更容易。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

预览中的更改包括：

- 更直观的用户属性，包括对象 ID、目录同步状态、创建类型和标识颁发者
- "立即搜索" 允许对姓名、电子邮件和对象 Id 进行合并搜索
- 按用户类型增强筛选 (member、guest、none) 、目录同步状态、创建类型、公司名称和域名
- 对属性（如名称和用户主体名称）的新排序功能
- 新用户总数使用搜索或筛选器进行更新

> [!NOTE]
> 此预览版当前不适用于 Azure AD B2C 租户。

## <a name="find-the-preview"></a>查找预览

默认情况下，预览处于启用状态，因此你可以立即使用它。 通过选择 "**所有用户**" 页上的 "**预览功能**"，可以查看最新的功能和改进。 作为此预览版的一部分更新的所有页面都将显示预览标记。 如果遇到任何问题，可以切换回旧体验：

1. 登录到 [Azure AD 管理中心](https://aad.portal.azure.com) ，然后选择 " **用户**"。
1. 从 " **用户–所有用户** " 页中，选择页面顶部的横幅。
1. 在 " **预览功能** " 窗格中，启用 " **增强的用户管理** "。

   ![如何以及在何处启用和禁用增强的用户管理](./media/users-search-enhanced/enable-preview.png)

我们非常感谢你的反馈，以便我们能够改善我们的经验。

## <a name="more-user-properties"></a>更多用户属性

我们对 " **所有用户** " 和 " **已删除用户** " 页上的可用列进行了一些更改。 除了我们为管理用户列表提供的现有列外，还添加了更多的列。

### <a name="all-users-page"></a>所有用户页

下面是 " **所有用户** " 页上显示的用户属性：

- 名称：用户的显示名称。
- 用户主体名称：用户的用户主体名称 (UPN) 。
- 用户类型：用户的用户类型，即 "成员" 或 "来宾"。
- 目录已同步：指示用户是否从本地目录同步。
- 标识颁发者：用于登录用户帐户的标识的颁发者。
- 对象 ID：用户的对象 ID。
- 创建类型：指示如何创建用户帐户。
- 公司名称：与用户关联的公司名称。
- 邀请状态：来宾用户的邀请状态。
- Mail：用户的电子邮件。

![显示在 "所有用户" 和 "已删除用户" 页上的新用户属性](./media/users-search-enhanced/user-properties.png)

### <a name="deleted-users-page"></a>已删除用户页

" **已删除用户** " 页包括 " **所有用户** " 页上可用的所有列，以及一些其他列，即：

- 删除日期：用户从组织中第一次删除的日期 (用户可) 还原。
- 永久删除日期：从组织中永久删除用户的日期。

某些列默认显示。 若要添加其他列，请在页面上选择 " **列** "，选择要添加的列名称，然后选择 **"确定"** 以保存首选项。

### <a name="identity-issuers"></a>标识颁发者

为任何用户选择 " **标识颁发者** " 列中的条目，以查看有关颁发者的其他详细信息，包括登录类型和分配的颁发者 ID。 **标识颁发者**列中的条目可以为多值。 如果有多个用户的标识颁发者，你会在 "**所有用户**" 和 "**已删除用户**" 页上的 "**标识颁发者**" 列中看到 "多个" 一词，详细信息窗格列出所有颁发者。

> [!NOTE]
> **源**列被多个列替代，包括**创建类型**、**目录同步**和**标识颁发者**，以便进行更精细的筛选。

## <a name="user-list-search"></a>用户列表搜索

输入搜索字符串时，搜索将使用 "开头为" 搜索，该搜索现在可在单个搜索中匹配姓名、电子邮件或对象 Id。 您可以在 "搜索" 框中输入这些属性中的任何一个，搜索将跨所有这些属性自动查看以返回任何匹配结果。 你可以对 " **所有用户** " 和 " **已删除用户** " 页执行相同的搜索。

## <a name="user-list-filtering"></a>用户列表筛选

筛选功能得到了增强，可为 " **所有用户** " 和 " **已删除用户** " 页提供更多筛选选项。 现在可以同时按多个属性进行筛选，并且可以按更多属性进行筛选。

### <a name="filtering-all-users-list"></a>筛选所有用户列表

下面是 " **所有用户** " 页上可筛选的属性：

- 用户类型： Member、guest、none
- 目录已同步状态：是，否
- 创建类型：邀请、电子邮件验证、本地帐户
- 邀请状态–等待接受，已接受
- 域名：输入域名
- 公司名称：输入公司名称
- 管理单元：选择此选项可将你查看的用户的范围限制为单个管理单元。 有关详细信息，请参阅 [管理单元管理预览版](directory-administrative-units.md)。

### <a name="filtering-deleted-users-list"></a>筛选删除的用户列表

" **已删除用户** " 页中有其他筛选器，而不是 " **所有用户** " 页。 下面是 " **已删除用户** " 页上可筛选的属性：

- 用户类型： Member、guest、none
- 目录已同步状态：是，否
- 创建类型：邀请、电子邮件验证、本地帐户
- 邀请状态：待定接受，已接受
- 删除日期：过去7天、14天或30天
- 域名：输入域名
- 公司名称：输入公司名称
- 永久删除日期：过去7天、14天或30天

## <a name="user-list-sorting"></a>用户列表排序

现在，你可以在 " **所有用户** " 和 " **已删除用户** " 页中按名称和用户主体名称进行排序。 您还可以在 " **已删除用户** " 列表中按删除日期排序。

## <a name="user-list-counts"></a>用户列表计数

你可以在 " **所有用户** " 和 " **已删除用户** " 页中查看用户总数。 搜索或筛选列表时，会更新计数以反映找到的用户总数。

!["所有用户" 页上的用户列表计数说明](./media/users-search-enhanced/user-list-sorting.png)

## <a name="frequently-asked-questions-faq"></a>常见问题 (FAQ)

问题 | Answer
-------- | ------
用户和来宾的批量功能发生了什么情况？ | 大容量操作仍适用于用户和来宾，包括批量创建、批量邀请、批量删除和下载用户。 我们只是将它们合并到称为 **大容量操作**的菜单。 你可以在 "**所有用户**" 页的顶部找到 "**批量操作**" 选项。
源列发生了什么情况？ | **源**列已替换为其他提供类似信息的列，同时允许您单独对这些值进行筛选。 示例包括 **创建类型**、已 **同步目录** 和 **标识颁发者**。
用户名列发生了什么情况？ | **用户名**列仍存在，但已被重命名为**用户主体名称**。 这更好地反映了该列中包含的信息。 你还会注意到，现在为 B2B 来宾显示了完整的用户主体名称。 这与你在 MS Graph 中获得的内容相匹配。  
为什么只能执行 "开始使用" 搜索而不是 "包含" 搜索？ | 有一些限制会阻止我们允许执行 "包含" 搜索。 我们听说过反馈，请继续关注。

## <a name="next-steps"></a>后续步骤

用户操作

- [添加或更改个人资料信息](../fundamentals/active-directory-users-profile-azure-portal.md)
- [添加或删除用户](../fundamentals/add-users-azure-active-directory.md)

大容量操作

- [下载用户列表](users-bulk-download.md)
- [批量添加用户](users-bulk-add.md)
- [批量删除用户](users-bulk-delete.md)
- [批量还原用户](users-bulk-restore.md)
