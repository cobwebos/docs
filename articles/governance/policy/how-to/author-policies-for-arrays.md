---
title: 针对资源的阵列属性创作策略
description: 了解如何使用数组参数和数组语言表达式、如何计算 [*] 别名，以及如何使用 Azure 策略定义规则附加元素。
ms.date: 11/26/2019
ms.topic: how-to
ms.openlocfilehash: 991d159f6444133d902382bc9ca43bc2acd201e2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280659"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>针对 Azure 资源的阵列属性创作策略

Azure 资源管理器属性通常定义为字符串和布尔值。 如果存在一对多关系，则将复杂属性改为定义为数组。 在 Azure 策略中，使用几种不同的方式来使用数组：

- [定义参数](../concepts/definition-structure.md#parameters)的类型，用于提供多个选项
- 使用或**notIn** **中**的条件的[策略规则](../concepts/definition-structure.md#policy-rule)的一部分
- 策略规则的一部分，它计算要评估的[\[\*\] 别名](../concepts/definition-structure.md#understanding-the--alias)：
  - 方案，如 "**无**"、"**任何**" 或 "**全部**"
  - 具有**计数**的复杂方案
- 用于替换或添加到现有数组的[追加效果](../concepts/effects.md#append)

本文介绍 Azure 策略的每个使用情况，并提供几个示例定义。

## <a name="parameter-arrays"></a>参数数组

### <a name="define-a-parameter-array"></a>定义参数数组

如果将参数定义为数组，则在需要多个值时可以实现策略的灵活性。
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

因为**类型**是_字符串_，所以，在分配策略时只能设置一个值。 如果分配了此策略，则仅允许在单个 Azure 区域内使用范围内的资源。 大多数策略定义需要允许使用批准的选项的列表，例如允许使用_eastus2_、 _eastus_和_westus2_。

若要创建策略定义以允许多个选项，请使用_数组_**类型**。 可以重写相同的策略，如下所示：

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
> 保存策略定义后，无法更改参数的**类型**属性。

在策略分配过程中，此新参数定义使用多个值。 定义了数组属性**allowedValues**后，在赋值期间可用的值将进一步限制为预定义的选项列表。 **AllowedValues**的使用是可选的。

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>在赋值期间将值传递给参数数组

通过 Azure 门户分配策略时，**类型**为_array_的参数显示为一个文本框。 提示显示 "Use;分隔值。 （例如，伦敦;纽约） "。 若要将_eastus2_、 _eastus_和_westus2_的允许位置值传递给参数，请使用以下字符串：

`eastus2;eastus;westus2`

使用 Azure CLI、Azure PowerShell 或 REST API 时，参数值的格式不同。 值通过同时包含参数名称的 JSON 字符串传递。

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

- Azure CLI：命令[az policy 赋值 create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) **with parameter parameter**
- Azure PowerShell： Cmdlet [AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment)与参数**PolicyParameter**
- REST API：在_PUT_ [create](/rest/api/resources/policyassignments/create)操作中作为请求正文的一部分作为 properties 属性的值 **。 parameters**属性

## <a name="policy-rules-and-arrays"></a>策略规则和数组

### <a name="array-conditions"></a>数组条件

可在其中使用_数组_
**类型**参数的策略规则[条件](../concepts/definition-structure.md#conditions)仅限于 `in` 和 `notIn`。 使用以下策略定义和条件 `equals` 作为示例：

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

尝试通过 Azure 门户创建此策略定义会导致错误，如以下错误消息：

- 由于验证错误，无法对策略 "{GUID}" 进行参数化。 请检查是否正确定义了策略参数。 语言表达式 "[parameters （' allowedLocations '）]" 的内部异常 "计算结果" 为类型 "Array"，应为 "String" 类型。 "。"

`equals` 的预期条件**类型**为_string_。 由于**allowedLocations**定义为**类型**_数组_，因此策略引擎将计算语言表达式，并引发错误。 在 `in` 和 `notIn` 条件下，策略引擎需要语言表达式中的**类型**_数组_。 若要解决此错误消息，请将 `equals` 更改为 `in` 或 `notIn`。

### <a name="evaluating-the--alias"></a>评估 [*] 别名

具有附加到其名称的 **\[\*\]** 的别名指示该**类型**是一个_数组_。 **\[\*\]** ，而不是计算整个数组的值，而是可以对数组中的每个元素进行单独计算。 每个项目评估有三个标准方案： _None_、 _Any_或_All_元素都匹配。 对于复杂方案，请使用[count](../concepts/definition-structure.md#count)。

**仅当** **if**规则评估为 true 时，策略引擎才会触发中的**效果**。
在 **\[\*\]** 计算数组中每个元素的方式的情况下，必须了解这一点。

下面的方案表的示例策略规则：

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

以下方案表的**ipRules**数组如下所示：

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

以下结果是条件和示例策略规则与上述现有值的数组的组合的结果：

|条件 |业务成效 | 场景 |说明 |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |没 |无匹配项 |一个数组元素的计算结果为 false （127.0.0.1！ = 127.0.0.1），另一个数组元素为 true （127.0.0.1！ = 192.168.1.1），因此**notEquals**条件为_false_ ，并且不触发该效果。 |
|`{<field>,"notEquals":"10.0.4.1"}` |策略效果 |无匹配项 |这两个数组元素的计算结果均为 true （10.0.4.1！ = 127.0.0.1 and 10.0.4.1！ = 192.168.1.1），因此**notEquals**条件为_true_ ，并触发该效果。 |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |策略效果 |一个或多个匹配项 |一个数组元素的计算结果为 false （127.0.0.1！ = 127.0.0.1），另一个数组元素为 true （127.0.0.1！ = 192.168.1.1），因此**notEquals**条件为_false_。 逻辑运算符的计算结果为 true （**不**是_false_），因此将触发该效果。 |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |没 |一个或多个匹配项 |这两个数组元素的计算结果均为 true （10.0.4.1！ = 127.0.0.1 和10.0.4.1！ = 192.168.1.1），因此**notEquals**条件为_true_。 逻辑运算符的计算结果为 false （**not** _true_），因此不会触发该效果。 |
|`"not":{<field>,"Equals":"127.0.0.1"}` |策略效果 |并非全部匹配 |一个数组元素的计算结果为 true （127.0.0.1 = = 127.0.0.1），另一个数组元素为 false （127.0.0.1 = = 192.168.1.1），因此**Equals**条件为_false_。 逻辑运算符的计算结果为 true （**不**是_false_），因此将触发该效果。 |
|`"not":{<field>,"Equals":"10.0.4.1"}` |策略效果 |并非全部匹配 |这两个数组元素的计算结果都为 false （10.0.4.1 = = 127.0.0.1，10.0.4.1 = = 192.168.1.1），因此**Equals**条件为_false_。 逻辑运算符的计算结果为 true （**不**是_false_），因此将触发该效果。 |
|`{<field>,"Equals":"127.0.0.1"}` |没 |所有匹配项 |一个数组元素的计算结果为 true （127.0.0.1 = = 127.0.0.1），另一个数组元素的值为 false （127.0.0.1 = = 192.168.1.1），因此**Equals**条件为_false_且不触发该效果。 |
|`{<field>,"Equals":"10.0.4.1"}` |没 |所有匹配项 |这两个数组元素的计算结果都为 false （10.0.4.1 = = 127.0.0.1，10.0.4.1 = = 192.168.1.1），因此**Equals**条件为_false_ ，并且不会触发该效果。 |

## <a name="the-append-effect-and-arrays"></a>追加效果和数组

[追加效果](../concepts/effects.md#append)的行为会有所不同，具体取决于**详细信息。字段**是 **\[\*\]** 别名。

- 如果不是 **\]别名 \*\[** ，则 append 会将整个数组替换为**value**属性
- 如果 **\[\*\]** 别名，则 append 将**value**属性添加到现有数组，或创建新数组

有关详细信息，请参阅[追加示例](../concepts/effects.md#append-examples)。

## <a name="next-steps"></a>后续步骤

- 查看[Azure 策略示例](../samples/index.md)中的示例。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何以[编程方式创建策略](programmatically-create.md)。
- 了解如何[修正不合规的资源](remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
