---
title: 管理资源组-Azure PowerShell
description: 使用 Azure PowerShell 通过 Azure 资源管理器管理资源组。 说明如何创建、列出和删除资源组。
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 25e001927d5c06b4a7e5639faaa054ae18b12bb7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75478768"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>使用 Azure PowerShell 管理 Azure 资源管理器资源组

了解如何使用[azure 资源管理器](overview.md)Azure PowerShell 来管理 azure 资源组。 若要管理 Azure 资源，请参阅[使用 Azure PowerShell 管理 azure 资源](manage-resources-powershell.md)。

有关管理资源组的其他文章：

- [使用 Azure 门户管理 Azure 资源组](manage-resources-portal.md)
- [使用 Azure CLI 管理 Azure 资源组](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>什么是资源组

资源组是用于保存 Azure 解决方案相关资源的容器。 资源组可以包含解决方案的所有资源，也可以只包含以组的形式进行管理的资源。 根据对组织有利的原则，决定如何将资源分配到资源组。 通常可将共享相同生命周期的资源添加到同一资源组，以便将其作为一个组轻松部署、更新和删除。

” 资源组存储有关资源的元数据。 因此，当指定资源组的位置时，也就指定了元数据的存储位置。 出于合规性原因，可能需要确保数据存储在某一特定区域。

” 资源组存储有关资源的元数据。 当指定资源组的位置时，也就指定了元数据的存储位置。

## <a name="create-resource-groups"></a>创建资源组

下面的 PowerShell 脚本将创建一个资源组，然后显示该资源组。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>列出资源组

下面的 PowerShell 脚本列出你的订阅下的资源组。

```azurepowershell-interactive
Get-AzResourceGroup
```

获取一个资源组：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>删除资源组

下面的 PowerShell 脚本删除资源组：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

有关资源管理器 Azure 如何对资源删除进行排序的详细信息，请参阅[azure 资源管理器资源组删除](delete-resource-group.md)。

## <a name="deploy-resources-to-an-existing-resource-group"></a>将资源部署到现有资源组

请参阅[将资源部署到现有资源组](manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group)。

若要验证资源组部署，请参阅[AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0)。

## <a name="deploy-a-resource-group-and-resources"></a>部署资源组和资源

可以使用资源管理器模板创建资源组，并将资源部署到组。 有关详细信息，请参阅[创建资源组并部署资源](../templates/deploy-to-subscription.md#resource-group-and-resources)。

## <a name="redeploy-when-deployment-fails"></a>部署失败时，重新部署

此功能也称为 *"出错时回滚"* 。 有关详细信息，请参阅[部署失败时重新部署](../templates/rollback-on-error.md)。

## <a name="move-to-another-resource-group-or-subscription"></a>移动到另一个资源组或订阅

可以将组中的资源移动到另一个资源组。 有关详细信息，请参阅[将资源移到新资源组或订阅](move-resource-group-and-subscription.md)。

## <a name="lock-resource-groups"></a>锁定资源组

锁定可防止组织中的其他用户意外删除或修改关键资源，如 Azure 订阅、资源组或资源。 

以下脚本将锁定资源组，以便无法删除资源组。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

以下脚本获取资源组的所有锁：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

有关详细信息，请参阅 [使用 Azure 资源管理器锁定资源](lock-resources.md)。

## <a name="tag-resource-groups"></a>标记资源组

可以将标记应用到资源组和资源，以按照逻辑组织资产。 有关信息，请参阅[使用标记来组织 Azure 资源](tag-resources.md#powershell)。

## <a name="export-resource-groups-to-templates"></a>将资源组导出到模板

设置资源组后，可以查看资源组的资源管理器模板。 导出模板有两个好处：

- 自动执行解决方案的将来部署，因为模板包含完整的基础结构。
- 通过查看代表您的解决方案的 JavaScript 对象表示法（JSON）来了解模板语法。

若要导出资源组中的所有资源，请使用[AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) cmdlet 并提供资源组名称。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

它将模板另存为本地文件。

您可以选择要导出的资源，而不是导出资源组中的所有资源。

若要导出一个资源，请传递该资源 ID。

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

若要导出多个资源，请在数组中传递资源 Id。

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

导出模板时，可以指定是否在模板中使用参数。 默认情况下，将包括资源名称的参数，但这些参数没有默认值。 在部署过程中必须传递该参数值。

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

在资源中，参数用于名称。

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

如果在导出模板时使用 `-IncludeParameterDefaultValue` 参数，则模板参数将包含设置为当前值的默认值。 您可以使用此默认值，也可以通过传入不同的值覆盖默认值。

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

如果在导出模板时使用 `-SkipResourceNameParameterization` 参数，则不会在模板中包括资源名称的参数。 而是直接将资源名称设置为其当前值。 在部署过程中无法自定义该名称。

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

导出模板功能不支持导出 Azure 数据工厂资源。 若要了解如何导出数据工厂资源，请参阅[在 Azure 数据工厂中复制或克隆数据工厂](https://aka.ms/exportTemplateViaAdf)。

若要导出通过经典部署模型创建的资源，则必须将[其迁移到资源管理器部署模型](https://aka.ms/migrateclassicresourcetoarm)。

有关详细信息，请参阅[Azure 门户中的单个和多个资源导出到模板](../templates/export-template-portal.md)。

## <a name="manage-access-to-resource-groups"></a>管理对资源组的访问权限

可以在 Azure 中通过[基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md) 这种方式管理对资源的访问。 有关详细信息，请参阅[使用 RBAC 和 Azure PowerShell 管理访问权限](../../role-based-access-control/role-assignments-powershell.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解 Azure 资源管理器，请参阅[azure 资源管理器概述](overview.md)。
- 若要了解资源管理器模板语法，请参阅[了解 Azure 资源管理器模板的结构和语法](../templates/template-syntax.md)。
- 若要了解如何开发模板，请参阅[分步教程](/azure/azure-resource-manager/)。
- 若要查看 Azure 资源管理器模板架构，请参阅[模板参考](/azure/templates/)。