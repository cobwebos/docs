---
title: 在 Azure 活动目录中为"我的应用"门户创建集合 |微软文档
description: 使用"我的应用"集合自定义"我的应用"页面，为您的最终用户提供更简单的"我的应用"体验。 将应用程序组织到具有单独选项卡的组中。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: c91b9ffc9e3487e492c91cb0f5825d0b725f9410
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120095"
---
# <a name="create-collections-on-the-my-apps-portal"></a>在"我的应用"门户上创建集合

您的用户可以使用"我的应用"门户查看和启动他们有权访问的基于云的应用程序。 默认情况下，用户可以访问的所有应用程序都列在一页上。 为了更好地为用户组织此页面，如果您有 Azure AD 高级 P1 或 P2 许可证，则可以设置集合。 使用集合，可以将相关应用程序（例如，按作业角色、任务或项目）组合在一起，并在单独的选项卡上显示它们。集合实质上将筛选器应用于用户已经可以访问的应用程序，因此用户只能看到集合中分配给它们的应用程序。

> [!NOTE]
> 本文介绍管理员如何启用和创建集合。 有关如何使用"我的应用"门户和集合的最终用户的信息，请参阅[访问和使用集合](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces)。

## <a name="enable-the-latest-my-apps-features"></a>启用最新的"我的应用"功能

1. 打开[**Azure 门户**](https://portal.azure.com/)，以用户管理员或全局管理员身份登录。

2. 转到**Azure 活动目录** > **用户设置**。

3. 在 **"用户要素预览"** 下，选择 **"管理用户要素预览设置**"。

4. 在 **"用户可以为我的应用使用预览功能"** 下，选择以下选项之一：
   * **已选择**- 启用特定组的要素。 使用 **"选择组**"选项选择要为其启用要素的组。  
   * **全部**- 启用所有用户的功能。

> [!NOTE]
> 要打开"我的应用"门户，用户可以使用组织的链接`https://myapps.microsoft.com`或自定义链接，例如`https://myapps.microsoft.com/contoso.com`。 启用新的"我的应用"体验后，"**更新的我的应用程序"体验将**显示在"我的应用"页面顶部，用户可以选择 **"试用"** 以查看新体验。 要停止使用新体验，用户可以从页面顶部的 **"离开新体验**"横幅中选择 **"是**"。

## <a name="create-a-collection"></a>创建集合

要创建集合，必须具有 Azure AD 高级 P1 或 P2 许可证。

1. 打开[**Azure 门户**](https://portal.azure.com/)并使用 Azure AD 高级 P1 或 P2 许可证登录管理员。

2. 转到**Azure 活动目录** > **企业应用程序**。

3. 在 **"管理**"下，选择 **"集合**"。

4. 选择 **"新建集合**"。 在 **"新建集合"** 页中，输入集合**的名称**（我们建议不要在名称中使用"集合"。 然后输入**描述**。

   ![新集合页](media/acces-panel-collections/new-collection.png)

5. 选择"**应用程序**"选项卡。选择 **" 添加应用程序**"，然后在 **"添加应用程序**"页中，选择要添加到集合中的所有应用程序，或使用 **"搜索**"框查找应用程序。

   ![将应用程序添加到集合](media/acces-panel-collections/add-applications.png)

6. 完成添加应用程序后，选择 **"添加**"。 将显示所选应用程序的列表。 您可以使用向上箭头更改列表中的应用程序顺序。 要向下移动应用程序或从集合中删除它，请选择 **"更多**"菜单 **（...）。**

7. 选择"**所有者**"选项卡。选择 **"添加用户和组**"，然后在"**添加用户和组**"页中，选择要向其分配所有权的用户或组。 完成选择用户和组后，**选择"选择**"。

9. 选择"**用户和组**"选项卡。选择 **" 添加用户和组**"，然后在"**添加用户和组**"页中，选择要将集合分配给的用户或组。 或者使用 **"搜索"** 框查找用户或组。 完成选择用户和组后，**选择"选择**"。

   ![添加用户和组](media/acces-panel-collections/add-users-and-groups.png)

11. 选择 **"审阅 " 创建**。 将显示新集合的属性。


## <a name="view-audit-logs"></a>查看审核日志

审核日志记录"我的应用"集合操作，包括创建集合的最终用户操作。 以下事件从"我的应用"生成：

* 创建集合
* 编辑集合
* 删除集合
* 启动应用程序（最终用户）
* 自助服务应用程序添加（最终用户）
* 自助服务应用程序删除（最终用户）

通过在"活动"部分中选择**Azure 活动目录** > **企业应用程序** > **审核日志**，可以在[Azure 门户](https://portal.azure.com)中访问审核日志。 对于**服务**，请选择 **"我的应用**"。

## <a name="get-support-for-my-account-pages"></a>获取对"我的帐户"页面的支持

在"我的应用"页面中，用户可以选择 **"我的帐户** > **查看我的帐户"** 以打开其帐户设置。 在 Azure AD**我的帐户**页上，用户可以管理其安全信息、设备、密码等。 他们还可以访问其 Office 帐户设置。

如果需要提交 Azure AD 帐户页或 Office 帐户页的问题支持请求，请按照以下步骤操作，以便正确路由请求： 

* 对于 Azure **AD"我的帐户"** 页的问题，请从 Azure 门户中打开支持请求。 转到**Azure 门户** > **Azure 活动目录** > **新支持请求**。

* 对于**Office"我的帐户"** 页面的问题，请从 Microsoft 365 管理中心内打开支持请求。 转到**微软 365 管理中心** > **支持**。 

## <a name="next-steps"></a>后续步骤
[Azure Active Directory 中应用程序的最终用户体验](end-user-experiences.md)