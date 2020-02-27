---
title: 定义输出值的多个实例
description: 使用 Azure 资源管理器模板中的复制操作可在从部署返回值时进行多次迭代。
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: db5c548c7bd4c60357d3656b1273b0192c497459
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624769"
---
# <a name="output-iteration-in-azure-resource-manager-templates"></a>Azure 资源管理器模板中的输出迭代

本文介绍如何为 Azure 资源管理器模板中的输出创建多个值。 通过将**copy**元素添加到模板的输出部分，可以在部署期间动态返回多个项。

你还可以将副本用于[资源](copy-resources.md)、[资源中的属性](copy-properties.md)和[变量](copy-variables.md)。

## <a name="outputs-iteration"></a>输出迭代

Copy 元素具有以下常规格式：

```json
"copy": [
  {
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

**Count**属性指定输出值所需的迭代数。

**输入**属性指定要重复的属性。 创建一个由**input**属性中的值构造的元素数组。 它可以是单个属性（例如字符串），也可以是具有多个属性的对象。

以下示例创建数量可变的存储帐户，并返回每个存储帐户的终结点：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageEndpoints": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
            }
        }
    }
}
```

前面的模板返回具有以下值的数组：

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

下一个示例返回新存储帐户的三个属性。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageInfo": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": {
                    "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
                    "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
                    "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
                }
            }
        }
    }
}
```

前面的示例返回具有以下值的数组：

```json
[
    {
        "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
        "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    },
    {
        "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
        "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    }
]
```

## <a name="next-steps"></a>后续步骤

* 要查看教程，请参阅[教程：使用资源管理器模板创建多个资源实例](template-tutorial-create-multiple-instances.md)。
* 有关 copy 元素的其他用法，请参阅：
  * [Azure 资源管理器模板中的资源迭代](copy-resources.md)
  * [Azure 资源管理器模板中的属性迭代](copy-properties.md)
  * [Azure 资源管理器模板中的变量迭代](copy-variables.md)
* 若要了解有关模板区段的信息，请参阅[创作 Azure 资源管理器模板](template-syntax.md)。
* 若要了解如何部署模板，请参阅[使用 Azure 资源管理器模板部署应用程序](deploy-powershell.md)。

