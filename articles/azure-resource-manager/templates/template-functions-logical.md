---
title: 模板函数-逻辑
description: 介绍 Azure 资源管理器模板中用于确定逻辑值的函数。
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 48c6a05c5a449626c66f9d75c8578b217906d8e0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483955"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>用于 Azure 资源管理器模板的逻辑函数

Resource Manager 提供了多个用于在模板中进行比较的函数。

* [and](#and)
* [bool](#bool)
* [if](#if)
* [not](#not)
* [or](#or)

## <a name="and"></a>和

`and(arg1, arg2, ...)`

检查所有参数值是否均为 true。

### <a name="parameters"></a>参数

| 参数 | 需要 | 类型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |是 |boolean |第一个值，需检查其是否为 true。 |
| arg2 |是 |boolean |第二个值，需检查其是否为 true。 |
| 其他参数 |否 |boolean |用于检查是否为 true 的其他参数。 |

### <a name="return-value"></a>返回值

如果所有值均为 true，则返回 True；否则返回 False。

### <a name="examples"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)演示如何使用逻辑函数。

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
| andExampleOutput | Bool | 错误 |
| orExampleOutput | Bool | 正确 |
| notExampleOutput | Bool | 错误 |

## <a name="bool"></a>bool

`bool(arg1)`

将参数转换为布尔值。

### <a name="parameters"></a>参数

| 参数 | 需要 | 类型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |是 |字符串或整数 |要转换为布尔值的值。 |

### <a name="return-value"></a>返回值
转换后的值的布尔值。

### <a name="examples"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json)演示如何对字符串或整数使用 bool。

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
| trueString | Bool | 正确 |
| falseString | Bool | 错误 |
| trueInt | Bool | 正确 |
| falseInt | Bool | 错误 |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

根据条件为 true 或 false 返回值。

### <a name="parameters"></a>参数

| 参数 | 需要 | 类型 | Description |
|:--- |:--- |:--- |:--- |
| 条件 (condition) |是 |boolean |要检查其是否为 true 或 false 的值。 |
| trueValue |是 | 字符串、int、对象或数组 |条件为 true 时返回的值。 |
| falseValue |是 | 字符串、int、对象或数组 |条件为 false 时返回的值。 |

### <a name="return-value"></a>返回值

如果第一个参数为 True，则返回第二个参数；否则返回第三个参数。

### <a name="remarks"></a>备注

当条件为**true**时，只计算 true 值。 如果条件为**false**，则只计算 false 值。 对于**if**函数，可以包括仅有条件地有效的表达式。 例如，您可以引用在一个条件下存在但不在其他条件下的资源。 下一节中显示了有条件地计算表达式的示例。

### <a name="examples"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json)演示如何使用 `if` 函数。

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
        },
        "objectOutput": {
            "type": "object",
            "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
        }
    }
}
```

前述示例的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| yesOutput | String | 是 |
| noOutput | String | 否 |
| objectOutput | 对象 | { "test": "value1" } |

以下[示例模板](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json)演示了如何将此函数用于仅有条件地有效的表达式。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "logAnalytics": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "condition": "[not(empty(parameters('logAnalytics')))]",
            "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2017-03-30",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
```

## <a name="not"></a>not

`not(arg1)`

将布尔值转换为其相反值。

### <a name="parameters"></a>参数

| 参数 | 需要 | 类型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |是 |boolean |要转换的值。 |

### <a name="return-value"></a>返回值

参数为 False 时返回 True。 参数为 True 时返回 False。

### <a name="examples"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)演示如何使用逻辑函数。

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
| andExampleOutput | Bool | 错误 |
| orExampleOutput | Bool | 正确 |
| notExampleOutput | Bool | 错误 |

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json)结合使用 **not** 和 [equals](template-functions-comparison.md#equals)。

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

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| checkNotEquals | Bool | 正确 |

## <a name="or"></a>或

`or(arg1, arg2, ...)`

检查任何参数值是否为 true。

### <a name="parameters"></a>参数

| 参数 | 需要 | 类型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |是 |boolean |第一个值，需检查其是否为 true。 |
| arg2 |是 |boolean |第二个值，需检查其是否为 true。 |
| 其他参数 |否 |boolean |用于检查是否为 true 的其他参数。 |

### <a name="return-value"></a>返回值

如果任何值为 true，则返回 True；否则返回 False。

### <a name="examples"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)演示如何使用逻辑函数。

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
| andExampleOutput | Bool | 错误 |
| orExampleOutput | Bool | 正确 |
| notExampleOutput | Bool | 错误 |

## <a name="next-steps"></a>后续步骤

* 有关 Azure 资源管理器模板中各部分的说明，请参阅[创作 Azure 资源管理器模板](template-syntax.md)。
* 要合并多个模板，请参阅[将链接的模板与 Azure 资源管理器配合使用](linked-templates.md)。
* 若要在创建资源类型时迭代指定的次数，请参阅[在 Azure 资源管理器中创建多个资源实例](create-multiple-instances.md)。
* 要查看如何部署已创建的模板，请参阅[使用 Azure 资源管理器模板部署应用程序](deploy-powershell.md)。

