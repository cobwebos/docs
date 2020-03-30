---
title: 在授权管理中创建新的访问包 - Azure AD
description: 了解如何在 Azure 活动目录授权管理中创建新的资源访问包。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c858a17d4574e6e45283df7c1276cd303f25297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262004"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 授权管理中创建新的访问包

使用访问包可以一次性设置好用于在访问包的生命周期内自动管理访问权限的资源和策略。 本文介绍如何创建新的访问包。

## <a name="overview"></a>概述

所有访问包必须放入称作“目录”的容器中。 目录定义可将哪些资源添加到访问包。 如果未指定目录，则访问包将会放入常规目录。 目前，无法将现有的访问包移到其他目录。

如果您是访问包管理器，则无法将您拥有的资源添加到目录中。 您仅限于使用目录中可用的资源。 如果需要将资源添加到目录，可以询问目录所有者。

所有访问包都必须至少有一个策略。 策略指定谁可以请求访问包以及审批和生命周期设置。 创建新访问包时，可为目录中的用户、不在目录中的用户以及（仅限）管理员直接分配创建一个初始策略，或者，可以选择在以后创建策略。

![创建访问包](./media/entitlement-management-access-package-create/access-package-create.png)

下面是创建新访问包的高级步骤。

1. 在标识治理中，启动进程以创建新的访问包。

1. 选择要在其中创建访问包的目录。

1. 将资源从目录添加到访问包。

1. 为每个资源分配资源角色。

1. 指定可以请求访问权限的用户。

1. 指定任何审批设置。

1. 指定生命周期设置。

## <a name="start-new-access-package"></a>启动新访问包

**先决条件角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 登录到 Azure[门户](https://portal.azure.com)。

1. 依次单击“Azure Active Directory”、“标识监管”。********

1. 在左侧菜单中，单击“访问包”。****

1. 单击“新建访问包”。****
   
    ![Azure 门户中的权利管理](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>基础

在“基本信息”选项卡上指定访问包的名称，并指定要在哪个目录中创建该访问包。****

1. 输入访问包的显示名称和说明。 当用户提交访问包请求时，会看到此信息。

1. 在“目录”下拉列表中，选择要在其中创建访问包的目录。**** 例如，你的某个目录所有者需要管理所有可请求的营销资源。 在这种情况下，你可以选择营销目录。

    你只会看到你有权在中创建访问包的目录。 要在现有目录中创建访问包，您必须是全局管理员或用户管理员，或者必须是该目录中的目录所有者或访问包管理器。

    ![访问包 - 基本信息](./media/entitlement-management-access-package-create/basics.png)

    如果您是全局管理员、用户管理员或目录创建者，并且希望在新目录中创建未列出的访问包，请单击"**创建新目录**"。 输入目录的名称和说明，然后单击“创建”。****

    正在创建的访问包及其包含的所有资源将添加到新目录中。 也可以稍后添加其他目录所有者。

1. 单击“下一步”****。

## <a name="resource-roles"></a>资源角色

在“资源角色”选项卡上，选择要包含在访问包中的资源。**** 请求并接收访问包的用户将接收访问包中的所有资源角色。

1. 单击要添加的资源类型（**组和团队**、**应用程序**或**SharePoint 网站**）。

1. 在出现的“选择”窗格中，从列表中选择一个或多个资源。

    ![访问包 - 资源角色](./media/entitlement-management-access-package-create/resource-roles.png)

    如果在常规目录或新目录中创建访问包，则可以从你拥有的目录中选择任何资源。 您必须至少是全局管理员、用户管理员或目录创建者。

    如果在现有目录中创建访问包，则可以选择该目录中现有的任何资源，而无需拥有该目录。

    如果您是全局管理员、用户管理员或目录所有者，则可以选择目录中尚未拥有的资源的其他选项。 如果选择当前不在所选目录中的资源，则这些资源也会添加到该目录，供其他目录管理员用来生成访问包。 如果只想选择当前位于所选目录中的资源，请在"选择"窗格顶部选中"**仅看到"** 复选框。

1. 选择资源后，在“角色”列表中选择要将用户分配到的资源角色。****

    ![访问包 - 资源角色选择](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. 单击“下一步”****。

## <a name="requests"></a>Requests

在"**请求"** 选项卡上，您将创建第一个策略来指定谁可以请求访问包以及审批设置。 稍后，您可以创建更多请求策略，以允许其他用户组使用自己的审批设置请求访问包。

![访问包 - 请求选项卡](./media/entitlement-management-access-package-create/requests.png)

根据您希望向谁请求此访问包，请在以下部分之一执行这些步骤。

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>查看 + 创建

在“查看 + 创建”选项卡上，可以查看设置并检查是否存在任何验证错误。****

1. 查看访问包的设置

    ![访问包 - 策略 - 启用策略设置](./media/entitlement-management-access-package-create/review-create.png)

1. 单击“创建”以创建访问包。****

    新访问包将显示在访问包列表中。

## <a name="creating-an-access-package-programmatically"></a>以编程方式创建访问包

您还可以使用 Microsoft 图形创建访问包。  具有已委派`EntitlementManagement.ReadWrite.All`权限的应用程序具有适当角色的用户可以将 API 调用

1. [在目录中列出访问包资源](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresources?view=graph-rest-beta)，并为目录中尚未存在的任何资源[创建访问包资源请求](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta)。
1. 在访问包目录中列出每个访问包资源的访问[包资源角色](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresourceroles?view=graph-rest-beta)。 然后，在随后创建访问包资源功能范围时，此角色列表将用于选择角色。
1. [创建访问包](https://docs.microsoft.com/graph/api/accesspackage-post?view=graph-rest-beta)。
1. [创建访问包分配策略](https://docs.microsoft.com/graph/api/accesspackageassignmentpolicy-post?view=graph-rest-beta)。
1. 为访问包中所需的每个资源角色[创建访问包资源功能范围](https://docs.microsoft.com/graph/api/accesspackage-post-accesspackageresourcerolescopes?view=graph-rest-beta)。

## <a name="next-steps"></a>后续步骤

- [共享链接以请求访问包](entitlement-management-access-package-settings.md)
- [更改访问包的资源角色](entitlement-management-access-package-resources.md)
- [直接将用户分配给访问包](entitlement-management-access-package-assignments.md)
