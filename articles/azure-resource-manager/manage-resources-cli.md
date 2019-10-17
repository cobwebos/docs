---
title: 使用 Azure CLI 管理 Azure 资源 |Microsoft Docs
description: 使用 Azure CLI 和 Azure 资源管理器来管理资源。 演示如何部署和删除资源。
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: d3c3ca4a95cff8b9a81be8e75b011ca83799dcaa
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390385"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>使用 Azure CLI 管理 Azure 资源

了解如何使用[azure 资源管理器](resource-group-overview.md)Azure CLI 来管理 azure 资源。 有关管理资源组，请参阅[使用 Azure CLI 管理 Azure 资源组](./manage-resource-groups-cli.md)。

有关管理资源的其他文章：

- [使用 Azure 门户管理 Azure 资源](./manage-resources-portal.md)
- [使用 Azure PowerShell 管理 Azure 资源](./manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>将资源部署到现有资源组

你可以使用 Azure CLI 直接部署 Azure 资源，或部署资源管理器模板来创建 Azure 资源。

### <a name="deploy-a-resource"></a>部署资源

以下脚本将创建一个存储帐户。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>部署模板

下面的脚本创建了部署快速入门模板，以创建存储帐户。 有关详细信息，请参阅[快速入门：使用 Visual Studio Code 创建 Azure 资源管理器模板](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell)。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

有关详细信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源](./resource-group-template-deploy-cli.md)。

## <a name="deploy-a-resource-group-and-resources"></a>部署资源组和资源

你可以创建资源组，并将资源部署到组。 有关详细信息，请参阅[创建资源组并部署资源](./deploy-to-subscription.md#resource-group-and-resources)。

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>将资源部署到多个订阅或资源组

通常情况下，将模板中的所有资源部署到单个资源组。 不过，在某些情况下，你可能希望将一组资源部署在一起但将其放置在不同的资源组或订阅中。 有关详细信息，请参阅[将 Azure 资源部署到多个订阅或资源组](./resource-manager-cross-resource-group-deployment.md)。

## <a name="delete-resources"></a>删除资源

以下脚本显示了如何删除存储帐户。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

有关资源管理器 Azure 如何对资源删除进行排序的详细信息，请参阅[azure 资源管理器资源组删除](./resource-group-delete.md)。

## <a name="move-resources"></a>移动资源

以下脚本显示了如何将存储帐户从一个资源组移到另一个资源组。

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

有关详细信息，请参阅[将资源移到新资源组或订阅](resource-group-move-resources.md)。

## <a name="lock-resources"></a>锁定资源

锁定可防止组织中的其他用户意外删除或修改关键资源，如 Azure 订阅、资源组或资源。 

以下脚本将锁定存储帐户，因此无法删除该帐户。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

以下脚本将获取存储帐户的所有锁：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

以下脚本将删除存储帐户的锁定：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

有关详细信息，请参阅 [使用 Azure 资源管理器锁定资源](resource-group-lock-resources.md)。

## <a name="tag-resources"></a>标记资源

标记有助于以逻辑方式组织资源组和资源。 有关信息，请参阅[使用标记来组织 Azure 资源](./resource-group-using-tags.md#azure-cli)。

## <a name="manage-access-to-resources"></a>管理对资源的访问权限

可以在 Azure 中通过[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 这种方式管理对资源的访问。 有关详细信息，请参阅[使用 RBAC 和 Azure CLI 管理访问权限](../role-based-access-control/role-assignments-cli.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解 Azure 资源管理器，请参阅[azure 资源管理器概述](./resource-group-overview.md)。
- 若要了解资源管理器模板语法，请参阅[了解 Azure 资源管理器模板的结构和语法](./resource-group-authoring-templates.md)。
- 若要了解如何开发模板，请参阅[分步教程](/azure/azure-resource-manager/)。
- 若要查看 Azure 资源管理器模板架构，请参阅[模板参考](/azure/templates/)。
