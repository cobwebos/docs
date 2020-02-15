---
title: 定义变量的多个实例
description: 在创建变量时，请使用 Azure 资源管理器模板中的复制操作多次迭代。
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 9e252a0b9721ffec99535c5d30e609e12e9e67eb
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210809"
---
# <a name="variable-iteration-in-azure-resource-manager-templates"></a>Azure 资源管理器模板中的变量迭代

本文介绍如何为 Azure 资源管理器模板中的变量创建多个值。 通过将**copy**元素添加到模板的 variables 节，你可以在部署期间动态设置变量的项数。 还应避免重复模板语法。

还可以对[资源中](copy-properties.md)的[资源](copy-resources.md)和属性使用 copy。

## <a name="variable-iteration"></a>变量迭代

Copy 元素具有以下常规格式：

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

**Name**属性是标识循环的任何值。 **Count**属性指定变量所需的迭代数。

**输入**属性指定要重复的属性。 创建一个由**input**属性中的值构造的元素数组。 它可以是单个属性（例如字符串），也可以是具有多个属性的对象。

下面的示例演示如何创建字符串值数组：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
     },
    "variables": {
        "copy": [
            {
                "name": "stringArray",
                "count": "[parameters('itemCount')]",
                "input": "[concat('item', copyIndex('stringArray', 1))]"
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('stringArray')]"
        }
    }
}
```

前面的模板返回具有以下值的数组：

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

下一个示例演示如何创建一个具有三个属性-name、diskSizeGB 和 diskIndex 的对象数组。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "copy": [
            {
                "name": "objectArray",
                "count": "[parameters('itemCount')]",
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('objectArray')]"
                }
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('objectArray')]"
        }
    }
}
```

前面的示例返回具有以下值的数组：

```json
[
    {
        "name": "myDataDisk1",
        "diskSizeGB": "1",
        "diskIndex": 0
    },
    {
        "name": "myDataDisk2",
        "diskSizeGB": "1",
        "diskIndex": 1
    },
    {
        "name": "myDataDisk3",
        "diskSizeGB": "1",
        "diskIndex": 2
    },
    {
        "name": "myDataDisk4",
        "diskSizeGB": "1",
        "diskIndex": 3
    },
    {
        "name": "myDataDisk5",
        "diskSizeGB": "1",
        "diskIndex": 4
    }
]
```

> [!NOTE]
> 变量迭代支持 offset 参数。 偏移量必须晚于迭代的名称，如 copyIndex （' diskNames '，1）。 如果不提供 offset 值，则第一个实例的默认值将默认为0。
>

还可以在变量中使用 copy 元素。 下面的示例创建一个对象，该对象将数组作为其值之一。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "topLevelObject": {
            "sampleProperty": "sampleValue",
            "copy": [
                {
                    "name": "disks",
                    "count": "[parameters('itemCount')]",
                    "input": {
                        "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                        "diskSizeGB": "1",
                        "diskIndex": "[copyIndex('disks')]"
                    }
                }
            ]
        }
    },
    "resources": [],
    "outputs": {
        "objectResult": {
            "type": "object",
            "value": "[variables('topLevelObject')]"
        }
    }
}
```

前面的示例返回具有以下值的对象：

```json
{
    "sampleProperty": "sampleValue",
    "disks": [
        {
            "name": "myDataDisk1",
            "diskSizeGB": "1",
            "diskIndex": 0
        },
        {
            "name": "myDataDisk2",
            "diskSizeGB": "1",
            "diskIndex": 1
        },
        {
            "name": "myDataDisk3",
            "diskSizeGB": "1",
            "diskIndex": 2
        },
        {
            "name": "myDataDisk4",
            "diskSizeGB": "1",
            "diskIndex": 3
        },
        {
            "name": "myDataDisk5",
            "diskSizeGB": "1",
            "diskIndex": 4
        }
    ]
}
```

下一个示例显示了可以使用变量的不同方式。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="copy-limits"></a>复制限制

计数不能超过800。

计数不能为负数。 如果部署 Azure PowerShell 2.6 或更高版本的模板，Azure CLI 2.0.74 或更高版本，或者 REST API 版本**2019-05-10**或更高版本，则可以将 count 设置为零。 PowerShell、CLI 和 REST API 的早期版本不支持计数为零。

## <a name="example-templates"></a>示例模板

下面的示例演示了为变量创建多个值的常见方案。

|模板  |说明  |
|---------|---------|
|[复制变量](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |演示对变量进行迭代的不同方法。 |
|[多个安全规则](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |将多个安全规则部署到网络安全组。 这会从参数构造安全规则。 有关参数，请参阅[多个 NSG 参数文件](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)。 |

## <a name="next-steps"></a>后续步骤

* 要查看教程，请参阅[教程：使用资源管理器模板创建多个资源实例](template-tutorial-create-multiple-instances.md)。
* 有关 copy 元素的其他用法，请参阅 azure[中的资源迭代资源管理器模板](copy-resources.md)和[azure 资源管理器模板中的属性迭代](copy-properties.md)。
* 若要了解有关模板区段的信息，请参阅[创作 Azure 资源管理器模板](template-syntax.md)。
* 若要了解如何部署模板，请参阅[使用 Azure 资源管理器模板部署应用程序](deploy-powershell.md)。

