---
title: 使用 Azure CLI 管理 Azure 资源管理器组 |Microsoft Docs
description: 使用 Azure CLI 通过 Azure 资源管理器管理资源组。 说明如何创建、列出和删除资源组。
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: d6a6ea0425bc9dd08e7eedb520b4fa1334ad242f
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390428"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>使用 Azure CLI 管理 Azure 资源管理器资源组

了解如何使用[azure 资源管理器](resource-group-overview.md)Azure CLI 来管理 azure 资源组。 若要管理 Azure 资源，请参阅[使用 Azure CLI 管理 azure 资源](./manage-resources-cli.md)。

有关管理资源组的其他文章：

- [使用 Azure 门户管理 Azure 资源组](./manage-resources-portal.md)
- [使用 Azure PowerShell 管理 Azure 资源组](./manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>什么是资源组

资源组是用于保存 Azure 解决方案相关资源的容器。 资源组可以包含解决方案的所有资源，也可以只包含想要作为组来管理的资源。 根据对组织有利的原则，决定如何将资源分配到资源组。 通常可将共享相同生命周期的资源添加到同一资源组，以便将其作为一个组轻松部署、更新和删除。

资源组存储与资源有关的元数据。 因此，当指定资源组的位置时，也就指定了元数据的存储位置。 出于合规性原因，可能需要确保数据存储在某一特定区域。

资源组存储与资源有关的元数据。 当指定资源组的位置时，也就指定了元数据的存储位置。

## <a name="create-resource-groups"></a>创建资源组

下面的 CLI 脚本创建一个资源组，然后显示该资源组。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>列出资源组

以下 CLI 脚本列出了你的订阅下的资源组。

```azurecli-interactive
az group list
```

获取一个资源组：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>删除资源组

以下 CLI 脚本删除资源组：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

有关资源管理器 Azure 如何对资源删除进行排序的详细信息，请参阅[azure 资源管理器资源组删除](./resource-group-delete.md)。

## <a name="deploy-resources-to-an-existing-resource-group"></a>将资源部署到现有资源组

请参阅[将资源部署到现有资源组](./manage-resources-cli.md#deploy-resources-to-an-existing-resource-group)。

## <a name="deploy-a-resource-group-and-resources"></a>部署资源组和资源

可以使用资源管理器模板创建资源组，并将资源部署到组。 有关详细信息，请参阅[创建资源组并部署资源](./deploy-to-subscription.md#resource-group-and-resources)。

## <a name="redeploy-when-deployment-fails"></a>部署失败时，重新部署

此功能也称为 *"出错时回滚"* 。 有关详细信息，请参阅[部署失败时重新部署](./rollback-on-error.md)。

## <a name="move-to-another-resource-group-or-subscription"></a>移动到另一个资源组或订阅

可以将组中的资源移动到另一个资源组。 有关详细信息，请参阅[移动资源](./manage-resources-cli.md#move-resources)。

## <a name="lock-resource-groups"></a>锁定资源组

锁定可防止组织中的其他用户意外删除或修改关键资源，如 Azure 订阅、资源组或资源。 

以下脚本将锁定资源组，以便无法删除资源组。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

以下脚本获取资源组的所有锁：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

以下脚本将删除锁定：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

有关详细信息，请参阅 [使用 Azure 资源管理器锁定资源](resource-group-lock-resources.md)。

## <a name="tag-resource-groups"></a>标记资源组

可以将标记应用到资源组和资源，以按照逻辑组织资产。 有关信息，请参阅[使用标记来组织 Azure 资源](./resource-group-using-tags.md#azure-cli)。

## <a name="export-resource-groups-to-templates"></a>将资源组导出到模板

成功设置资源组后，你可能想要查看资源组的资源管理器模板。 导出模板有两个好处：

- 由于模板包含所有完整的基础结构，因此将来会自动完成解决方案的部署。
- 通过查看代表您的解决方案的 JavaScript 对象表示法（JSON）来了解模板语法。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

该脚本在控制台上显示该模板。  复制 JSON，并将其另存为文件。

有关详细信息，请参阅[Azure 门户中的单个和多个资源导出到模板](./export-template-portal.md)。

## <a name="manage-access-to-resource-groups"></a>管理对资源组的访问权限

可以在 Azure 中通过[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 这种方式管理对资源的访问。 有关详细信息，请参阅[使用 RBAC 和 Azure CLI 管理访问权限](../role-based-access-control/role-assignments-cli.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解 Azure 资源管理器，请参阅[azure 资源管理器概述](./resource-group-overview.md)。
- 若要了解资源管理器模板语法，请参阅[了解 Azure 资源管理器模板的结构和语法](./resource-group-authoring-templates.md)。
- 若要了解如何开发模板，请参阅[分步教程](/azure/azure-resource-manager/)。
- 若要查看 Azure 资源管理器模板架构，请参阅[模板参考](/azure/templates/)。