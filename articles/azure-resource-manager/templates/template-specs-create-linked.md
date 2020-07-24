---
title: 使用链接模板创建模板规范
description: 了解如何创建具有链接模板的模板规范。
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: c2648cb8a71be709406f314d02a226ed097be6f0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095889"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>教程：使用链接模板创建模板规范（预览）

了解如何创建具有[链接模板](linked-templates.md#linked-template)的[模板规范](template-specs.md)。 使用模板规范与组织中的其他用户共享 ARM 模板。 本文介绍如何使用 `relativePath` [部署资源](/azure/templates/microsoft.resources/deployments)的新属性来创建模板规范以打包主模板及其链接的模板。

## <a name="prerequisites"></a>先决条件

具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 模板规范当前为预览版。 若要使用它，必须[注册预览版](https://aka.ms/templateSpecOnboarding)。

## <a name="create-linked-templates"></a>创建链接模板

创建主模板和链接的模板。

若要链接模板，请将[部署资源](/azure/templates/microsoft.resources/deployments)添加到主模板。 在 `templateLink` 属性中，根据父模板的路径指定链接模板的相对路径。

链接模板在**上linkedTemplate.js**调用，并存储在存储主模板的路径中名为 "**项目**" 的子文件夹中。  可以对 relativePath 使用以下值之一：

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

如果在上的 linkedTemplate.js中调用了另一个 linkedTemplate2.js，并将上的 linkedTemplate2.js存储在同一项目子文件夹中，则在 linkedTemplate.js上指定的 relativePath 将**在上linkedTemplate2.js**。

1. 创建具有以下 JSON 的主模板。 将主模板azuredeploy.js保存到本地计算机**上**。 本教程假定你已保存到**c:\Templates\linkedTS\azuredeploy.js的**路径，但你可以使用任何路径。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
          }
        }
      },
      "variables": {
        "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Web/serverfarms",
          "apiVersion": "2016-09-01",
          "name": "[variables('appServicePlanName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "B1",
            "tier": "Basic",
            "size": "B1",
            "family": "B",
            "capacity": 1
          },
          "kind": "linux",
          "properties": {
            "perSiteScaling": false,
            "reserved": true,
            "targetWorkerCount": 0,
            "targetWorkerSizeId": 0
          }
        },
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "createStorage",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > 的 apiVersion `Microsoft.Resources/deployments` 必须是2020-06-01 或更高版本。

1. 在保存主模板的文件夹中创建一个名为 "**项目**" 的目录。
1. 用以下 JSON 创建链接模板：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS",
            "Premium_LRS"
          ],
          "metadata": {
            "description": "Storage Account type"
          }
        },
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        }
      },
      "variables": {
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {
        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        }
      }
    }
    ```

1. 将模板另存为**项目**文件夹中的**linkedTemplate.js** 。

## <a name="create-template-spec"></a>创建模板规范

模板规范存储在资源组中。  创建资源组，然后使用以下脚本创建模板规范。 模板规范名称为**webSpec**。

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -ResourceGroupName templateSpecRG `
  -Name webSpec `
  -Version "1.0.0.0" `
  -Location westus2 `
  -TemplateJsonFile "c:\Templates\linkedTS\azuredeploy.json"
```

完成后，可以从 Azure 门户或使用以下 cmdlet 查看模板规范：

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

## <a name="deploy-template-spec"></a>部署模板规范

你现在可以部署模板规范。部署模板规范就像部署它包含的模板一样，只是你传入了模板规范的资源 ID。使用相同的部署命令，并在需要时传入模板规范的参数值。

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

## <a name="next-steps"></a>后续步骤

若要了解如何将模板规范部署为链接模板，请参阅[教程：将模板规范部署为链接模板](template-specs-deploy-linked-template.md)。
