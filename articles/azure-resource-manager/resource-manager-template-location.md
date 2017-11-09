---
title: "模板中的 Azure 资源位置 | Microsoft 文档"
description: "说明如何为 Azure Resource Manager 模板中的资源设置位置"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: tomfitz
ms.openlocfilehash: 73e50a593c41e841dcaf184abb895406ff5001e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="set-resource-location-in-azure-resource-manager-templates"></a>在 Azure Resource Manager 模板中设置资源位置
部署模板时，必须提供每个资源的位置。 本主题说明如何确定可用于每种资源类型的订阅的位置。

## <a name="determine-supported-locations"></a>确定支持的位置

有关每种资源类型的支持位置的完整列表，请参阅[可用产品（按区域）](https://azure.microsoft.com/regions/services/)。 但是，订阅可能并非有权访问该列表中的所有位置。 要查看可用于订阅的位置的自定义列表，请使用 Azure PowerShell 或 Azure CLI。 

以下示例使用 PowerShell 获取 `Microsoft.Web\sites` 资源类型的位置：

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

以下示例使用 Azure CLI 2.0获取 `Microsoft.Web\sites` 资源类型的位置：

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="set-location-in-template"></a>在模板中设置位置

确定资源的支持位置后，需要在模板中设置该位置。 设置此值的最简单方法是在支持资源类型的位置中创建资源组，并将每个位置设置为 `[resourceGroup().location]`。 可以将模板重新部署到不同位置中的资源组，而不更改模板或参数中的任何值。 

以下示例显示了部署到与资源组相同位置的存储帐户：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

如果需要在模板中对位置进行硬编码，请提供其中一个支持的区域的名称。 以下示例演示始终部署到美国中北部的存储帐户：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="next-steps"></a>后续步骤
* 有关如何创建模板的建议，请参阅 [Best practices for creating Azure Resource Manager templates](resource-manager-template-best-practices.md)（创建 Azure Resource Manager 模板的最佳做法）。

