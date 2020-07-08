---
title: 模板结构和语法
description: 使用声明性 JSON 语法描述 Azure Resource Manager 模板的结构和属性。
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: 4fdf386aa3b17f46589183706b2a91637acacdb7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208818"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>了解 ARM 模板的结构和语法

本文介绍 Azure 资源管理器 (ARM) 模板的结构。 演示了模板的不同部分，以及可在相应部分使用的属性。

本文面向对 ARM 模板有一定了解的用户， 其中提供了有关模板结构的详细信息。 如果需要通过分步教程来了解创建模板的过程，请参阅[教程：创建和部署第一个 Azure 资源管理器模板](template-tutorial-create-first-template.md)。

## <a name="template-format"></a>模板格式

使用最简单的结构时，模板有以下元素：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| 元素名称 | 必须 | 说明 |
|:--- |:--- |:--- |
| $schema |是 |描述模板语言版本的 JSON 架构文件所在的位置。 所用版本号取决于部署范围和 JSON 编辑器。<br><br>如果使用的是[具有 Azure 资源管理器工具扩展的 VS Code](use-vs-code-to-create-template.md)，请使用最新版本进行资源组部署：<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>其他编辑器（包括 Visual Studio）可能无法处理此架构。 对于这些编辑器，请使用：<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>对于订阅部署，请使用：<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>对于管理组部署，请使用：<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>对于租户部署，请使用：<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| contentVersion |是 |模板的版本（例如 1.0.0.0）。 可为此元素提供任意值。 使用此值记录模板中的重要更改。 使用模板部署资源时，此值可用于确保使用正确的模板。 |
| apiProfile |否 | 用作资源类型 API 版本集合的 API 版本。 使用此值可以避免为模板中的每个资源指定 API 版本。 如果你指定 API 配置文件版本但不指定资源类型的 API 版本，则资源管理器将使用配置文件中为该资源类型定义的 API 版本。<br><br>将模板部署到不同的环境（例如 Azure Stack 和全球 Azure）时，API 配置文件属性非常有用。 使用 API 配置文件版本可确保模板自动使用两个环境均支持的版本。 有关最新 API 配置文件版本以及配置文件中定义的资源 API 版本的列表，请参阅 [API 配置文件](https://github.com/Azure/azure-rest-api-specs/tree/master/profile)。<br><br>有关详细信息，请参阅[使用 API 配置文件跟踪版本](templates-cloud-consistency.md#track-versions-using-api-profiles)。 |
| [parameters](#parameters) |否 |执行部署以自定义资源部署时提供的值。 |
| [variables](#variables) |否 |在模板中用作 JSON 片段以简化模板语言表达式的值。 |
| [functions](#functions) |否 |可在模板中使用的用户定义函数。 |
| [resources](#resources) |是 |已在资源组或订阅中部署/更新的资源类型。 |
| [outputs](#outputs) |否 |部署后返回的值。 |

每个元素均有可设置的属性。 本文稍后将更详细地介绍模板的各个节。

## <a name="parameters"></a>parameters

在模板的 parameters 节中，可以指定在部署资源时能够输入的值。 一个模板中最多可以有 256 个参数。 可以通过使用包含多个属性的对象来减少参数的数目。

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

| 元素名称 | 必须 | 说明 |
|:--- |:--- |:--- |
| parameter-name |是 |参数的名称。 必须是有效的 JavaScript 标识符。 |
| type |是 |参数值的类型。 允许的类型和值为 **string**、**securestring**、**int**、**bool**、**object**、**secureObject** 和 **array**。 请参阅[数据类型](#data-types)。 |
| defaultValue |否 |参数的默认值，如果没有为参数提供任何值。 |
| allowedValues |否 |用来确保提供正确值的参数的允许值数组。 |
| minValue |否 |int 类型参数的最小值，此值是包容性的。 |
| maxValue |否 |int 类型参数的最大值，此值是包容性的。 |
| minLength |否 |string、secure string 和 array 类型参数的最小长度，此值是包容性的。 |
| maxLength |否 |string、secure string 和 array 类型参数的最大长度，此值是包容性的。 |
| description |否 |通过门户向用户显示的参数的说明。 有关详细信息，请参阅[模板中的注释](#comments)。 |

有关如何使用参数的示例，请参阅 [Azure 资源管理器模板中的参数](template-parameters.md)。

### <a name="data-types"></a>数据类型

对于作为内联参数传递的整数，值的范围可能受限于用于部署的 SDK 或命令行工具。 例如，使用 PowerShell 部署模板时，整数类型的范围可能为 -2147483648 到 2147483647。 为了避免此限制，请在[参数文件](parameter-files.md)中指定大的整数值。 资源类型会针对整数属性应用其自己的限制。

在模板中指定布尔值和整数值时，请勿对值使用引号。 使用双引号将字符串值引起来。

使用大括号将对象括起来。 使用中括号将数组括起来。

将参数设置为安全字符串或安全对象时，参数的值不会保存到部署历史记录中，也不会记入日志。 但是，如果将该安全值设置为不应为安全值的属性，则该值不会受到保护。 例如，如果将安全字符串设置为标记，则该值将以纯文本的形式存储。 使用安全字符串作为密码和机密。

如需通过示例了解如何设置数据类型的格式，请参阅[参数类型格式](parameter-files.md#parameter-type-formats)。

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

有关使用 `copy` 为变量创建多个值的信息，请参阅[变量迭代](copy-variables.md)。

有关如何使用变量的示例，请参阅 [Azure 资源管理器模板中的变量](template-variables.md)。

## <a name="functions"></a>函数

在模板中，可以创建自己的函数。 这些函数可在模板中使用。 通常，定义不想要在整个模板中重复执行的复杂表达式。 从模板中支持的表达式和[函数](template-functions.md)创建用户定义函数。

定义用户函数时，存在一些限制：

* 该函数不能访问变量。
* 函数仅可使用函数中定义的参数。 如果在用户定义的函数中使用[参数函数](template-functions-deployment.md#parameters)，则只能使用该函数的参数。
* 该函数不能调用其他用户定义的函数。
* 该函数不能使用[引用函数](template-functions-resource.md#reference)。
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

| 元素名称 | 必须 | 说明 |
|:--- |:--- |:--- |
| 命名空间 |是 |自定义函数的命名空间。 用于避免与模板函数的命名冲突。 |
| function-name |是 |自定义函数的名称。 调用函数时，将函数名称与命名空间组合在一起。 例如，若要在命名空间 contoso 中调用名为 uniqueName 的函数，请使用 `"[contoso.uniqueName()]"`。 |
| parameter-name |否 |要在自定义函数中使用的参数的名称。 |
| parameter-value |否 |参数值的类型。 允许的类型和值为 **string**、**securestring**、**int**、**bool**、**object**、**secureObject** 和 **array**。 |
| output-type |是 |输出值的类型。 输出值支持的类型与函数输入参数相同。 |
| output-value |是 |由函数计算并返回的模板语言表达式。 |

有关如何使用自定义函数的示例，请参阅 [Azure 资源管理器模板中的用户定义函数](template-user-defined-functions.md)。

## <a name="resources"></a>资源

在 resources 节，可以定义部署或更新的资源。

使用以下结构定义资源：

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "apiVersion": "<api-version-of-resource>",
      "name": "<name-of-the-resource>",
      "comments": "<your-reference-notes>",
      "location": "<location-of-resource>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
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

| 元素名称 | 必须 | 说明 |
|:--- |:--- |:--- |
| condition | 否 | 布尔值，该值指示在此部署期间是否将预配资源。 为 `true` 时，在部署期间创建资源。 为 `false` 时，此部署将跳过资源。 请参阅[条件](conditional-resource-deployment.md)。 |
| type |是 |资源的类型。 此值是资源提供程序的命名空间和资源类型（例如 **Microsoft.Storage/storageAccounts**）的组合。 若要确定可用值，请参阅[模板参考](/azure/templates/)。 对于子资源，类型的格式取决于该资源是嵌套在父资源中，还是在父资源的外部定义。 请参阅[设置子资源的名称和类型](child-resource-name-type.md)。 |
| apiVersion |是 |用于创建资源的 REST API 版本。 若要确定可用值，请参阅[模板参考](/azure/templates/)。 |
| name |是 |资源的名称。 该名称必须遵循 RFC3986 中定义的 URI 构成部分限制。 向外部各方公开资源名称的 Azure 服务会验证名称，以确保它不是试图窃取另一标识。 对于子资源，名称的格式取决于该资源是嵌套在父资源中，还是在父资源的外部定义。 请参阅[设置子资源的名称和类型](child-resource-name-type.md)。 |
| comments |否 |用于描述模板中资源的注释。 有关详细信息，请参阅[模板中的注释](template-syntax.md#comments)。 |
| location |多种多样 |提供的资源支持的地理位置。 可以选择任何可用位置，但通常选取靠近用户的位置。 通常还会将彼此交互的资源置于同一区域。 大多数资源类型需要一个位置，但某些类型（如角色分配）不需要位置。 请参阅[设置资源位置](resource-location.md)。 |
| dependsOn |否 |部署此资源之前必须部署的资源。 Resource Manager 会评估资源之间的依赖关系，并按正确的顺序部署资源。 如果资源互不依赖，则会并行部署资源。 该值可以是资源名称或资源唯一标识符的逗号分隔列表。 在此模板中仅部署列出的资源。 未在此模板中定义的资源必须是已存在的资源。 避免添加不必要的依赖项，因为这些依赖项可能会降低部署速度并创建循环依赖项。 有关设置依赖项的指导，请参阅[在 Azure Resource Manager 模板中定义依赖项](define-resource-dependency.md)。 |
| tags |否 |与资源关联的标记。 应用可以在订阅中对资源进行逻辑组织的标记。 |
| sku | 否 | 某些资源接受定义了要部署的 SKU 的值。 例如，可以为存储帐户指定冗余类型。 |
| kind | 否 | 某些资源接受定义了你部署的资源类型的值。 例如，可以指定要创建的 Cosmos DB 的类型。 |
| copy |否 |如果需要多个实例，则为要创建的资源数。 默认模式为并行。 若不想同时部署所有资源，请指定为串行模式。 有关详细信息，请参阅[在 Azure 资源管理器中创建多个资源实例](copy-resources.md)。 |
| plan | 否 | 某些资源接受定义了要部署的计划的值。 例如，可以为虚拟机指定市场映像。 |
| properties |否 |特定于资源的配置设置。 properties 的值与创建资源时，在 REST API 操作（PUT 方法）的请求正文中提供的值相同。 还可以指定副本数组，为一个属性创建多个实例。 若要确定可用值，请参阅[模板参考](/azure/templates/)。 |
| resources |否 |依赖于所定义的资源的子资源。 只能提供父资源的架构允许的资源类型。 不隐式表示对父资源的依赖。 必须显式定义该依赖关系。 请参阅[设置子资源的名称和类型](child-resource-name-type.md)。 |

## <a name="outputs"></a>Outputs

在 Outputs 节中，可以指定从部署返回的值。 一般情况下，将从已部署的资源返回值。

以下示例演示了输出定义的结构：

```json
"outputs": {
  "<output-name>": {
    "condition": "<boolean-value-whether-to-output-value>",
    "type": "<type-of-output-value>",
    "value": "<output-value-expression>",
    "copy": {
      "count": <number-of-iterations>,
      "input": <values-for-the-variable>
    }
  }
}
```

| 元素名称 | 必须 | 说明 |
|:--- |:--- |:--- |
| output-name |是 |输出值的名称。 必须是有效的 JavaScript 标识符。 |
| condition |否 | 指示此输出值是否返回的布尔值。 如果为 `true`，则该值包含在部署的输出中。 如果为 `false`，则此部署将跳过输出值。 如果未指定，则默认值为 `true`。 |
| type |是 |输出值的类型。 输出值支持的类型与模板输入参数相同。 如果指定 **securestring** 作为输出类型，则值不会显示在部署历史记录中，并且无法从另一个模板检索。 若要在多个模板中使用机密值，请在 Key Vault 中存储该机密，并在参数文件中引用该机密。 有关详细信息，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](key-vault-parameter.md)。 |
| value |否 |要求值并作为输出值返回的模板语言表达式。 请指定 **value** 或 **copy**。 |
| copy |否 | 用于返回多个值作为输出。 请指定 **value** 或 **copy**。 有关详细信息，请参阅 [Azure 资源管理器模板中的输出迭代](copy-outputs.md)。 |

有关如何使用输出的示例，请参阅 [Azure 资源管理器模板中的输出](template-outputs.md)。

<a id="comments"></a>

## <a name="comments-and-metadata"></a>注释和元数据

可通过几个选项向模板添加注释和元数据。

### <a name="comments"></a>注释

对于内联注释，可使用 `//` 或 `/* ... */`，但此语法不适用于所有工具。 如果添加此类注释，请务必使用支持内联 JSON 注释的工具。

> [!NOTE]
> 若要使用 Azure CLI 2.3.0 或更低版本部署包含注释的模板，必须使用 `--handle-extended-json-format` 开关。

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

在 Visual Studio Code 中，[Azure 资源管理器工具扩展](use-vs-code-to-create-template.md#install-resource-manager-tools-extension)可以自动检测资源管理器模板并相应地更改语言模式。 如果在 VS Code 右下角看到 **Azure 资源管理器模板**，则可使用内联注释。 内联注释不再标记为无效。

![Visual Studio Code Azure 资源管理器模板模式](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Metadata

几乎可以在模板中的任意位置添加 `metadata` 对象。 资源管理器会忽略该对象，但 JSON 编辑器可能会警告你该属性无效。 在对象中，定义所需的属性。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

对于参数，添加具有 `description` 属性的 `metadata` 对象****。

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

![显示参数提示](./media/template-syntax/show-parameter-tip.png)

对于资源，添加 `comments` 元素或元数据对象****。 以下示例同时显示了注释元素和元数据对象。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "comments": "Storage account used to store VM disks",
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

对于输出，将元数据对象添加到输出值****。

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

## <a name="multi-line-strings"></a>多行字符串

可将一个字符串分成多个行。 例如，请参见以下 JSON 示例中的 location 属性和注释之一。

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

若要使用 Azure CLI 2.3.0 或更低版本部署包含多行字符串的模板，必须使用 `--handle-extended-json-format` 开关。

## <a name="next-steps"></a>后续步骤

* 若要查看许多不同类型的解决方案的完整模型，请参阅 [Azure Quickstart Templates](https://azure.microsoft.com/documentation/templates/)（Azure 快速入门模板）。
* 有关用户可以使用的来自模板中的函数的详细信息，请参阅 [Azure Resource Manager Template Functions](template-functions.md)（Azure Resource Manager 模板函数）。
* 若要在部署期间合并多个模板，请参阅[将已链接的模板与 Azure 资源管理器配合使用](linked-templates.md)。
* 有关创建模板的建议，请参阅 [Azure 资源管理器模板的最佳做法](template-best-practices.md)。
* 有关常见问题的解答，请参阅[有关 ARM 模板](frequently-asked-questions.md)的常见问题。
