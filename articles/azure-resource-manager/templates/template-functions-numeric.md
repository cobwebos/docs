---
title: 模板函数 - 数值
description: 介绍可在 Azure Resource Manager 模板中使用的用于处理数值的函数。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: dc15ade453fc5ea4dc031ced0377892f4f8cf27d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192342"
---
# <a name="numeric-functions-for-arm-templates"></a>ARM 模板的数值函数

资源管理器提供了以下用于在 Azure 资源管理器（ARM）模板中处理整数的函数：

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

## <a name="add"></a>add

`add(operand1, operand2)`

返回提供的两个整数的总和。

### <a name="parameters"></a>parameters

| 参数 | 必须 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
|operand1 |是 |int |被加数。 |
|operand2 |是 |int |加数。 |

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

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| addResult | int | 8 |

## <a name="copyindex"></a>copyIndex

`copyIndex(loopName, offset)`

返回一个迭代循环的索引。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| loopName | 否 | 字符串 | 用于获取迭代的循环的名称。 |
| offset |否 |int |要添加到从零开始的迭代值的数。 |

### <a name="remarks"></a>备注

此函数始终用于 **copy** 对象。 如果没有提供**偏移量**的值，则返回当前迭代值。 从零开始的迭代值。

**loopName** 可用于指定 copyIndex 是引用资源迭代还是引用属性迭代。 如果没有为 **loopName** 提供值，则将使用当前的资源类型迭代。 当在属性上迭代时，请为 **loopName** 提供值。

有关使用 copy 的详细信息，请参阅：

* [ARM 模板中的资源迭代](copy-resources.md)
* [ARM 模板中的属性迭代](copy-properties.md)
* [ARM 模板中的变量迭代](copy-variables.md)
* [ARM 模板中的输出迭代](copy-outputs.md)

### <a name="example"></a>示例

以下示例显示名称中包含 copy 循环和索引值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {}
}
```

### <a name="return-value"></a>返回值

一个表示迭代的当前索引的整数。

## <a name="div"></a>div

`div(operand1, operand2)`

返回提供的两个整数在整除后的商。

### <a name="parameters"></a>parameters

| 参数 | 必须 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| operand1 |是 |int |被除数。 |
| operand2 |是 |int |除数。 不能为0。 |

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

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| divResult | int | 2 |

## <a name="float"></a>FLOAT

`float(arg1)`

将值转换为浮点数。 仅当将自定义参数传递给应用程序（例如，逻辑应用）时，才使用此函数。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
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

## <a name="int"></a>int

`int(valueToConvert)`

将指定的值转换为整数。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
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

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| intResult | Int | 4 |

## <a name="max"></a>max

`max (arg1)`

返回整数数组或逗号分隔的整数列表中的最大值。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
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

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>min

`min (arg1)`

返回整数数组或逗号分隔的整数列表中的最小值。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
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

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="mod"></a>mod

`mod(operand1, operand2)`

返回使用提供的两个整数整除后的余数。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| operand1 |是 |int |被除数。 |
| operand2 |是 |int |用于相除的数字不能为0。 |

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

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| modResult | Int | 1 |

## <a name="mul"></a>mul

`mul(operand1, operand2)`

返回提供的两个整数的积。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
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

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

## <a name="sub"></a>sub

`sub(operand1, operand2)`

返回提供的两个整数在相减后的结果。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
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

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| subResult | Int | 4 |

## <a name="next-steps"></a>后续步骤

* 有关 Azure 资源管理器模板中各部分的说明，请参阅[了解 ARM 模板的结构和语法](template-syntax.md)。
* 若要在创建资源类型时迭代指定的次数，请参阅[在 Azure 中创建多个资源实例资源管理器](copy-resources.md)。
