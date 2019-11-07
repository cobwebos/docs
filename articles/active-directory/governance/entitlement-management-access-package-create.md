---
title: 在 Azure AD 的权利管理中创建新的访问包-Azure Active Directory
description: 了解如何创建要在 Azure Active Directory 授权管理中共享的新资源访问包。
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
ms.openlocfilehash: 71aa999809ba3d3e32d38162dfaba869d9716031
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73602709"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 权限管理中创建新的访问包

使用访问包可以一次性设置好用于在访问包的生命周期内自动管理访问权限的资源和策略。 本文介绍如何创建新的访问包。

## <a name="overview"></a>概述

所有访问包必须放入称作“目录”的容器中。 目录定义可将哪些资源添加到访问包。 如果未指定目录，则访问包将会放入常规目录。 目前，无法将现有的访问包移到其他目录。

如果你是访问包管理器，则无法向目录中添加你拥有的资源。 您只能使用目录中提供的资源。 如果需要将资源添加到目录，则可以询问目录所有者。

所有访问包都必须至少有一个策略。 策略指定谁可以请求访问包以及审批和生命周期设置。 创建新访问包时，可为目录中的用户、不在目录中的用户以及（仅限）管理员直接分配创建一个初始策略，或者，可以选择在以后创建策略。

![创建访问包](./media/entitlement-management-access-package-create/access-package-create.png)

下面是创建新访问包的概要步骤。

1. 在标识管理中，启动创建新访问包的过程。

1. 选择要在其中创建访问包的目录。

1. 将目录中的资源添加到 access 包。

1. 为每个资源分配资源角色。

1. 指定可请求访问的用户。

1. 指定任何审批设置。

1. 指定生命周期设置。

## <a name="start-new-access-package"></a>启动新访问包

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理器

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击“访问包”。

1. 单击“新建访问包”。
   
    ![Azure 门户中的权利管理](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>基础知识

在“基本信息”选项卡上指定访问包的名称，并指定要在哪个目录中创建该访问包。

1. 输入访问包的显示名称和说明。 当用户提交访问包请求时，会看到此信息。

1. 在“目录”下拉列表中，选择要在其中创建访问包的目录。 例如，你的某个目录所有者需要管理所有可请求的营销资源。 在这种情况下，你可以选择营销目录。

    你只会看到你有权在中创建访问包的目录。 若要在现有目录中创建访问包，你必须是全局管理员或用户管理员，或者必须是该目录中的目录所有者或访问包管理器。

    ![访问包 - 基本信息](./media/entitlement-management-access-package-create/basics.png)

    如果你是全局管理员、用户管理员或目录创建者，并且你想要在未列出的新目录中创建你的访问包，请单击 "**创建新目录**"。 输入目录的名称和说明，然后单击“创建”。

    正在创建的访问包及其包含的所有资源将添加到新目录中。 也可以稍后添加其他目录所有者。

1. 单击“下一步”。

## <a name="resource-roles"></a>资源角色

在“资源角色”选项卡上，选择要包含在访问包中的资源。 请求并接收访问包的用户将接收访问包中的所有资源角色。

1. 单击要添加的资源类型（**组和团队**、**应用程序**或**SharePoint 站点**）。

1. 在出现的“选择”窗格中，从列表中选择一个或多个资源。

    ![访问包 - 资源角色](./media/entitlement-management-access-package-create/resource-roles.png)

    如果在常规目录或新目录中创建访问包，则可以从你拥有的目录中选择任何资源。 至少必须是全局管理员、用户管理员或目录创建者。

    如果在现有目录中创建访问包，则可以选择该目录中现有的任何资源，而无需拥有该目录。

    如果你是全局管理员、用户管理员或目录所有者，你可以选择其他选项来选择你拥有但尚未包含在目录中的资源。 如果选择当前不在所选目录中的资源，则这些资源也会添加到该目录，供其他目录管理员用来生成访问包。 如果只想选择当前在所选目录中的资源，请选中 "仅在选择窗格顶部**显示**" 复选框。

1. 选择资源后，在“角色”列表中选择要将用户分配到的资源角色。

    ![访问包 - 资源角色选择](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. 单击“下一步”。

## <a name="requests"></a>请求

在 "**请求**" 选项卡上，可以创建第一个策略来指定谁可以请求访问包和审批设置。 稍后，你可以创建更多的请求策略，以允许其他用户组使用其自己的审批设置来请求访问包。

![访问包-请求选项卡](./media/entitlement-management-access-package-create/requests.png)

根据你希望能够请求此访问包的人员，请执行以下部分之一中的步骤。

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>查看 + 创建

在“查看 + 创建”选项卡上，可以查看设置并检查是否存在任何验证错误。

1. 查看访问包的设置

    ![访问包 - 策略 - 启用策略设置](./media/entitlement-management-access-package-create/review-create.png)

1. 单击“创建”以创建访问包。

    新访问包将显示在访问包列表中。

## <a name="next-steps"></a>后续步骤

- [共享用于请求访问包的链接](entitlement-management-access-package-settings.md)
- [更改访问包的资源角色](entitlement-management-access-package-resources.md)
