---
title: 在 Azure Active Directory 中管理组的成员 | Microsoft Docs
description: 如何在 Azure Active Directory 中的组中添加或删除用户和设备
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: a35e85feb10997f458c7f3764920891e7932de59
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="manage-group-membership-for-users-in-your-azure-active-directory-tenant"></a>在 Azure Active Directory 租户中管理用户的组成员身份
本文介绍如何在 Azure Active Directory (Azure AD) 中管理组的成员。

## <a name="how-do-i-find-the-members-and-manage-them"></a>如何查找成员并对其进行管理？
1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务”，在文本框中输入“用户和组”，并选择 **Enter**。

   ![打开“用户管理”](./media/active-directory-groups-members-azure-portal/search-user-management.png)
3. 在“用户和组”边栏选项卡中，选择“所有组”。

   ![打开“组”边栏选项卡](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)
4. 在“用户和组 - 所有组”边栏选项卡中，选择一个组。
5. 在“组 - 组名”边栏选项卡中，选择“成员”。

   ![打开“成员”边栏选项卡](./media/active-directory-groups-members-azure-portal/view-group-members.png)
6. 要将成员添加到组，请在“组 - 成员”边栏选项卡中，选择“添加成员”。

   ![添加成员命令](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)
7. 在“成员”边栏选项卡中，选择一个或多个要添加到组的用户或设备，然后选择边栏选项卡底部的“选择”按钮以将它们添加到组。 “用户”框会通过将输入内容与用户或设备名称的任何部分进行匹配来筛选显示内容。 该框中不允许使用任何通配符。
8. 若要从组中删除成员，请在“组 - 成员”边栏选项卡中，选择一个成员。
9. 在“成员名”边栏选项卡中，选择“删除”命令，然后在出现提示时确认选择。

   ![删除成员命令](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)
10. 完成组的成员更改后，请选择“保存”。

## <a name="additional-information"></a>其他信息
这些文章提供了有关 Azure Active Directory 的更多信息。

* [查看现有组](active-directory-groups-view-azure-portal.md)
* [创建新组并添加成员](active-directory-groups-create-azure-portal.md)
* [管理组的设置](active-directory-groups-settings-azure-portal.md)
* [管理组的成员身份](active-directory-groups-membership-azure-portal.md)
* [管理组中用户的动态规则](active-directory-groups-dynamic-membership-azure-portal.md)
