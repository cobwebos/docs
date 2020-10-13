---
title: 管理资源组 - Azure PowerShell
description: 使用 Azure PowerShell 通过 Azure 资源管理器管理资源组。 说明如何创建、列出和删除资源组。
author: mumian
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: jgao
ms.openlocfilehash: 5c72dd234cf6d8558fb49882170fe33bac1900dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372130"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>使用 Azure PowerShell 管理 Azure 资源管理器资源组

了解如何将 Azure PowerShell 与 [Azure 资源管理器](overview.md)配合使用来管理 Azure 资源组。 若要管理 Azure 资源，请参阅[使用 Azure PowerShell 管理 Azure 资源](manage-resources-powershell.md)。

有关资源组管理的其他文章：

- [使用 Azure 门户管理 Azure 资源组](manage-resources-portal.md)
- [使用 Azure CLI 管理 Azure 资源组](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>什么是资源组

资源组是用于保存 Azure 解决方案相关资源的容器。 资源组可以包含解决方案的所有资源，也可以只包含以组的形式进行管理的资源。 根据对组织有利的原则，决定如何将资源分配到资源组。 通常可将共享相同生命周期的资源添加到同一资源组，以便将其作为一个组轻松部署、更新和删除。

” 资源组存储有关资源的元数据。 因此，当指定资源组的位置时，也就指定了元数据的存储位置。 出于合规性原因，可能需要确保数据存储在某一特定区域。

” 资源组存储有关资源的元数据。 当指定资源组的位置时，也就指定了元数据的存储位置。

## <a name="create-resource-groups"></a>创建资源组

以下 PowerShell 脚本将创建一个资源组。

```azurepowershell-interactive
New-AzResourceGroup -Name demoResourceGroup -Location westus
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

若要详细了解 Azure 资源管理器如何管理资源的删除，请参阅 [Azure 资源管理器资源组的删除](delete-resource-group.md)。

## <a name="deploy-resources-to-an-existing-resource-group"></a>将资源部署到现有的资源组

请参阅[将资源部署到现有的资源组](manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group)。

若要验证资源组部署，请参阅 [Test-AzResourceGroupDeployment](/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0)。

## <a name="deploy-a-resource-group-and-resources"></a>部署资源组和资源

可以使用资源管理器模板创建一个资源组并将资源部署到该组。 有关详细信息，请参阅[创建资源组并部署资源](../templates/deploy-to-subscription.md#resource-groups)。

## <a name="redeploy-when-deployment-fails"></a>部署失败时，重新部署

此功能也称为“出错时回滚”  。 有关详细信息，请参阅[在部署失败时重新部署](../templates/rollback-on-error.md)。

## <a name="move-to-another-resource-group-or-subscription"></a>移到另一个资源组或订阅

可将组中的资源移到另一个资源组。 有关详细信息，请参阅[将资源移到新资源组或订阅](move-resource-group-and-subscription.md)。

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

有关详细信息，请参阅 [使用 Azure 资源管理器锁定资源](lock-resources.md)。

## <a name="tag-resource-groups"></a>标记资源组

可以将标记应用到资源组和资源，以按照逻辑组织资产。 有关信息，请参阅[使用标记组织 Azure 资源](tag-resources.md#powershell)。

## <a name="export-resource-groups-to-templates"></a>将资源组导出到模板

设置资源组后，可以查看资源组的资源管理器模板。 导出模板有两个好处：

- 由于模板包含整个基础结构，因此将来可以自动完成解决方案的部署。
- 通过查看代表解决方案的 JavaScript 对象表示法 (JSON)，了解模板语法。

若要导出资源组中的所有资源，请使用 [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) cmdlet 并提供资源组名称。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

它将模板另存为本地文件。

可以选择要导出的资源，而不是导出资源组中的所有资源。

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

若要导出多个资源，请在数组中传递资源 ID。

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

导出模板时，可以指定是否在模板中使用参数。 默认情况下，包含资源名称的参数，但它们没有默认值。 在部署过程中必须传递该参数值。

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

如果在导出模板时使用 `-IncludeParameterDefaultValue` 参数，则模板参数包括设置为当前值的默认值。 可以使用该默认值，也可以通过传入不同的值来覆盖默认值。

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

如果在导出模板时使用 `-SkipResourceNameParameterization` 参数，则模板中不包括资源名称的参数。 而是，资源名称直接在资源上设置为其当前值。 无法在部署过程中自定义该名称。

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

若要导出通过经典部署模型创建的资源，必须[将其迁移到资源管理器部署模型](https://aka.ms/migrateclassicresourcetoarm)。

有关详细信息，请参阅[将 Azure 门户中的单资源和多资源导出到模板](../templates/export-template-portal.md)。

## <a name="manage-access-to-resource-groups"></a>管理对资源组的访问

可以通过 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 管理对 Azure 中资源的访问权限。 有关详细信息，请参阅 [使用 Azure PowerShell 添加或删除 Azure 角色分配](../../role-based-access-control/role-assignments-powershell.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解 Azure 资源管理器，请参阅 [Azure 资源管理器概述](overview.md)。
- 若要了解资源管理器模板语法，请参阅[了解 Azure 资源管理器模板的结构和语法](../templates/template-syntax.md)。
- 若要了解如何开发模板，请参阅[分步教程](../index.yml)。
- 若要查看 Azure 资源管理器模板架构，请参阅[模板参考](/azure/templates/)。
