---
title: 如何删除用户对应用程序的访问权限 | Microsoft Docs
description: 了解如何删除用户对应用程序的访问权限
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: e227f46db05c108c27322e8dd76177cf9849c937
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330326"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>如何删除用户对应用程序的访问权限

本文介绍如何删除用户对应用程序的访问权限。

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>我要删除特定用户或组到应用程序的分配

若要删除用户或组对应用程序的分配，请按照[在 Azure Active Directory 中从企业应用删除用户或组分配](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)一文中所列的步骤进行操作。

我要禁用每个用户对应用程序的所有访问权限

若要禁用所有用户对应用程序的登录，请遵循[在 Azure Active Directory 中对企业应用禁用用户登录](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)一文中所列出的步骤。

## <a name="i-want-to-delete-an-application-entirely"></a>我要彻底删除应用程序

若要**删除应用程序**，请按照以下说明进行操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

   * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  选择要删除的应用程序。

7.  加载应用程序后，在应用程序顶部的“概述”窗格中，单击“删除”图标。

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>我要禁用用户未来针对应用程序的所有同意操作

针对整个目录禁用用户同意操作，可防止最终用户同意任何应用程序。 管理员仍可代表用户执行同意操作。 若要深入了解应用程序同意，以及为何要或不这样操作，请参阅[了解用户和管理员同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)。

若要**禁用用户未来在整个目录中执行的所有同意操作**，请根据以下说明进行操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“用户设置”。

6.  通过将“用户可以允许应用访问其数据”切换为“否”来禁用用户未来的所有同意操作，并单击“保存”按钮。


# <a name="next-steps"></a>后续步骤
[管理对应用的访问](manage-apps/what-is-access-management.md)
