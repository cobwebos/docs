---
title: 模板中的参数
description: 介绍如何在 Azure 资源管理器模板中定义参数。
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 3c0c40d5ddb47144fb42c99a9377d1b6e66854b9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483903"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Azure 资源管理器模板中的参数

本文介绍如何定义和使用 Azure 资源管理器模板中的参数。 通过为参数提供不同的值，可以为不同的环境重复使用模板。

资源管理器在启动部署操作之前解析参数值。 无论在模板中使用参数，资源管理器会将其替换为解析的值。

## <a name="define-parameter"></a>定义参数

以下示例展示了一个简单的参数定义。 它定义了一个名为**storageSKU**的参数。 参数是一个字符串值，只接受对其预期使用有效的值。 在部署过程中未提供任何值时，参数使用默认值。

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
  }
}
```

## <a name="use-parameter"></a>使用参数

在模板中，使用[parameters](template-functions-deployment.md#parameters)函数引用参数的值。 在下面的示例中，参数值用于为存储帐户设置 SKU。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="template-functions"></a>模板功能

为参数指定默认值时，可以使用大多数模板函数。 可以使用另一个参数值来生成默认值。 以下模板演示了如何在默认值中使用函数。 如果没有为站点提供任何名称，则会创建一个唯一的字符串值，并将其追加到**site**。 如果没有为主机计划提供名称，它将使用站点的值并追加 **-plan**。

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

不能在 parameters 节中使用[reference](template-functions-resource.md#reference)函数或任何[list](template-functions-resource.md#list)函数。 这些函数获取资源的运行时状态，在解析参数时不能在部署之前执行。

## <a name="objects-as-parameters"></a>对象作为参数

通过将相关值作为对象传入，可以更轻松地对这些值进行组织。 此方式还可以减少模板中的参数的数量。

下面的示例演示一个作为对象的参数。 默认值显示对象的所需属性。

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

通过使用点运算符引用该对象的属性。

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="example-templates"></a>示例模板

下面的示例演示使用参数的方案。

|模板  |Description  |
|---------|---------|
|[包含用于默认值的函数的参数](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | 演示了为参数定义默认值时如何使用模板函数。 该模板不部署任何资源。 它构造参数值并返回这些值。 |
|[参数对象](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | 演示了将对象用于参数。 该模板不部署任何资源。 它构造参数值并返回这些值。 |


## <a name="next-steps"></a>后续步骤

* 若要了解参数的可用属性，请参阅[了解 Azure 资源管理器模板的结构和语法](template-syntax.md)。
* 若要了解如何以文件的形式传递参数值，请参阅[创建资源管理器参数文件](parameter-files.md)。
* 有关创建参数的建议，请参阅[最佳做法-参数](template-best-practices.md#parameters)。
