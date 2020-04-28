---
title: 编辑组信息 - Azure Active Directory | Microsoft Docs
description: 有关如何使用 Azure Active Directory 编辑组信息的说明。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc06abca08b2522ac57552e85f7c1bac3ef854af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "68561873"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>使用 Azure Active Directory 编辑组信息

使用 Azure Active Directory (Azure AD) 可编辑组的设置，包括更新其名称、描述或成员身份类型。

## <a name="to-edit-your-group-settings"></a>编辑组设置
1. 使用目录的全局管理员帐户登录到 [Azure 门户](https://portal.azure.com)。

2. 依次选择“Azure Active Directory”、“组”。  

    此时会出现“组 - 所有组”页，其中显示了所有处于活动状态的组。 

3. 从“组 - 所有组”  页面上，在“搜索”  框中尽量完整地键入组名称。 对于本文而言，我们将搜索“MDM 策略 - 西部”  组。

    搜索结果将显示在“搜索”  框中，随着你键入更多的字符而进行更新。

    ![“所有组”页面，其中的“搜索”框中具有搜索文本](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. 选择“MDM 策略 - 西部”  组，然后从“管理”区域中选择“属性”。  

    ![“组概述”页面，其中突出显示了“成员”选项和信息](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. 根据需要更新“常规设置”  信息，包括：

    ![组的属性设置](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **组名称。** 编辑现有的组名称。
    
    - **组说明。** 编辑现有的组说明。

    - **组类型。** 创建组后无法更改组类型。 若要更改**组类型**，必须删除组并新建一个。
    
    - **成员身份类型。** 更改成员身份类型。 有关各种可用成员身份类型的详细信息，请参阅[如何：使用 Azure Active Directory 门户创建基本组并添加成员](active-directory-groups-create-azure-portal.md)。
    
    - **对象 ID。** 无法更改对象 ID，但可以复制它以在用于组的 PowerShell 命令中使用。 有关使用 PowerShell cmdlet 的详细信息，请参阅[用于配置组设置的 Azure Active Directory cmdlet](../users-groups-roles/groups-settings-v2-cmdlets.md)。

## <a name="next-steps"></a>后续步骤
这些文章提供了有关 Azure Active Directory 的更多信息。

- [查看组和成员](active-directory-groups-view-azure-portal.md)

- [创建基本组并添加成员](active-directory-groups-create-azure-portal.md)

- [如何在组中添加或删除成员](active-directory-groups-members-azure-portal.md)

- [管理组中用户的动态规则](../users-groups-roles/groups-create-rule.md)

- [管理组的成员身份](active-directory-groups-membership-azure-portal.md)

- [使用组管理对资源的访问权限](active-directory-manage-groups.md)

- [将 Azure 订阅关联或添加到 Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
