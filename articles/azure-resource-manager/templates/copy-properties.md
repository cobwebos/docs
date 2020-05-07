---
title: 定义属性的多个实例
description: 在资源上创建属性时，可以使用 Azure 资源管理器模板中的复制操作进行多次迭代。
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 9fde2ecf14bc5b29bb31ffa78e067b780438578a
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583405"
---
# <a name="property-iteration-in-arm-templates"></a>ARM 模板中的属性迭代

本文介绍如何在 Azure 资源管理器 (ARM) 模板中创建一个属性的多个实例。 通过将 **copy** 元素添加到模板中资源的 properties 节，可以在部署过程中动态设置属性的项数。 还可以避免重复模板语法。

还可以将 copy 用于 [resources](copy-resources.md)、[variables](copy-variables.md) 和 [outputs](copy-outputs.md)。

## <a name="syntax"></a>语法

copy 元素采用以下常规格式：

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

对于 **name**，提供要创建的资源属性的名称。

**count** 属性指定要对该属性进行的迭代次数。

**input** 属性指定要重复的属性。 你将创建一个由 **input** 属性中的值构造的元素数组。

## <a name="copy-limits"></a>复制限制

count 不能超过 800。

count 不能为负数。 如果使用最新版本的 Azure CLI、PowerShell 或 REST API 部署模板，则可以为零。 具体来说，您必须使用：

* Azure PowerShell **2.6**或更高版本
* Azure CLI **2.0.74**或更高版本
* REST API 版本**2019-05-10**或更高版本
* 对于部署资源类型，[链接部署](linked-templates.md)必须使用 API 版本**2019-05-10**或更高版本

更早版本的 PowerShell、CLI 和 REST API 不支持将 count 设为零。

## <a name="property-iteration"></a>属性迭代

以下示例演示如何将 `copy` 应用到虚拟机上的 dataDisks 属性：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 16,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

请注意，在属性迭代中使用 `copyIndex` 时，必须提供迭代的名称。 属性迭代还支持 offset 参数。 偏移量必须在迭代名称之后，例如 copyIndex('dataDisks', 1)。

Resource Manager 在部署期间会扩展 `copy` 数组。 该数组的名称将成为属性的名称。 输入值将成为对象属性。 已部署的模板将成为：

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

处理数组时可以使用复制操作，因为可对数组中的每个元素执行迭代操作。 可以对数组使用 `length` 函数来指定迭代计数，并使用 `copyIndex` 来检索数组中的当前索引。

以下示例模板为作为数组传入的数据库创建故障转移组。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "primaryServerName": {
            "type": "string"
        },
        "secondaryServerName": {
            "type": "string"
        },
        "databaseNames": {
            "type": "array",
            "defaultValue": [
                "mydb1",
                "mydb2",
                "mydb3"
            ]
        }
    },
    "variables": {
        "failoverName": "[concat(parameters('primaryServerName'),'/', parameters('primaryServerName'),'failovergroups')]"
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers/failoverGroups",
            "apiVersion": "2015-05-01-preview",
            "name": "[variables('failoverName')]",
            "properties": {
                "readWriteEndpoint": {
                    "failoverPolicy": "Automatic",
                    "failoverWithDataLossGracePeriodMinutes": 60
                },
                "readOnlyEndpoint": {
                    "failoverPolicy": "Disabled"
                },
                "partnerServers": [
                    {
                        "id": "[resourceId('Microsoft.Sql/servers', parameters('secondaryServerName'))]"
                    }
                ],
                "copy": [
                    {
                        "name": "databases",
                        "count": "[length(parameters('databaseNames'))]",
                        "input": "[resourceId('Microsoft.Sql/servers/databases', parameters('primaryServerName'), parameters('databaseNames')[copyIndex('databases')])]"
                    }
                ]
            }
        }
    ],
    "outputs": {
    }
}
```

copy 元素是一个数组，因此，可以为资源指定多个属性。

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

可将资源迭代和属性迭代结合使用。 按名称引用属性迭代。

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="example-templates"></a>示例模板

以下示例显示了为一个属性创建多个值的常见方案。

|模板  |说明  |
|---------|---------|
|[数据磁盘数可变的 VM 部署](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |通过虚拟机部署多个数据磁盘。 |

## <a name="next-steps"></a>后续步骤

* 若要完成教程，请参阅[教程：使用 ARM 模板创建多个资源实例](template-tutorial-create-multiple-instances.md)。
* 有关 copy 元素的其他用法，请参阅：
  * [ARM 模板中的资源迭代](copy-resources.md)
  * [ARM 模板中的变量迭代](copy-variables.md)
  * [ARM 模板中的输出迭代](copy-outputs.md)
* 若要了解有关模板区段的信息，请参阅[创作 ARM 模板](template-syntax.md)。
* 若要了解如何部署模板，请参阅[使用 ARM 模板部署应用程序](deploy-powershell.md)。

