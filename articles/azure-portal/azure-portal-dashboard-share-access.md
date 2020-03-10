---
title: 使用基于角色的访问控制共享 Azure 门户仪表板
description: 本文介绍如何通过使用基于角色的访问控制在 Azure 门户中共享仪表板。
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/29/2020
ms.author: mblythe
ms.openlocfilehash: e8d251cef9e67cb8fc0c11df8ce546383f75a679
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389710"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>通过使用基于角色的访问控制来共享 Azure 仪表板

配置仪表板后，可将其发布，并与组织中的其他用户共享。 你允许其他人使用 Azure[基于角色的访问控制](../role-based-access-control/role-assignments-portal.md)（RBAC）查看仪表板。 将用户或用户组分配给角色。 该角色定义这些用户是否可以查看或修改已发布的仪表板。

所有已发布的仪表板都作为 Azure 资源实现。 它们作为订阅中的可管理项存在，并包含在资源组中。 从访问控制角度来看，仪表板与其他资源（例如虚拟机或存储帐户）没有什么不同。

> [!TIP]
> 仪表板中的各个磁贴会根据其显示的资源强制实施自身的访问控制要求。 您可以广泛地共享仪表板，同时保护各个磁贴上的数据。
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>了解仪表板的访问控制

使用基于角色的访问控制 (RBAC)，可以将用户分配到处于三个不同范围级别的角色：

* 订阅
* 资源组
* resource

分配的权限将从订阅向下继承到资源。 已发布的仪表板是一个资源。 你可能已将用户分配到适用于已发布仪表板的订阅的角色。

假设已有 Azure 订阅，并且团队中的各个成员都分配了订阅的*所有者*、*参与者*或*读取者*角色。 作为所有者或参与者的用户可以在订阅中列出、查看、创建、修改或删除仪表板。 读者可以列出和查看仪表板，但不能修改或删除它们。 具有读取者访问权限的用户可以对已发布的仪表板进行本地编辑（例如在排查问题时），但不能将这些更改发布回服务器。 他们可以为自己制作仪表板的私有副本。

你还可以将包含多个仪表板或单个仪表板的资源组的权限分配给资源组。 例如，可以决定一组用户在整个订阅中应具有有限的权限，但对特定仪表板具有更高的访问权限。 将这些用户分配到该仪表板的角色。

## <a name="publish-dashboard"></a>发布仪表板

假设你配置了要与订阅中的一组用户共享的仪表板。 以下步骤演示如何将仪表板共享到名为存储管理器的组。 你可以根据自己的需要命名你的组。 有关详细信息，请参阅[Azure Active Directory 中的管理组](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

在分配访问权限之前，必须先发布仪表板。

1. 在仪表板中，选择“共享”。

    ![为仪表板选择共享](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. 在**共享和访问控制**中，选择 "**发布**"。

    ![发布仪表板](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     默认情况下，共享将仪表板发布到名为**仪表板**的资源组。

仪表板现已发布。 如果自订阅继承的权限合适，则无需执行任何其他操作。 你的组织中的其他用户可以根据其订阅级别角色访问和修改仪表板。

## <a name="assign-access-to-a-dashboard"></a>向仪表板分配访问权限

可以将一组用户分配给该仪表板的角色。

1. 发布仪表板后，在**共享和访问控制**中，选择 "**管理用户**"。

    ![管理仪表板的用户](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

    若要从仪表板访问**共享和访问控制**，请选择 "**共享** **" 或 "取消共享"** 选项。

1. 选择 "**角色分配**"，以查看已为其分配了此仪表板角色的现有用户。

1. 若要添加新用户或组，请选择“添加”。

    ![添加用户以访问仪表板](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. 选择表示要授予的权限的角色。 对于此示例，请选择“参与者”。

1. 选择要分配给角色的用户或组。 如果在列表中看不到要查找的用户或组，请使用 "搜索" 框。 可用组的列表取决于在 Active Directory 中创建的组。

1. 完成添加用户或组后，请选择“确定”。

    会将新分配添加到用户列表。 其**访问权限**列为**已分配**而不是**继承**。

    ![已分配的角色](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>后续步骤

* 有关角色的列表，请参阅[Azure 资源的内置角色](../role-based-access-control/built-in-roles.md)。
* 若要了解有关管理资源的信息，请参阅[使用 Azure 门户管理 Azure 资源](resource-group-portal.md)。

