---
title: "将 Azure 资源部署到多个订阅和资源组 | Microsoft Docs"
description: "介绍如何在部署期间将多个 Azure 订阅和资源组作为目标。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2018
ms.author: tomfitz
ms.openlocfilehash: 40b2d04fe829c51a58fb3bec1519a590a12cfdb8
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>将 Azure 资源部署到多个订阅或资源组

通常情况下，将模板中的所有资源部署到单个[资源组](resource-group-overview.md)。 不过，在某些情况下，你可能希望将一组资源部署在一起但将其放置在不同的资源组或订阅中。 例如，你可能希望将 Azure Site Recovery 的备份虚拟机部署到一个单独的资源组和位置。 资源管理器允许使用嵌套的模板将不同于父模板所用订阅和资源组的多个不同订阅和资源组作为目标。

> [!NOTE]
> 在单个部署中可以仅部署到五个资源组。

## <a name="specify-a-subscription-and-resource-group"></a>指定订阅和资源组

若要将不同的资源作为目标，请使用嵌套模板或链接模板。 `Microsoft.Resources/deployments` 资源类型提供 `subscriptionId` 和 `resourceGroup` 的参数。 使用这些属性可为嵌套部署指定不同的订阅和资源组。 在运行部署之前，所有资源组都必须存在。 如果未指定订阅 ID 或资源组，将使用父模板中的订阅和资源组。

若要指定不同的资源组和订阅，请使用：

```json
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "resourceGroup": "[parameters('secondResourceGroup')]",
        "subscriptionId": "[parameters('secondSubscriptionID')]",
        ...
    }
]
```

如果资源组位于同一订阅中，则可以删除“subscriptionId”值。

以下示例部署两个存储帐户 - 一个在部署期间指定的资源组中，另一个在 `secondResourceGroup` 参数指定的资源组中：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "secondResourceGroup": {
            "type": "string"
        },
        "secondSubscriptionID": {
            "type": "string",
            "defaultValue": ""
        },
        "secondStorageLocation": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
        "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
    },
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "[parameters('secondResourceGroup')]",
            "subscriptionId": "[parameters('secondSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
                            "location": "[parameters('secondStorageLocation')]",
                            "sku":{
                                "name": "Standard_LRS"
                            },
                            "kind": "Storage",
                            "properties": {
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        }
    ]
}
```

如果将 `resourceGroup` 设置为不存在的资源组的名称，则部署会失败。

若要部署示例模板，请使用 Azure PowerShell 4.0.0 或更高版本，也可以使用 Azure CLI 2.0.0 或更高版本。

## <a name="use-the-resourcegroup-function"></a>使用 resourceGroup() 函数

对于跨资源组部署，[resourceGroup() 函数](resource-group-template-functions-resource.md#resourcegroup)根据指定嵌套模板的方式而以不同的方式解析。 

如果在一个模板内嵌入另一个模板，嵌套模板中的 resourceGroup() 会解析至父资源组。 被嵌入的模板使用以下格式：

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() refers to parent resource group
    }
}
```

如果链接到单独的模板，则链接模板的 resourceGroup() 会解析到嵌套资源组。 链接模板使用以下格式：

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() in linked template refers to linked resource group
    }
}
```

## <a name="example-templates"></a>示例模板

以下模板演示多个资源组部署。 部署模板的脚本显示在表格之后。

|模板  |说明  |
|---------|---------|
|[跨订阅模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |将一个存储帐户部署到一个资源组，将另一个存储帐户部署到第二个资源组。 如果第二个资源组位于其他订阅，请包含该订阅的 ID 值。 |
|[跨资源组模板属性](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |演示如何解析 `resourceGroup()` 函数。 此模板不部署任何资源。 |

### <a name="powershell"></a>PowerShell

对于 PowerShell，若要将两个存储帐户部署到同一订阅中的两个资源组，请使用：

```powershell
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzureRmResourceGroup -Name $firstRG -Location southcentralus
New-AzureRmResourceGroup -Name $secondRG -Location eastus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

对于 PowerShell，若要将两个存储帐户部署到两个订阅，请使用：

```powershell
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzureRmSubscription -Subscription $secondSub
New-AzureRmResourceGroup -Name $secondRG -Location eastus

Select-AzureRmSubscription -Subscription $firstSub
New-AzureRmResourceGroup -Name $firstRG -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

对于 PowerShell，若要测试如何解析父模板、内联模板和链接模板的资源组对象，请使用：

```powershell
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

### <a name="azure-cli"></a>Azure CLI

对于 Azure CLI，若要将两个存储帐户部署到同一订阅中的两个资源组，请使用：

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

对于 Azure CLI，若要将两个存储帐户部署到两个订阅，请使用：

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

对于 Azure CLI，若要测试如何解析父模板、内联模板和链接模板的资源组对象，请使用：

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

## <a name="next-steps"></a>后续步骤

* 若要了解如何在模板中定义参数，请参阅[了解 Azure 资源管理器模板的结构和语法](resource-group-authoring-templates.md)。
* 有关解决常见部署错误的提示，请参阅[排查使用 Azure 资源管理器时的常见 Azure 部署错误](resource-manager-common-deployment-errors.md)。
* 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用模板](resource-manager-powershell-sas-token.md)。
