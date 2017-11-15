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
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 85fff4c8c5a68a4ebaa63b263e90d0220c273e23
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2017
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>了解 Azure 资源管理器模板的结构和语法
本主题介绍 Azure 资源管理器模板的结构， 演示了模板的不同部分，以及可在相应部分使用的属性。 模板中包含可用于为部署构造值的 JSON 和表达式。 有关创建模板的分步教程，请参阅[创建第一个 Azure 资源管理器模板](resource-manager-create-first-template.md)。

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
| 参数 |否 |执行部署以自定义资源部署时提供的值。 |
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
        "<variable-name>": { 
            <variable-complex-type-value> 
        }
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

本主题稍后将更详细地介绍模板的各个节。

## <a name="expressions-and-functions"></a>表达式和函数
模板的基本语法为 JSON。 但是，表达式和函数扩展了模板中可用的 JSON 值。  表达式在 JSON 字符串文本中编写，其中第一个和最后一个字符分别是 `[` 和 `]` 括号。 部署模板时会计算表达式的值。 尽管编写为字符串文本，但表达式的计算结果可以是不同的 JSON 类型，例如数组或整数，具体取决于实际的表达式。  要使用一个括号 `[` 在开头括住文本字符串但不将其解释为表达式，请额外添加一个括号，使字符串以 `[[` 开头。

通常，会将表达式与函数一起使用，以执行用于配置部署的操作。 如同在 JavaScript 中一样，函数调用的格式为 `functionName(arg1,arg2,arg3)`。 使用点和 [index] 运算符引用属性。

以下示例演示如何在构造值时使用一些函数：

```json
"variables": {
    "location": "[resourceGroup().location]",
    "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
    "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

有关模板函数的完整列表，请参阅 [Azure 资源管理器模板函数](resource-group-template-functions.md)。 

## <a name="parameters"></a>parameters
在模板的 parameters 节中，可以指定在部署资源时能够输入的值。 提供针对特定环境（例如开发、测试和生产环境）定制的参数值可以自定义部署。 无需在模板中提供参数，但如果没有参数，模板始终部署具有相同名称、位置和属性的相同资源。

使用以下结构定义参数：

```json
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
}
```

| 元素名称 | 必选 | 说明 |
|:--- |:--- |:--- |
| parameterName |是 |参数的名称。 必须是有效的 JavaScript 标识符。 |
| type |是 |参数值的类型。 请参阅允许类型的列表（此表后面）。 |
| defaultValue |否 |参数的默认值，如果没有为参数提供任何值。 |
| allowedValues |否 |用来确保提供正确值的参数的允许值数组。 |
| minValue |否 |int 类型参数的最小值，此值是包容性的。 |
| maxValue |否 |int 类型参数的最大值，此值是包容性的。 |
| minLength |否 |string、secureString 和 array 类型参数的最小长度，此值是包容性的。 |
| maxLength |否 |string、secureString 和 array 类型参数的最大长度，此值是包容性的。 |
| description |否 |通过门户向用户显示的参数的说明。 |

允许的类型和值是：

* **string**
* **secureString**
* **int**
* **bool**
* **object** 
* **secureObject**
* **array**

要将某个参数指定为可选，请提供 defaultValue（可以为空字符串）。 

如果在模板中指定的参数名称与部署模板时所用命令中的参数匹配，则可能会对提供的值造成混淆。 Resource Manager 解决此混淆问题的方式是将后缀 **FromTemplate** 添加到模板参数。 例如，如果在模板中包括一个名为“ResourceGroupName”的参数，则该参数会与 [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) cmdlet 中的“ResourceGroupName”参数冲突。 在部署期间，系统会提示用户提供 **ResourceGroupNameFromTemplate** 的值。 通常，为避免此类混乱，不应按部署操作所用的参数名称命令参数。

> [!NOTE]
> 所有密码、密钥和其他机密信息应使用 **secureString** 类型。 要将敏感数据传入 JSON 对象，请使用 **secureObject** 类型。 部署资源后，无法读取 secureString 或 secureObject 类型的模板参数。 
> 
> 例如，部署历史记录中的以下条目会显示字符串和对象的值，但不会显示 secureString 和 secureObject 的值。
>
> ![显示部署值](./media/resource-group-authoring-templates/show-parameters.png)  
>

以下示例演示如何定义参数：

```json
"parameters": {
    "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
    },
    "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
    },
    "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
    },
    "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
    }
}
```

若要了解如何在部署过程中输入参数值，请参阅[使用 Azure 资源管理器模板部署应用程序](resource-group-template-deploy.md)。 

## <a name="variables"></a>变量
在 variables 节中构造可在整个模板中使用的值。 不需要定义变量，但使用变量可以减少复杂的表达式，从而简化模板。

使用以下结构定义变量：

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    }
}
```

以下示例演示如何定义从两个参数值构造出的变量：

```json
"variables": {
    "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
}
```

下一个示例演示一个属于复杂的 JSON 类型的变量，以及从其他变量构造出的变量：

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
"variables": {
    "environmentSettings": {
        "test": {
            "instancesSize": "Small",
            "instancesCount": 1
        },
        "prod": {
            "instancesSize": "Large",
            "instancesCount": 4
        }
    },
    "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
    "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
    "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
}
```

你可以使用 copy 语法创建带有多个元素数组的变量。 为元素数量提供一个数字。 每个元素的属性都包含在 input 对象中。 你可以在变量中使用 copy，或用其创建变量。 下例说明了这两种方法：

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
        }
      ]
    },
    "copy": [
      {
        "name": "disks-top-level-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('disks-top-level-array')]"
        }
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
    "exampleArray": {
      "value": "[variables('disks-top-level-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="resources"></a>资源
在 resources 节，可以定义部署或更新的资源。 本部分可能比较复杂，因为必须了解所部署类型才能提供正确的值。 有关需要设置的特定资源值（apiVersion、type 和 properties），请参阅[在 Azure 资源管理器模板中定义资源](/azure/templates/)。 

使用以下结构定义资源：

```json
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
]
```

| 元素名称 | 必选 | 说明 |
|:--- |:--- |:--- |
| 条件 | 否 | 指示是否部署资源的布尔值。 |
| apiVersion |是 |用于创建资源的 REST API 版本。 |
| type |是 |资源的类型。 此值是资源提供程序的命名空间和资源类型（例如 **Microsoft.Storage/storageAccounts**）的组合。 |
| 名称 |是 |资源的名称。 该名称必须遵循 RFC3986 中定义的 URI 构成部分限制。 此外，向第三方公开资源名称的 Azure 服务会验证名称，以确保它不是尝试窃取另一个身份。 |
| location |多种多样 |提供的资源支持的地理位置。 可以选择任何可用位置，但通常最好选取一个接近用户的位置。 通常，在同一区域放置彼此交互的资源也很有用。 大多数资源类型需要一个位置，但某些类型 （如角色分配）不需要位置。 请参阅[在 Azure 资源管理器模板中设置资源位置](resource-manager-template-location.md)。 |
| 标记 |否 |与资源关联的标记。 请参阅[标记 Azure 资源管理器模板中的资源](resource-manager-template-tags.md)。 |
| 注释 |否 |用于描述模板中资源的注释 |
| 复制 |否 |需要多个实例时应创建的资源数。 默认模式为并行。 在不想同时部署所有资源时，请指定为串行模式。 有关详细信息，请参阅[在 Azure 资源管理器中创建多个资源实例](resource-group-create-multiple.md)。 |
| dependsOn |否 |必须在部署此资源前部署的资源。 Resource Manager 评估资源之间的依赖关系，并根据正确顺序进行部署。 如果资源互不依赖，则会并行部署资源。 该值可以是资源名称或资源唯一标识符的逗号分隔列表。 仅列出在此模板中部署的资源。 此模板中未定义的资源必须已存在。 避免添加不必要的依赖项，因为这些依赖项可能会降低部署速度并创建循环依赖项。 有关设置依赖项的指导，请参阅[在 Azure 资源管理器模板中定义依赖项](resource-group-define-dependencies.md)。 |
| properties |否 |特定于资源的配置设置。 properties 的值与创建资源时，在 REST API 操作（PUT 方法）的请求正文中提供的值相同。 还可以指定副本数组，以创建一个属性的多个实例。 有关详细信息，请参阅[在 Azure 资源管理器中创建多个资源实例](resource-group-create-multiple.md)。 |
| 资源 |否 |依赖于所定义的资源的子资源。 只能提供父资源的架构允许的资源类型。 子资源的完全限定类型包含父资源类型，例如 **Microsoft.Web/sites/extensions**。 不隐式表示对父资源的依赖。 必须显式定义该依赖关系。 |

resources 节包含要部署的资源数组。 在每个资源内，还可以定义子资源数组。 因此，resources 节的结构可能类似于：

```json
"resources": [
  {
      "name": "resourceA",
  },
  {
      "name": "resourceB",
      "resources": [
        {
            "name": "firstChildResourceB",
        },
        {   
            "name": "secondChildResourceB",
        }
      ]
  },
  {
      "name": "resourceC",
  }
]
```      

有关定义子资源的详细信息，请参阅[在 Resource Manager 模板中设置子资源的名称和类型](resource-manager-template-child-resource.md)。

“条件”元素指定是否部署资源。 此元素的值解析为 true 或 false。 例如，若要指定是否部署新的存储帐户，请使用：

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

有关使用新资源或现有资源的示例，请参阅[新的或现有的条件模板](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResources.NewOrExisting.json)。

若要指定是使用密码还是 SSH 密钥来部署虚拟机，请在模板中定义两个虚拟机版本，并使用“条件”来区分使用情况。 传递参数，其指定部署哪种方案。

```json
{
    "condition": "[equals(parameters('passwordOrSshKey'),'password')]",
    "apiVersion": "2016-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('vmName'),'password')]",
    "properties": {
        "osProfile": {
            "computerName": "[variables('vmName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
        },
        ...
    },
    ...
},
{
    "condition": "[equals(parameters('passwordOrSshKey'),'sshKey')]",
    "apiVersion": "2016-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('vmName'),'ssh')]",
    "properties": {
        "osProfile": {
            "linuxConfiguration": {
                "disablePasswordAuthentication": "true",
                "ssh": {
                    "publicKeys": [
                        {
                            "path": "[variables('sshKeyPath')]",
                            "keyData": "[parameters('adminSshKey')]"
                        }
                    ]
                }
            }
        },
        ...
    },
    ...
}
``` 

有关使用密码或 SSH 密钥部署虚拟机的示例，请参阅[用户名或 SSH 条件模板](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResourcesUsernameOrSsh.json)。

## <a name="outputs"></a>Outputs
在 Outputs 节中，可以指定从部署返回的值。 例如，可能会返回用于访问已部署资源的 URI。

以下示例演示了输出定义的结构：

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| 元素名称 | 必选 | 说明 |
|:--- |:--- |:--- |
| outputName |是 |输出值的名称。 必须是有效的 JavaScript 标识符。 |
| type |是 |输出值的类型。 输出值支持的类型与模板输入参数相同。 |
| value |是 |评估并作为输出值返回的模板语言表达式。 |

以下示例演示了 Outputs 节中返回的值。

```json
"outputs": {
    "siteUri" : {
        "type" : "string",
        "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
    }
}
```

有关如何处理输出的详细信息，请参阅[在 Azure 资源管理器模板中共享状态](best-practices-resource-manager-state.md)。

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
