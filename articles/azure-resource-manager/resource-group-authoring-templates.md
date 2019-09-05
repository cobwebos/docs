---
title: Azure 资源管理器模板的结构和语法 | Microsoft Docs
description: 使用声明性 JSON 语法描述 Azure 资源管理器模板的结构和属性。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: tomfitz
ms.openlocfilehash: d396b6b48687e451396849cc256c25f847a219cf
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306844"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>了解 Azure 资源管理器模板的结构和语法

本文介绍 Azure 资源管理器模板的结构。 演示了模板的不同部分，以及可在相应部分使用的属性。

本文面向对资源管理器模板有一定了解的用户， 它提供有关模板结构的详细信息。 若要了解如何创建模板，请参阅[Azure 资源管理器模板](template-deployment-overview.md)。

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

| 元素名称 | 必填 | 描述 |
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

## <a name="parameters"></a>Parameters

在模板的 parameters 节中，可以指定在部署资源时能够输入的值。 一个模板中最多可以有 256 个参数。 您可以使用包含多个属性的对象来减少参数的数量。

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

| 元素名称 | 必填 | 描述 |
|:--- |:--- |:--- |
| 参数-名称 |是 |参数的名称。 必须是有效的 JavaScript 标识符。 |
| type |是 |参数值的类型。 允许的类型和值为 **string**、**securestring**、**int**、**bool**、**object**、**secureObject** 和 **array**。 |
| defaultValue |否 |参数的默认值，如果没有为参数提供任何值。 |
| allowedValues |否 |用来确保提供正确值的参数的允许值数组。 |
| minValue |否 |int 类型参数的最小值，此值是包容性的。 |
| maxValue |否 |int 类型参数的最大值，此值是包容性的。 |
| minLength |否 |string、secure string 和 array 类型参数的最小长度，此值是包容性的。 |
| maxLength |否 |string、secure string 和 array 类型参数的最大长度，此值是包容性的。 |
| description |否 |通过门户向用户显示的参数的说明。 有关详细信息，请参阅[模板中的注释](#comments)。 |

## <a name="variables"></a>变量

在 variables 节中构造可在整个模板中使用的值。 不需要定义变量，但使用变量可以减少复杂的表达式，从而简化模板。

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

## <a name="functions"></a>函数

在模板中，可以创建自己的函数。 这些函数可在模板中使用。 通常，定义不想要在整个模板中重复执行的复杂表达式。 从模板中支持的表达式和[函数](resource-group-template-functions.md)创建用户定义函数。

定义用户函数时，存在一些限制：

* 该函数不能访问变量。
* 函数仅可使用函数中定义的参数。 如果在用户定义的函数中使用[参数函数](resource-group-template-functions-deployment.md#parameters)，则只能使用该函数的参数。
* 该函数不能调用其他用户定义的函数。
* 该函数不能使用[引用函数](resource-group-template-functions-resource.md#reference)。
* 该函数的参数不能具有默认值。

```json
"functions": [
  {
    "namespace": "<namespace-for-functions>",
    "members": {
      "<function-name>": {
        "parameters": [
          {
            "name": "<parameter-name>",
            "type": "<type-of-parameter-value>"
          }
        ],
        "output": {
          "type": "<type-of-output-value>",
          "value": "<function-return-value>"
        }
      }
    }
  }
],
```

| 元素名称 | 必填 | 描述 |
|:--- |:--- |:--- |
| 命名空间 |是 |自定义函数的命名空间。 使用可避免与模板函数发生命名冲突。 |
| 函数名称 |是 |自定义函数的名称。 调用函数时，将函数名称与命名空间组合在一起。 例如，若要在命名空间 contoso 中调用名为 uniqueName 的函数`"[contoso.uniqueName()]"`，请使用。 |
| 参数-名称 |否 |要在自定义函数中使用的参数的名称。 |
| 参数-值 |否 |参数值的类型。 允许的类型和值为 **string**、**securestring**、**int**、**bool**、**object**、**secureObject** 和 **array**。 |
| 输出类型 |是 |输出值的类型。 输出值支持的类型与函数输入参数相同。 |
| output-值 |是 |计算并从函数返回的模板语言表达式。 |

## <a name="resources"></a>资源

在 resources 节，可以定义部署或更新的资源。

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

| 元素名称 | 必填 | 描述 |
|:--- |:--- |:--- |
| 条件 | 否 | 布尔值，该值指示在此部署期间是否将预配资源。 为 `true` 时，在部署期间创建资源。 为 `false` 时，此部署将跳过资源。 请参阅[条件](conditional-resource-deployment.md)。 |
| apiVersion |是 |用于创建资源的 REST API 版本。 若要确定可用值，请参阅[模板参考](/azure/templates/)。 |
| type |是 |资源的类型。 此值是资源提供程序的命名空间和资源类型（例如 **Microsoft.Storage/storageAccounts**）的组合。 若要确定可用值，请参阅[模板参考](/azure/templates/)。 对于子资源，类型的格式取决于该资源是嵌套在父资源中，还是在父资源的外部定义。 请参阅[设置子资源的名称和类型](child-resource-name-type.md)。 |
| name |是 |资源的名称。 该名称必须遵循 RFC3986 中定义的 URI 构成部分限制。 向外部方公开资源名称的 Azure 服务将验证名称，以确保它不会尝试欺骗其他标识。 对于子资源，名称的格式取决于该资源是嵌套在父资源中，还是在父资源的外部定义。 请参阅[设置子资源的名称和类型](child-resource-name-type.md)。 |
| location |多种多样 |提供的资源支持的地理位置。 可以选择任何可用位置，但通常最好选取一个接近用户的位置。 通常，在同一区域放置彼此交互的资源也很有用。 大多数资源类型需要一个位置，但某些类型（如角色分配）不需要位置。 请参阅[设置资源位置](resource-location.md)。 |
| 标记 |否 |与资源关联的标记。 应用标签以跨订阅按逻辑对资源进行组织。 |
| 注释 |否 |用于描述模板中资源的注释。 有关详细信息，请参阅[模板中的注释](resource-group-authoring-templates.md#comments)。 |
| 复制 |否 |需要多个实例时应创建的资源数。 默认模式为并行。 若不想同时部署所有资源，请指定为串行模式。 有关详细信息，请参阅[在 Azure 资源管理器中创建多个资源实例](resource-group-create-multiple.md)。 |
| dependsOn |否 |必须在部署此资源前部署的资源。 Resource Manager 评估资源之间的依赖关系，并根据正确顺序进行部署。 如果资源互不依赖，则会并行部署资源。 该值可以是资源名称或资源唯一标识符的逗号分隔列表。 仅列出在此模板中部署的资源。 未在此模板中定义的资源必须是已存在的资源。 避免添加不必要的依赖项，因为这些依赖项可能会降低部署速度并创建循环依赖项。 有关设置依赖项的指导，请参阅[在 Azure 资源管理器模板中定义依赖项](resource-group-define-dependencies.md)。 |
| properties |否 |特定于资源的配置设置。 properties 的值与创建资源时，在 REST API 操作（PUT 方法）的请求正文中提供的值相同。 还可以指定副本数组，为一个属性创建多个实例。 若要确定可用值，请参阅[模板参考](/azure/templates/)。 |
| sku | 否 | 某些资源接受定义了要部署的 SKU 的值。 例如，可以为存储帐户指定冗余类型。 |
| kind | 否 | 某些资源接受定义了你部署的资源类型的值。 例如，可以指定要创建的 Cosmos DB 的类型。 |
| 计划 | 否 | 某些资源接受定义了要部署的计划的值。 例如，可以为虚拟机指定市场映像。 | 
| 资源 |否 |依赖于所定义的资源的子资源。 只能提供父资源的架构允许的资源类型。 不隐式表示对父资源的依赖。 必须显式定义该依赖关系。 请参阅[设置子资源的名称和类型](child-resource-name-type.md)。 |

## <a name="outputs"></a>outputs

在 Outputs 节中，可以指定从部署返回的值。 一般情况下，将从已部署的资源返回值。

以下示例演示了输出定义的结构：

```json
"outputs": {
  "<output-name>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| 元素名称 | 必填 | 描述 |
|:--- |:--- |:--- |
| 输出-名称 |是 |输出值的名称。 必须是有效的 JavaScript 标识符。 |
| condition |否 | 指示此输出值是否返回的布尔值。 如果为 `true`，则该值包含在部署的输出中。 如果为 `false`，则此部署将跳过输出值。 如果未指定，则默认值为 `true`。 |
| type |是 |输出值的类型。 输出值支持的类型与模板输入参数相同。 如果指定 **securestring** 作为输出类型，则值不会显示在部署历史记录中，并且无法从另一个模板检索。 若要在多个模板中使用机密值，请在 Key Vault 中存储该机密，并在参数文件中引用该机密。 有关详细信息，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](resource-manager-keyvault-parameter.md)。 |
| value |是 |评估并作为输出值返回的模板语言表达式。 |

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

## <a name="next-steps"></a>后续步骤

* 若要查看许多不同类型的解决方案的完整模型，请参阅 [Azure Quickstart Templates](https://azure.microsoft.com/documentation/templates/)（Azure 快速入门模板）。
* 有关用户可以使用的来自模板中的函数的详细信息，请参阅 [Azure 资源管理器模板函数](resource-group-template-functions.md)。
* 若要在部署期间合并多个模板，请参阅[将已链接的模板与 Azure 资源管理器配合使用](resource-group-linked-templates.md)。
* 有关创建模板的建议，请参阅 [Azure 资源管理器模板的最佳做法](template-best-practices.md)。
* 有关如何创建可以跨所有 Azure 环境和 Azure Stack 使用的资源管理器模板的建议，请参阅[开发用于实现云一致性的 Azure 资源管理器模板](templates-cloud-consistency.md)。
