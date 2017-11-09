---
title: "在 Azure Active Directory 中管理组所属的组 | Microsoft 文档"
description: "在 Azure Active Directory 中，组可以包含其他组。 下面介绍如何管理这些成员身份。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e785c2d0-7724-47d4-a56e-c58280c08a14
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 44911ec2c278f7af7b8ec4bd971bc97b342a8bf6
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="manage-to-which-groups-a-group-belongs-in-your-azure-active-directory-tenant"></a>在 Azure Active Directory 租户中管理某个组属于哪些组
在 Azure Active Directory 中，组可以包含其他组。 下面介绍如何管理这些成员身份。

## <a name="how-do-i-find-the-groups-of-which-my-group-is-a-member"></a>如何查找包含我的组的组？
1. 使用目录的全局管理员帐户登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
2. 选择“用户和组”。

   ![打开“用户和组”映像](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
1. 选择“所有组”。

   ![选择“组”映像](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
1. 选择组。
2. 选择“组成员身份”。

   ![打开“组成员身份”映像](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
1. 要将组添加为另一个组的成员，请在“组 - 组成员身份”边栏选项卡中，选择“添加”命令。
2. 从“选择组”边栏选项卡中选择一个组，并选择边栏选项卡底部的“选择”按钮。 一次只可以将组添加到一个组。 “用户”框会通过将输入内容与用户或设备名称的任何部分进行匹配来筛选显示内容。 该框中不允许使用任何通配符。

   ![添加组成员身份](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. 要将组作为另一个组的成员删除，请在“组 - 组成员身份”边栏选项卡中，选择一个组。
9. 选择“删除”命令，然后在出现提示时确认选择。

   ![删除成员身份命令](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. 为组完成组成员身份更改后，请选择“保存”。

## <a name="additional-information"></a>其他信息
这些文章提供了有关 Azure Active Directory 的更多信息。

* [查看现有组](active-directory-groups-view-azure-portal.md)
* [创建新组并添加成员](active-directory-groups-create-azure-portal.md)
* [管理组的设置](active-directory-groups-settings-azure-portal.md)
* [管理组的成员](active-directory-groups-members-azure-portal.md)
* [管理组中用户的动态规则](active-directory-groups-dynamic-membership-azure-portal.md)
