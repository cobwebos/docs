---
title: 为资源上的数组属性创作策略
description: 了解如何使用数组参数和数组语言表达式，如何计算 [*] 别名，以及如何使用 Azure Policy 定义规则追加元素。
ms.date: 05/20/2020
ms.topic: how-to
ms.openlocfilehash: f3d30f76d555386e5ab8041a0b8cc82b5b60e28e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684253"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>为 Azure 资源上的数组属性创作策略

Azure 资源管理器属性通常定义为字符串和布尔值。 存在一对多关系时，复杂属性将定义为数组。 在 Azure Policy 中，通过以下几种不同的方式来使用数组：

- [定义参数](../concepts/definition-structure.md#parameters)的类型，用于提供多个选项
- 使用条件 in 或 notIn 的[策略规则](../concepts/definition-structure.md#policy-rule)的一部分 
- 用于计算[\[\*\]别名](../concepts/definition-structure.md#understanding-the--alias)的策略规则的一部分：
  - 方案，如“无”、“任何”或“全部”  
  - 具有计数的复杂方案
- 在[追加效果](../concepts/effects.md#append)中，用于替换或添加到现有数组

本文介绍 Azure Policy 对每种方式的使用情况，并提供了几个示例定义。

## <a name="parameter-arrays"></a>参数数组

### <a name="define-a-parameter-array"></a>定义参数数组

需要多个值时，将参数定义为数组可以实现策略的灵活性。
此策略定义允许参数 allowedLocations 的任何单个位置，默认为 _eastus2_：

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

由于类型为字符串，因此在分配策略时只能设置一个值。 如果分配了此策略，则仅允许在单个 Azure 区域内使用范围内的资源。 大多数策略定义需要允许批准的选项列表，例如允许 eastus2、eastus 和 westus2。

若要创建策略定义以允许多个选项，请使用“数组”类型。 同一个策略可以重写，如下所示：

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> 保存策略定义后，无法更改参数上的属性。

在策略分配过程中，此新参数定义会使用多个值。 定义数组属性 allowedValues 后，分配期间可用的值将进一步限制为预定义的选项列表。 可以选择使用 allowedValues。

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>在分配期间将值传递给参数数组

通过 Azure 门户分配策略时，数组类型的参数显示为单个文本框。 提示显示“使用 ; 来分隔值。 （例如，伦敦;纽约）”。 若要将 eastus2、eastus 和 westus2 的允许位置值传递到参数，请使用以下字符串：

`eastus2;eastus;westus2`

使用 Azure CLI、Azure PowerShell 或 REST API 时，参数值的格式不同。 这些值通过 JSON 字符串（还包括参数名称）传递。

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

若要将此字符串与每个 SDK 一起使用，请使用以下命令：

- Azure CLI：带有参数 params 的命令 [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create)
- Azure PowerShell：带有参数 PolicyParameter 的 Cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment)
- REST API：在 PUT [create](/rest/api/resources/policyassignments/create) 操作中，作为请求正文（作为 properties.parameters 属性的值）的一部分

## <a name="policy-rules-and-arrays"></a>策略规则和数组

### <a name="array-conditions"></a>数组条件

可与参数的数组
类型一起使用的策略规则[条件](../concepts/definition-structure.md#conditions)限制为 `in` 和 `notIn`。 以带有条件 `equals` 的以下策略定义为例：

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

尝试通过 Azure 门户创建此策略定义会导致类似如下的错误消息：

- “由于验证错误，无法对策略‘{GUID}’进行参数化。 请检查策略参数定义是否正确。 内部异常语言表达式‘[parameters('allowedLocations')]’的计算结果为‘数组’类型，预期类型为‘字符串’。”

条件 `equals` 的预期类型为_字符串_。 由于 allowedLocations 被定义为数组类型，因此策略引擎会计算语言表达式并引发错误 。 在 `in` 和 `notIn` 条件下，策略引擎在语言表达式中应为“数组”类型。 若要解决此错误消息，请将 `equals` 更改为 `in` 或 `notIn`。

### <a name="evaluating-the--alias"></a>计算 [*] 别名

将 \[\*\] 附加到其名称的别名表明其类型为“数组” 。 \[\*\] 可以使用逻辑 AND 分别计算数组的每个元素，而不是计算整个数组的值。 每个项目中有三个标准方案可供计算使用：“无”、“任何”或“全部”元素匹配。 对于复杂方案，请使用[计数](../concepts/definition-structure.md#count)。

仅当 if 规则的计算结果为 true 时，策略引擎才会触发 then 的效果  。
此事实对于了解 \[\*\] 如何计算数组的每个单独元素非常重要。

方案表的示例策略规则如下：

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

对于以下方案表，ipRules 数组如下所示：

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

对于下面的每个条件示例，请将 `<field>` 替换为 `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`。

以下结果是条件和示例策略规则与上述现有值的数组的组合带来的结果：

|条件 |业务成效 | 场景 |说明 |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |无 |无匹配 |一个数组元素的计算结果为 false (127.0.0.1 != 127.0.0.1)，另一个的计算结果为 true (127.0.0.1 != 192.168.1.1)，因此 notEquals 条件为 false，不会触发该效果。 |
|`{<field>,"notEquals":"10.0.4.1"}` |策略效果 |无匹配 |两个数组元素的计算结果均为 true（10.0.4.1 != 127.0.0.1 和 10.0.4.1 != 192.168.1.1），因此 notEquals 条件为 true，会触发该效果。 |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |策略效果 |一个或多个匹配 |一个数组元素的计算结果为 false (127.0.0.1 != 127.0.0.1)，另一个的计算结果为 true (127.0.0.1 != 192.168.1.1)，因此 notEquals 条件为 false。 逻辑运算符的计算结果为 true（不为 false），因此会触发该效果。 |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |无 |一个或多个匹配 |两个数组元素的计算结果均为 true（10.0.4.1 != 127.0.0.1 和 10.0.4.1 != 192.168.1.1），因此 notEquals 条件为 true。 逻辑运算符的计算结果为 false（不为 true），因此不会触发该效果。 |
|`"not":{<field>,"Equals":"127.0.0.1"}` |策略效果 |并非全部匹配 |一个数组元素的计算结果为 true (127.0.0.1 == 127.0.0.1)，另一个的计算结果为 false (127.0.0.1 == 192.168.1.1)，因此 Equals 条件为 false。 逻辑运算符的计算结果为 true（不为 false），因此会触发该效果。 |
|`"not":{<field>,"Equals":"10.0.4.1"}` |策略效果 |并非全部匹配 |两个数组元素的计算结果均为 false（10.0.4.1 == 127.0.0.1 和 10.0.4.1 == 192.168.1.1），因此 Equals 条件为 false。 逻辑运算符的计算结果为 true（不为 false），因此会触发该效果。 |
|`{<field>,"Equals":"127.0.0.1"}` |无 |全部匹配 |一个数组元素的计算结果为 true (127.0.0.1 == 127.0.0.1)，另一个的计算结果为 false (127.0.0.1 == 192.168.1.1)，因此 Equals 条件为 false，不会触发该效果。 |
|`{<field>,"Equals":"10.0.4.1"}` |无 |全部匹配 |两个数组元素的计算结果均为 false（10.0.4.1 == 127.0.0.1 和 10.0.4.1 == 192.168.1.1），因此 Equals 条件为 false，不会触发该效果。 |

## <a name="the-append-effect-and-arrays"></a>追加效果和数组

[追加效果](../concepts/effects.md#append)的行为有所不同，具体取决于 details.field 是否为 \[\*\] 别名 。

- 如果不是 \[\*\] 别名，则追加会将整个数组替换为值属性 
- 如果是 \[\*\] 别名，则追加会将值属性添加到现有数组或创建新数组 

有关详细信息，请参阅[追加示例](../concepts/effects.md#append-examples)。

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何[以编程方式创建策略](programmatically-create.md)。
- 了解如何[修正不符合的资源](remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
