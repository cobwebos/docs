---
title: 将模板规范部署为链接模板
description: 了解如何在链接部署中部署现有模板规范。
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: dacf2fba3ff78f3ff92741b49edad8fdf5bffe29
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918377"
---
# <a name="tutorial-deploy-a-template-spec-as-a-linked-template-preview"></a>教程： (预览版将模板规范部署为链接模板) 

了解如何使用[链接的部署](linked-templates.md#linked-template)部署现有[模板规范](template-specs.md)。 使用模板规范与组织中的其他用户共享 ARM 模板。 创建模板规范后，可以使用 Azure PowerShell 或 Azure CLI 来部署模板规范。 你还可以使用链接模板将模板规范部署为解决方案的一部分。

## <a name="prerequisites"></a>先决条件

具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 模板规格当前提供预览版。 若要使用它，必须[注册预览版](https://aka.ms/templateSpecOnboarding)。

## <a name="create-a-template-spec"></a>创建模板规范

遵循 [快速入门：创建和部署模板规范](quickstart-create-template-specs.md) ，以创建用于部署存储帐户的模板规范。 下一节需要模板规范、模板规范名称和模板规范版本的资源组名称。

## <a name="create-the-main-template"></a>创建主模板

若要在 ARM 模板中部署模板规范，请将 [部署资源](/azure/templates/microsoft.resources/deployments) 添加到主模板。 在 `templateLink` 属性中，指定模板规范的资源 ID。使用以下名为的 JSON **azuredeploy.js**创建模板。 本教程假定你已保存到 **c:\Templates\deployTS\azuredeploy.js的** 路径，但你可以使用任何路径。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "tsResourceGroup":{
      "type": "string",
      "metadata": {
        "Description": "Specifies the resource group name of the template spec."
      }
    },
    "tsName": {
      "type": "string",
      "metadata": {
        "Description": "Specifies the name of the template spec."
      }
    },
    "tsVersion": {
      "type": "string",
      "defaultValue": "1.0.0.0",
      "metadata": {
        "Description": "Specifies the version the template spec."
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "metadata": {
        "Description": "Specifies the storage account type required by the template spec."
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
          "id": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
        },
        "parameters": {
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }
  ],
  "outputs": {
    "templateSpecId": {
      "type": "string",
      "value": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
    }
  }
}
```

模板规范 ID 是使用函数生成的 [`resourceID()`](template-functions-resource.md#resourceid) 。 如果 templateSpec 位于当前部署的同一资源组中，则 resourceID ( # A1 函数中的资源组参数是可选的。  还可以直接传入资源 ID 作为参数。 若要获取 ID，请使用：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$id = (Get-AzTemplateSpec -ResourceGroupName $resourceGroupName -Name $templateSpecName -Version $templateSpecVersion).Version.Id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
id = $(az template-specs show --name $templateSpecName --resource-group $resourceGroupName --version $templateSpecVersion --query "id")
```

> [!NOTE]
> 获取模板规范 id 时出现一个已知问题，然后将其分配给 Windows PowerShell 中的变量。

---

将参数传递给模板规范的语法为：

```json
"parameters": {
  "storageAccountType": {
    "value": "[parameters('storageAccountType')]"
  }
}
```

> [!NOTE]
> 的 apiVersion `Microsoft.Resources/deployments` 必须是2020-06-01 或更高版本。

## <a name="deploy-the-template"></a>部署模板

部署链接模板时，它将同时部署 web 应用程序和存储帐户。 部署与部署其他 ARM 模板相同。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

New-AzResourceGroupDeployment `
  -ResourceGroupName webRG `
  -TemplateFile "c:\Templates\deployTS\azuredeploy.json"
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

az deployment group create \
  --resource-group webRG \
  --template-file "c:\Templates\deployTS\azuredeploy.json"

```

---

## <a name="next-steps"></a>后续步骤

若要了解有关创建包含关联模板的模板规格的信息，请参阅[创建关联模板的模板规格](template-specs-create-linked.md)。
