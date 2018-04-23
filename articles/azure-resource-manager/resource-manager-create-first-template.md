---
title: 创建第一个 Azure 资源管理器模板 | Microsoft Docs
description: 分步指南：创建第一个 Azure 资源管理器模板。 本指南介绍如何使用存储帐户的模板参考来创建模板。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/30/2018
ms.topic: get-started-article
ms.author: tomfitz
ms.openlocfilehash: 38ed7040c4f1497192c8c37887dc6281aa7935ea
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="create-and-deploy-your-first-azure-resource-manager-template"></a>创建和部署第一个 Azure 资源管理器模板
本主题介绍如何通过相关步骤创建第一个 Azure 资源管理器模板。 Resource Manager 模板为 JSON 文件，用于定义针对解决方案进行部署时所需的资源。 若要了解与部署和管理 Azure 解决方案相关联的概念，请参阅 [Azure 资源管理器概述](resource-group-overview.md)。 如果有现成的资源，需要为这些资源获取模板，请参阅[从现有资源导出 Azure 资源管理器模板](resource-manager-export-template.md)。

若要创建和修改模板，需要 JSON 编辑器。 [Visual Studio Code](https://code.visualstudio.com/) 是轻量型开源跨平台代码编辑器。 强烈建议使用 Visual Studio Code 来创建资源管理器模板。 本文假定你使用 VS Code。 如果有其他 JSON 编辑器（例如 Visual Studio），可以使用该编辑器。

## <a name="prerequisites"></a>先决条件

* Visual Studio Code。 根据需要从 [https://code.visualstudio.com/](https://code.visualstudio.com/) 进行安装。
* Azure 订阅。 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 已在本地安装 [Azure PowerShell](/powershell/azure/install-azurerm-ps) 或 [Azure CLI](/cli/azure/install-azure-cli)。 本教程需要进行本地安装，因为模板会另存为本地文件。 若要使用 Cloud Shell，必须[将模板加载到存储帐户](resource-group-template-deploy-cli.md#deploy-template-from-cloud-shell)。

## <a name="create-template"></a>创建模板

一开始请使用简单的模板将存储帐户部署到订阅。

1. 选择“文件” > “新建文件”。 

   ![新建文件](./media/resource-manager-create-first-template/new-file.png)

2. 将以下 JSON 语法复制并粘贴到文件中：

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
     },
     "variables": {
     },
     "resources": [
       {
         "name": "[concat('storage', uniqueString(resourceGroup().id))]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "sku": {
           "name": "Standard_LRS"
         },
         "kind": "Storage",
         "location": "South Central US",
         "tags": {},
         "properties": {}
       }
     ],
     "outputs": {  }
   }
   ```

   存储帐户名称有多种限制，因此难以设置。 该名称必须为 3 到 24 个字符，只能使用数字和小写字母，而且必需唯一。 前述模板使用 [uniqueString](resource-group-template-functions-string.md#uniquestring) 函数生成哈希值。 此模板通过添加前缀 storage 扩展了该哈希值增的含义。 

3. 在本地文件夹中将该文件另存为 azuredeploy.json。

   ![保存模板](./media/resource-manager-create-first-template/save-template.png)

## <a name="deploy-template"></a>部署模板

已做好部署此模板的准备。 请使用 PowerShell 或 Azure CLI 创建一个资源组。 然后，将存储帐户部署到该资源组。

* 对于 PowerShell，请在包含模板的文件夹中使用以下命令：

   ```powershell
   Connect-AzureRmAccount
   
   New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
   New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json
   ```

* 若要在本地安装 Azure CLI，请在包含模板的文件夹中使用以下命令：

   ```azurecli
   az login

   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file azuredeploy.json
   ```

部署完成后，存储帐户就会存在于资源组中。

## <a name="customize-the-template"></a>自定义模板

该模板可以正常使用，但不灵活。 它始终将本地冗余存储部署到美国中南部。 名称始终为存储后跟哈希值。 若要允许将模板用于不同的方案，请向模板添加参数。

以下示例显示带两个参数的 parameters 节。 第一个参数 `storageSKU` 用于指定冗余类型。 它将可以传入的值限制为适用于存储帐户的值。 它还指定默认值。 第二个参数 `storageNamePrefix` 设置为最多允许 11 个字符。 它指定默认值。

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  },
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
      "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
    }
  }
},
```

在 variables 节，请添加名为 `storageName` 的变量。 它组合了来自变量的前缀值和来自 [uniqueString](resource-group-template-functions-string.md#uniquestring) 函数的哈希值。 它使用 [toLower](resource-group-template-functions-string.md#tolower) 函数将所有字符转换为小写。

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

若要将这些新值用于存储帐户，请更改资源定义：

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    "kind": "Storage",
    "location": "[resourceGroup().location]",
    "tags": {},
    "properties": {}
  }
],
```

请注意，存储帐户的名称现在设置为已添加的变量。 SKU 名称设置为参数的值。 位置设置为资源组所在的位置。

保存文件。 

模板现在如下所示：

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {}
    }
  ],
  "outputs": {  }
}
```

## <a name="redeploy-template"></a>重新部署模板

请使用不同的值重新部署模板。

对于 PowerShell，请使用：

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix newstore -storageSKU Standard_RAGRS
```

对于 Azure CLI，请使用：

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

## <a name="use-autocomplete"></a>使用自动完成功能

目前为止，你在模板上所做的工作只是从本文复制并粘贴 JSON。 但在开发自己的模板时，需查找并指定适用于资源类型的属性和值。 VS Code 读取资源类型的架构，并建议属性和值。 若要查看自动完成功能，请转到模板的属性元素处，然后添加新的一行。 键入一个引号，此时你会注意到，VS Code 立即建议可以在属性元素中使用的名称。

![显示可用属性](./media/resource-manager-create-first-template/show-properties.png)

选择“加密”。 键入冒号 (:)，VS Code 会建议添加新对象。

![添加对象](./media/resource-manager-create-first-template/add-object.png)

若要添加对象，请按 Tab 或 Enter。

再次键入引号，此时会看到 VS Code 建议适用于加密的属性。

![显示加密属性](./media/resource-manager-create-first-template/show-encryption-properties.png)

选择“服务”，然后根据 VS Code 扩展继续添加值，直到：

```json
"properties": {
    "encryption":{
        "services":{
            "blob":{
              "enabled":true
            }
        }
    }
}
```

为存储帐户启用 Blob 加密。 但是，VS Code 发现了一个问题。 请注意，加密有一个警告。

![加密警告](./media/resource-manager-create-first-template/encryption-warning.png)

若要查看警告，请将鼠标悬停在绿线上。

![缺少属性](./media/resource-manager-create-first-template/missing-property.png)

可以看到，加密元素需要 keySource 属性。 在服务对象后添加一个逗号，然后添加 keySource 属性。 VS Code 建议使用 "Microsoft.Storage" 作为有效值。 完成后，属性元素为：

```json
"properties": {
    "encryption":{
        "services":{
            "blob":{
              "enabled":true
            }
        },
        "keySource":"Microsoft.Storage"
    }
}
```

最终模板为：

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "encryption":{
          "services":{
            "blob":{
              "enabled":true
            }
          },
          "keySource":"Microsoft.Storage"
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="deploy-encrypted-storage"></a>部署加密的存储

再次部署模板并提供新的存储帐户名称。

对于 PowerShell，请使用：

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix storesecure
```

对于 Azure CLI，请使用：

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageNamePrefix=storesecure
```

## <a name="clean-up-resources"></a>清理资源

不再需要时，请通过删除资源组来清理部署的资源。

对于 PowerShell，请使用：

```powershell
Remove-AzureRmResourceGroup -Name examplegroup
```

对于 Azure CLI，请使用：

```azurecli
az group delete --name examplegroup
```

## <a name="next-steps"></a>后续步骤
* 若要获得模板开发方面的更大帮助，可以安装 VS Code 扩展。 有关详细信息，请参阅[使用 Visual Studio Code 扩展创建 Azure 资源管理器模板](resource-manager-vscode-extension.md)
* 若要详细了解模板的结构，请参阅[创作 Azure 资源管理器模板](resource-group-authoring-templates.md)。
* 若要了解存储帐户的属性，请查看[存储帐户模板参考](/azure/templates/microsoft.storage/storageaccounts)。
* 若要查看许多不同类型的解决方案的完整模型，请参阅 [Azure Quickstart Templates](https://azure.microsoft.com/documentation/templates/)（Azure 快速入门模板）。
