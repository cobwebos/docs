---
title: Azure 资源管理器模板变量 | Microsoft Docs
description: 介绍如何使用声明性 JSON 语法定义 Azure 资源管理器模板中的变量。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2019
ms.author: tomfitz
ms.openlocfilehash: 50feca90d375d6afd3b04afe019ad9f9025f19dc
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56308564"
---
# <a name="variables-section-of-azure-resource-manager-templates"></a>Azure 资源管理器模板的变量部分
在 variables 节中构造可在整个模板中使用的值。 不需要定义变量，但使用变量可以减少复杂的表达式，从而简化模板。

## <a name="define-and-use-a-variable"></a>定义和使用变量

以下示例介绍了变量定义。 它为存储帐户名称创建字符串值。 它使用多个模板函数获取参数值，并将其连接到唯一字符串。

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

定义资源时需使用该变量。

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

## <a name="available-definitions"></a>可用定义

前述示例介绍了一种定义变量的方法。 可以使用以下任一定义：

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    },
    "<variable-object-name>": {
        "copy": [
            {
                "name": "<name-of-array-property>",
                "count": <number-of-iterations>,
                "input": <object-or-value-to-repeat>
            }
        ]
    },
    "copy": [
        {
            "name": "<variable-array-name>",
            "count": <number-of-iterations>,
            "input": <object-or-value-to-repeat>
        }
    ]
}
```

## <a name="configuration-variables"></a>配置变量

可以使用复杂的 JSON 类型定义环境的相关值。 

```json
"variables": {
    "environmentSettings": {
        "test": {
            "instanceSize": "Small",
            "instanceCount": 1
        },
        "prod": {
            "instanceSize": "Large",
            "instanceCount": 4
        }
    }
},
```

在参数中，可以创建一个值，用于指示要使用的配置值。

```json
"parameters": {
    "environmentName": {
        "type": "string",
        "allowedValues": [
          "test",
          "prod"
        ]
    }
},
```

通过以下方式检索当前设置：

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="use-copy-element-in-variable-definition"></a>在变量定义中使用 copy 元素

若要创建变量的多个实例，请在变量部分中使用 `copy` 属性。 可以创建一个由 `input` 属性中的值构造的元素数组。 可以在变量中使用 `copy` 属性，或在变量部分的顶层使用该属性。 在变量迭代中使用 `copyIndex` 时，必须提供迭代的名称。

以下示例演示如何使用该复制：

```json
"variables": {
  "disk-array-on-object": {
    "copy": [
      {
        "name": "disks",
        "count": 3,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('disks')]"
        }
      }
    ]
  },
  "copy": [
    {
      "name": "disks-top-level-array",
      "count": 3,
      "input": {
        "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
        "diskSizeGB": "1",
        "diskIndex": "[copyIndex('disks-top-level-array')]"
      }
    },
    {
      "name": "top-level-string-array",
      "count": 5,
      "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
    }
  ]
},
```

评估复制表达式后，变量 disk-array-on-object 包含下面的对象，该对象具有名为“磁盘”的数组：

```json
{
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
    }
  ]
}
```

变量 disks-top-level-array 包含以下数组：

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
  }
]
```

变量 top-level-string-array 包含以下数组：

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

需要获取参数值并将其映射到资源值时，使用副本将很有效。 以下示例对参数值进行格式设置，以便用于定义安全规则：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "securityRules": {
      "type": "array"
    }
  },
  "variables": {
    "copy": [
      {
        "name": "securityRules",
        "count": "[length(parameters('securityRules'))]",
        "input": {
          "name": "[parameters('securityRules')[copyIndex('securityRules')].name]",
          "properties": {
            "description": "[parameters('securityRules')[copyIndex('securityRules')].description]",
            "priority": "[parameters('securityRules')[copyIndex('securityRules')].priority]",
            "protocol": "[parameters('securityRules')[copyIndex('securityRules')].protocol]",
            "sourcePortRange": "[parameters('securityRules')[copyIndex('securityRules')].sourcePortRange]",
            "destinationPortRange": "[parameters('securityRules')[copyIndex('securityRules')].destinationPortRange]",
            "sourceAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].sourceAddressPrefix]",
            "destinationAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].destinationAddressPrefix]",
            "access": "[parameters('securityRules')[copyIndex('securityRules')].access]",
            "direction": "[parameters('securityRules')[copyIndex('securityRules')].direction]"
          }
        }
      }
    ]
  },
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location": "[resourceGroup().location]",
      "properties": {
        "securityRules": "[variables('securityRules')]"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="example-templates"></a>示例模板

这些示例模板演示了一些使用变量的情况。 部署这些模板，测试在不同情况下如何处理变量。 

|模板  |说明  |
|---------|---------|
| [变量定义](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | 演示不同类型的变量。 该模板不部署任何资源。 它构造变量值并返回这些值。 |
| [配置变量](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | 演示如何使用定义配置值的变量。 该模板不部署任何资源。 它构造变量值并返回这些值。 |
| [网络安全规则](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json)和[参数文件](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | 以正确的格式构造数组，以便将安全规则分配给网络安全组。 |


## <a name="next-steps"></a>后续步骤
* 若要查看许多不同类型的解决方案的完整模型，请参阅 [Azure Quickstart Templates](https://azure.microsoft.com/documentation/templates/)（Azure 快速入门模板）。
* 有关用户可以使用的来自模板中的函数的详细信息，请参阅 [Azure 资源管理器模板函数](resource-group-template-functions.md)。
* 有关创建模板的建议，请参阅 [Azure 资源管理器模板的最佳做法](template-best-practices.md)。
* 可能需要使用不同资源组中的资源。 使用跨多个资源组共享的存储帐户或虚拟网络时，这种情况很常见。 有关详细信息，请参阅 [resourceId 函数](resource-group-template-functions-resource.md#resourceid)。