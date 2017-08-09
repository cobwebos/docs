---
title: "Azure 资源管理器模板函数 - 逻辑 | Microsoft Docs"
description: "介绍 Azure 资源管理器模板中用于确定逻辑值的函数。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 313601ad99cdc12c4b50f5469959d37a9fa70d35
ms.contentlocale: zh-cn
ms.lasthandoff: 08/02/2017

---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>用于 Azure 资源管理器模板的逻辑函数

Resource Manager 提供了多个用于在模板中进行比较的函数。

* [and](#and)
* [bool](#bool)
* [if](#if)
* [not](#not)
* [or](#or)

## <a name="and"></a>and
`and(arg1, arg2)`

检查两个参数值是否均为 true。

### <a name="parameters"></a>parameters

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |布尔值 |第一个值，需检查其是否为 true。 |
| arg2 |是 |布尔值 |第二个值，需检查其是否为 true。 |

### <a name="return-value"></a>返回值

如果两个值均为 true，则返回 True；否则返回 False。

### <a name="examples"></a>示例

以下示例演示如何使用逻辑函数。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

前述示例的输出为：

| Name | 类型 | 值 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |


## <a name="bool"></a>bool
`bool(arg1)`

将参数转换为布尔值。

### <a name="parameters"></a>parameters

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |字符串或整数 |要转换为布尔值的值。 |

### <a name="return-value"></a>返回值
转换后的值的布尔值。

### <a name="examples"></a>示例

以下示例演示如何对字符串或整数使用 bool。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

上面具有默认值的示例的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="if"></a>if
`if(condition, trueValue, falseValue)`

根据条件为 true 或 false 返回值。

### <a name="parameters"></a>parameters

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| 条件 |是 |布尔值 |要检查是否为 true 的值。 |
| trueValue |是 | 字符串、int、对象或数组 |条件为 true 时返回的值。 |
| falseValue |是 | 字符串、int、对象或数组 |条件为 false 时返回的值。 |

### <a name="return-value"></a>返回值

如果第一个参数为 True，则返回第二个参数；否则返回第三个参数。

### <a name="remarks"></a>备注

此函数可用于有条件地设置资源属性。 下面的示例并不是完整的模板，但它显示了有条件地设置可用性集的相关部分。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "availabilitySet": {
            "type": "string",
            "allowedValues": [
                "yes",
                "no"
            ]
        }
    },
    "variables": {
        ...
        "availabilitySetName": "availabilitySet1",
        "availabilitySet": {
            "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
        }
    },
    "resources": [
        {
            "condition": "[equals(parameters('availabilitySet'),'yes')]",
            "type": "Microsoft.Compute/availabilitySets",
            "name": "[variables('availabilitySetName')]",
            ...
        },
        {
            "apiVersion": "2016-03-30",
            "type": "Microsoft.Compute/virtualMachines",
            "properties": {
                "availabilitySet": "[if(equals(parameters('availabilitySet'),'yes'), variables('availabilitySet'), json('null'))]",
                ...
            }
        },
        ...
    ],
    ...
}
```

### <a name="examples"></a>示例

以下示例演示如何使用 `if` 函数。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        }
    }
}
```

前述示例的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| yesOutput | String | 是 |
| noOutput | String | 否 |


## <a name="not"></a>not
`not(arg1)`

将布尔值转换为其相反值。

### <a name="parameters"></a>parameters

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |布尔值 |要转换的值。 |


### <a name="return-value"></a>返回值

参数为 False 时返回 True。 参数为 True 时返回 False。

### <a name="examples"></a>示例

以下示例演示如何使用逻辑函数。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

前述示例的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

以下示例结合使用 not 和 [equals](resource-group-template-functions-comparison.md#equals)。

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
```

前述示例的输出为：

| Name | 类型 | 值 |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |


## <a name="or"></a>或
`or(arg1, arg2)`

检查其中任一参数值是否为 true。

### <a name="parameters"></a>parameters

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |布尔值 |第一个值，需检查其是否为 true。 |
| arg2 |是 |布尔值 |第二个值，需检查其是否为 true。 |

### <a name="return-value"></a>返回值

如果任一值为 true，则返回 True；否则返回 False。

### <a name="examples"></a>示例

以下示例演示如何使用逻辑函数。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

前述示例的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |


## <a name="next-steps"></a>后续步骤
* 有关 Azure Resource Manager 模板中各部分的说明，请参阅 [Authoring Azure Resource Manager templates](resource-group-authoring-templates.md)（创作 Azure Resource Manager 模板）。
* 要合并多个模板，请参阅 [Using linked templates with Azure Resource Manager](resource-group-linked-templates.md)（将链接的模板与 Azure Resource Manager 配合使用）。
* 若要在创建资源类型时迭代指定的次数，请参阅 [Create multiple instances of resources in Azure Resource Manager](resource-group-create-multiple.md)（在 Azure Resource Manager 中创建多个资源实例）。
* 若要查看如何部署已创建的模板，请参阅 [Deploy an application with Azure Resource Manager template](resource-group-template-deploy.md)（使用 Azure Resource Manager 模板部署应用程序）。


