---
title: 使用 Azure PowerShell 管理 Azure 资源 |Microsoft Docs
description: 使用 Azure PowerShell 和 Azure 资源管理来管理你的资源。
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 241b820122fe1c82b9a68829db87635745c051d9
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56824862"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>使用 Azure PowerShell 管理 Azure 资源

了解如何使用 Azure PowerShell [Azure 资源管理器](resource-group-overview.md)来管理 Azure 资源。 管理资源组，请参阅[使用 Azure PowerShell 管理 Azure 资源组](./manage-resource-groups-powershell.md)。

有关管理资源的其他文章：

- [使用 Azure 门户管理 Azure 资源](./manage-resources-portal.md)
- [使用 Azure PowerShell 管理 Azure 资源](./manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>将资源部署到现有的资源组

可以直接通过使用 Azure PowerShell 部署 Azure 资源或部署资源管理器模板以创建 Azure 资源。

### <a name="deploy-a-resource"></a>部署资源

以下脚本创建的存储帐户。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>部署模板

以下脚本将创建部署快速入门模板以创建存储帐户。 有关详细信息，请参阅[快速入门：使用 Visual Studio Code 创建 Azure 资源管理器模板](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell)。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

有关详细信息，请参阅[使用资源管理器模板和 Azure PowerShell 部署资源](./resource-group-template-deploy.md)。

## <a name="deploy-a-resource-group-and-resources"></a>部署资源组和资源

可以创建资源组，并将资源部署到组。 有关详细信息，请参阅[创建资源组并部署资源](./deploy-to-subscription.md#create-resource-group-and-deploy-resources)。

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>将资源部署到多个订阅或资源组

通常情况下，将模板中的所有资源部署到单个资源组。 不过，在某些情况下，你可能希望将一组资源部署在一起但将其放置在不同的资源组或订阅中。 有关详细信息，请参阅[Azure 资源部署到多个订阅或资源组](./resource-manager-cross-resource-group-deployment.md)。

## <a name="delete-resources"></a>删除资源

以下脚本演示如何删除存储帐户。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

有关 Azure 资源管理器如何排序的资源删除操作的详细信息，请参阅[Azure 资源管理器资源组删除](./resource-group-delete.md)。

## <a name="move-resources"></a>移动资源

以下脚本演示如何从一个资源组中删除存储帐户，另一个资源组。

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

若要完成教程，请参阅[教程：将 Azure 资源移到另一个资源组或订阅](./resource-manager-tutorial-move-resources.md)。 

有关详细信息，请参阅[将资源移到新资源组或订阅](resource-group-move-resources.md)。

## <a name="lock-resources"></a>锁定资源

锁定可以防止意外删除或修改关键资源，如 Azure 订阅、 资源组或资源在组织中的其他用户。 

以下脚本锁定存储帐户，因此无法删除该帐户。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

以下脚本获取的存储帐户的所有锁：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

以下脚本将删除存储帐户的锁定：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

有关详细信息，请参阅 [使用 Azure 资源管理器锁定资源](resource-group-lock-resources.md)。

## <a name="tag-resources"></a>标记资源

标记可帮助以逻辑方式组织资源组和资源。 有关信息，请参阅[使用标记来组织 Azure 资源](./resource-group-using-tags.md#powershell)。

## <a name="manage-access-to-resources"></a>管理对资源的访问权限

可以在 Azure 中通过[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 这种方式管理对资源的访问。 有关详细信息，请参阅[使用 RBAC 和 Azure PowerShell 管理访问](../role-based-access-control/role-assignments-powershell.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解 Azure 资源管理器，请参阅[Azure 资源管理器概述](./resource-group-overview.md)。
- 若要了解资源管理器模板语法，请参阅[了解结构和 Azure 资源管理器模板的语法](./resource-group-authoring-templates.md)。
- 若要了解如何开发模板，请参阅[分步教程](/azure/azure-resource-manager/)。
- 若要查看 Azure 资源管理器模板的架构，请参阅[模板引用](/azure/templates/)。