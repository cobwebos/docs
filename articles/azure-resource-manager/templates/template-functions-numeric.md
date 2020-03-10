---
title: 模板函数-数值
description: 介绍可在 Azure 资源管理器模板中使用的用于处理数值的函数。
ms.topic: conceptual
ms.date: 11/08/2017
ms.openlocfilehash: 91aa637701acb278e81b7eb86aa3ae2db15acc28
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380647"
---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>用于 Azure 资源管理器模板的数值函数

Resource Manager 提供以下用于处理整数的函数：

* [add](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [max](#max)
* [min](#min)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="add"></a>add
`add(operand1, operand2)`

返回提供的两个整数的总和。

### <a name="parameters"></a>参数

| 参数 | 必需 | Type | 说明 |
|:--- |:--- |:--- |:--- | 
|operand1 |是 |int |要添加的第一个数。 |
|operand2 |是 |int |要添加的第二个数。 |

### <a name="return-value"></a>返回值

一个整数，包含参数的总和。

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json)将添加两个参数。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

上面具有默认值的示例的输出为：

| 名称 | Type | 值 |
| ---- | ---- | ----- |
| addResult | Int | 8 |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json 
```

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

返回一个迭代循环的索引。 

### <a name="parameters"></a>参数

| 参数 | 必需 | Type | 说明 |
|:--- |:--- |:--- |:--- |
| loopName | 否 | 字符串 | 用于获取迭代的循环的名称。 |
| offset |否 |int |要添加到从零开始的迭代值的数。 |

### <a name="remarks"></a>备注

此函数始终用于 **copy** 对象。 如果没有提供**偏移量**的值，则返回当前迭代值。 从零开始的迭代值。 定义资源或变量时，你可以使用迭代循环。

**loopName** 可用于指定 copyIndex 是引用资源迭代还是引用属性迭代。 如果没有为 **loopName** 提供值，则将使用当前的资源类型迭代。 当在属性上迭代时，请为 **loopName** 提供值。 
 
有关如何使用 **copyIndex** 的完整说明，请参阅[在 Azure 资源管理器中创建多个资源实例](copy-resources.md)。

有关定义变量时使用“copyIndex”的示例，请参阅[变量](template-syntax.md#variables)。

### <a name="example"></a>示例

以下示例显示名称中包含 copy 循环和索引值。 

```json
"resources": [ 
  { 
    "name": "[concat('examplecopy-', copyIndex())]", 
    "type": "Microsoft.Web/sites", 
    "copy": { 
      "name": "websitescopy", 
      "count": "[parameters('count')]" 
    }, 
    ...
  }
]
```

### <a name="return-value"></a>返回值

一个表示迭代的当前索引的整数。

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

返回提供的两个整数在整除后的商。

### <a name="parameters"></a>参数

| 参数 | 必需 | Type | 说明 |
|:--- |:--- |:--- |:--- |
| operand1 |是 |int |被除数。 |
| operand2 |是 |int |除数。 不能为 0。 |

### <a name="return-value"></a>返回值

一个表示商的整数。

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json)将一个参数除以另一个参数。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 8,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

上面具有默认值的示例的输出为：

| 名称 | Type | 值 |
| ---- | ---- | ----- |
| divResult | Int | 2 |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json 
```

<a id="float" />

## <a name="float"></a>浮点数
`float(arg1)`

将值转换为浮点数。 仅当将自定义参数传递给应用程序（例如，逻辑应用）时，才使用此函数。

### <a name="parameters"></a>参数

| 参数 | 必需 | Type | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |字符串或整数 |要转换为浮点数的值。 |

### <a name="return-value"></a>返回值
一个浮点数。

### <a name="example"></a>示例

以下示例演示如何使用 float 将参数传递给逻辑应用：

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
            "custom1": {
                "value": "[float('3.0')]"
            },
            "custom2": {
                "value": "[float(3)]"
            },
```

<a id="int" />

## <a name="int"></a>int
`int(valueToConvert)`

将指定的值转换为整数。

### <a name="parameters"></a>参数

| 参数 | 必需 | Type | 说明 |
|:--- |:--- |:--- |:--- |
| valueToConvert |是 |字符串或整数 |要转换为整数的值。 |

### <a name="return-value"></a>返回值

转换后的值的整数值。

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json)将用户提供的参数值转换为整数。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToConvert": { 
            "type": "string",
            "defaultValue": "4"
        }
    },
    "resources": [
    ],
    "outputs": {
        "intResult": {
            "type": "int",
            "value": "[int(parameters('stringToConvert'))]"
        }
    }
}
```

上面具有默认值的示例的输出为：

| 名称 | Type | 值 |
| ---- | ---- | ----- |
| intResult | Int | 4 |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

<a id="max" />

## <a name="max"></a>max
`max (arg1)`

返回整数数组或逗号分隔的整数列表中的最大值。

### <a name="parameters"></a>参数

| 参数 | 必需 | Type | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |整数数组或逗号分隔的整数列表 |要获取最大值的集合。 |

### <a name="return-value"></a>返回值

一个整数，表示集合中的最大值。

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json)演示如何对整数数组和整数列表使用 max：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

上面具有默认值的示例的输出为：

| 名称 | Type | 值 |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>min
`min (arg1)`

返回整数数组或逗号分隔的整数列表中的最小值。

### <a name="parameters"></a>参数

| 参数 | 必需 | Type | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |整数数组或逗号分隔的整数列表 |要获取最小值的集合。 |

### <a name="return-value"></a>返回值

一个整数，表示集合中的最小值。

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json)演示如何对整数数组和整数列表使用 min：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

上面具有默认值的示例的输出为：

| 名称 | Type | 值 |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="mod" />

## <a name="mod"></a>mod
`mod(operand1, operand2)`

返回使用提供的两个整数整除后的余数。

### <a name="parameters"></a>参数

| 参数 | 必需 | Type | 说明 |
|:--- |:--- |:--- |:--- |
| operand1 |是 |int |被除数。 |
| operand2 |是 |int |非零除数。 |

### <a name="return-value"></a>返回值
一个表示余数的整数。

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json)将返回一个参数除以另一个参数后所得的余数。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

上面具有默认值的示例的输出为：

| 名称 | Type | 值 |
| ---- | ---- | ----- |
| modResult | Int | 1 |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

返回提供的两个整数的积。

### <a name="parameters"></a>参数

| 参数 | 必需 | Type | 说明 |
|:--- |:--- |:--- |:--- |
| operand1 |是 |int |要乘以的第一个数。 |
| operand2 |是 |int |要乘以的第二个数。 |

### <a name="return-value"></a>返回值

一个表示积的整数。

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json)将一个参数乘以另一个参数。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

上面具有默认值的示例的输出为：

| 名称 | Type | 值 |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

<a id="sub" />

## <a name="sub"></a>sub
`sub(operand1, operand2)`

返回提供的两个整数在相减后的结果。

### <a name="parameters"></a>参数

| 参数 | 必需 | Type | 说明 |
|:--- |:--- |:--- |:--- |
| operand1 |是 |int |被减数。 |
| operand2 |是 |int |减数。 |

### <a name="return-value"></a>返回值
一个表示减后结果的整数。

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json)将一个参数与另一个参数相减。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

上面具有默认值的示例的输出为：

| 名称 | Type | 值 |
| ---- | ---- | ----- |
| subResult | Int | 4 |

要使用 Azure CLI 部署此示例模板，请使用：

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

要使用 PowerShell 部署此示例模板，请使用：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

## <a name="next-steps"></a>后续步骤
* 有关 Azure 资源管理器模板中各部分的说明，请参阅[创作 Azure 资源管理器模板](template-syntax.md)。
* 要合并多个模板，请参阅[将链接的模板与 Azure 资源管理器配合使用](linked-templates.md)。
* 若要在创建资源类型时迭代指定的次数，请参阅[在 Azure 资源管理器中创建多个资源实例](copy-resources.md)。
* 若要查看如何部署已创建的模板，请参阅[使用 Azure 资源管理器模板部署应用程序](deploy-powershell.md)。

