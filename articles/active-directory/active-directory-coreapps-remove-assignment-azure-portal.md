---
title: "在 Azure Active Directory 中删除企业应用的用户或组分配 | Microsoft Docs"
description: "如何在 Azure Active Directory 的企业应用中删除对用户或组的访问权限分配"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7b2d365b-ae92-477f-9702-353cc6acc5ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 99cbc54b4daa988dbf741275ce92d1c863af6720
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>在 Azure Active Directory 中删除企业应用的用户或组分配
可以轻松地在 Azure Active Directory (Azure AD) 中删除用户或组对企业应用程序的已分配访问权限。 必须具有适当的权限才能管理企业应用，并且必须是目录的全局管理员。

## <a name="how-do-i-remove-a-user-or-group-assignment"></a>如何删除用户或组分配？
1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“更多服务”，在文本框中输入 **Azure Active Directory**，并选择“Enter”。
3. 在“Azure Active Directory - 目录名”边栏选项卡（即，正在管理的目录的 Azure AD 边栏选项卡）中，选择“企业应用程序”。

    ![打开企业应用](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)
4. 在“企业应用程序”边栏选项卡中，选择“所有应用程序”。 此时会显示可管理应用的列表。
5. 在在“企业应用程序 - 所有应用程序”边栏选项卡中，选择一个应用。
6. 在“appname”边栏选项卡（即标题中包含所选应用名称的边栏选项卡）中，选择“用户和组”。

    ![选择用户或组](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)
7. 在“***appname*** - 用户和组分配”边栏选项卡中，选择一个或多个用户或组，并选择“删除”命令。 出现提示时确认所作的决定。

    ![选择“删除”命令](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="next-steps"></a>后续步骤
* [查看所有组](active-directory-groups-view-azure-portal.md)
* [向企业应用分配用户或组](active-directory-coreapps-assign-user-azure-portal.md)
* [禁用企业应用的用户登录](active-directory-coreapps-disable-app-azure-portal.md)
* [更改企业应用的名称或徽标](active-directory-coreapps-change-app-logo-user-azure-portal.md)
