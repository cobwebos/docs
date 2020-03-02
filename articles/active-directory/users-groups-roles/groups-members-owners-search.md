---
title: 搜索和筛选组成员和所有者（预览）-Azure Active Directory |Microsoft Docs
description: 搜索和筛选 Azure 门户中的组成员和所有者。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/28/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a815446b79b3e5ec0a75e5d179953956643b16c9
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206106"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>在 Azure Active Directory 中搜索组和成员（预览）

本文介绍如何在 Azure Active Directory （Azure AD）门户中搜索组的成员和所有者，以及如何使用搜索筛选器作为组改善预览的一部分。 组经验中提供了许多改进，可帮助你快速、轻松地管理组，包括成员和所有者。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

此预览版中的更改包括：

- 新组搜索功能，如组名称中的子字符串搜索
- 成员和所有者列表中的新筛选和排序选项
- 成员和所有者列表的新搜索功能
- 大型组的更精确组计数

## <a name="enabling-and-managing-the-preview"></a>启用和管理预览

我们已使你可以轻松加入预览版：

  1. 登录到[Azure AD 门户](https://portal.azure.com)，并选择 "**组**"。
  2. 从 "组–所有组" 页中，选择页面顶部的横幅以加入预览。

还可以通过在 "**所有组**" 页上选择 "**预览信息**" 来查看最新功能和改进。 加入预览后，可以在所有组页面上看到预览标记，这些页面具有改进功能，并且是预览的一部分。 并非每个组页面都已在此预览版中更新。

如果遇到任何问题，可以通过选择 "**所有组**" 页顶部的标题来切换回旧体验。 我们非常感谢你的反馈，以便我们能够改善我们的经验。

## <a name="group-search-and-sorting"></a>组搜索和排序

已对组列表搜索进行了增强，以便在可以输入搜索字符串时，搜索将自动对组名称列表执行 `startswith` 和子字符串搜索。 子字符串搜索仅对整个单词执行，不包含特殊字符。 子字符串搜索区分大小写。

!["所有组" 页上的新子字符串搜索](./media/groups-members-owners-search/groups-search-preview.png)

例如，"策略" 搜索现在会同时返回 "MDM policy –西部" 和 "策略组"。 不返回名为 "New_policy" 的组。

- 还可以对组成员身份列表执行相同的搜索。
- 你现在可以使用 "名称" 列标题右侧的箭头按名称对 "组" 列表进行排序，以便以升序或降序对列表进行排序。

## <a name="group-member-search-and-filtering"></a>组成员搜索和筛选

### <a name="search-group-member-and-owner-lists"></a>搜索组成员和所有者列表

现在，你可以按名称搜索特定组的成员，并对该组所有者的列表执行相同的搜索。 在新体验中，如果在搜索框中输入字符串，将自动执行 startswith 搜索。 例如，搜索 "Scott" 将返回 Scott Wilkinson。

![新子字符串在组成员和所有者列表中搜索](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>筛选成员和所有者列表

除搜索外，现在你可以按用户类型筛选成员和所有者列表。 这是在列表的 "用户类型" 列中找到的信息。 因此，你可以按成员和来宾筛选列表，以确定组中是否存在任何来宾。

### <a name="view-and-manage-membership"></a>查看和管理成员身份

除了查看特定组的直接成员，你现在还可以在 "成员" 页中查看组的所有成员的列表。 "成员" 列表包括组的所有唯一成员，包括任何可传递的成员。

还可以单独搜索和筛选直接成员列表和 "所有成员" 列表。 筛选 "所有成员" 列表不会影响直接成员列表上应用的筛选器。

## <a name="improved-group-member-counts"></a>已改进的组成员计数

我们改进了 "组**概述**" 页，以便为所有大小的组提供组成员计数。 即使成员数量超过1000的组，也可以看到成员计数。 你现在可以在 "**概述**" 页上查看组的直接成员总数和总成员身份计数（包括可传递成员的组的唯一成员）。

![组成员身份计数中的更高准确性](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>后续步骤

这些文章提供了有关在 Azure AD 中使用组的其他信息。

- [查看组和成员](../fundamentals/active-directory-groups-view-azure-portal.md)
- [管理组成员身份](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [管理组中用户的动态规则](groups-create-rule.md)
- [编辑组设置](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [使用组管理对资源的访问权限](../fundamentals/active-directory-manage-groups.md)
- [使用组管理对 SaaS 应用程序的访问权限](groups-saasapps.md)
- [使用 PowerShell 命令管理组](groups-settings-v2-cmdlets.md)
- [将 Azure 订阅添加到 Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
