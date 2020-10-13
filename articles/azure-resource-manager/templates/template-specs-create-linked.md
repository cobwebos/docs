---
title: 创建具有链接模板的模板规格
description: 了解如何创建具有链接模板的模板规格。
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: adcce8194f380b90eb9a29f4da25763e112b9f12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91728533"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>教程：创建具有链接模板的模板规格（预览）

了解如何创建具有[链接模板](linked-templates.md#linked-template)的[模板规格](template-specs.md)。 使用模板规格与组织中的其他用户共享 ARM 模板。 本文介绍如何使用 `relativePath` [部署资源](/azure/templates/microsoft.resources/deployments)的属性创建模板规范以打包主模板及其链接的模板。

## <a name="prerequisites"></a>先决条件

具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 模板规格当前提供预览版。 若要使用它，必须[注册预览版](https://aka.ms/templateSpecOnboarding)。

## <a name="create-linked-templates"></a>创建链接模板

创建主模板和链接模板。

若要链接模板，请将 [部署资源](/azure/templates/microsoft.resources/deployments) 添加到主模板。 在 `templateLink` 属性中，根据父模板的路径指定链接模板的相对路径。

该链接模板名为 linkedTemplate.json，存储在主模板所在的路径中名为 artifacts 的子文件夹中 。  你可以使用 relativePath 的以下值之一：

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

`relativePath` 属性始终与声明 `relativePath` 的模板文件相关，因此，如果有另一个 linkedTemplate2.json 从 linkedTemplate.json 调用，并且 linkedTemplate2.json 存储在相同的 artifacts 子文件夹中，则在 linkedTemplate.json 中指定的 relativePath 仅仅是 `linkedTemplate2.json`。

1. 使用以下 JSON 创建主模板。 将主模板作为 azuredeploy.json 保存到本地计算机。 本教程假设你已将主模板保存到路径 c:\Templates\linkedTS\azuredeploy.json，但你可以使用任何路径。

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
    > `Microsoft.Resources/deployments` 的 apiVersion 必须是 2020-06-01 或更高版本。

1. 在保存主模板的文件夹中创建名为 artifacts 的目录。
1. 使用以下 JSON 创建链接模板：

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

1. 将模板作为 linkedTemplate.json 保存在 artifacts 文件夹中 。

## <a name="create-template-spec"></a>创建模板规格

模板规格存储在资源组中。  创建资源组，然后使用以下脚本创建模板规格。 模板规格的名称为 webSpec。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -Name webSpec `
  -Version "1.0.0.0" `
  -ResourceGroupName templateSpecRG `
  -Location westus2 `
  -TemplateFile "c:\Templates\linkedTS\azuredeploy.json"
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name templateSpecRG \
  --location westus2

az ts create \
  --name webSpec \
  --version "1.0.0.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "c:\Templates\linkedTS\azuredeploy.json"
```

---

完成后，可以从 Azure 门户或使用以下 cmdlet 查看模板规格：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show --name webSpec --resource-group templateSpecRG --version "1.0.0.0"
```

---

## <a name="deploy-template-spec"></a>部署模板规格

现在即可部署模板规格。部署模板规格就像部署它包含的模板一样，只不过你传入了模板规格的资源 ID。使用相同的部署命令，并在需要时为模板规格传递参数值。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Versions.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

id = $(az template-specs show --name webSpec --resource-group templateSpecRG --version "1.0.0.0" --query "id")

az deployment group create \
  --resource-group webRG \
  --template-spec $id
```

> [!NOTE]
> 获取模板规格 ID 并将其分配到 Windows PowerShell 中的变量时存在一个已知问题。

---

## <a name="next-steps"></a>后续步骤

若要了解如何将模板规格部署为链接模板，请参阅[教程：将模板规格部署为链接模板](template-specs-deploy-linked-template.md)。
