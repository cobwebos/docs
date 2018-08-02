---
title: 使应用程序不出现在用户在 Azure Active Directory 中的体验中 | Microsoft Docs
description: 如何使应用程序不出现在用户在 Azure Active Directory 访问面板或 Office 365 启动器的体验中。
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
ms.topic: conceptual
ms.date: 01/04/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 55f80396df4cbfe7d0a16a6a5066b68aadc0bdd3
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368915"
---
# <a name="hide-an-application-from-users-experience-in-azure-active-directory"></a>使应用程序不出现在用户在 Azure Active Directory 中的体验中

如果有不希望显示在用户的访问面板或 Office 365 启动器上的应用程序，可使用隐藏此应用磁贴的选项。  以下两个选项可用于从用户的应用启动器中隐藏应用程序。

- 使第三方应用程序不显示在用户访问面板和 Office 365 应用启动器中
- 从用户访问面板中隐藏所有 Office 365 应用程序

隐藏该应用后，用户仍对该应用具有权限，但不会看到该应用显示在其应用启动器上。 必须具有适当的权限才能管理企业应用，并且必须是目录的全局管理员。


## <a name="hiding-an-application-from-users-end-user-experiences"></a>使应用程序不出现在用户的最终用户体验中
可以按照下面的步骤，根据你的情况，从访问面板中隐藏应用程序。

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>如何使第三方应用不显示在用户的访问面板和 O365 应用启动器上？
按照以下步骤从用户的访问面板和 Office 365 应用启动器中隐藏应用程序。

1.  使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
2.  选择“所有服务”，在文本框中输入 **Azure Active Directory**，并选择“Enter”。
3.  在 **Azure Active Directory - *目录名称*** 屏幕上 （即所管理目录的 Azure AD 屏幕），选择 “**企业应用程序**”。
![企业应用](./media/hide-application-from-user-portal/app1.png)
4.  在“企业应用程序”屏幕上，选择“所有应用程序”。 此时会显示可管理应用的列表。
5.  在“企业应用程序 - 所有应用程序”屏幕上，选择一个应用。</br>
![企业应用](./media/hide-application-from-user-portal/app2.png)
6.  在“appname”屏幕（即标题中包含所选应用名称的屏幕）上，选择“属性”。
7.  在“appname - 属性”屏幕上，对于“是否对用户可见?”选择“是”。
![企业应用](./media/hide-application-from-user-portal/app3.png)
8.  选择“保存”命令。

### <a name="how-do-i-hide-office-365-applications-from-users-access-panel"></a>如何从用户的访问面板中隐藏 Office 365 应用程序？

按照以下步骤从访问面板中隐藏所有 Office 365 应用程序。 这些应用仍将显示在 Office 365 门户中。

1.  使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
2.  选择“所有服务”，在文本框中输入 **Azure Active Directory**，并选择“Enter”。
3.  在 **Azure Active Directory - *目录名称*** 屏幕上 （即所管理目录的 Azure AD 屏幕），选择 “**用户设置**”。
4.  在“用户设置”屏幕上的“企业应用程序”下，对于“用户只能在 Office 365 门户中查看 Office 365 应用”选择“是”。

![企业应用](./media/hide-application-from-user-portal/apps4.png)

## <a name="next-steps"></a>后续步骤
* [查看所有组](../fundamentals/active-directory-groups-view-azure-portal.md)
* [向企业应用分配用户或组](assign-user-or-group-access-portal.md)
* [删除企业应用的用户或组分配](remove-user-or-group-access-portal.md)
* [更改企业应用的名称或徽标](change-name-or-logo-portal.md)

