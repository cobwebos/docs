---
title: "Azure 资源管理器模板的结构和语法 | Microsoft Docs"
description: "使用声明性 JSON 语法描述 Azure 资源管理器模板的结构和属性。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2017
ms.author: tomfitz
ms.openlocfilehash: b0bc5abd768be0fa5876aaef108cd71a15d94510
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2017
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>了解 Azure 资源管理器模板的结构和语法
本文介绍 Azure 资源管理器模板的结构。 演示了模板的不同部分，以及可在相应部分使用的属性。 模板中包含可用于为部署构造值的 JSON 和表达式。 有关创建模板的分步教程，请参阅[创建第一个 Azure 资源管理器模板](resource-manager-create-first-template.md)。

## <a name="template-format"></a>模板格式
使用最简单的结构时，模板包含以下元素：

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "resources": [  ],
    "outputs": {  }
}
```

| 元素名称 | 必选 | 说明 |
|:--- |:--- |:--- |
| $schema |是 |描述模板语言版本的 JSON 架构文件所在的位置。 使用上一示例中所示的 URL。 |
| contentVersion |是 |模板的版本（例如 1.0.0.0）。 可为此元素提供任意值。 使用模板部署资源时，此值可用于确保使用正确的模板。 |
| parameters |否 |执行部署以自定义资源部署时提供的值。 |
| variables |否 |在模板中用作 JSON 片段以简化模板语言表达式的值。 |
| 资源 |是 |已在资源组中部署或更新的资源类型。 |
| outputs |否 |部署后返回的值。 |

每个元素均包含可设置的属性。 下例包含一个模板的完整语法：

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  
        "<parameter-name>" : {
            "type" : "<type-of-parameter-value>",
            "defaultValue": "<default-value-of-parameter>",
            "allowedValues": [ "<array-of-allowed-values>" ],
            "minValue": <minimum-value-for-int>,
            "maxValue": <maximum-value-for-int>,
            "minLength": <minimum-length-for-string-or-array>,
            "maxLength": <maximum-length-for-string-or-array-parameters>,
            "metadata": {
                "description": "<description-of-the parameter>" 
            }
        }
    },
    "variables": {
        "<variable-name>": "<variable-value>",
        "<variable-object-name>": {
            <variable-complex-type-value>
        },
        "<variable-object-name>": {
            "copy": [
                {
                    "name": "<name-of-array-property>",
                    "count": <number-of-iterations>,
                    "input": {
                        <properties-to-repeat>
                    }
                }
            ]
        },
        "copy": [
            {
                "name": "<variable-array-name>",
                "count": <number-of-iterations>,
                "input": {
                    <properties-to-repeat>
                }
            }
        ]
    },
    "resources": [
      {
          "condition": "<boolean-value-whether-to-deploy>",
          "apiVersion": "<api-version-of-resource>",
          "type": "<resource-provider-namespace/resource-type-name>",
          "name": "<name-of-the-resource>",
          "location": "<location-of-resource>",
          "tags": {
              "<tag-name1>": "<tag-value1>",
              "<tag-name2>": "<tag-value2>"
          },
          "comments": "<your-reference-notes>",
          "copy": {
              "name": "<name-of-copy-loop>",
              "count": "<number-of-iterations>",
              "mode": "<serial-or-parallel>",
              "batchSize": "<number-to-deploy-serially>"
          },
          "dependsOn": [
              "<array-of-related-resource-names>"
          ],
          "properties": {
              "<settings-for-the-resource>",
              "copy": [
                  {
                      "name": ,
                      "count": ,
                      "input": {}
                  }
              ]
          },
          "resources": [
              "<array-of-child-resources>"
          ]
      }
    ],
    "outputs": {
        "<outputName>" : {
            "type" : "<type-of-output-value>",
            "value": "<output-value-expression>"
        }
    }
}
```

本文稍后将更详细地介绍模板的各个节。

## <a name="syntax"></a>语法
模板的基本语法为 JSON。 但是，表达式和函数扩展了模板中可用的 JSON 值。  表达式在 JSON 字符串文本中编写，其中第一个和最后一个字符分别是 `[` 和 `]` 括号。 部署模板时会计算表达式的值。 尽管编写为字符串文本，但表达式的计算结果可以是不同的 JSON 类型，例如数组或整数，具体取决于实际的表达式。  要使用一个括号 `[` 在开头括住文本字符串但不将其解释为表达式，请额外添加一个括号，使字符串以 `[[` 开头。

通常，会将表达式与函数一起使用，以执行用于配置部署的操作。 如同在 JavaScript 中一样，函数调用的格式为 `functionName(arg1,arg2,arg3)`。 使用点和 [index] 运算符引用属性。

以下示例演示如何在构造值时使用一些函数：

```json
"variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
}
```

有关模板函数的完整列表，请参阅 [Azure 资源管理器模板函数](resource-group-template-functions.md)。 

## <a name="parameters"></a>parameters
在模板的 parameters 节中，可以指定在部署资源时能够输入的值。 提供针对特定环境（例如开发、测试和生产环境）定制的参数值可以自定义部署。 无需在模板中提供参数，但如果没有参数，模板始终部署具有相同名称、位置和属性的相同资源。

以下示例展示了一个简单的参数定义：

```json
"parameters": {
  "siteNamePrefix": {
    "type": "string",
    "metadata": {
      "description": "The name prefix of the web app that you wish to create."
    }
  },
},
```

有关定义参数的信息，请参阅 [Azure 资源管理器模板的参数部分](resource-manager-templates-parameters.md)。

## <a name="variables"></a>变量
在 variables 节中构造可在整个模板中使用的值。 不需要定义变量，但使用变量可以减少复杂的表达式，从而简化模板。

以下示例展示了一个简单的变量定义：

```json
"variables": {
  "webSiteName": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
},
```

有关定义变量的信息，请参阅 [Azure 资源管理器模板的变量部分](resource-manager-templates-variables.md)。

## <a name="resources"></a>资源
在 resources 节，可以定义部署或更新的资源。 本部分可能比较复杂，因为必须了解所部署类型才能提供正确的值。

```json
"resources": [
  {
    "apiVersion": "2016-08-01",
    "name": "[variables('webSiteName')]",
    "type": "Microsoft.Web/sites",
    "location": "[resourceGroup().location]",
    "properties": {
      "serverFarmId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Web/serverFarms/<plan-name>"
    }
  }
],
```

有关详细信息，请参阅 [Azure 资源管理器模板的资源部分](resource-manager-templates-resources.md)。

## <a name="outputs"></a>Outputs
在 Outputs 节中，可以指定从部署返回的值。 例如，可能会返回用于访问已部署资源的 URI。

```json
"outputs": {
  "newHostName": {
    "type": "string",
    "value": "[reference(variables('webSiteName')).defaultHostName]"
  }
}
```

有关详细信息，请参阅 [Azure 资源管理器模板的输出部分](resource-manager-templates-outputs.md)。

## <a name="template-limits"></a>模板限制

将模板大小限制为 1 MB 以内，每个参数文件大小限制为 64 KB 以内。 已完成对迭代资源定义、变量值和参数值的扩展后，1 MB 的限制将适用于该模板的最终状态。 

还将受限于：

* 256 个参数
* 256 个变量
* 800 个资源（包括副本计数）
* 64 个输出值
* 模板表达式中 24,576 个字符

通过使用嵌套模板，可超出某些模板限制。 有关详细信息，请参阅[部署 Azure 资源时使用链接的模板](resource-group-linked-templates.md)。 若要减少参数、变量或输出的数量，可以将几个值合并为一个对象。 有关详细信息，请参阅[对象即参数](resource-manager-objects-as-parameters.md)。

## <a name="next-steps"></a>后续步骤
* 若要查看许多不同类型的解决方案的完整模型，请参阅 [Azure Quickstart Templates](https://azure.microsoft.com/documentation/templates/)（Azure 快速入门模板）。
* 有关用户可以使用的来自模板中的函数的详细信息，请参阅 [Azure 资源管理器模板函数](resource-group-template-functions.md)。
* 要在部署期间合并多个模板，请参阅[将已链接的模板与 Azure 资源管理器配合使用](resource-group-linked-templates.md)。
* 可能需要使用不同资源组中的资源。 使用跨多个资源组共享的存储帐户或虚拟网络时，此方案很常见。 有关详细信息，请参阅 [resourceId 函数](resource-group-template-functions-resource.md#resourceid)。
