---
title: "在 Azure Active Directory 预览版中管理包含你所在组的组 | Microsoft 文档"
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
ms.date: 09/12/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1968862a68d86c0f42e86ecb1f23aeebd264076c


---
# <a name="manage-the-groups-your-group-is-a-member-of-in-azure-active-directory-preview"></a>在 Azure Active Directory 预览版中管理包含你的组的组
在 Azure Active Directory 预览版中，组可以包含其他组。 [预览包括哪些内容？](active-directory-preview-explainer.md) 下面介绍如何管理这些成员身份。

## <a name="how-do-i-find-the-groups-my-group-is-a-member-of"></a>如何查找包含我的组的组？
1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“更多服务”，在文本框中输入“用户和组”，然后选择 **Enter**。
   
   ![打开“用户管理”](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
3. 在“用户和组”边栏选项卡中，选择“所有组”。
   
   ![打开“组”边栏选项卡](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
4. 在“用户和组 - 所有组”边栏选项卡中，选择一个组。
5. 在“组 - 组名”**边栏选项卡中，选择“组成员身份”****。
   
   ![打开“组成员身份”边栏选项卡](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
6. 若要将你的组添加为另一个组的成员，请在“组 - 组成员身份”边栏选项卡中，选择“添加”命令。
7. 从“选择组”边栏选项卡中选择一个组，然后选择边栏选项卡底部的“选择”按钮。 一次只可以将你的组添加到一个组。 “用户”框会通过将输入内容与用户或设备名称的任何部分进行匹配来筛选显示内容。 该框中不允许使用任何通配符。
   
   ![添加组成员身份](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. 若要将你的组作为另一个组的成员删除，请在“组 - 组成员身份”边栏选项卡中，选择一个组。
9. 在“组名”边栏选项卡中，选择“删除”命令，然后在出现提示时确认你的选择。
   
   ![删除成员身份命令](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. 为你的组完成组成员身份更改后，请选择“保存”。

## <a name="additional-information"></a>其他信息
这些文章提供了有关 Azure Active Directory 的更多信息。

* [查看现有组](active-directory-groups-view-azure-portal.md)
* [创建新组并添加成员](active-directory-groups-create-azure-portal.md)
* [管理组的设置](active-directory-groups-settings-azure-portal.md)
* [管理组的成员](active-directory-groups-members-azure-portal.md)
* [管理组中用户的动态规则](active-directory-groups-dynamic-membership-azure-portal.md)




<!--HONumber=Nov16_HO3-->


