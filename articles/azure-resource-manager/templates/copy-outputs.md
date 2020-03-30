---
title: 定义输出值的多个实例
description: 在 Azure 资源管理器模板中使用复制操作在从部署返回值时多次迭代。
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 3889260d02f438274c80e99e99136515499443e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153380"
---
# <a name="output-iteration-in-arm-templates"></a>ARM 模板中的输出迭代

本文介绍如何在 Azure 资源管理器 （ARM） 模板中为输出创建多个值。 通过将**复制**元素添加到模板的输出部分，可以在部署期间动态返回多个项。

还可以将 copy 与[资源](copy-resources.md)、[资源中的属性](copy-properties.md)和[变量](copy-variables.md)一起使用。

## <a name="outputs-iteration"></a>输出迭代

复制元素具有以下常规格式：

```json
"copy": [
  {
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

**count**属性指定所需的输出值的迭代次数。

**输入**属性指定要重复的属性。 创建从**输入**属性中的值构造的元素数组。 它可以是单个属性（如字符串），也可以是具有多个属性的对象。

以下示例创建可变数量的存储帐户，并为每个存储帐户返回终结点：

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

下一个示例从新的存储帐户返回三个属性。

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

* 要浏览教程，请参阅[教程：使用 ARM 模板创建多个资源实例](template-tutorial-create-multiple-instances.md)。
* 有关复制元素的其他用途，请参阅：
  * [ARM 模板中的资源迭代](copy-resources.md)
  * [ARM 模板中的属性迭代](copy-properties.md)
  * [ARM 模板中的可变迭代](copy-variables.md)
* 如果要了解模板的各个部分，请参阅[创作 ARM 模板](template-syntax.md)。
* 要了解如何部署模板，请参阅[使用 ARM 模板部署应用程序](deploy-powershell.md)。

