---
title: 模板中的变量
description: 介绍如何在 Azure 资源管理器模板中定义变量。
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 5bd7acd759c553e629febdb141aefd63cfa4dd4b
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149085"
---
# <a name="variables-in-azure-resource-manager-template"></a>Azure 资源管理器模板中的变量

本文介绍如何在 Azure 资源管理器模板中定义和使用变量。 可以使用变量来简化模板。 可以定义一个包含复杂表达式的变量，而不必在整个模板中重复使用复杂表达式。 然后，可以在整个模板中根据需要引用该变量。

资源管理器会在启动部署操作之前解析变量。 只要在模板中使用变量，资源管理器就会将其替换为解析的值。

## <a name="define-variable"></a>定义变量

以下示例介绍了变量定义。 它为存储帐户名称创建字符串值。 它使用多个模板函数来获取参数值，并将其连接到唯一字符串。

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

不能在 variables 节中使用 [reference](resource-group-template-functions-resource.md#reference) 函数或任何 [list](resource-group-template-functions-resource.md#list) 函数。 在解析变量时，这些函数获取资源的运行时状态，不能在部署之前执行。

## <a name="use-variable"></a>使用变量

在模板中，可以使用 [variables](resource-group-template-functions-deployment.md#variables) 函数引用参数值。 以下示例演示如何使用资源属性的变量。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="configuration-variables"></a>配置变量

可以定义变量来保存配置环境所需的相关值。 可以将变量定义为一个包含值的对象。 以下示例演示的对象包含的值适用于两个环境 - **test** 和 **prod**。

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

若要检索指定环境的设置，请将变量和参数一起使用。

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>示例模板

以下示例演示了使用变量的方案。

|模板  |说明  |
|---------|---------|
| [变量定义](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | 演示不同类型的变量。 该模板不部署任何资源。 它构造变量值并返回这些值。 |
| [配置变量](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | 演示如何使用定义配置值的变量。 该模板不部署任何资源。 它构造变量值并返回这些值。 |
| [网络安全规则](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json)和[参数文件](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | 以正确的格式构造数组，以便将安全规则分配给网络安全组。 |

## <a name="next-steps"></a>后续步骤

* 若要了解变量的可用属性，请参阅[了解 Azure 资源管理器模板的结构和语法](resource-group-authoring-templates.md)。
* 有关创建变量的建议，请参阅[最佳做法 - 变量](template-best-practices.md#variables)。
