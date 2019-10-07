---
title: 针对 Azure 资源中的数组属性创作策略
description: 了解如何使用 Azure Policy 定义规则来创建数组参数、创建数组语言表达式的规则、评估 [*] 别名，以及将元素追加到现有数组。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/06/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: e5b90eb975d0d495723a70095b447d37e051fc0b
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978040"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>针对 Azure 资源中的数组属性创作策略

Azure 资源管理器属性往往定义为字符串和布尔值。 存在一个对多的关系时，复杂属性将定义为数组。 在 Azure Policy 中，可通过多种不同的方式使用数组：

- 可提供多个选项的[定义参数](../concepts/definition-structure.md#parameters)类型
- 使用条件 **in** 或 **notIn** 的[策略规则](../concepts/definition-structure.md#policy-rule)部分
- 可评估 [\[\*\] 别名](../concepts/definition-structure.md#understanding-the--alias)，以评估 **None**、**Any** 或 **All** 等特定方案的策略规则部分
- 使用可以替换或者可以添加到现有数组的 [append 效果](../concepts/effects.md#append)

本文将会介绍 Azure Policy 的每种用法，并提供几个示例定义。

## <a name="parameter-arrays"></a>参数数组

### <a name="define-a-parameter-array"></a>定义参数数组

如果需要多个值，将参数定义为数组可以提高策略的灵活性。
此策略定义允许参数**allowedLocations**的任何单个位置，并默认为_eastus2_：

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

由于**类型**是_字符串_，因此在分配该策略时，只能设置一个值。 如果分配此策略，只允许单个 Azure 区域中的处于范围内的资源。 大多数策略定义需要允许使用批准的选项的列表，例如允许使用_eastus2_、 _eastus_和_westus2_。

若要创建允许多个选项的策略定义，请使用“数组”**类型**。 可按如下所示重新编写同一策略：

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
> 保存策略定义后，无法更改参数中的 **type** 属性。

在分配策略期间，此新参数定义将采用多个值。 如果定义了数组属性 **allowedValues**，则在分配期间，可用值将进一步限制为预定义的选项列表。 **allowedValues** 是可选的。

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>在分配期间将值传递给参数数组

通过 Azure 门户分配策略时，“数组”**类型**的参数将显示为单个文本框。 提示中会指出“请使用 ; 来分隔值。 (例如 London;New York)”。 若要将_eastus2_、 _eastus_和_westus2_的允许位置值传递给参数，请使用以下字符串：

`eastus2;eastus;westus2`

使用 Azure CLI、Azure PowerShell 或 REST API 时，参数值的格式是不同的。 值将会通过也包含参数名称的 JSON 字符串进行传递。

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

若要在每个 SDK 中使用此字符串，请使用以下命令：

- Azure CLI：命令 [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create)，结合参数 **params**
- Azure PowerShell：cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment)，结合参数 **PolicyParameter**
- REST API：在请求正文中使用 _PUT_ [create](/rest/api/resources/policyassignments/create) 操作作为 **properties.parameters** 属性的值

## <a name="policy-rules-and-arrays"></a>策略规则和数组

### <a name="array-conditions"></a>数组条件

可以结合“数组”
**类型**的参数使用的策略规则[条件](../concepts/definition-structure.md#conditions)限制为 `in` 和 `notIn`。 以包含条件 `equals` 的以下策略定义为例：

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

尝试通过 Azure 门户创建此策略定义会导致出现如下所示的错误消息：

- “由于出现验证错误，无法参数化策略 '{GUID}'。 请检查是否正确定义了策略参数。 出现内部异常‘语言表达式 '[parameters('allowedLocations')]' 评估结果的类型为‘数组’，而预期类型为‘字符串’。’”

条件 `equals` 的预期**类型**为“字符串”。 由于 **allowedLocations** 定义为“数组”**类型**，因此策略引擎会评估该语言表达式并引发错误。 使用 `in` 和 `notIn` 条件时，策略引擎预期语言表达式中的**类型**为“数组”。 若要解决此错误消息，请将 `equals` 更改为 `in` 或 `notIn`。

### <a name="evaluating-the--alias"></a>评估 [*] 别名

名称中附加有 **[\*]** 的别名表示**类型**为“数组”。 指定 **[\*]** 可以评估数组的每个元素，而不会评估整个数组的值。 这种按项评估的功能在三种场合下非常有用：None、Any 和 All。

仅当 **if** 规则评估为 true 时，策略引擎才会在 **then** 中触发**效果**。
若要根据上下文了解 **[\*]** 如何评估数组的每个元素，必须知道这一事实。

场景表的示例策略规则如下：

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

以下场景表的 **ipRules** 数组如下所示：

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

以下结果是将上面所示现有值的条件、示例策略规则和数组合并后的结果：

|条件 |结果 |说明 |
|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |无 |一个数组元素评估为 false (127.0.0.1 != 127.0.0.1)，一个数组元素评估为 true (127.0.0.1 != 192.168.1.1)，因此，**notEquals** 条件为 _false_，且不会触发效果。 |
|`{<field>,"notEquals":"10.0.4.1"}` |策略效果 |两个数组元素均评估为 true（10.0.4.1 != 127.0.0.1，10.0.4.1 != 192.168.1.1），因此，**notEquals** 条件为 _true_，并且会触发效果。 |
|`"not":{<field>,"Equals":"127.0.0.1"}` |策略效果 |一个数组元素评估为 true (127.0.0.1 == 127.0.0.1)，一个数组元素评估为 false (127.0.0.1 == 192.168.1.1)，因此，**Equals** 条件为 _false_。 逻辑运算符评估为 true (**not** _false_)，因此会触发效果。 |
|`"not":{<field>,"Equals":"10.0.4.1"}` |策略效果 |两个数组元素均评估为 false（10.0.4.1 == 127.0.0.1，10.0.4.1 == 192.168.1.1），因此，**Equals** 条件为 _false_。 逻辑运算符评估为 true (**not** _false_)，因此会触发效果。 |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |策略效果 |一个数组元素评估为 false (127.0.0.1 != 127.0.0.1)，一个数组元素评估为 true (127.0.0.1 != 192.168.1.1)，因此，**notEquals** 条件为 _false_。 逻辑运算符评估为 true (**not** _false_)，因此会触发效果。 |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |无 |两个数组元素均评估为 true（10.0.4.1 != 127.0.0.1，10.0.4.1 != 192.168.1.1），因此，**notEquals** 条件为 _true_。 逻辑运算符评估为 false (**not** _true_)，因此不会触发效果。 |
|`{<field>,"Equals":"127.0.0.1"}` |无 |一个数组元素评估为 true (127.0.0.1 == 127.0.0.1)，一个数组元素评估为 false (127.0.0.1 == 192.168.1.1)，因此，**Equals** 条件为 _false_，且不会触发效果。 |
|`{<field>,"Equals":"10.0.4.1"}` |无 |两个数组元素均评估为 false（10.0.4.1 == 127.0.0.1，10.0.4.1 == 192.168.1.1），因此，**Equals** 条件为 _false_，且不会触发效果。 |

## <a name="the-append-effect-and-arrays"></a>append 效果和数组

[append 效果](../concepts/effects.md#append)的行为根据 **details.field** 是否为 **[\*]** 别名而有所不同。

- 如果不是 **[\*]** 别名，则 append 会将整个数组替换为 **value** 属性
- 如果是 **[\*]** 别名，则 append 会将 **value** 属性添加到现有数组，或创建新数组

有关详细信息，请参阅 [append 示例](../concepts/effects.md#append-examples)。

## <a name="next-steps"></a>后续步骤

- 查看[Azure 策略示例](../samples/index.md)中的示例。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何以[编程方式创建策略](programmatically-create.md)。
- 了解如何[修正不合规的资源](remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。