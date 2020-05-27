---
title: include 文件
description: include 文件
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/19/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a00291182059506aeab9cde965fa4cbd5177ecf7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132136"
---
如果用户没有应用标记所需的访问权限，则可以将**标记参与者**角色分配给用户。 有关详细信息，请参阅[教程：使用 RBAC 和 Azure 门户向用户授予对 Azure 资源的访问权限](../articles/role-based-access-control/quickstart-assign-role-user-portal.md)。

1. 若要查看资源或资源组的标记，请在概述中查找现有标记。 如果以前未应用标记，则该列表为空。

   ![查看资源或资源组的标记](./media/resource-manager-tag-resources/view-tags.png)

1. 若要添加标记，请选择“单击此处以添加标记”****。

1. 提供名称和值。

   ![添加标记](./media/resource-manager-tag-resources/add-tag.png)

1. 根据需要继续添加标记。 完成后，选择“保存”****。

   ![保存标记](./media/resource-manager-tag-resources/save-tags.png)

1. 标记现已显示在概述中。

   ![显示标记](./media/resource-manager-tag-resources/view-new-tags.png)

1. 若要添加或删除标记，请选择“更改”****。

1. 若要删除标记，请选择回收站图标。 然后选择 "**保存**"。

   ![删除标记](./media/resource-manager-tag-resources/delete-tag.png)

将标记批量分配到多个资源：

1. 从任意资源列表中，选中想要分配标记的资源的复选框。 然后选择 "**分配标记**"。

   ![选择多个资源](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. 添加名称和值。 完成后，选择“保存”****。

   ![选择“分配”](./media/resource-manager-tag-resources/select-assign.png)

查看所有具有标记的资源：

1. 在 "Azure 门户" 菜单上，搜索 "**标记**"。 从可用选项中选择它。

   ![按标记查找](./media/resource-manager-tag-resources/find-tags-general.png)

1. 选择用于查看资源的标记。

   ![选择标记](./media/resource-manager-tag-resources/select-tag.png)

1. 此时会显示所有具有该标记的资源。

   ![按标记查看资源](./media/resource-manager-tag-resources/view-resources-by-tag.png)
