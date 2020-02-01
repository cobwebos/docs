---
title: 策略定义结构的详细信息
description: 介绍如何使用策略定义为组织中的 Azure 资源建立约定。
ms.date: 11/26/2019
ms.topic: conceptual
ms.openlocfilehash: e37ff6e1bde594014510880492c2572ad1634400
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904417"
---
# <a name="azure-policy-definition-structure"></a>Azure 策略定义结构

资源策略定义由 Azure 策略用于建立资源约定。 每个定义描述了资源符合性，以及当资源不符合时要采取的措施。
通过定义约定，可以控制成本并更轻松地管理资源。 例如，可以指定仅允许某些类型的虚拟机。 或者，你可以要求所有资源都有特定标记。 策略由所有子资源继承。 如果将策略应用到资源组，则该策略适用于该资源组中的所有资源。

策略定义架构可在此处找到： [https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

使用 JSON 创建策略定义。 策略定义包含以下内容的元素：

- 众
- Parameters
- 显示名称
- description
- 策略规则
  - 逻辑计算
  - 实际

例如，以下 JSON 显示了限制资源部署位置的策略：

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": [ "westus2" ]
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

所有 Azure 策略示例均在[Azure 策略示例](../samples/index.md)中。

## <a name="mode"></a>模式

**模式**的配置取决于策略是以 Azure 资源管理器属性还是资源提供程序属性为目标。

### <a name="resource-manager-modes"></a>资源管理器模式

**模式**决定将为策略评估哪些资源类型。 支持的模式如下：

- `all`：评估资源组和所有资源类型
- `indexed`：仅评估支持标记和位置的资源类型

建议在大多数情况下将**模式**设置为 `all`。 通过门户创建的所有策略定义都使用 `all` 模式。 如果使用 PowerShell 或 Azure CLI，则可以手动指定**模式**参数。 如果策略定义不包含**模式**值，则默认为 Azure PowerShell 中 `all`，并 `null` Azure CLI 中。 `null` 模式等同于使用 `indexed` 支持向后兼容性。

创建强制标记或位置的策略时，应使用 `indexed`。 虽然不是必需的，但它会阻止不支持标记和位置的资源显示为符合性结果中不符合的资源。 **资源组**是个例外。 在资源组上强制位置或标记的策略应将**模式**设置为 "`all`"，并专门针对 `Microsoft.Resources/subscriptions/resourceGroups` 类型。 有关示例，请参阅[强制资源组标记](../samples/enforce-tag-rg.md)。 有关支持标记的资源的列表，请参阅[Azure 资源的标记支持](../../../azure-resource-manager/management/tag-support.md)。

### <a name="a-nameresource-provider-modes-resource-provider-modes-preview"></a><a name="resource-provider-modes" />资源提供程序模式（预览）

预览期间目前支持以下资源提供程序模式：

- 用于管理[Azure Kubernetes 服务](../../../aks/intro-kubernetes.md)上的许可控制器规则 `Microsoft.ContainerService.Data`。 使用此资源提供程序模式的策略**必须**使用[EnforceRegoPolicy](./effects.md#enforceregopolicy)效果。
- 用于管理 Azure 上的 AKS 引擎 Kubernetes 群集的 `Microsoft.Kubernetes.Data`。
  使用此资源提供程序模式的策略**必须**使用[EnforceOPAConstraint](./effects.md#enforceopaconstraint)效果。
- 用于在[Azure Key Vault](../../../key-vault/key-vault-overview.md)中管理保管库和证书的 `Microsoft.KeyVault.Data`。

> [!NOTE]
> 资源提供程序模式仅支持内置策略定义，并在预览时不支持计划。

## <a name="parameters"></a>Parameters

参数可减少策略定义的数量，有助于简化策略管理。 请考虑参数，如窗体上的字段-`name`、`address`、`city``state`。 这些参数的值始终保持不变，但是，它们的值会根据表单中填写的个体发生变化。
生成策略时，参数的工作方式相同。 通过在策略定义中包含参数，你可以使用不同的值针对不同的方案重复使用该策略。

> [!NOTE]
> 可以向现有的和分配的定义添加参数。 新参数必须包含**defaultValue**属性。 这会阻止现有策略或计划的现有分配间接变为无效。

### <a name="parameter-properties"></a>参数属性

参数具有以下在策略定义中使用的属性：

- **名称**：参数的名称。 由策略规则中的 `parameters` 部署函数使用。 有关详细信息，请参阅[使用参数值](#using-a-parameter-value)。
- `type`：确定参数是否为**字符串**、**数组**、**对象**、**布尔值**、**整数**、**浮点数**或**日期时间**。
- `metadata`：定义主要由 Azure 门户用于显示用户友好信息的子属性：
  - `description`：有关参数用途的说明。 可用于提供可接受值的示例。
  - `displayName`：门户中为参数显示的友好名称。
  - `strongType`：（可选）通过门户分配策略定义时使用。 提供上下文感知列表。 有关详细信息，请参阅[strongType](#strongtype)。
  - `assignPermissions`：（可选）将设置为_true_ ，以便 Azure 门户在策略分配过程中创建角色分配。 如果希望在分配范围之外分配权限，此属性很有用。 策略中的每个角色定义有一个角色分配（或计划中的所有策略中的每个角色定义）。 参数值必须是有效的资源或范围。
- `defaultValue`：（可选）如果未指定任何值，则设置赋值中的参数值。
  在更新分配的现有策略定义时是必需的。
- `allowedValues`：（可选）提供参数数组，该参数在赋值期间接受。

例如，你可以定义一个策略定义，以限制可在其中部署资源的位置。 可能**allowedLocations**该策略定义的参数。 策略定义的每个分配都将使用此参数来限制接受的值。 使用**strongType**可在通过门户完成分配时提供增强的体验：

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>使用参数值

在策略规则中，用以下 `parameters` 函数语法引用参数：

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

此示例引用了在[参数属性](#parameter-properties)中演示的**allowedLocations**参数。

### <a name="strongtype"></a>strongType

在 `metadata` 属性中，可以使用**strongType**提供 Azure 门户中的多选选项列表。 **StrongType**的允许值当前包括：

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`
- `omsWorkspace`
- `Microsoft.EventHub/Namespaces/EventHubs`
- `Microsoft.EventHub/Namespaces/EventHubs/AuthorizationRules`
- `Microsoft.EventHub/Namespaces/AuthorizationRules`
- `Microsoft.RecoveryServices/vaults`
- `Microsoft.RecoveryServices/vaults/backupPolicies`

## <a name="definition-location"></a>定义位置

创建计划或策略时，需要指定定义位置。 定义位置必须是管理组或订阅。 此位置确定可向其分配方案或策略的范围。 资源必须是要作为赋值目标的定义位置的层次结构内或子级的直接成员。

如果定义位置是：

- 只能**为订阅中**的资源分配策略。
- 仅限**管理组**-可以为子管理组和子订阅中的资源分配策略。 如果计划将策略定义应用于多个订阅，则该位置必须是包含这些订阅的管理组。

## <a name="display-name-and-description"></a>显示名称和说明

使用**displayName**和**description**标识策略定义，并在使用时提供上下文。 **displayName**最大长度为_128_个**字符，最大长度为** _512_个字符。

> [!NOTE]
> 在创建或更新策略定义时， **id**、**类型**和**名称**由 json 的外部属性定义，在 json 文件中不是必需的。 通过 SDK 获取策略定义会将**id**、**类型**和**名称**属性作为 JSON 的一部分返回，但每个属性都是与策略定义相关的只读信息。

## <a name="policy-rule"></a>策略规则

策略规则由**If**和**Then**块组成。 在**If**块中，定义一个或多个指定何时强制实施策略的条件。 您可以对这些条件应用逻辑运算符，以精确定义策略的方案。

在**Then**块中，定义满足**If**条件时产生的效果。

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>逻辑运算符

支持的逻辑运算符为：

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

**Not**语法反转条件的结果。 **AllOf**语法（与逻辑**And**操作相似）要求所有条件均为 true。 **AnyOf**语法（与逻辑**Or**操作相似）要求一个或多个条件为 true。

可以嵌套逻辑运算符。 下面的示例演示了嵌套在**allOf**操作中的**not**操作。

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>条件

条件计算**字段**或**值**访问器是否满足特定的条件。 支持的条件有：

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "value"`
- `"lessOrEquals": "value"`
- `"greater": "value"`
- `"greaterOrEquals": "value"`
- `"exists": "bool"`

使用**like**和**notLike**条件时，可以在值中提供通配符 `*`。
此值不应包含多个通配符 `*`。

使用**match**和**notMatch**条件时，请提供 `#` 来匹配数字，`?` 对于字母，`.` 与任何字符匹配，并使用任何其他字符匹配该实际字符。
**match**和**notMatch**区分大小写。 **MatchInsensitively**和**notMatchInsensitively**中提供不区分大小写的替代项。 有关示例，请参阅[允许多种名称模式](../samples/allow-multiple-name-patterns.md)。

### <a name="fields"></a>字段

使用字段构成条件。 字段与资源请求负载中的属性相匹配，并描述资源的状态。

支持以下字段：

- `name`
- `fullName`
  - 返回资源的全名。 资源的全名是由任何父资源名称（例如 "myServer/myDatabase"）预置的资源名称。
- `kind`
- `type`
- `location`
  - 对于位置不可知的资源，请使用**global** 。 有关示例，请参阅[示例-允许的位置](../samples/allowed-locations.md)。
- `identity.type`
  - 返回在资源上启用的[托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)的类型。
- `tags`
- `tags['<tagName>']`
  - 此括号语法支持具有标点的标记名称（例如连字符、句点或空格）。
  - 其中 **\<tagName\>** 是要验证其条件的标记的名称。
  - 示例： `tags['Acct.CostCenter']` 其中**CostCenter**是标记的名称。
- `tags['''<tagName>''']`
  - 此括号语法通过使用双撇号进行转义来支持在其上有撇号的标记名称。
  - 其中 **"\<tagName\>"** 是用于验证条件的标记的名称。
  - 示例： `tags['''My.Apostrophe.Tag''']`，其中 **"My. tag"** 是标记的名称。
- 属性别名-对于列表，请参阅[别名](#aliases)。

> [!NOTE]
> `tags.<tagName>`、`tags[tagName]`和 `tags[tag.with.dots]` 仍可接受声明标记字段的方式。 但是，首选的表达式是上面列出的表达式。

#### <a name="use-tags-with-parameters"></a>使用带参数的标记

参数值可传递给标记字段。 向标记字段传递参数可以在策略分配过程中提高策略定义的灵活性。

在下面的示例中，`concat` 用于为标记命名为的标记的标记字段查找。 如果该标记不存在，则使用 "`resourcegroup()` 查找" 功能，将 "**修改**" 效果用于使用为审核资源父资源组上的相同命名标记集的值添加标记。

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ]
        }
    }
}
```

### <a name="value"></a>“值”

还可以使用**值**构成条件。 **值**对照[参数](#parameters)、支持的[模板函数](#policy-functions)或文本检查条件。
**值**与任何支持的[条件](#conditions)成对出现。

> [!WARNING]
> 如果_模板函数_的结果为错误，则策略评估将失败。 失败评估是隐式**拒绝**。 有关详细信息，请参阅[避免模板失败](#avoiding-template-failures)。

#### <a name="value-examples"></a>值示例

此策略规则示例使用**value**来比较 `resourceGroup()` 函数的结果和返回的**name**属性与 `*netrg`**类似**的条件。 规则在名称以 `*netrg`结尾的任何资源组中拒绝 `Microsoft.Network/*`**类型**的任何资源。

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

此策略规则示例使用**value**来检查多个嵌套函数的结果是否**等于**`true`。 规则拒绝没有至少三个标记的任何资源。

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": true
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>避免模板失败

使用中的_模板函数_可以**实现许多**复杂的嵌套函数。 如果_模板函数_的结果为错误，则策略评估将失败。 失败评估是隐式**拒绝**。 在某些情况下失败的**值**的示例：

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

上面的示例策略规则使用[substring （）](../../../azure-resource-manager/templates/template-functions-string.md#substring)将**name**的前三个字符与**abc**进行比较。 如果**name**小于三个字符，`substring()` 函数将导致错误。 此错误将导致策略变成**拒绝**效果。

相反，请使用[if （）](../../../azure-resource-manager/templates/template-functions-logical.md#if)函数检查**name**等于**abc**的前三个字符是否不允许**名称**少于三个字符来导致错误：

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

在修改后的策略规则中，`if()` 将检查**名称**长度，然后再尝试获取长度少于三个字符的值的 `substring()`。 如果**name**太短，则改为返回值 "not 始于 abc"，并将其与**abc**进行比较。 短名称不以**abc**开头的资源仍未通过策略规则，但在评估过程中不再导致错误。

### <a name="count"></a>Count

计算资源有效负载中数组的多少个成员满足条件表达式的条件可以使用**计数**表达式形成。 常见的方案是检查是否至少有一个 "、" 和 "全部为"，或数组成员是否满足条件。 **count**计算一个条件表达式的每个数组成员并为_true_结果求和，然后将结果与表达式运算符进行比较。

**计数**表达式的结构为：

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

以下属性与**count**一起使用：

- **count. field** （必需）：包含数组的路径，必须是数组别名。 如果缺少数组，则表达式的计算结果为_false_ ，而不考虑条件表达式。
- **count。 where** （可选）：用于分别计算**count** [\[\] alias](#understanding-the--alias)数组成员的每个 \*的条件表达式。 如果未提供此属性，则将路径为 "field" 的所有数组成员均计算为_true_。 任何[条件](../concepts/definition-structure.md#conditions)都可以在此属性内使用。
  可以在此属性内使用[逻辑运算符](#logical-operators)来创建复杂的评估要求。
- **\<条件\>** （必需）：将值与满足计数的项的数目进行比较 **。 where**条件表达式。 应使用数值[条件](../concepts/definition-structure.md#conditions)。

#### <a name="count-examples"></a>计数示例

示例1：检查数组是否为空

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

示例2：检查是否只有一个数组成员满足条件表达式

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

示例3：检查是否至少有一个数组成员满足条件表达式

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

示例4：检查所有对象数组成员是否满足条件表达式

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

示例5：检查所有字符串数组成员是否满足条件表达式

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
            "like": "*@contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

示例 6 **：使用**值内部**值**检查所有数组成员是否满足条件表达式

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "value": "[last(split(first(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]')), '@'))]",
            "equals": "contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

示例7：检查是否至少有一个数组成员与条件表达式中的多个属性相匹配

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>实际

Azure 策略支持以下类型的影响：

- **Append**：将定义的字段集添加到请求
- **Audit**：会在活动日志中生成一个警告事件，但不会使请求失败
- **AuditIfNotExists**：如果相关资源不存在，则会在活动日志中生成警告事件
- **Deny**：在活动日志中生成事件，并导致请求失败
- **DeployIfNotExists**：部署相关资源（如果尚不存在）
- **已禁用**：不对策略规则的符合性评估资源
- **EnforceOPAConstraint** （预览版）：为 Azure 上的自托管 Kubernetes 群集配置打开策略代理招生控制器和网关控制器 v3 （预览版）
- **EnforceRegoPolicy** （预览版）：在 Azure Kubernetes 服务中配置打开策略代理招生控制器和网关守卫 v2
- **修改**：添加、更新或删除资源中定义的标记

有关每个效果、评估顺序、属性和示例的完整详细信息，请参阅[了解 Azure 策略影响](effects.md)。

### <a name="policy-functions"></a>策略函数

除了下面的函数和用户定义的函数外，还可以在策略规则中使用所有[资源管理器模板函数](../../../azure-resource-manager/templates/template-functions.md)：

- copyIndex （）
- 部署（）
- 成员列表
- newGuid （）
- pickZones()
- 提供程序（）
- reference （）
- resourceId （）
- variables （）

以下函数可在策略规则中使用，但不同于 Azure 资源管理器模板中的使用：

- addDays(dateTime, numberOfDaysToAdd)
  - **dateTime**： [Required] 通用 ISO 8601 dateTime 格式 "Yyyy-mm-dd-yyyy-mm-ddthh： MM： fffffffZ" 中的字符串
  - **numberOfDaysToAdd**： [必需] 整数-要添加的天数
- utcNow （）-与资源管理器模板不同，此值可在 defaultValue 之外使用。
  - 返回一个字符串，该字符串设置为当前日期和时间，采用通用 ISO 8601 DateTime 格式 "yyyy-mm-dd-Yyyy-mm-ddthh： MM： fffffffZ"

此外，`field` 函数可用于策略规则。 `field` 主要与**AuditIfNotExists**和**DeployIfNotExists**一起用于引用正在评估的资源的字段。 可在[DeployIfNotExists 示例](effects.md#deployifnotexists-example)中查看此用法的示例。

#### <a name="policy-function-example"></a>策略函数示例

此策略规则示例使用 `resourceGroup` 资源函数获取**name**属性，并将其与 `concat` 数组和对象函数结合使用，以生成强制资源名称以资源组名称开头的 `like` 条件。

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>别名

您可以使用属性别名来访问资源类型的特定属性。 使用别名可以限制资源上属性允许的值或条件。 每个别名映射到给定资源类型不同 API 版本的路径。 在策略评估期间，策略引擎会获取该 API 版本的属性路径。

别名列表始终增长。 若要查找 Azure 策略当前支持的别名，请使用以下方法之一：

- 适用于 Visual Studio Code 的 Azure 策略扩展（推荐）

  使用[适用于 Visual Studio Code 的 Azure 策略扩展](../how-to/extension-for-vscode.md)来查看和发现资源属性的别名。

  ![适用于 Visual Studio Code 的 Azure 策略扩展](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- Azure 资源关系图

  使用 `project` 运算符可显示资源的**别名**。

  ```kusto
  Resources
  | where type=~'microsoft.storage/storageaccounts'
  | limit 1
  | project aliases
  ```
  
  ```azurecli-interactive
  az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```
  
  ```azurepowershell-interactive
  Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>了解 [*] 别名

可用的几个别名具有显示为 "normal" 名称的版本，另一个具有 **\[\*** 附加到它的 \]。 例如:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

"Normal" 别名将该字段表示为单个值。 当整个值集必须完全按定义时，此字段适用于完全匹配比较方案，不再更少。

利用 **\[\*\]** 别名，可以对照数组中每个元素的值和每个元素的特定属性进行比较。 这种方法可以比较 "if of"、"if any of" 和 "if all of" 方案的元素属性。 对于更复杂的方案，请使用 "[计数](#count)条件" 表达式。 使用**ipRules\[\*\]** ，示例将验证每个_操作_是否为_Deny_，但不必担心存在多少个规则或 IP_值_是多少。
此示例规则检查**ipRules\[\*\]** 的所有匹配项，并且仅当找**不到至少**一个匹配项时应用**effectType** ：

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "10.0.4.1"
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```



有关详细信息，请参阅[评估 [\*] 别名](../how-to/author-policies-for-arrays.md#evaluating-the--alias)。

## <a name="initiatives"></a>行动

利用方案，你可以将多个相关的策略定义组合在一起，简化分配和管理，因为你将组作为单个项来处理。 例如，可以将相关标记策略定义分组到单个计划中。 您可以应用计划，而不是单独分配每个策略。

下面的示例演示如何创建处理两个标记的计划： `costCenter` 和 `productName`。 它使用两个内置策略来应用默认标记值。

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>后续步骤

- 查看[Azure 策略示例](../samples/index.md)中的示例。
- 查看[了解策略影响](effects.md)。
- 了解如何以[编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取相容性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不合规的资源](../how-to/remediate-resources.md)。
- 使用[Azure 管理组组织资源](../../management-groups/overview.md)，查看管理组的作用。
