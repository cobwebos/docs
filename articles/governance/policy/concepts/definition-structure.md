---
title: 策略定义结构的详细信息
description: 介绍如何使用策略定义为组织中的 Azure 资源建立约定。
ms.date: 08/27/2020
ms.topic: conceptual
ms.openlocfilehash: 076493fa8fd54e9585d09a3dd352eabdee652f18
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079024"
---
# <a name="azure-policy-definition-structure"></a>Azure Policy 定义结构

Azure Policy 可为资源建立多种约定。 策略定义描述资源符合性[条件](#conditions)，以及在符合某个条件时要采取的效果。 条件会将资源属性[字段](#fields)与所需值进行比较。 资源属性字段是通过[别名](#aliases)进行访问的。 资源属性字段可为单值字段，也可为包含多值的[数组](#understanding-the--alias)。 数组上的条件评估会有所不同。
如需了解更多，请参见[条件](#conditions)。

通过定义约定，可以控制成本并更轻松地管理资源。 例如，可指定仅允许特定类型的虚拟机。 也可要求资源使用特定的标记。 策略分配由子资源继承。 如果将策略分配应用到资源组，则会将其应用到该资源组中的所有资源。

有关策略定义架构的描述，请参阅此处：[https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json)

使用 JSON 创建策略定义。 策略定义包含以下项的元素：

- 显示名称
- description
- mode
- metadata
- parameters
- 策略规则
  - 逻辑评估
  - 效果

例如，以下 JSON 说明限制资源部署位置的策略：

```json
{
    "properties": {
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "mode": "Indexed",
        "metadata": {
            "version": "1.0.0",
            "category": "Locations"
        },
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

Azure Policy 内置和模式位于 [Azure Policy 示例](../samples/index.md)。

## <a name="display-name-and-description"></a>显示名称和说明

请使用“displayName”和“description”来标识策略定义，并提供其使用上下文 。 **displayName** 的最大长度为 128 个字符，**description** 的最大长度为 512 个字符。

> [!NOTE]
> 在创建或更新策略定义期间，“ID”、“类型”和“名称”是由 JSON 外部属性定义，不需要在 JSON 文件中进行定义。 通过 SDK 获取策略定义将返回“ID”、“类型”和“名称”属性作为 JSON 的一部分，但每个属性都是与策略定义相关的只读信息。

## <a name="type"></a>类型

尽管无法设置 type 属性，但 SDK 返回了三个值且在门户中可见：

- `Builtin`：这些策略定义由 Microsoft 提供并维护。
- `Custom`：客户创建的所有策略定义都具有此值。
- `Static`：表示具有 Microsoft**所有权**的[法规遵从性](./regulatory-compliance.md)策略定义。 这些策略定义的合规性结果是 Microsoft 基础结构上的第三方审核结果。 在 Azure 门户中，此值有时显示为“Microsoft 托管”。 有关详细信息，请参阅[云中责任分担](../../../security/fundamentals/shared-responsibility.md)。

## <a name="mode"></a>模式

“模式”的配置要取决于策略是针对 Azure 资源管理器属性还是资源提供程序属性。

### <a name="resource-manager-modes"></a>资源管理器模式

“模式”决定要为策略定义评估的资源类型。 支持的模式包括：

- `all`：评估资源组、订阅和所有资源类型
- `indexed`：仅评估支持标记和位置的资源类型

例如，资源 `Microsoft.Network/routeTables` 支持标记和位置，并在这两种模式中进行评估。 但是，无法标记资源 `Microsoft.Network/routeTables/routes`，也不会在 `Indexed` 模式下对其进行评估。

大多数情况下，建议将“mode”设置为 `all`。 通过门户创建的所有策略定义使用 `all` 模式。 如果使用 PowerShell 或 Azure CLI，则可以手动指定 **mode** 参数。 如果策略定义不包含 **mode** 值，为提供后向兼容性，在 Azure PowerShell 中默认为 `all`，在 Azure CLI 中默认为 `null`。 `null` 模式等同于使用 `indexed` 来支持后向兼容性。

在创建强制执行标记或位置的策略时，应该使用 `indexed`。 虽然并不是必需的，但是它会阻止不支持标记和位置的资源，使其不会在符合性结果中显示为不兼容。 有一个例外情况，就是资源组和订阅。 策略定义若在资源组或订阅上强制执行位置或标记，则应将“模式”设为 `all`，并明确以 `Microsoft.Resources/subscriptions/resourceGroups` 或 `Microsoft.Resources/subscriptions` 类型为目标。 有关示例，请参阅[模式：标记 - 示例 #1](../samples/pattern-tags.md)。 有关支持标记的资源列表，请参阅[有关 Azure 资源的标记支持](../../../azure-resource-manager/management/tag-support.md)。

### <a name="resource-provider-modes-preview"></a><a name="resource-provider-modes"></a>资源提供程序模式（预览版）

在预览版期间，目前支持以下资源提供程序模式：

- `Microsoft.ContainerService.Data`，用于管理 [Azure Kubernetes 服务](../../../aks/intro-kubernetes.md)上的许可控制器规则。 使用此资源提供程序模式的定义 **必须** 使用 [EnforceRegoPolicy](./effects.md#enforceregopolicy) 效果。 此模式将被弃用。
- `Microsoft.Kubernetes.Data`，用于在 Azure 上或外部管理 Kubernetes 群集。 使用该资源提供程序模式的定义使用效果“审核”、“拒绝”和“已禁用”  。 不_推荐_使用[EnforceOPAConstraint](./effects.md#enforceopaconstraint)效果。
- `Microsoft.KeyVault.Data`，用于管理 [Azure Key Vault](../../../key-vault/general/overview.md) 中的保管库和证书。

> [!NOTE]
> 资源提供程序模式仅支持内置策略定义，且在预览版期间暂不支持计划。

## <a name="metadata"></a>Metadata

`metadata` 可选属性用于存储关于策略定义的信息。 客户可在 `metadata` 中定义对其组织有用的任何属性和值。 但是，Azure Policy 和内置项使用一些常见属性。

### <a name="common-metadata-properties"></a>常见元数据属性

- `version`（字符串）：跟踪有关策略定义的内容版本的详细信息。
- `category`（字符串）：确定在 Azure 门户中的哪个类别下显示策略定义。
- `preview`（布尔值）：如果策略定义为“预览”，则为 True 或 false 标志。
- `deprecated`（布尔值）：如果策略定义被标记为“已弃用”，则为 True 或 false 标志。

> [!NOTE]
> Azure Policy 服务会使用 `version`、`preview` 和 `deprecated` 属性，将变更级别传达给内置策略定义或计划和状态。 `version` 的格式为：`{Major}.{Minor}.{Patch}`。 特定状态（例如“弃用”或“预览版”）会附加至 `version` 属性，或另一个属性中附加为“布尔值”。 有关 Azure Policy 内置项版本控制方式的详细信息，请参阅[内置项版本控制](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md)。

## <a name="parameters"></a>parameters

参数可减少策略定义的数量，有助于简化策略管理。 使用类似窗体中字段的参数 - `name`、`address`、`city`、`state`。 这些参数始终不变，但其值会基于窗体中的各填写内容变化。
构建策略时，参数同样适用。 如果在策略定义中包括参数，就可以通过使用不同的值重新使用策略以执行不同方案。

> [!NOTE]
> 参数可以添加到现有和已分配的定义。 新参数必须包含 defaultValue 属性。 这可以防止策略或计划的现有分配间接被设为无效。

### <a name="parameter-properties"></a>参数属性

参数有下述可以在策略定义中使用的属性：

- `name`：参数的名称。 由策略规则中的 `parameters` 部署函数使用。 有关详细信息，请参阅[使用参数值](#using-a-parameter-value)。
- `type`：确定参数是“字符串”、“数组”、“对象”、“布尔值”、“整数”、“浮点数”，还是“日期/时间”。
- `metadata`：定义主要由 Azure 门户用来显示用户友好信息的子属性：
  - `description`：说明参数的用途。 可以用来提供可接受值的示例。
  - `displayName`：在门户中显示的用于参数的友好名称。
  - `strongType`：（可选）通过门户分配策略定义时使用。 提供上下文感知列表。 有关详细信息，请参阅 [strongType](#strongtype)。
  - `assignPermissions`：（可选）设置为“true”，使 Azure 门户在策略分配过程中创建角色分配。 如果希望在分配范围之外分配权限，此属性会很有用。 策略中每个角色定义（或计划中所有策略中的每个角色定义）都有一个角色分配。 参数值必须是有效的资源或范围。
- `defaultValue`：（可选）设置分配的参数的值（如果值未给定）。
  在更新已分配的现有策略定义时必须使用此项。
- `allowedValues`：（可选）提供参数在分配过程中所接受值的数组。

例如，可以定义策略定义来限制资源的部署位置。 **allowedLocations** 可以是该策略定义的一个参数。 每次分配策略定义来限制接受的值时，会使用此参数。 使用 **strongType** 可以在通过门户完成分配时提供增强的体验：

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

在策略规则中，你可以使用下列 `parameters` 函数语法引用参数：

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

此示例引用 **allowedLocations** 参数，该参数已在[参数属性](#parameter-properties)中演示过。

### <a name="strongtype"></a>strongType

在 `metadata` 属性中，可以使用 **strongType** 提供 Azure 门户中的选项多选列表。 strongType 可以是受支持的资源类型，也可以是允许值。 若要确定资源类型是否对 strongType有效，请使用 [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider)。 资源类型“StrongType”的格式为 `<Resource Provider>/<Resource Type>`。 例如 `Microsoft.Network/virtualNetworks/subnets`。

支持部分不是由 **Get-AzResourceProvider** 返回的资源类型。 这些类型包括：

- `Microsoft.RecoveryServices/vaults/backupPolicies`

strongType 的非资源类型允许值包括：

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>定义位置

创建计划或策略时，需要指定定义位置。 定义位置必须是管理组或订阅。 此位置决定了计划或策略的分配范围。 资源必须是用于分配的目标定义位置的层次结构中的直系成员或子代。

如果定义位置是：

- **订阅** - 只能将该订阅中的资源分配给策略。
- **管理组** - 只能将子管理组和子订阅中的资源分配给策略。 如果计划将策略定义应用于多个订阅，则位置必须是包含订阅的管理组。

## <a name="policy-rule"></a>策略规则

策略规则包括 **If** 和 **Then** 块。 在 **If** 块中，定义强制执行策略时指定的一个或多个条件。 可以对这些条件应用逻辑运算符，以精确定义策略的方案。

在 **Then** 块中，定义满足 **If** 条件时产生的效果。

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

**not** 语法反转条件的结果。 **allOf** 语法（与逻辑 **And** 操作相似）要求所有条件为 true。 **anyOf** 语法（与逻辑 **Or** 操作相似）要求一个或多个条件为 true。

可以嵌套逻辑运算符。 以下示例显示了嵌套在 allOf 操作中的 not 操作。

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

### <a name="conditions"></a>Conditions

条件用于评估 **field** 或 **value** 访问器是否符合特定标准。 支持的条件有：

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
- `"less": "dateValue"` | `"less": "stringValue"` | `"less": intValue`
- `"lessOrEquals": "dateValue"` | `"lessOrEquals": "stringValue"` | `"lessOrEquals": intValue`
- `"greater": "dateValue"` | `"greater": "stringValue"` | `"greater": intValue`
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` |
  `"greaterOrEquals": intValue`
- `"exists": "bool"`

对于“less”、“lessOrEquals”、“greater”和“greaterOrEquals”，如果属性类型与条件类型不匹配，则会引发错误。 字符串比较是使用 `InvariantCultureIgnoreCase` 进行。

使用 like 和 notLike 条件时，请在值中指定通配符 `*`。
值不应包含多个通配符 `*`。

当使用 match 和 notMatch 条件时，请提供 `#` 来匹配数字、`?` 来匹配字母、`.` 来匹配所有字符，以及提供任何其他字符来匹配该实际字符。 尽管 match 和 notMatch 区分大小写，但用于评估 stringValue 的所有其他条件都不区分大小写 。 “matchInsensitively”和“notMatchInsensitively”中提供了不区分大小写的替代方案 。

在 \[\*\] 别名数组字段值中，数组中的每个元素都会使用元素间的逻辑 and 进行单独计算。 有关详细信息，请参阅[评估 \[\*\] 别名](../how-to/author-policies-for-arrays.md#evaluating-the--alias)。

### <a name="fields"></a>字段

使用字段构成条件。 字段匹配资源请求有效负载中的属性，并说明资源的状态。

支持以下字段：

- `name`
- `fullName`
  - 返回资源全名。 资源全名是最前面为任意父资源名称的资源名称（例如“myServer/myDatabase”）。
- `kind`
- `type`
- `location`
  - 对于不限位置的资源，请使用 **global**。
- `identity.type`
  - 返回在资源上启用的[托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)类型。
- `tags`
- `tags['<tagName>']`
  - 此括号语法支持具有标点符号的标记名称，例如连字符、句点或空格。
  - 其中的 \<tagName\> 是要验证其条件的标记的名称。
  - 示例：`tags['Acct.CostCenter']`，其中 Acct.CostCenter 是标记的名称。
- `tags['''<tagName>''']`
  - 此括号语法通过双撇号进行转义，可支持在其中包含撇号的标记名称。
  - 其中的“\<tagName\>”是要验证其条件的标记的名称。
  - 示例：`tags['''My.Apostrophe.Tag''']`，其中 'My.Apostrophe.Tag' 是标记的名称。
- 属性别名 - 有关列表，请参阅[别名](#aliases)。

> [!NOTE]
> `tags.<tagName>``tags[tagName]` 和 `tags[tag.with.dots]` 仍然是可接受的用于声明标记字段的方式。 但是，首选表达式是上面列出的那些。

#### <a name="use-tags-with-parameters"></a>使用带参数的标记

参数值可以传递给标记字段。 将参数传递给标记字段可在策略分配期间提高策略定义的灵活性。

在以下示例中，`concat` 用于为名为 tagName 参数值的标记创建标记字段查找。 如果该标记不存在，系统会使用“修改”效果，通过 `resourcegroup()` 查找函数使用在已审计资源父资源组上具有相同名称的标签值来添加该标记。

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

### <a name="value"></a>值

也可使用 **value** 来形成条件。 **value** 会针对[参数](#parameters)、[支持的模板函数](#policy-functions)或文本来检查条件。 **value** 可与任何支持的[条件](#conditions)配对。

> [!WARNING]
> 如果模板函数的结果是一个错误，则策略评估会失败。 评估失败是一种隐式**拒绝**。 有关详细信息，请参阅[避免模板失败](#avoiding-template-failures)。 使用 DoNotEnforce 的 [enforcementMode](./assignment-structure.md#enforcement-mode)，以防止在测试和验证新策略定义期间，由于新的或更新的资源评估失败而受到影响。

#### <a name="value-examples"></a>Value 示例

此策略规则示例使用 **value** 将 `resourceGroup()` 函数和返回的 **name** 属性的结果与 **like** 条件 `*netrg` 进行对比。 此规则会拒绝名称以 `*netrg` 结尾的任何资源组中类型不为 `Microsoft.Network/*` 的任何资源。

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

此策略规则示例使用 value 来检查多个嵌套函数的结果是否等于 `true`。 此规则拒绝并没有至少三个标记的资源。

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": "true"
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>避免模板失败

在 value 中使用模板函数允许使用许多复杂嵌套函数。 如果模板函数的结果是一个错误，则策略评估会失败。 评估失败是一种隐式**拒绝**。 在某些情况下失败的 value 示例：

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

上面的示例策略规则使用 [substring()](../../../azure-resource-manager/templates/template-functions-string.md#substring) 将名称的前三个字符与 abc 进行比较。 如果名称少于三个字符，`substring()` 函数会导致错误。 此错误会导致策略变为“拒绝”效果。

相反地，请改用 [if()](../../../azure-resource-manager/templates/template-functions-logical.md#if) 函数来检查名称的前三个字符是否等于 abc，不允许名称少于三个字符，这会导致错误：

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

使用修订后的策略规则，`if()` 会先检查名称的长度，然后尝试在少于三个字符的值上获取 `substring()`。 如果名称太短，则会改为返回“不是以 abc 开头”的值，并将其与 abc 进行比较。 短名称不是以 abc 开头的资源仍会导致策略规则失败，但在评估过程中不会再造成错误。

### <a name="count"></a>Count

计算资源有效负载中陈列有多少成员符合条件表达式的条件，可以使用 Count 表达式来构成。 常见的方案是检查“其中至少一个”、“只有一个”、“全部”或“没有”数组成员符合条件。 Count 会计算条件表达式每个 [\[\*\] 别名](#understanding-the--alias)数组成员，并加总 true 结果，然后将结果与表达式运算符进行比较。 “Count”表达式最多可添加到单个 policyRule 定义 3 次 。

Count 表达式的结构如下：

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

以下属性与 count 搭配使用：

- count.field（必需）：包含数组路径，且必须为数组别名。 如果缺少数组，则表达式的计算结果为 false，而不考虑条件表达式。
- count.where（可选）：此条件表达式会单独计算 count.field 的每个 [\[\*\] 别名](#understanding-the--alias)数据成员。 如果未提供此属性，具有“字段”路径的所有数组成员将评估为 true。 任何[条件](../concepts/definition-structure.md#conditions)都可在此属性内使用。
  可在此属性中使用[逻辑运算符](#logical-operators)来创建复杂的评估要求。
- **\<condition\>** （必需）：该值将与满足 **count.where** 条件表达式的项数进行比较。 应使用数字[条件](../concepts/definition-structure.md#conditions)。

#### <a name="count-examples"></a>计数示例

示例 1：检查数组是否为空

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

示例 2：检查是否只有一个数组成员符合条件表达式

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

示例 3：检查是否至少有一个数组成员符合条件表达式

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

示例 4：检查是否所有对象数组成员都符合条件表达式

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

示例 5：检查是否至少有一个数组成员与条件表达式中的多个属性匹配

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

### <a name="effect"></a>效果

Azure Policy 支持以下类型的效果：

- Append：会将定义的字段集添加到请求
- Audit：会在活动日志中生成一个警告事件，但不会使请求失败
- AuditIfNotExists：如果相关资源不存在，则会在活动日志中生成一个警告事件
- Deny：会在活动日志中生成一个事件，并使请求失败
- DeployIfNotExists：如果相关资源不存在，则部署该资源
- Disabled：不评估资源是否符合策略规则
- EnforceOPAConstraint（预览版）：针对 Azure 上的自托管 Kubernetes 群集，配置 Open Policy Agent 许可控制器与 Gatekeeper v3（预览版）
- EnforceRegoPolicy（预览版）：在 Azure Kubernetes 服务中，配置 Open Policy Agent 许可控制器与 Gatekeeper v2
- Modify：添加、更新或删除资源中已定义的标记

有关每种效果、评估顺序、属性和示例的完整详细信息，请参阅[了解 Azure Policy 效果](effects.md)。

### <a name="policy-functions"></a>策略函数

除了以下函数和用户定义的函数外，所有[资源管理器模板函数](../../../azure-resource-manager/templates/template-functions.md)均可在策略规则中使用：

- copyIndex()
- deployment()
- list*
- newGuid()
- pickZones()
- providers()
- reference()
- resourceId()
- variables()

> [!NOTE]
> 在 deployIfNotExists 策略定义中，仍可在其模板部署的 `details.deployment.properties.template` 部分中使用这些函数。

以下函数可在策略规则中使用，但与在 Azure 资源管理器模板（ARM 模板）中使用不同：

- `utcNow()` - 与 ARM 模板不同，该属性可以在 defaultValue 之外使用。
  - 以通用 ISO 8601 日期/时间格式“yyyy-MM-ddTHH:mm:ss.fffffffZ”返回一个设置为当前日期和时间的字符串

以下函数仅适用于策略规则：

- `addDays(dateTime, numberOfDaysToAdd)`
  - dateTime：[必需] 字符串 - 通用 ISO 8601 日期/时间格式“yyyy-MM-ddTHH:mm:ss.fffffffZ”的字符串
  - numberOfDaysToAdd：[必需] 整数 - 要增加的天数
- `field(fieldName)`
  - fieldName：[必需] 字符串 - 要检索的[字段](#fields)名称
  - 从 If 条件计算的资源中返回该字段的值
  - `field` 主要用于 **AuditIfNotExists** 和 **DeployIfNotExists**，以引用所评估资源上的字段。 可以在 [DeployIfNotExists 示例](effects.md#deployifnotexists-example)中看到这种用法的示例。
- `requestContext().apiVersion`
  - 返回已触发策略评估的请求的 API 版本（示例：`2019-09-01`）。
    该值是 PUT/PATCH 请求中用于对资源创建/更新进行评估的 API 版本。 在对现有资源进行符合性评估时，将会一律使用最新的 API 版本。
  
#### <a name="policy-function-example"></a>策略函数示例

此策略规则示例使用 `resourceGroup` 资源函数获取 **name** 属性，并将该属性与 `concat` 数组和对象函数结合使用以构建 `like` 条件，该条件强制资源名称以资源组名称开头。

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

可以使用属性别名来访问资源类型的特定属性。 通过别名，可限制允许用于资源属性的值和条件。 每个别名会映射到给定资源类型不同 API 版本的路径。 在策略评估期间，策略引擎会获取该 API 版本的属性路径。

别名列表始终不断增长。 若要找出 Azure Policy 当前支持哪些别名，请使用以下方法之一：

- 适用于 Visual Studio Code 的 Azure Policy 扩展（推荐）

  使用[适用于 Visual Studio Code 的 Azure Policy 扩展](../how-to/extension-for-vscode.md)来查看和发现资源属性的别名。

  :::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="适用于 Visual Studio Code 的 Azure Policy 扩展" border="false":::

- Azure Resource Graph

  使用 `project` 运算符来显示资源的别名。

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

  > [!NOTE]
  > 若要查找可用于 [修改](./effects.md#modify) 效果的别名，请在 Azure PowerShell **4.6.0** 或更高版本中使用以下命令：
  >
  > ```azurepowershell-interactive
  > Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }
  > ```

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
  GET https://management.azure.com/providers/?api-version=2019-10-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>了解 [*] 别名

很多可用的别名都会有一个显示为“正常”名称的版本，以及一个附加有 \[\*\] 的版本。 例如：

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

“正常”别名会将该字段表示为单个值。 当整个值集必须与定义完全一致，不能多也不能少时，此字段适用于精确匹配的比较场景。

\[\*\] 别名可以比较数组中每个元素的值，以及每个元素的特定属性。 此方法可以比较“如果没有”、“如果任何”和“如果所有”方案的元素属性。 对于更复杂的方案，请使用 [count](#count) 条件表达式。 使用 ipRules\[\*\]，一个示例会验证每个操作是否为“拒绝”，但不用担心存在多少个规则或 IP 值是什么。
此示例规则会检查 ipRules\[\*\].value 是否符合 10.0.4.1，并在没有找到至少一个匹配项时才应用 effectType：

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

## <a name="next-steps"></a>后续步骤

- 请参阅[计划定义结构](./initiative-definition-structure.md)
- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看[了解策略效果](effects.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取符合性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
