---
title: 搜索和筛选组成员和所有者（预览） - Azure 活动目录 |微软文档
description: 搜索和筛选 Azure 门户中的成员和所有者。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206106"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Azure 活动目录中的搜索组和成员（预览）

本文介绍如何搜索组的成员和所有者，以及如何在 Azure 活动目录 （Azure AD） 门户中使用搜索筛选器作为组改进预览的一部分。 在组体验方面有很多改进，可帮助您快速轻松地管理组（包括成员和所有者）。 有关预览的详细信息，请参阅 Microsoft [Azure 预览的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

此预览中的更改包括：

- 新的组搜索功能，如组名称中的子字符串搜索
- 成员和所有者列表上的新筛选和排序选项
- 成员和所有者列表的新搜索功能
- 大组更准确的组计数

## <a name="enabling-and-managing-the-preview"></a>启用和管理预览

我们可以轻松加入预览：

  1. 登录到 Azure [AD 门户](https://portal.azure.com)，然后选择**组**。
  2. 从"组 + 所有组"页面中，选择页面顶部的横幅以加入预览。

您还可以通过在"**所有组**"页面上选择 **"预览信息**"来查看最新的功能和改进。 加入预览后，您可以在具有改进且属于预览的所有组页面上看到预览标记。 不是每个组页面都已作为此预览的一部分进行更新。

如果遇到任何问题，可以通过选择 **"所有组"** 页面顶部的横幅来切换旧体验。 我们感谢您的反馈，以便我们可以改善我们的体验。

## <a name="group-search-and-sorting"></a>分组搜索和排序

组列表搜索已增强，以便在可以输入搜索字符串时，搜索会自动在组名称列表中执行`startswith`和子字符串搜索。 子字符串搜索仅对整个单词执行，不包括特殊字符。 子字符串搜索区分大小写。

!["所有组"页上的新子字符串搜索](./media/groups-members-owners-search/groups-search-preview.png)

例如，搜索"策略"现在将返回"MDM 策略 + 西部"和"策略组"。 不会返回名为"New_policy"的组。

- 您还可以对组成员身份列表执行相同的搜索。
- 现在，您可以使用名称列标题右侧的箭头按名称按名称对组列表进行排序，以便按升序或降序对列表进行排序。

## <a name="group-member-search-and-filtering"></a>组成员搜索和筛选

### <a name="search-group-member-and-owner-lists"></a>搜索组成员和所有者列表

现在，您可以按名称搜索特定组的成员，也可以对组的所有者列表执行相同的搜索。 在新体验中，如果在搜索框中输入字符串，将自动执行"开始搜索"。 例如，搜索"斯科特"将返回斯科特·威尔金森。

![对组成员和所有者列表的新子字符串搜索](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>筛选器成员和所有者列表

除了搜索之外，现在还可以按用户类型筛选成员和所有者列表。 这是在列表的用户类型列中找到的信息。 因此，您可以按成员和来宾筛选列表，以确定组中是否有任何来宾。

### <a name="view-and-manage-membership"></a>查看和管理成员资格

除了查看特定组的直接成员外，您现在还可以查看"成员"页中该组所有成员的列表。 成员列表包括组的所有唯一成员，包括任何传递成员。

您还可以单独搜索和筛选直接成员列表和所有成员列表。 筛选所有成员列表不会影响应用于直接成员列表的筛选器。

## <a name="improved-group-member-counts"></a>改进的组成员计数

我们改进了组**概述**页面，以为所有大小的组提供组成员计数。 即使对于成员超过 1，000 个的组，也可以查看成员计数。 现在，您可以在 **"概述"** 页上查看组的直接成员总数和成员总数（包括传递成员在内的组的所有唯一成员）。

![组成员数的准确性更高](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>后续步骤

这些文章提供了有关在 Azure AD 中处理组的其他信息。

- [查看组和成员](../fundamentals/active-directory-groups-view-azure-portal.md)
- [管理组成员身份](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [管理组中用户的动态规则](groups-create-rule.md)
- [编辑组设置](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [使用组管理对资源的访问权限](../fundamentals/active-directory-manage-groups.md)
- [使用组管理对 SaaS 应用程序的访问权限](groups-saasapps.md)
- [使用 PowerShell 命令管理组](groups-settings-v2-cmdlets.md)
- [将 Azure 订阅添加到 Azure 活动目录](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
