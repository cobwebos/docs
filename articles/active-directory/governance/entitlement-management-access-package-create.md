---
title: 在 Azure AD 权限管理 (预览版) 中创建新的访问包-Azure Active Directory
description: 了解如何在 Azure Active Directory 授权管理 (预览版) 中创建要共享的新资源访问包。
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
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f033cf57c5a285e94372728677c91e021065fa9
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678191"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 权限管理 (预览版) 中创建新的访问包

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用访问包可以对访问包的生命周期自动管理访问权限的资源和策略进行一次性设置。 本文介绍如何创建新的访问包。

## <a name="overview"></a>概述

所有访问包都必须放入名为目录的容器中。 目录定义可以添加到访问包的资源。 如果未指定目录, 则访问包将被放入 "常规" 目录中。 目前不能将现有的访问包移动到不同的目录。

所有访问包都必须至少有一个策略。 策略指定谁可以请求访问包以及批准和过期设置。 当你创建新的访问包时, 你可以为目录中的用户创建一个初始策略, 对于不在你的目录中的用户, 仅针对管理员直接分配, 或者你可以选择在以后创建该策略。

下图显示了创建新访问包的高级过程。

![创建访问包进程](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>启动新的访问包

**必备角色：** 全局管理员、用户管理员或目录所有者

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击“访问包”。

    ![Azure 门户中的权利管理](./media/entitlement-management-shared/elm-access-packages.png)

1. 单击“新建访问包”。

## <a name="basics"></a>基本

在 "**基本**信息" 选项卡上, 为访问包指定一个名称, 并指定要在其中创建访问包的目录。

1. 为访问包输入 "显示名称" 和 "说明"。 用户提交访问包的请求时, 会看到此信息。

1. 在 "**目录**" 下拉列表中, 选择要在其中创建访问包的目录。 例如, 你可能有一个目录所有者, 该所有者管理可请求的所有营销资源。 在这种情况下, 您可以选择 "营销" 目录。

    你将只能看到你有权在中创建访问包的目录。 若要在现有目录中创建访问包, 你必须至少是全局管理员、用户管理员、该目录中的目录所有者或该目录中的访问程序包管理器。

    ![访问包-基础](./media/entitlement-management-access-package-create/basics.png)

    如果你是全局管理员或用户管理员, 并且想要在未列出的新目录中创建你的访问包, 请单击 "**新建**"。 输入目录名称和说明, 然后单击 "**创建**"。

    你正在创建的访问包和其中包含的任何资源将添加到新目录中。 稍后, 还可以添加其他目录所有者。


1. 单击“下一步”。

## <a name="resource-roles"></a>资源角色

在 "**资源角色**" 选项卡上, 选择要包括在访问包中的资源。  请求和接收访问包的用户将接收访问包中的所有资源角色。

1. 单击要添加的资源类型 ("**组**"、"**应用程序**" 或 " **SharePoint 网站**")。

1. 在出现的 "选择" 窗格中, 从列表中选择一个或多个资源。

    ![访问包-资源角色](./media/entitlement-management-access-package-create/resource-roles.png)

    如果要在 "常规目录" 或新目录中创建访问包, 则可以从拥有的目录中选择任何资源。 至少必须是全局管理员、用户管理员或目录创建者。

    如果要在现有目录中创建访问包, 可以选择目录中已有的任何资源, 而无需拥有该目录。

    如果你是全局管理员、用户管理员或目录所有者, 则可以选择其他选项来选择你拥有但尚未包含在目录中的资源。 如果选择当前不在所选目录中的资源, 则还会将这些资源添加到目录中, 以便其他目录管理员使用生成访问包。 如果你只想要选择当前在所选目录中的资源, 请选中 "**仅查看**" "选择平移" 顶部的复选框。

1. 选择资源后, 请在 "**角色**" 列表中选择要将用户分配给该资源的角色。

    ![访问包-资源角色选择](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. 单击“下一步”。

## <a name="policy"></a>策略

在 "**策略**" 选项卡上, 您可以创建第一个策略来指定谁可以请求访问包以及批准和过期设置。 稍后, 你可以创建更多的策略, 以允许其他用户组使用其自己的批准和过期设置来请求访问包。 你还可以选择稍后创建该策略。

1. 将**创建第一个策略**切换到**现在**或**更高版本**。

    ![访问包-策略](./media/entitlement-management-access-package-create/policy.png)

1. 如果你选择 "**以后**", 请跳到 "[查看 + 创建](#review--create)" 部分以创建你的访问包。

1. 如果选择 "**现在**", 请执行以下策略部分之一中的步骤。

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>查看 + 创建

在 "**查看**" 和 "创建" 选项卡上, 可以查看设置并检查是否存在任何验证错误。

1. 查看访问包的设置

    ![访问包-策略-启用策略设置](./media/entitlement-management-access-package-create/review-create.png)

1. 单击 "**创建**" 以创建访问包。

    新访问包将显示在访问包列表中。

## <a name="next-steps"></a>后续步骤

- [编辑和管理现有访问包](entitlement-management-access-package-edit.md)
- [添加目录所有者或访问包管理器](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [创建和管理目录](entitlement-management-catalog-create.md)
