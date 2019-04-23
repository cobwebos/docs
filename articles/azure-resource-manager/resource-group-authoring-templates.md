---
title: Azure 资源管理器模板的结构和语法 | Microsoft Docs
description: 使用声明性 JSON 语法描述 Azure 资源管理器模板的结构和属性。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/18/2019
ms.author: tomfitz
ms.openlocfilehash: 94ed3c876ece827e4decd2b5b14332f5e854ab83
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004425"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>了解 Azure 资源管理器模板的结构和语法

本文介绍 Azure 资源管理器模板的结构。 演示了模板的不同部分，以及可在相应部分使用的属性。 模板中包含可用于为部署构造值的 JSON 和表达式。

本文面向对资源管理器模板有一定了解的用户， 其中提供了有关模板结构和语法的详细信息。 有关创建模板的简介，请参阅[创建第一个 Azure 资源管理器模板](resource-manager-create-first-template.md)。

## <a name="template-format"></a>模板格式

使用最简单的结构时，模板有以下元素：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| 元素名称 | 需要 | 描述 |
|:--- |:--- |:--- |
| $schema |是 |描述模板语言版本的 JSON 架构文件所在的位置。<br><br> 对于资源组部署，请使用：`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>对于订阅部署，请使用：`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |是 |模板的版本（例如 1.0.0.0）。 可为此元素提供任意值。 使用此值记录模板中的重要更改。 使用模板部署资源时，此值可用于确保使用正确的模板。 |
| apiProfile |否 | 用作资源类型 API 版本集合的 API 版本。 使用此值可以避免为模板中的每个资源指定 API 版本。 如果你指定 API 配置文件版本但不指定资源类型的 API 版本，则资源管理器将使用配置文件中为该资源类型定义的 API 版本。<br><br>将模板部署到不同的环境（例如 Azure Stack 和全球 Azure）时，API 配置文件属性非常有用。 使用 API 配置文件版本可确保模板自动使用两个环境均支持的版本。 有关最新 API 配置文件版本以及配置文件中定义的资源 API 版本的列表，请参阅 [API 配置文件](https://github.com/Azure/azure-rest-api-specs/tree/master/profile)。<br><br>有关详细信息，请参阅[使用 API 配置文件跟踪版本](templates-cloud-consistency.md#track-versions-using-api-profiles)。 |
| [parameters](#parameters) |否 |执行部署以自定义资源部署时提供的值。 |
| [variables](#variables) |否 |在模板中用作 JSON 片段以简化模板语言表达式的值。 |
| [functions](#functions) |否 |可在模板中使用的用户定义函数。 |
| [resources](#resources) |是 |已在资源组或订阅中部署/更新的资源类型。 |
| [outputs](#outputs) |否 |部署后返回的值。 |

每个元素均有可设置的属性。 本文稍后将更详细地介绍模板的各个节。

## <a name="syntax"></a>语法

模板的基本语法为 JSON。 但是，可以使用表达式来扩展模板中可用的 JSON 值。  表达式分别以方括号 `[` 与 `]` 开头和结尾。 部署模板时会计算表达式的值。 表达式可以返回字符串、整数、布尔值、数组或对象。 以下示例演示了参数默认值中的表达式：

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

在该表达式中，语法 `resourceGroup()` 调用资源管理器提供的、在模板中使用的某个函数。 如同在 JavaScript 中一样，函数调用的格式为 `functionName(arg1,arg2,arg3)`。 语法 `.location` 从该函数返回的对象中检索一个属性。

模板函数及其参数不区分大小写。 例如，Resource Manager 将 **variables('var1')** 和 **VARIABLES('VAR1')** 视为相同。 在求值时，除非函数明确修改大小写（例如，使用 toUpper 或 toLower 进行修改），否则函数将保留大小写。 某些资源类型可能会提出大小写要求，而不考虑函数求值方式。

要使用一个括号 `[` 在开头括住文本字符串但不将其解释为表达式，请额外添加一个括号，使字符串以 `[[` 开头。

若要将字符串值作为参数传递给函数，请使用单引号。

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

若要转义表达式中的双引号（例如，在模板中添加 JSON 对象），请使用反斜杠。

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

模板表达式不能超过 24,576 个字符。

有关模板函数的完整列表，请参阅 [Azure 资源管理器模板函数](resource-group-template-functions.md)。 

## <a name="parameters"></a>parameters

在模板的 parameters 节中，可以指定在部署资源时能够输入的值。 提供针对特定环境（例如开发、测试和生产环境）定制的参数值可以自定义部署。 无需在模板中提供参数，但如果没有参数，模板始终部署具有相同名称、位置和属性的相同资源。

一个模板中最多可以有 256 个参数。 如本文中所示，可以通过使用包含多个属性的对象来减少参数的数量。

### <a name="available-properties"></a>可用属性

参数的可用属性为：

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

| 元素名称 | 需要 | 描述 |
|:--- |:--- |:--- |
| parameterName |是 |参数的名称。 必须是有效的 JavaScript 标识符。 |
| type |是 |参数值的类型。 允许的类型和值为 **string**、**securestring**、**int**、**bool**、**object**、**secureObject** 和 **array**。 |
| defaultValue |否 |参数的默认值，如果没有为参数提供任何值。 |
| allowedValues |否 |用来确保提供正确值的参数的允许值数组。 |
| minValue |否 |int 类型参数的最小值，此值是包容性的。 |
| maxValue |否 |int 类型参数的最大值，此值是包容性的。 |
| minLength |否 |string、secure string 和 array 类型参数的最小长度，此值是包容性的。 |
| maxLength |否 |string、secure string 和 array 类型参数的最大长度，此值是包容性的。 |
| description |否 |通过门户向用户显示的参数的说明。 有关详细信息，请参阅[模板中的注释](#comments)。 |

### <a name="define-and-use-a-parameter"></a>定义和使用参数

以下示例展示了一个简单的参数定义。 它定义了参数的名称，并指定该参数接受字符串值。 该参数仅接受对它的预期用途有意义的值。 它指定了在部署期间未提供值时将使用的默认值。 最后，该参数包括了其用途说明。

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

在模板中，可以使用以下语法引用该参数的值：

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

### <a name="template-functions-with-parameters"></a>包含参数的模板函数

为参数指定默认值时，可以使用大多数模板函数。 可以使用另一个参数值来生成默认值。 以下模板演示了如何在默认值中使用函数：

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

不能在 parameters 节中使用 `reference` 函数。 参数在部署之前进行评估，因此，`reference` 函数无法获取资源的运行时状态。 

### <a name="objects-as-parameters"></a>对象作为参数

通过将相关值作为对象传入，可以更轻松地对这些值进行组织。 此方式还可以减少模板中的参数的数量。

在模板中定义参数，并在部署过程中指定 JSON 对象，而不是单个值。 

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

然后，通过使用点运算符引用参数的子属性。

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

### <a name="parameter-example-templates"></a>参数示例模板

这些示例模板演示了使用参数的一些方案。 请部署这些模板来测试在不同方案中参数是如何处理的。

|模板  |描述  |
|---------|---------|
|[包含用于默认值的函数的参数](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | 演示了为参数定义默认值时如何使用模板函数。 该模板不部署任何资源。 它构造参数值并返回这些值。 |
|[参数对象](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | 演示了将对象用于参数。 该模板不部署任何资源。 它构造参数值并返回这些值。 |

## <a name="variables"></a>变量

在 variables 节中构造可在整个模板中使用的值。 不需要定义变量，但使用变量可以减少复杂的表达式，从而简化模板。

### <a name="available-definitions"></a>可用定义

以下示例演示了可用于定义变量的选项：

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

有关使用 `copy` 为变量创建多个值的信息，请参阅[变量迭代](resource-group-create-multiple.md#variable-iteration)。

### <a name="define-and-use-a-variable"></a>定义和使用变量

以下示例介绍了变量定义。 它为存储帐户名称创建字符串值。 它使用多个模板函数来获取参数值，并将其连接到唯一字符串。

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

### <a name="configuration-variables"></a>配置变量

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

### <a name="variable-example-templates"></a>变量示例模板

这些示例模板演示了一些使用变量的情况。 部署这些模板，测试在不同情况下如何处理变量。 

|模板  |描述  |
|---------|---------|
| [变量定义](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | 演示不同类型的变量。 该模板不部署任何资源。 它构造变量值并返回这些值。 |
| [配置变量](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | 演示如何使用定义配置值的变量。 该模板不部署任何资源。 它构造变量值并返回这些值。 |
| [网络安全规则](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json)和[参数文件](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | 以正确的格式构造数组，以便将安全规则分配给网络安全组。 |


## <a name="functions"></a>函数

在模板中，可以创建自己的函数。 这些函数可在模板中使用。 通常，定义不想要在整个模板中重复执行的复杂表达式。 从模板中支持的表达式和[函数](resource-group-template-functions.md)创建用户定义函数。

定义用户函数时，存在一些限制：

* 该函数不能访问变量。
* 函数仅可使用函数中定义的参数。 如果在用户定义的函数中使用[参数函数](resource-group-template-functions-deployment.md#parameters)，则只能使用该函数的参数。
* 该函数不能调用其他用户定义的函数。
* 该函数不能使用[引用函数](resource-group-template-functions-resource.md#reference)。
* 该函数的参数不能具有默认值。

函数需要一个命名空间值以避免命名与模板函数发生冲突。 下面的示例演示一个返回存储帐户名称的函数：

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

可以使用以下代码调用该函数：

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="resources"></a>资源
在 resources 节，可以定义部署或更新的资源。

### <a name="available-properties"></a>可用属性

使用以下结构定义资源：

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
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
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
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
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| 元素名称 | 需要 | 描述 |
|:--- |:--- |:--- |
| 条件 | 否 | 布尔值，该值指示在此部署期间是否将预配资源。 为 `true` 时，在部署期间创建资源。 为 `false` 时，此部署将跳过资源。 请参阅[条件](#condition)。 |
| apiVersion |是 |用于创建资源的 REST API 版本。 若要确定可用值，请参阅[模板参考](/azure/templates/)。 |
| type |是 |资源的类型。 此值是资源提供程序的命名空间和资源类型（例如 **Microsoft.Storage/storageAccounts**）的组合。 若要确定可用值，请参阅[模板参考](/azure/templates/)。 资源的子资源类型的格式依赖于是否已嵌套在父资源或父资源的外部定义。 请参阅[子资源](#child-resources)。 |
| 名称 |是 |资源的名称。 该名称必须遵循 RFC3986 中定义的 URI 构成部分限制。 此外，向第三方公开资源名称的 Azure 服务会验证名称，以确保它不会尝试窃取另一身份。 资源的子资源的名称的格式依赖于是否已嵌套在父资源或父资源的外部定义。 请参阅[子资源](#child-resources)。 |
| 位置 |多种多样 |提供的资源支持的地理位置。 可以选择任何可用位置，但通常最好选取一个接近用户的位置。 通常，在同一区域放置彼此交互的资源也很有用。 大多数资源类型需要一个位置，但某些类型（如角色分配）不需要位置。 |
| 标记 |否 |与资源关联的标记。 应用标签以跨订阅按逻辑对资源进行组织。 |
| 注释 |否 |用于描述模板中资源的注释。 有关详细信息，请参阅[模板中的注释](resource-group-authoring-templates.md#comments)。 |
| 复制 |否 |需要多个实例时应创建的资源数。 默认模式为并行。 若不想同时部署所有资源，请指定为串行模式。 有关详细信息，请参阅[在 Azure 资源管理器中创建多个资源实例](resource-group-create-multiple.md)。 |
| dependsOn |否 |必须在部署此资源前部署的资源。 Resource Manager 评估资源之间的依赖关系，并根据正确顺序进行部署。 如果资源互不依赖，则会并行部署资源。 该值可以是资源名称或资源唯一标识符的逗号分隔列表。 仅列出在此模板中部署的资源。 未在此模板中定义的资源必须是已存在的资源。 避免添加不必要的依赖项，因为这些依赖项可能会降低部署速度并创建循环依赖项。 有关设置依赖项的指导，请参阅[在 Azure 资源管理器模板中定义依赖项](resource-group-define-dependencies.md)。 |
| properties |否 |特定于资源的配置设置。 properties 的值与创建资源时，在 REST API 操作（PUT 方法）的请求正文中提供的值相同。 还可以指定副本数组，为一个属性创建多个实例。 若要确定可用值，请参阅[模板参考](/azure/templates/)。 |
| sku | 否 | 某些资源接受定义了要部署的 SKU 的值。 例如，可以为存储帐户指定冗余类型。 |
| kind | 否 | 某些资源接受定义了你部署的资源类型的值。 例如，可以指定要创建的 Cosmos DB 的类型。 |
| 计划 | 否 | 某些资源接受定义了要部署的计划的值。 例如，可以为虚拟机指定市场映像。 | 
| 资源 |否 |依赖于所定义的资源的子资源。 只能提供父资源的架构允许的资源类型。 不隐式表示对父资源的依赖。 必须显式定义该依赖关系。 请参阅[子资源](#child-resources)。 |

### <a name="condition"></a>条件

当您必须决定在部署期间是否创建资源时，使用`condition`元素。 此元素的值解析为 true 或 false。 如果值为 true，则创建了该资源。 如果值为 false，则未创建该资源。 值只能应用到整个资源。

通常，当要创建新资源或使用现有资源时，可以使用此值。 例如，若要指定是部署了新存储帐户，还是使用了现有的存储帐户，请使用：

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

有关使用 `condition` 元素的完整示例模板，请参阅[具有新的或现有虚拟网络、存储和公共 IP 的 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)。

如果您使用[引用](resource-group-template-functions-resource.md#reference)或[列表](resource-group-template-functions-resource.md#list)有条件地部署，该函数的资源的函数求值时，即使未部署资源。 如果该函数所引用的资源不存在，则会出错。 使用[如果](resource-group-template-functions-logical.md#if)函数以确保部署资源时该函数才会评估的条件。 请参阅[如果函数](resource-group-template-functions-logical.md#if)示例模板，将使用，以及使用有条件地部署资源的引用。

### <a name="resource-names"></a>资源名称

通常，会在 Resource Manager 中使用三种类型的资源名称：

* 必须唯一的资源名称。
* 不一定是唯一的资源名称，不过，可以选择提供可帮助识别资源的名称。
* 通用的资源名称。

对于具有数据访问终结点的任何资源类型，请提供**唯一的资源名称**。 需要唯一名称的一些常见资源类型包括：

* Azure 存储<sup>1</sup> 
* Azure 应用服务的 Web 应用功能
* SQL Server
* Azure 密钥保管库
* 用于 Redis 的 Azure 缓存
* Azure 批处理
* Azure 流量管理器
* Azure 搜索
* Azure HDInsight

<sup>1</sup> 存储帐户名必须使用小写字母，包含 24 个或更少的字符，并且不包含任何连字符。

设置名称时，可以手动创建唯一的名称或使用 [uniqueString()](resource-group-template-functions-string.md#uniquestring) 函数生成名称。 可能还需要在 **uniqueString** 结果中添加一个前缀或后缀。 修改唯一的名称可以更方便地通过名称识别资源类型。 例如，可以使用以下变量生成存储帐户的唯一名称：

```json
"variables": {
  "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

对于某些资源类型，可能需要提供**标识名称**，但该名称并非一定是唯一的。 对于这些资源类型，请提供一个可以描述其用途或特征的名称。

```json
"parameters": {
  "vmName": { 
    "type": "string",
    "defaultValue": "demoLinuxVM",
    "metadata": {
      "description": "The name of the VM to create."
    }
  }
}
```

对于主要通过其他资源访问的资源类型，可以在模板中使用硬编码的**通用名称**。 例如，可为 SQL Server 上的防火墙规则设置一个标准的通用名称：

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

### <a name="resource-location"></a>资源位置

部署模板时，必须提供每个资源的位置。 不同的位置中支持不同的资源类型。 若要获取资源类型支持的位置，请参阅 [Azure 资源提供程序和类型](resource-manager-supported-services.md)。

使用参数指定资源的位置，并将默认值设置为 `resourceGroup().location`。

以下示例显示了部署到作为参数指定的位置的存储帐户：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

### <a name="child-resources"></a>子资源

在某些资源类型内，还可以定义子资源数组。 子资源是仅在另一个资源的上下文内存在的资源。 例如，如果没有 SQL 服务器，则不存在 SQL 数据库；因此，此数据库是此服务器的子资源。 可以在服务器的定义内定义数据库。

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "exampleserver",
  ...
  "resources": [
    {
      "apiVersion": "2017-10-01-preview",
      "type": "databases",
      "name": "exampledatabase",
      ...
    }
  ]
}
```

但是，无需在服务器内定义数据库。 可以定义顶级子资源。 如果父资源未部署在同一模板中，或者想要使用 `copy` 创建多个子资源，可以使用此方法。 使用此方法时，必须提供完整的资源类型，并在子资源名称中包括父资源名称。

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "exampleserver",
  "resources": [ 
  ],
  ...
},
{
  "apiVersion": "2017-10-01-preview",
  "type": "Microsoft.Sql/servers/databases",
  "name": "exampleserver/exampledatabase",
  ...
}
```

提供的类型和名称的值因父资源内部或外部父资源是否定义子资源。

当嵌套在父资源时，使用：

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

当父资源的外部定义，使用：

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

当嵌套时，将类型设置为`databases`但其完整资源类型仍是`Microsoft.Sql/servers/databases`。 可不提供 `Microsoft.Sql/servers/`，因为假设它继承父资源类型。 子资源名称设置为 `exampledatabase`，但完整名称包括父名称。 可不提供 `exampleserver`，因为假设它继承父资源。

向资源构造完全限定的引用时，类型和名称的分段组合顺序并不是这两者的简单串联。 相反，在命名空间后，需采用“类型/名称”对从最不具体到最具体的序列：

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

例如：

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` 正确，`Microsoft.Compute/virtualMachines/extensions/myVM/myExt` 不正确

## <a name="outputs"></a>Outputs

在 Outputs 节中，可以指定从部署返回的值。 一般情况下，将从已部署的资源返回值。

### <a name="available-properties"></a>可用属性

以下示例演示了输出定义的结构：

```json
"outputs": {
  "<outputName>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| 元素名称 | 需要 | 描述 |
|:--- |:--- |:--- |
| outputName |是 |输出值的名称。 必须是有效的 JavaScript 标识符。 |
| 条件 |否 | 指示此输出值是否返回的布尔值。 如果为 `true`，则该值包含在部署的输出中。 如果为 `false`，则此部署将跳过输出值。 如果未指定，则默认值为 `true`。 |
| type |是 |输出值的类型。 输出值支持的类型与模板输入参数相同。 如果指定**securestring**输出类型，值不会显示部署历史记录中，并且无法从另一个模板中检索。 若要在多个模板中使用机密值，将机密存储在 Key Vault 中，引用参数文件中的机密。 有关详细信息，请参阅[使用 Azure 密钥保管库以在部署期间传递安全参数值](resource-manager-keyvault-parameter.md)。 |
| value |是 |评估并作为输出值返回的模板语言表达式。 |

### <a name="define-and-use-output-values"></a>定义和使用输出值

以下示例演示如何返回公共 IP 地址的资源 ID：

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

下一个示例显示如何根据是否部署了新的资源 ID 有条件地返回公共 IP 地址的资源 ID：

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

有关条件输出的简单示例，请参阅[条件输出模板](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)。

部署后，可以使用脚本检索值。 对于 PowerShell，请使用：

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

对于 Azure CLI，请使用：

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

可以使用 [reference](resource-group-template-functions-resource.md#reference) 函数从链接模板中检索输出值。 若要从链接模板中获取输出值，请使用如下所示的语法检索属性值：`"[reference('deploymentName').outputs.propertyName.value]"`。

从链接模板获取输出属性时，属性名称不能包含短划线。

以下示例演示如何通过从链接模板检索值，在负载均衡器上设置 IP 地址。

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

不能在[嵌套模板](resource-group-linked-templates.md#link-or-nest-a-template)的 outputs 节中使用 `reference` 函数。 若要返回嵌套模板中部署的资源的值，请将嵌套模板转换为链接模板。

### <a name="output-example-templates"></a>输出示例模板

|模板  |描述  |
|---------|---------|
|[复制变量](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | 创建复杂变量，并输出这些值。 不部署任何资源。 |
|[公共 IP 地址](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | 创建公共 IP 地址并输出资源 ID。 |
|[负载均衡器](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | 链接到前面的模板。 创建负载均衡器时，请使用输出中的资源 ID。 |


<a id="comments" />

## <a name="comments-and-metadata"></a>注释和元数据

可通过几个选项向模板添加注释和元数据。

几乎可以在模板中的任意位置添加 `metadata` 对象。 资源管理器会忽略该对象，但 JSON 编辑器可能会警告你该属性无效。 在对象中，定义所需的属性。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

对于参数，添加具有 `description` 属性的 `metadata` 对象。

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

通过门户部署模板时，在说明中提供的文本自动用作该参数的提示。

![显示参数提示](./media/resource-group-authoring-templates/show-parameter-tip.png)

对于资源，添加 `comments` 元素或元数据对象。 以下示例同时显示了注释元素和元数据对象。

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

对于输出，将元数据对象添加到输出值。

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

无法将元数据对象添加到用户定义的函数。

对于内联注释，可使用 `//`，但此语法不适用于所有工具。 无法使用 Azure CLI 来部署带有内联注释的模板。 而且，无法使用门户模板编辑器处理带有内联注释的模板。 如果添加此类注释，请务必使用支持内联 JSON 注释的工具。

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ // storage account and network interface must be deployed first
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

在 VS Code 中，可以将语言模式设置为带注释的 JSON。 内联注释不再标记为无效。 更改模式：

1. 打开语言模式选择 (Ctrl+K M)

1. 选择“带注释的 JSON”。

   ![选择语言模式](./media/resource-group-authoring-templates/select-json-comments.png)

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>后续步骤
* 若要查看许多不同类型的解决方案的完整模型，请参阅 [Azure Quickstart Templates](https://azure.microsoft.com/documentation/templates/)（Azure 快速入门模板）。
* 有关用户可以使用的来自模板中的函数的详细信息，请参阅 [Azure 资源管理器模板函数](resource-group-template-functions.md)。
* 若要在部署期间合并多个模板，请参阅[将已链接的模板与 Azure 资源管理器配合使用](resource-group-linked-templates.md)。
* 有关创建模板的建议，请参阅 [Azure 资源管理器模板的最佳做法](template-best-practices.md)。
* 有关如何创建可以跨所有 Azure 环境和 Azure Stack 使用的资源管理器模板的建议，请参阅[开发用于实现云一致性的 Azure 资源管理器模板](templates-cloud-consistency.md)。
