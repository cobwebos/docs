---
title: 使用 Azure 门户管理 Azure 资源 |Microsoft Docs
description: 使用 Azure 门户和 Azure 资源管理来管理资源。
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 20bf38b87ce29f8506a5611ecd25cf38f6d4ed61
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56824982"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>使用 Azure 门户管理 Azure 资源

了解如何使用[Azure 门户](https://portal.azure.com)与[Azure 资源管理器](resource-group-overview.md)来管理 Azure 资源。 管理资源组，请参阅[使用 Azure 门户管理 Azure 资源组](./manage-resource-groups-portal.md)。

有关管理资源的其他文章：

- [使用 Azure CLI 管理 Azure 资源](./manage-resources-cli.md)
- [使用 Azure PowerShell 管理 Azure 资源](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>将资源部署到资源组

创建 Resource Manager 模板后，你可以使用 Azure 门户部署 Azure 资源。 有关创建模板，请参阅[快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](./resource-manager-quickstart-create-templates-use-the-portal.md)。 用于部署模板使用门户，请参阅[使用资源管理器模板和 Azure 门户部署资源](resource-group-template-deploy-portal.md)。

## <a name="open-resources"></a>打开资源

Azure 资源进行组织通过 Azure 服务和资源组。 以下过程显示如何打开一个名为存储帐户**mystorage0207**。 虚拟机驻留在名为的资源组**mystorage0207rg**。

若要打开的服务类型的资源：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左窗格中，选择 Azure 服务。 在这种情况下，**存储帐户**。  如果没有看到列出的服务，选择**所有服务**，然后选择服务类型。

    ![在门户中打开 azure 资源](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. 选择你想要打开的资源。

    ![在门户中打开 azure 资源](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    存储帐户如下所示：

    ![在门户中打开 azure 资源](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

若要打开的资源的资源组：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左窗格中，选择**资源组**若要列出组中的资源。
3. 选择你想要打开的资源。 

## <a name="manage-resources"></a>管理资源

在门户中查看某一资源时，可以看到用于管理该特定资源的选项。

![管理 Azure 资源](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

屏幕截图显示了 Azure 虚拟机的管理选项。 您可以执行操作，如启动、 重新启动和停止虚拟机。

## <a name="delete-resources"></a>删除资源

1. 在门户中打开该资源。 有关步骤，请参阅[打开资源](#open-resources)。
2. 选择“删除”。 以下屏幕截图显示了虚拟机的管理选项。

    ![删除 azure 资源](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. 键入以确认删除，然后选择的资源名称**删除**。

有关 Azure 资源管理器如何排序的资源删除操作的详细信息，请参阅[Azure 资源管理器资源组删除](./resource-group-delete.md)。

## <a name="move-resources"></a>移动资源

1. 在门户中打开该资源。 有关步骤，请参阅[打开资源](#open-resources)。
2. 选择“移动”。 以下屏幕截图显示了存储帐户的管理选项。

    ![azure 移动资源](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. 选择**移到另一个资源组**或**到另一个订阅 Moeve**具体取决于你的需求。

有关详细信息，请参阅[将资源移到新资源组或订阅](resource-group-move-resources.md)。

## <a name="lock-resources"></a>锁定资源

锁定可以防止意外删除或修改关键资源，如 Azure 订阅、 资源组或资源在组织中的其他用户。 

1. 在门户中打开该资源。 有关步骤，请参阅[打开资源](#open-resources)。
2. 选择**锁定**。 以下屏幕截图显示了存储帐户的管理选项。

    ![锁定 azure 资源](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. 选择**添加**，然后指定锁属性。

有关详细信息，请参阅 [使用 Azure 资源管理器锁定资源](resource-group-lock-resources.md)。

## <a name="tag-resources"></a>标记资源

标记可帮助以逻辑方式组织资源组和资源。 

1. 在门户中打开该资源。 有关步骤，请参阅[打开资源](#open-resources)。
2. 选择**标记**。 以下屏幕截图显示了存储帐户的管理选项。

    ![标记 azure 资源](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. 指定的标记属性，并选择**保存**。

有关信息，请参阅[使用标记来组织 Azure 资源](./resource-group-using-tags.md#portal)。

## <a name="monitor-resources"></a>监视资源

打开资源时，门户会显示默认图形和表用于监视该资源类型。 下面的屏幕截图显示了图形方式针对虚拟机：

![监视 azure 资源](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

可以选择要固定到仪表板关系图的图右上角上的图钉图标。 若要了解如何使用仪表板，请参阅[在 Azure 门户中创建和共享仪表板](../azure-portal/azure-portal-dashboards.md)。

## <a name="manage-access-to-resources"></a>管理对资源的访问权限

可以在 Azure 中通过[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 这种方式管理对资源的访问。 有关详细信息，请参阅[使用 RBAC 和 Azure 门户管理访问权限](../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解 Azure 资源管理器，请参阅[Azure 资源管理器概述](./resource-group-overview.md)。
- 若要了解资源管理器模板语法，请参阅[了解结构和 Azure 资源管理器模板的语法](./resource-group-authoring-templates.md)。
- 若要了解如何开发模板，请参阅[分步教程](/azure/azure-resource-manager/)。
- 若要查看 Azure 资源管理器模板的架构，请参阅[模板引用](/azure/templates/)。