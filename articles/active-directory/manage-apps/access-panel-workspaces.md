---
title: 在 Azure Active Directory 中创建我的应用门户的工作区 |Microsoft Docs
description: 使用 "我的应用" 工作区自定义我的应用页面，以便为你的最终用户提供更简单的应用体验。 以单独的选项卡的方式将应用程序组织到组中。
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
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a986c2032fc41bbd34230c5b0ead745f88b000f
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199823"
---
# <a name="create-workspaces-on-the-my-apps-preview-portal"></a>在 "我的应用" （预览版）门户中创建工作区

你的用户可以使用 "我的应用（预览）" 门户来查看和启动他们有权访问的基于云的应用程序。 默认情况下，用户可以访问的所有应用程序都在一个页面上一起列出。 为了更好地为用户组织此页面，如果你有 Azure AD Premium P1 或 P2 许可证，则可以设置工作区。 使用工作区，你可以将相关的应用程序（例如，按作业角色、任务或项目）组合在一起，并将其显示在单独的选项卡上。工作区实质上是对用户可以访问的应用程序应用筛选器，因此用户只会看到工作区中已分配给他们的那些应用程序。

> [!NOTE]
> 本文介绍管理员如何启用和创建工作区。 有关如何使用 "我的应用" 门户和工作区的最终用户的信息，请参阅[访问和使用工作区](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces)。

## <a name="enable-my-apps-preview-features"></a>启用 My Apps 预览功能

1. 打开[**Azure 门户**](https://portal.azure.com/)并以 "用户管理员" 或 "全局管理员" 身份登录。

2. 请参阅**Azure Active Directory** > **用户设置**。

3. 在 "**用户功能预览**" 下，选择 "**管理用户功能预览设置**"。

4. 在 "**用户可以使用我的应用的预览功能**" 下，选择以下选项之一：
   * 已**选择**-启用特定组的预览功能。 使用 "**选择组**" 选项可以选择要为其启用预览功能的组。  
   * **所有**-启用所有用户的预览功能。

   ![用户预览功能](media/access-panel-workspaces/user-preview-features.png)

> [!NOTE]
> 若要打开 "我的应用" 门户，用户可以使用链接 `https://myapps.microsoft.com` 或组织的自定义链接，如 `https://myapps.microsoft.com/contoso.com`。 如果用户未重定向到 "我的应用" 预览版，用户应尝试 `https://myapplications.microsoft.com` 或 `https://myapplications.microsoft.com/contoso.com`。

## <a name="create-a-workspace"></a>创建工作区

若要创建工作区，必须有 Azure AD Premium P1 或 P2 许可证。

1. 打开[**Azure 门户**](https://portal.azure.com/)并以具有 Azure AD Premium P1 或 P2 许可证的管理员身份登录。

2. 中转到**Azure Active Directory** > **企业应用程序**。

3. 在 "**管理**" 下，选择 "**工作区（预览）** "。

4. 选择 "**新建工作区**"。 在 "**新建工作区**" 页上，输入工作区的**名称**（建议不要在名称中使用 "工作区"。 然后输入**说明**。

   ![创建新的工作区](media/access-panel-workspaces/new-workspace.png)

5. 选择“查看 + 创建”。 新工作区的属性随即出现。

6. 选择 "**应用程序**" 选项卡。在 "**添加应用程序**" 下，选择要添加到工作区的所有应用程序，或使用**搜索**框查找应用程序。 

   ![向工作区添加应用程序](media/access-panel-workspaces/add-applications.png)

7. 选择 **添加** 。 此时将显示所选应用程序的列表。 您可以使用向上键和向下键来更改列表中应用程序的顺序。

   ![工作区中的应用程序列表](media/access-panel-workspaces/add-applications-list.png)

8. 选择 "**用户和组**" 选项卡。若要添加用户或组，请选择 "**添加用户**"。 

9. 在 "**选择成员**" 页上，选择要将工作区分配到的用户或组。 或使用 "**搜索**" 框查找用户或组。

   ![将用户和组分配到工作区](media/access-panel-workspaces/add-users-and-groups.png)

10. 选择完用户和组后，选择 "**选择**"。

11. 若要将用户的角色从 "**读取" 访问权限**更改为 "**所有者**" 或 "反之亦然"，请单击 "当前角色"，然后选择新角色。

    ![向用户和组分配角色](media/access-panel-workspaces/users-groups-list-role.png)

## <a name="view-audit-logs"></a>查看审核日志

审核日志记录我的应用工作区操作，包括工作区创建最终用户操作。 以下事件是从我的应用生成的：

* 创建工作区
* 编辑工作区
* 删除工作区
* 启动应用程序（最终用户）
* 自助应用程序添加（最终用户）
* 自助服务应用程序删除（最终用户）

可以通过在 "活动" 部分中选择 " **Azure Active Directory** > **企业应用程序** > **审核日志**" 来访问[Azure 门户](https://portal.azure.com)中的审核日志。 对于 "**服务**"，选择 **"我的应用**"。

   ![向用户和组分配角色](media/access-panel-workspaces/audit-log-myapps.png)


## <a name="next-steps"></a>后续步骤
[Azure Active Directory 中的应用程序的最终用户体验](end-user-experiences.md)