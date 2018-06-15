---
title: 在 Azure Active Directory 中更改企业应用的名称或徽标 | Microsoft Docs
description: 如何在 Azure Active Directory 中更改自定义企业应用的名称或徽标
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: ad424d6ca8ea8c35aa502a3d1bd98940591c38e8
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35302033"
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>在 Azure Active Directory 中更改企业应用的名称或徽标
在 Azure Active Directory (Azure AD) 中更改自定义企业应用程序的名称或徽标很容易。 必须具有适当的权限才能进行这些更改，并且必须是自定义应用的创建者。

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>如何更改企业应用的名称或徽标？
1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“所有服务”，在文本框中输入 **Azure Active Directory**，并选择“Enter”。
3. 在“Azure Active Directory - *directoryname*”窗格（即，正在管理的目录的 Azure AD 窗格）中，选择“企业应用程序”。

    ![打开企业应用](./media/change-name-or-logo-portal/open-enterprise-apps.png)
4. 在“企业应用程序”窗格中，选择“所有应用程序”。 此时会显示可管理应用的列表。
5. 在“企业应用程序 - 所有应用程序”窗格中，选择一个应用。
6. 在 ***appname*** 窗格（即标题中包含所选应用的名称的窗格）中，选择“属性”。

    ![选择属性命令](./media/change-name-or-logo-portal/select-app.png)
7. 在“***appname*** - 属性”窗格中，浏览找到可用作新徽标的文件，并/或编辑应用名称。

    ![更改应用徽标或 nameproperties 命令](./media/change-name-or-logo-portal/change-logo.png)
8. 选择“保存”命令。

## <a name="next-steps"></a>后续步骤
* [查看所有组](../active-directory-groups-view-azure-portal.md)
* [向企业应用分配用户或组](assign-user-or-group-access-portal.md)
* [删除企业应用的用户或组分配](remove-user-or-group-access-portal.md)
* [禁用企业应用的用户登录](disable-user-sign-in-portal.md)
