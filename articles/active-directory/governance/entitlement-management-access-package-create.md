---
title: 在 Azure AD 授权管理 （预览版）-Azure Active Directory 中创建新的访问包
description: 了解如何创建你想要在 Azure Active Directory 权利管理 （预览版） 中共享的资源的新访问包。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/24/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ad6570a3f30e40e4074502a8ce85bf739f58d3f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866420"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 授权管理 （预览版） 中创建新的访问包

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 授权管理当前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

访问包便可以执行一次性的安装程序的资源和策略自动管理生命周期内访问包的访问权限。 本文介绍如何创建新的访问包。

## <a name="overview"></a>概述

所有访问包必须都置于名为目录的容器。 目录定义可以向您访问包添加哪些资源。 如果未指定目录，则访问包将放入常规目录。 目前，不能将现有访问包移到不同的目录。

所有访问包都必须至少一个策略。 策略指定谁可以请求访问包以及批准和过期时间设置。 创建新的访问包时，可以在目录中，为不在目录中，仅管理员直接分配的用户创建初始策略为用户或您可以选择更高版本创建的策略。

下图显示了以创建新的访问包的高级别过程。

![创建访问包过程](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>启动新访问包

**必备角色：** 用户管理员或目录所有者

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 单击**Azure Active Directory** ，然后单击**标识监管**。

1. 在左侧菜单中，单击**访问包**。

    ![在 Azure 门户中的权利管理](./media/entitlement-management-shared/elm-access-packages.png)

1. 单击**新访问包**。

## <a name="basics"></a>基础

上**基础知识**选项卡上，为访问包提供一个名称并指定哪些目录创建在访问包。

1. 输入的显示名称和访问包的说明。 在提交的请求访问包时，用户将看到此信息。

1. 在中**目录**下拉列表中，选择你想要创建访问该目录包中。 例如，可以管理所有可以请求的市场营销资源的目录所有者。 在这种情况下，可以选择市场营销的目录。

    您将只看到目录有权创建访问包中的。 若要在现有的目录中创建访问包，您必须至少为一个用户管理员、 目录所有者或访问包管理器。

    ![访问包的基础知识](./media/entitlement-management-access-package-create/basics.png)

    如果你想要访问包创建新目录中，单击**新建**。 输入目录名称和描述，然后单击**创建**。

    你要创建访问包，并且包括在其中的任何资源将添加到新目录。 此外，你将自动成为目录的第一个所有者。 您可以添加其他目录所有者。

    若要创建一个新的目录，您必须至少是用户管理员或目录创建者。

1. 单击“下一步”。

## <a name="resource-roles"></a>资源角色

上**资源角色**选项卡上，选择要包含在访问包中的资源。

1. 单击你想要添加的资源类型 (**组**，**应用程序**，或**SharePoint 站点**)。

1. 在选择窗格中显示，从列表中选择一个或多个资源。

    ![访问包-资源角色](./media/entitlement-management-access-package-create/resource-roles.png)

    如果要在常规目录或一个新的目录中创建访问包，您将能够读取你拥有的目录中的任何资源。 您必须是至少一个用户管理员或目录创建者。

    如果要在现有的目录中创建访问包，可以选择任何资源，而无需拥有它已在目录中。

    如果您是用户管理员或目录的所有者，必须选择目录中尚未包括的资源所拥有其他的选项。 如果所选目录中的当前未选择资源，这些资源还将添加到其他目录管理员可以生成具有访问包目录中。 如果只想要选择当前在所选的目录中，检查的资源**只会看到**选择平移顶部的复选框。

1. 在选择资源之后,**角色**列表中，选择想要分配的用户资源的角色。

    ![访问包-资源角色选择](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. 单击“下一步”。

## <a name="policy"></a>策略

上**策略**选项卡上，创建第一个策略指定谁可以请求访问包以及批准和过期时间设置。 更高版本，可以创建多个策略，以允许用户使用其自己的审批和过期设置请求访问包的其他组。 您还可以选择更高版本创建策略。

1. 设置**创建第一个策略**切换到**现在**或**更高版本**。

    ![访问包的策略](./media/entitlement-management-access-package-create/policy.png)

1. 如果选择**更高版本**，向下跳过[查看 + 创建](#review--create)部分，以创建访问包。

1. 如果选择**现在**，执行下列策略部分之一中的步骤。

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>查看 + 创建

上**查看 + 创建**选项卡上，你可以查看您的设置和检查是否有任何验证错误。

1. 查看访问包的设置

    ![访问包的支持策略的策略设置](./media/entitlement-management-access-package-create/review-create.png)

1. 单击**创建**创建访问包。

    访问包的列表中将显示新的访问包。

## <a name="next-steps"></a>后续步骤

- [编辑和管理现有访问包](entitlement-management-access-package-edit.md)
- [创建和管理目录](entitlement-management-catalog-create.md)
