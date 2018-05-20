---
title: 应用程序在访问面板上的显示方式 | Microsoft Docs
description: 关于应用程序为何在访问面板上显示的疑难解答
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.reviewr: japere
ms.openlocfilehash: 0d4203ea39adf027d783a482198f523e18cbc246
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="how-applications-appear-on-the-access-panel"></a>应用程序为何在访问面板上显示

访问面板是一个基于 Web 的门户，在 Azure Active Directory (Azure AD) 中拥有工作或学校帐户的用户可以使用它来查看和启动 Azure AD 管理员已授权他们访问的基于云的应用程序。 这些应用程序代表用户在 Azure AD 门户中进行配置。 管理员可以直接向用户或用户所在组提供应用程序，这会导致应用程序出现在用户的访问面板上。

## <a name="general-issues-to-check-first"></a>首先要检查的常规问题

-   如果为用户或用户所在的组删除了某个应用程序，请在几分钟后重新登录该用户的访问面板以查看该应用程序是否已删除。

-   如果为用户或用户所在的组删除了某个许可证，则可能要花较长时间，具体取决于被更改的组的大小和复杂程度。 登录访问面板前请多等一会儿。

## <a name="problems-related-to-assigning-applications-to-users"></a>有关向用户分配应用程序的问题

如果用户以前被分配到应用程序，则可能会在其访问面板中看到该应用程序。 下面是一些检查方式：

-   [检查用户是否被分配到应用程序](#check-if-a-user-is-assigned-to-the-application)

-   [检查用户是否符合与应用程序相关的许可证](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>检查用户是否被分配给应用程序

若要检查是否将用户分配给了应用程序，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

6.  **搜索**有关应用程序的名称。

7.  单击“用户和组”。

8.  检查用户是否被分配给应用程序。

  * 要将用户从应用程序中删除，请**单击用户对应的行**，并选择“删除”。

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>检查用户是否符合与应用程序相关的许可证

若要检查用户的已分配许可证，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击“许可证”查看当前已分配给用户的许可证。

   * 如果已向用户分配了 Office 许可证，则第一方 Office 应用程序会显示在用户的访问面板中。

## <a name="problems-related-to-assigning-applications-to-groups"></a>有关为组分配应用程序的问题

由于用户所在组已分配有应用程序，因此他们可以在访问面板中看到应用程序。 下面是一些检查方式：

-   [检查用户的组成员身份](#check-a-users-group-memberships)

-   [检查用户是否是被分配许可证的组的成员](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>检查用户的组成员身份

若要检查组的成员身份，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击“组”

8.  检查用户是否是分配给应用程序的组的成员。

   * 要将用户从组中删除，请**单击组对应的行**，并选择删除。

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>检查用户是否是被分配许可证的组的成员

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击“组”

8.  单击特定组所在的行。

9.  单击“许可证”查看已分配给组的许可证有哪些。

  * 如果已向组分配 Office 许可证，则某个第一方 Office 应用程序可能会显示在用户的访问面板中。


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>如果这些故障排除步骤未解决此问题

打开支持票证，并提供以下信息（如有）：

-   相关错误 ID

-   UPN（用户电子邮件地址）

-   租户 ID

-   浏览器类型

-   错误发生的时区和时间/时间段

-   Fiddler 跟踪

## <a name="next-steps"></a>后续步骤
[使用 Azure Active Directory 管理应用程序](manage-apps/what-is-application-management.md)
