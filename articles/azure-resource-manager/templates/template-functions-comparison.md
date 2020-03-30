---
title: 模板函数 - 比较
description: 介绍可在 Azure 资源管理器模板中使用的用于比较值的函数。
ms.topic: conceptual
ms.date: 09/05/2017
ms.openlocfilehash: 42009e8543e307f2d3e4643ddaa79f492f9bdfee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156355"
---
# <a name="comparison-functions-for-arm-templates"></a>ARM 模板的比较功能

资源管理器提供了多个功能，用于在 Azure 资源管理器 （ARM） 模板中进行比较。

* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="equals"></a>equals
`equals(arg1, arg2)`

检查两个值是否相等。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |int、string、array 或 object |要检查是否相等的第一个值。 |
| arg2 |是 |int、string、array 或 object |要检查是否相等的第二个值。 |

### <a name="return-value"></a>返回值

如果值相等，返回 **True**；否则返回 **False**。

### <a name="remarks"></a>备注

Equals 函数通常与 `condition` 元素配合使用来测试是否部署资源。

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

### <a name="example"></a>示例

下列[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json)检查不同类型的值是否相等。 所有默认值都返回 True。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 1
        },
        "firstString": {
            "type": "string",
            "defaultValue": "a"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "firstObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[equals(parameters('firstString'), parameters('secondString'))]"
        },
        "checkArrays": {
            "type": "bool",
            "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
        },
        "checkObjects": {
            "type": "bool",
            "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
        }
    }
}
```

上面具有默认值的示例的输出为：

| “属性” | 类型 | “值” |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | True |
| checkArrays | Bool | True |
| checkObjects | Bool | True |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json)结合使用 [not](template-functions-logical.md#not) 和 **equals**。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
}
```

前述示例的输出为：

| “属性” | 类型 | “值” |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

## <a name="greater"></a>greater
`greater(arg1, arg2)`

检查第一个值是否大于第二个值。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |int 或 string |用于大于比较的第一个值。 |
| arg2 |是 |int 或 string |用于大于比较的第二个值。 |

### <a name="return-value"></a>返回值

如果第一个值大于第二个值，返回 **True**；否则返回 **False**。

### <a name="example"></a>示例

下列[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json)检查一个值是否大于另一个值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greater(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

上面具有默认值的示例的输出为：

| “属性” | 类型 | “值” |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

检查第一个值是否大于或等于第二个值。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |int 或 string |用于大于或等于比较的第一个值。 |
| arg2 |是 |int 或 string |用于大于或等于比较的第二个值。 |

### <a name="return-value"></a>返回值

如果第一个值大于或等于第二个值，返回 **True**；否则返回 **False**。

### <a name="example"></a>示例

下列[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json)检查一个值是否大于等于另一个值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

上面具有默认值的示例的输出为：

| “属性” | 类型 | “值” |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

## <a name="less"></a>less
`less(arg1, arg2)`

检查第一个值是否小于第二个值。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |int 或 string |用于小于比较的第一个值。 |
| arg2 |是 |int 或 string |用于小于比较的第二个值。 |

### <a name="return-value"></a>返回值

如果第一个值小于第二个值，返回 **True**；否则返回 **False**。

### <a name="example"></a>示例

下列[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json)检查一个值是否小于另一个值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[less(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

上面具有默认值的示例的输出为：

| “属性” | 类型 | “值” |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

检查第一个值是否小于或等于第二个值。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |int 或 string |用于小于或等于比较的第一个值。 |
| arg2 |是 |int 或 string |用于小于或等于比较的第二个值。 |

### <a name="return-value"></a>返回值

如果第一个值小于或等于第二个值，返回 **True**；否则返回 **False**。

### <a name="example"></a>示例

下列[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json)检查一个值是否小于等于另一个值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

上面具有默认值的示例的输出为：

| “属性” | 类型 | “值” |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az deployment group create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

## <a name="next-steps"></a>后续步骤
* 有关 Azure 资源管理器模板中各部分的说明，请参阅[创作 Azure 资源管理器模板](template-syntax.md)。
* 要合并多个模板，请参阅[使用 Azure 资源管理器使用链接模板](linked-templates.md)。
* 要迭代创建资源类型时指定的次数，请参阅[在 Azure 资源管理器中创建多个资源实例](copy-resources.md)。
* 若要查看如何部署已创建的模板，请参阅[使用 Azure 资源管理器模板部署应用程序](deploy-powershell.md)。

