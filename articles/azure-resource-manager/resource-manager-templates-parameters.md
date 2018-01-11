---
title: "Azure 资源管理器模板 parameters 节 | Microsoft Docs"
description: "介绍了使用声明性 JSON 语法的 Azure 资源管理器模板的 parameters 节。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/11/2017
ms.author: tomfitz
ms.openlocfilehash: 7d0f53751bf529d52c156a8b9319b10560eb8997
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2017
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Azure 资源管理器模板的 Parameters 节
在模板的 parameters 节中，可以指定在部署资源时能够输入的值。 提供针对特定环境（例如开发、测试和生产环境）定制的参数值可以自定义部署。 无需在模板中提供参数，但如果没有参数，模板始终部署具有相同名称、位置和属性的相同资源。

一个模板中最多可以有 255 个参数。 如本文中所示，可以通过使用包含多个属性的对象来减少参数的数量。

## <a name="define-and-use-a-parameter"></a>定义和使用参数

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

## <a name="available-properties"></a>可用属性

前面的示例仅显示了可以在 parameters 节中使用的部分属性。 可用属性包括：

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
| type |是 |参数值的类型。 允许的类型和值为 **string**、**secureString**、**int**、**bool**、**object**、**secureObject** 和 **array**。 |
| defaultValue |否 |参数的默认值，如果没有为参数提供任何值。 |
| allowedValues |否 |用来确保提供正确值的参数的允许值数组。 |
| minValue |否 |int 类型参数的最小值，此值是包容性的。 |
| maxValue |否 |int 类型参数的最大值，此值是包容性的。 |
| minLength |否 |string、secureString 和 array 类型参数的最小长度，此值是包容性的。 |
| maxLength |否 |string、secureString 和 array 类型参数的最大长度，此值是包容性的。 |
| description |否 |通过门户向用户显示的参数的说明。 |

## <a name="template-functions-with-parameters"></a>包含参数的模板函数

为参数提供默认值时，可以使用大多数模板函数。 可以使用另一个参数值来生成默认值。 以下模板演示了如何在默认值中使用函数：

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

## <a name="objects-as-parameters"></a>对象作为参数

通过将相关值作为对象传入，可以更轻松地对这些值进行组织。 此方式还可以减少模板中的参数的数量。

在模板中定义参数，并在部署过程中指定 JSON 对象，而不是单个值。 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
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
    "location":"[resourceGroup().location]",
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

## <a name="recommendations"></a>建议
使用参数时，以下信息可以提供帮助：

* 尽量不要使用参数。 尽可能地使用变量或文本值。 只针对以下场合使用参数：
   
   * 想要根据环境使用不同变体的设置（SKU、大小、容量）。
   * 想要方便识别而指定的资源名称。
   * 经常用来完成其他任务的值（例如管理员用户名）。
   * 机密（例如密码）。
   * 创建资源类型的多个实例时要使用的值的数目或数组。
* 对参数名称使用混合大小写。
* 对元数据中提供每个参数的说明。

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* 定义参数（密码和 SSH 密钥除外）的默认值。 通过提供默认值，参数在部署过程中成为可选的。 默认值可以是空字符串。 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* 对所有密码和机密使用 **SecureString**。 要将敏感数据传入 JSON 对象，请使用 **secureObject** 类型。 部署资源后，无法读取 secureString 或 secureObject 类型的模板参数。 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* 尽量避免使用参数来指定位置。 改用资源组的 **location** 属性。 如果对所有资源使用 **resourceGroup().location** 表达式，请将模板中的资源部署在资源组所在的同一位置：
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         ...
     }
   ]
   ```
   
   如果只有有限数量的位置支持某种资源类型，可能需要在模板中直接指定有效的位置。 如果必须使用 **location** 参数，请尽量与可能需要位于同一位置的资源共享该参数值。 此方式可以最大程度地减少要求用户提供位置信息的次数。
* 避免对资源类型的 API 版本使用参数或变量。 资源的属性和值可能会因版本号的不同而异。 如果将 API 版本设置为参数或变量，代码编辑器中的 IntelliSense 无法确定正确架构。 并且会在模板中将 API 版本硬编码。
* 避免在模板中指定与部署命令中的参数匹配的参数名称。 资源管理器通过向模板参数添加后缀 **FromTemplate** 解决了此命名冲突。 例如，如果在模板中包括一个名为“ResourceGroupName”的参数，则该参数会与 [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) cmdlet 中的“ResourceGroupName”参数冲突。 在部署期间，系统会提示用户提供 **ResourceGroupNameFromTemplate** 的值。

## <a name="example-templates"></a>示例模板

这些示例模板演示了使用参数的一些方案。 请部署这些模板来测试在不同方案中参数是如何处理的。

|模板  |说明  |
|---------|---------|
|[包含用于默认值的函数的参数](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | 演示了为参数定义默认值时如何使用模板函数。 该模板不部署任何资源。 它构造参数值并返回这些值。 |
|[参数对象](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | 演示了将对象用于参数。 该模板不部署任何资源。 它构造参数值并返回这些值。 |

## <a name="next-steps"></a>后续步骤

* 若要查看许多不同类型的解决方案的完整模型，请参阅 [Azure Quickstart Templates](https://azure.microsoft.com/documentation/templates/)（Azure 快速入门模板）。
* 若要了解如何在部署过程中输入参数值，请参阅[使用 Azure 资源管理器模板部署应用程序](resource-group-template-deploy.md)。 
* 有关用户可以使用的来自模板中的函数的详细信息，请参阅 [Azure 资源管理器模板函数](resource-group-template-functions.md)。
* 有关使用参数对象的信息，请参阅[将对象用作 Azure 资源管理器模板中的参数](/azure/architecture/building-blocks/extending-templates/objects-as-parameters)。