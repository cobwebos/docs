---
title: 定义输出值的多个实例
description: 从部署中返回值时，可以在 Azure 资源管理器模板中使用复制操作进行多次迭代。
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 50c4b4b8f301ad88d3dfde98ace1aed4431693db
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583429"
---
# <a name="output-iteration-in-arm-templates"></a>ARM 模板中的输出迭代

本文介绍如何为 Azure 资源管理器（ARM）模板中的输出创建多个值。 通过将 **copy** 元素添加到模板的 outputs 节，可以在部署过程中动态返回许多项。

还可以将 copy 用于[资源](copy-resources.md)、[资源中的属性](copy-properties.md)，以及[变量](copy-variables.md)。

## <a name="syntax"></a>语法

copy 元素采用以下常规格式：

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

**count** 属性指定要对该输出值进行的迭代次数。

**input** 属性指定要重复的属性。 你将创建一个由 **input** 属性中的值构造的元素数组。 它可以是单个属性（例如字符串），也可以是具有多个属性的对象。

## <a name="copy-limits"></a>复制限制

count 不能超过 800。

count 不能为负数。 如果使用最新版本的 Azure CLI、PowerShell 或 REST API 部署模板，则可以为零。 具体来说，您必须使用：

* Azure PowerShell **2.6**或更高版本
* Azure CLI **2.0.74**或更高版本
* REST API 版本**2019-05-10**或更高版本
* 对于部署资源类型，[链接部署](linked-templates.md)必须使用 API 版本**2019-05-10**或更高版本

更早版本的 PowerShell、CLI 和 REST API 不支持将 count 设为零。

## <a name="outputs-iteration"></a>输出迭代

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

* 若要完成教程，请参阅[教程：使用 ARM 模板创建多个资源实例](template-tutorial-create-multiple-instances.md)。
* 有关 copy 元素的其他用法，请参阅：
  * [ARM 模板中的资源迭代](copy-resources.md)
  * [ARM 模板中的属性迭代](copy-properties.md)
  * [ARM 模板中的变量迭代](copy-variables.md)
* 若要了解有关模板区段的信息，请参阅[创作 ARM 模板](template-syntax.md)。
* 若要了解如何部署模板，请参阅[使用 ARM 模板部署应用程序](deploy-powershell.md)。

