---
title: 使用 Azure PowerShell 管理 Azure 资源管理器组 |Microsoft Docs
description: 使用 Azure PowerShell 管理 Azure 资源管理器组。
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 3d6a102b794ca9c43e1dd18f923f6ce224596499
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296263"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>使用 Azure PowerShell 管理 Azure 资源管理器资源组

了解如何将 Azure PowerShell 与 [Azure 资源管理器](resource-group-overview.md)配合使用来管理 Azure 资源组。 若要管理 Azure 资源，请参阅[使用 Azure PowerShell 管理 Azure 资源](./manage-resources-powershell.md)。

有关资源组管理的其他文章：

- [使用 Azure 门户管理 Azure 资源组](./manage-resources-portal.md)
- [使用 Azure CLI 管理 Azure 资源组](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>什么是资源组

资源组是用于保存 Azure 解决方案相关资源的容器。 资源组可以包含解决方案的所有资源，也可以只包含想要作为组来管理的资源。 根据对组织有利的原则，决定如何将资源分配到资源组。 通常可将共享相同生命周期的资源添加到同一资源组，以便将其作为一个组轻松部署、更新和删除。

” 资源组存储有关资源的元数据。 因此，当指定资源组的位置时，也就指定了元数据的存储位置。 出于合规性原因，可能需要确保数据存储在某一特定区域。

” 资源组存储有关资源的元数据。 指定资源组的位置时，也会指定元数据的存储位置。

## <a name="create-resource-groups"></a>创建资源组

以下 PowerShell 脚本将创建一个资源组，然后显示该资源组。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>列出资源组

以下 PowerShell 脚本将列出订阅下的资源组。

```azurepowershell-interactive
Get-AzResourceGroup
```

若要获取某个资源组，请执行以下操作：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>删除资源组

以下 PowerShell 脚本删除某个资源组：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

若要详细了解 Azure 资源管理器如何管理资源的删除，请参阅 [Azure 资源管理器资源组的删除](./resource-group-delete.md)。

## <a name="deploy-resources-to-an-existing-resource-group"></a>将资源部署到现有的资源组

请参阅[将资源部署到现有的资源组](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group)。

若要验证资源组部署，请参阅 [Test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0)。

## <a name="deploy-a-resource-group-and-resources"></a>部署资源组和资源

可以使用资源管理器模板创建一个资源组并将资源部署到该组。 有关详细信息，请参阅[创建资源组并部署资源](./deploy-to-subscription.md#create-resource-group-and-deploy-resources)。

## <a name="redeploy-when-deployment-fails"></a>部署失败时，重新部署

此功能也称为“出错时回滚”  。 有关详细信息，请参阅[在部署失败时重新部署](./resource-group-template-deploy.md#redeploy-when-deployment-fails)。

## <a name="move-to-another-resource-group-or-subscription"></a>移到另一个资源组或订阅

可将组中的资源移到另一个资源组。 有关详细信息，请参阅[将资源移到新资源组或订阅](./resource-group-move-resources.md#move-resources)。

## <a name="lock-resource-groups"></a>锁定资源组

锁定可以防止组织中的其他用户意外删除或修改关键资源，例如 Azure 订阅、资源组或资源。 

以下脚本锁定了一个资源组，因此无法删除该资源组。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

以下脚本获取资源组的所有锁：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

有关详细信息，请参阅 [使用 Azure 资源管理器锁定资源](resource-group-lock-resources.md)。

## <a name="tag-resource-groups"></a>标记资源组

可以将标记应用到资源组和资源，以按照逻辑组织资产。 有关信息，请参阅[使用标记组织 Azure 资源](./resource-group-using-tags.md#powershell)。

## <a name="export-resource-groups-to-templates"></a>将资源组导出到模板

设置你的资源组之后, 可以查看资源组的资源管理器模板。 导出模板有两个好处：

- 自动执行未来部署的解决方案，因为该模板包含完整的基础结构。
- 通过查看代表解决方案的 JavaScript 对象表示法 (JSON)，了解模板语法。

若要导出的资源组中的所有资源，请使用[都导出 AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) cmdlet 和提供资源组名称。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

它将模板保存为本地文件。

而不是导出的资源组中的所有资源，可以选择要导出哪些资源。

若要导出一个资源，请将传递该资源 id。

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

若要导出多个资源，请将传递一个数组中的资源 Id。

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

当导出模板时，可以指定是否在模板中使用参数。 默认情况下，资源名称的参数包括但它们不具有默认值。 必须在部署期间传递该参数值。

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

在资源中，该参数使用的名称。

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

如果使用`-IncludeParameterDefaultValue`参数导出模板时，模板参数包括设置为当前值的默认值。 可以使用该默认值，也可以通过传入不同的值来覆盖默认值。

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

如果使用`-SkipResourceNameParameterization`参数的资源名称不为模板中包含导出的模板参数时。 而是直接在其当前值的资源上设置资源名称。 不能在部署期间自定义名称。

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

有关详细信息，请参阅[单个和多资源导出到 Azure 门户中的模板](./export-template-portal.md)。

## <a name="manage-access-to-resource-groups"></a>管理对资源组的访问

可以在 Azure 中通过[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 这种方式管理对资源的访问。 有关详细信息，请参阅[使用 RBAC 和 Azure PowerShell 管理访问权限](../role-based-access-control/role-assignments-powershell.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解 Azure 资源管理器，请参阅 [Azure 资源管理器概述](./resource-group-overview.md)。
- 若要了解资源管理器模板语法，请参阅[了解 Azure 资源管理器模板的结构和语法](./resource-group-authoring-templates.md)。
- 若要了解如何开发模板，请参阅[分步教程](/azure/azure-resource-manager/)。
- 若要查看 Azure 资源管理器模板架构，请参阅[模板参考](/azure/templates/)。