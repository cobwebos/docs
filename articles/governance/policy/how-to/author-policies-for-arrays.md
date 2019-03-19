---
title: Azure 资源上的阵列属性的作者策略
description: 了解如何创建数组的参数，创建语言表达式的数组的规则，评估 [*] 别名，以及如何将元素追加到现有数组与 Azure 策略定义规则。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/06/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 62267a4549355212a18654ff9781b2164ba19fa9
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2019
ms.locfileid: "57860234"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Azure 资源上的阵列属性的作者策略

Azure 资源管理器属性通常定义为字符串和布尔值。 存在一个对多关系时，复杂属性改为定义为数组。 在 Azure 策略数组用在多种不同的方式：

- 类型[definition 参数](../concepts/definition-structure.md#parameters)，以提供多个选项
- 一部分[策略规则](../concepts/definition-structure.md#policy-rule)使用条件**中**或**notIn**
- 策略规则的计算结果的一部分[ \[ \* \]别名](../concepts/definition-structure.md#understanding-the--alias)若要计算特定方案如**None**，**任何**，或**所有**
- 在中[追加效果](../concepts/effects.md#append)来替换或添加到现有的数组

本文介绍了每次使用 Azure 策略，并提供了几个示例中定义。

## <a name="parameter-arrays"></a>参数数组

### <a name="define-a-parameter-array"></a>定义参数数组

需要多个值时，作为一个数组中定义的参数允许策略灵活性。
此策略定义允许的参数的任何单个位置**allowedLocations**默认值为_eastus2_:

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

作为**类型**已_字符串_，只有一个值时可以设置分配策略。 如果此策略分配，在单个 Azure 区域中仅允许范围内的资源。 需要有关的已批准的选项，例如允许列表允许大多数策略定义_eastus2_， _eastus_，并_westus2_。

若要创建策略定义，以允许多个选项，请使用_数组_**类型**。 可以按如下所示重新编写相同的策略：

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
> 保存策略定义后，**类型**参数上的属性不能更改。

此新的参数定义在分配策略期间采用多个值。 与数组属性**allowedValues**定义，在分配过程中可用的值是进一步限制为预定义的选择列表。 利用**allowedValues**是可选的。

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>在分配期间将值传递给参数数组

分配策略通过 Azure 门户的参数时**类型**_数组_显示为单个文本框。 提示表示"使用;若要分隔值。 （例如伦敦;New York)"。 若要允许的位置的值传递_eastus2_， _eastus_，并_westus2_到参数，可使用以下字符串：

`eastus2;eastus;westus2`

使用 Azure CLI、 Azure PowerShell 或 REST API 时不同的参数值的格式。 将值传递通过 JSON 字符串，它还包括参数的名称。

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

若要与每个 SDK 配合使用此字符串，请使用以下命令：

- Azure CLI：命令[az 策略分配创建](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create)参数与**params**
- Azure PowerShell：Cmdlet[新建 AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment)参数与**PolicyParameter**
- REST API：在中_放_[创建](/rest/api/resources/policyassignments/create)操作作为请求正文的一部分的值作为**properties.parameters**属性

## <a name="policy-rules-and-arrays"></a>策略规则和数组

### <a name="array-conditions"></a>数组条件

策略规则[条件](../concepts/definition-structure.md#conditions)的_数组_
**类型**可能使用的参数使用仅限于`in`和`notIn`。 采取以下策略定义具有条件`equals`作为示例：

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

尝试创建此策略定义，通过 Azure 门户导致错误，如此错误消息：

- "策略 {GUID} 无法参数化，因为验证错误。 请检查策略参数是否正确定义。 内部异常评估结果的语言表达式 [parameters('allowedLocations')] 是数组类型需要的类型为 'String' '。"

预期**类型**的条件`equals`是_字符串_。 由于**allowedLocations**指**类型**_数组_，策略引擎计算语言表达式，并将引发错误。 与`in`并`notIn`条件，策略引擎预期**类型**_数组_语言表达式中。 若要解决此错误消息，请更改`equals`至任一`in`或`notIn`。

### <a name="evaluating-the--alias"></a>评估 [*] 别名

具有别名 **[\*]** 附加到其名称指示**类型**是_数组_。 而不是评估整个数组的值 **[\*]** 就可以评估每个元素的数组。 有三种方案这每项评估中很有用：None、 任何，并且所有。

策略引擎触发器**效果**中**然后**时，才**如果**规则评估为 true。
这一点非常重要的方式的上下文中理解 **[\*]** 数组的每个元素的计算结果。

下面的方案中表的示例策略规则：

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

**IpRules**数组，如下所示为下方案表：

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

对于以下每个条件示例中，替换`<field>`与`"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`。

条件的示例策略规则和更高版本的现有值的数组的组合的结果的输出如下：

|条件 |结果 |说明 |
|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |执行任何操作 |一个数组元素的计算结果为 false (127.0.0.1 ！ = 127.0.0.1)，另一个为 true (127.0.0.1 ！ = 192.168.1.1)，因此**notEquals**条件_false_和效果不会触发。 |
|`{<field>,"notEquals":"10.0.4.1"}` |策略效果 |这两个数组元素的计算结果为 true (10.0.4.1 ！ = 127.0.0.1 和 10.0.4.1 ！ = 192.168.1.1)，因此**notEquals**条件_true_和触发效果。 |
|`"not":{<field>,"Equals":"127.0.0.1"}` |策略效果 |一个数组元素的计算结果为 true (127.0.0.1 = = 127.0.0.1)，另一个为 false (127.0.0.1 = = 192.168.1.1)，因此**等于**条件_false_。 逻辑运算符计算结果为 true (**不** _false_)，因此触发效果。 |
|`"not":{<field>,"Equals":"10.0.4.1"}` |策略效果 |这两个数组元素的计算结果为 false (10.0.4.1 = = 127.0.0.1 和 10.0.4.1 = = 192.168.1.1)，因此**等于**条件_false_。 逻辑运算符计算结果为 true (**不** _false_)，因此触发效果。 |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |策略效果 |一个数组元素的计算结果为 false (127.0.0.1 ！ = 127.0.0.1)，另一个为 true (127.0.0.1 ！ = 192.168.1.1)，因此**notEquals**条件_false_。 逻辑运算符计算结果为 true (**不** _false_)，因此触发效果。 |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |执行任何操作 |这两个数组元素的计算结果为 true (10.0.4.1 ！ = 127.0.0.1 和 10.0.4.1 ！ = 192.168.1.1)，因此**notEquals**条件_true_。 逻辑运算符的计算结果为 false (**不** _true_)，因此效果不会触发。 |
|`{<field>,"Equals":"127.0.0.1"}` |执行任何操作 |一个数组元素的计算结果为 true (127.0.0.1 = = 127.0.0.1)，另一个为 false (127.0.0.1 = = 192.168.1.1)，因此**等于**条件_false_和效果不会触发。 |
|`{<field>,"Equals":"10.0.4.1"}` |执行任何操作 |这两个数组元素的计算结果为 false (10.0.4.1 = = 127.0.0.1 和 10.0.4.1 = = 192.168.1.1)，因此**等于**条件_false_和效果不会触发。 |

## <a name="the-append-effect-and-arrays"></a>追加效果和数组

[追加效果](../concepts/effects.md#append)行为有所不同，具体取决于**details.field**是 **[\*]** 别名与否。

- 时未 **[\*]** 别名，追加替换整个数组**值**属性
- 当 **[\*]** 别名，追加添加**值**属性设置为现有数组，或创建新的数组

有关详细信息，请参阅[追加示例](../concepts/effects.md#append-examples)。

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](../samples/index.md)中查看示例
- 查看[策略定义结构](../concepts/definition-structure.md)
- 查看[了解策略效果](../concepts/effects.md)
- 了解如何[以编程方式创建策略](programmatically-create.md)
- 了解如何[修正不符合资源](remediate-resources.md)
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组