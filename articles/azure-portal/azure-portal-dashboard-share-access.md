---
title: "通过使用 RBAC 共享 Azure 门户仪表板 | Microsoft 文档"
description: "本文介绍如何通过使用基于角色的访问控制在 Azure 门户中共享仪表板。"
services: azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: multiple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: tomfitz
ms.openlocfilehash: 50a965f9773524a36bedc3d040bf1a1ca391507f
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2017
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>通过使用基于角色的访问控制来共享 Azure 仪表板
配置仪表板后，可将其发布，并与组织中的其他用户共享。 通过使用 Azure [基于角色的访问控制](../active-directory/role-based-access-control-configure.md)，允许他人查看仪表板。 将一个用户或一组用户分配到某角色，该角色定义这些用户是否可以查看或修改已发布的仪表板。 

所有已发布的仪表板都实现为 Azure 资源，这意味着它们在订阅中作为可管理项存在，并且包含在资源组中。  从访问控制角度来看，仪表板与其他资源（例如虚拟机或存储帐户）没有什么不同。

> [!TIP]
> 仪表板中的各个磁贴会根据其显示的资源强制实施自身的访问控制要求。  因此，可以设计一个仪表板，该仪表板可供广泛共享，同时保护各个磁贴上的数据。
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>了解仪表板的访问控制
使用基于角色的访问控制 (RBAC)，可以将用户分配到处于三个不同范围级别的角色：

* 订阅
* 资源组
* resource

分配的权限从订阅向下继承到资源。 已发布的仪表板是一个资源。 因此，可能已将用户分配到订阅的角色，这些角色也适用于已发布的仪表板。 

下面是一个示例。  假设已有 Azure 订阅，并且团队中的各个成员都分配了订阅的**所有者**、**参与者**或**读取者**角色。 作为所有者或参与者的用户能够列出、查看、创建、修改或删除该订阅中的仪表板。  作为读取者的用户能够列出并查看仪表板，但不能修改或删除它们。  具有读取者访问权限的用户能够对已发布的仪表板进行本地编辑（例如排查问题时），但不能将这些更改发布回服务器。  他们可以为自己制作仪表板的私有副本

但是，也可以将权限分配给包含若干个仪表板的资源组或单个仪表板。 例如，可以决定一组用户在整个订阅中应具有有限的权限，但对特定仪表板具有更高的访问权限。 将这些用户分配给该仪表板的角色。 

## <a name="publish-dashboard"></a>发布仪表板
假设已配置完成要与订阅中的一组用户共享的仪表板。 下面的步骤描述了一个称为存储管理器的自定义组，但也可以将组命名为任何想要的名称。 有关创建 Active Directory 组并将用户添加到该组的详细信息，请参阅[在 Azure Active Directory 中管理组](../active-directory/active-directory-groups-create-azure-portal.md)。

1. 在仪表板中，选择“共享”。
   
     ![选择共享](./media/azure-portal-dashboard-share-access/select-share.png)
2. 在分配访问权限之前，必须先发布仪表板。 默认情况下，仪表板将发布到名为“仪表板”的资源组。 选择“发布”。
   
     ![发布](./media/azure-portal-dashboard-share-access/publish.png)

仪表板现已发布。 如果从订阅继承的权限合适，则不需要再执行任何操作。 组织中的其他用户能够根据其订阅级别角色访问和修改仪表板。 但是，在本教程中，我们将一组用户分配给该仪表板的角色。

## <a name="assign-access-to-a-dashboard"></a>向仪表板分配访问权限
1. 发布仪表板后，选择“管理用户”。
   
     ![管理用户](./media/azure-portal-dashboard-share-access/manage-users.png)
2. 会看到已为其分配了此仪表板角色的现有用户列表。 现有用户列表可能与下方图片有所不同。 分配很可能继承自订阅。 若要添加新用户或组，请选择“添加”。
   
     ![添加用户](./media/azure-portal-dashboard-share-access/existing-users.png)
3. 选择表示要授予的权限的角色。 对于此示例，请选择“参与者”。
   
     ![选择角色](./media/azure-portal-dashboard-share-access/select-role.png)
4. 选择要分配给角色的用户或组。 如果在列表中没有看到要查找的用户或组，请使用搜索框。 可用组列表取决于已在 Active Directory 中创建的组。
   
     ![选择用户](./media/azure-portal-dashboard-share-access/select-user.png) 
5. 完成添加用户或组后，请选择“确定”。 
6. 会将新分配添加到用户列表。 请注意，其“访问权限”将被列为“已分配”，而非“已继承”。
   
     ![已分配的角色](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>后续步骤
* 有关角色列表，请参阅 [RBAC：内置角色](../active-directory/role-based-access-built-in-roles.md)。
* 若要了解有关管理资源的信息，请参阅[通过门户管理 Azure 资源](resource-group-portal.md)。

