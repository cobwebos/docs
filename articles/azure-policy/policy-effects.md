---
title: 了解 Azure 策略效果
description: Azure 策略定义具有各种效果，可确定管理和报告符合性的方式。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 23bbbe9cf86268f93ae1f8fcec9303efa8a673de
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796710"
---
# <a name="understanding-policy-effects"></a>了解策略效果

Azure 策略中的每个策略定义都具有单一效果，在策略规则的 if 段评估为匹配正在扫描的资源时，该效果决定扫描期间会发生的情况。 如果这些效果适用于新资源、更新的资源或现有资源，则它们的行为也会有所不同。

目前在策略定义中支持五种效果：

- 附加
- 审核
- AuditIfNotExists
- 拒绝
- DeployIfNotExists

## <a name="order-of-evaluation"></a>评估顺序

当通过 Azure 资源管理器请求创建或更新资源时，策略会在将请求交给适当的资源提供程序之前处理几种效果。
这样做可以防止资源提供程序在资源不符合策略的设计治理控制时进行不必要的处理。 策略将按照策略或初始分配创建已分配的所有策略定义列表，该列表按照范围（不包括排除项）应用到资源，并准备根据每个定义评估资源。

- 首先评估“附加”。 由于“附加”可能会改变请求，因此由“附加”所做的更改可能会阻止审核或拒绝触发的效果。
- 然后评估“拒绝”。 通过在“审核”之前评估“拒绝”，可以防止两次记录不需要的资源。
- 然后在请求传输到资源提供程序之前评估“审核”。

一旦将请求提供给资源提供程序并且资源提供程序返回成功状态代码，就会评估 AuditIfNotExists 和 DeployIfNotExists 以确定是否需要后续符合性记录或操作。

## <a name="append"></a>附加

附加用于在创建或更新期间向请求的资源添加其他字段。 在诸如 costCenter 之类的资源上添加标记或为存储资源指定允许的 IP 可能会很有用。

### <a name="append-evaluation"></a>“附加”评估

如前所述，在创建或更新资源期间，“附加”会在资源提供程序处理请求之前进行评估。 当满足策略规则的 if 条件时，“附加”会向资源添加字段。 如果“附加”效果使用其他值替代原始请求中的值，则它会充当拒绝效果并拒绝该请求。

当使用附加效果的策略定义作为评估周期的一部分运行时，它不会更改已存在的资源。 相反，它会将符合 if 条件的任意资源标记为不符合。

### <a name="append-properties"></a>附加属性

附加效果只有“详细信息”数组，它是必需的。 因为“详细信息”是一个数组，它可能需要单个字段/值对或倍数。 请参阅[策略定义](policy-definition.md#fields)，获取可接受的字段列表。

### <a name="append-examples"></a>附加示例

示例 1：单个字段/值对附加标记。

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

示例 2：多个字段/值对附加一组标记。

```json
"then": {
    "effect": "append",
    "details": [{
            "field": "tags.myTag",
            "value": "myTagValue"
        },
        {
            "field": "tags.myOtherTag",
            "value": "myOtherTagValue"
        }
    ]
}
```

示例 3：使用[别名](policy-definition.md#aliases)具有数组值的单个字段/值对，可在存储帐户上设置 IP 规则。

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

## <a name="deny"></a>拒绝

“拒绝”用于通过策略定义防止与期望标准不匹配的资源请求，并使请求失败。

### <a name="deny-evaluation"></a>拒绝评估

创建或更新资源时，“拒绝”会在发送给资源提供程序之前阻止请求。 该请求返回为 403（禁止访问）。 在门户中，可以将“禁止访问”视为由于策略分配而阻止的部署状态。

在评估周期中，具有与资源匹配的拒绝效果的策略定义被标记为不符合，但不对该资源执行任何操作。

### <a name="deny-properties"></a>拒绝属性

“拒绝”效果没有任何可用于策略定义的 then 条件的其他属性。

### <a name="deny-example"></a>“拒绝”示例

示例：使用“拒绝”效果。

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>审核

“审核”效果用于评估不合规资源时在审核日志中创建警告事件，但不会停止请求。

### <a name="audit-evaluation"></a>审核评估

在资源发送到资源提供程序之前，“审核”效果是创建或更新资源期间最后运行的效果。 “审核”效果同样适用于资源请求和评估周期，并对活动日志执行 `Microsoft.Authorization/policies/audit/action` 操作。 在这两种情况下，资源将标记为不符合。

### <a name="audit-properties"></a>审核属性。

“审核”效果没有任何可用于策略定义的 then 条件的其他属性。

### <a name="audit-example"></a>审核示例

示例：使用“审核”效果。

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists 对匹配 if 条件的资源启用审核，但没有在 then 条件的“details”中指定的组件。

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists 评估

AuditIfNotExists 在资源提供程序处理针对资源的创建或更新请求并返回成功状态代码后运行。 如果没有相关资源或如果由 ExistenceCondition 定义的资源未评估为 true，则会触发此效果。 触发该效果后，会以与“审核”效果相同的方式对活动日志执行 `Microsoft.Authorization/policies/audit/action` 操作。 触发后，满足 if 条件的资源是标记为不符合的资源。

### <a name="auditifnotexists-properties"></a>AuditIfNotExists 属性

AuditIfNotExists 效果的“details”属性具有定义要匹配的相关资源的所有子属性。

- **Type** [必选]
  - 指定要匹配的相关资源的类型。
  - 首先尝试提取 if 条件资源下的资源，然后在与 if 条件资源相同的资源组中进行查询。
- **Name**（可选）
  - 指定要匹配的资源的确切名称，并使策略提取一个特定资源，而不是指定类型的所有资源。
- **ResourceGroupName**（可选）
  - 允许相关资源的匹配来自不同的资源组。
  - 如果 type 是 if 条件资源下的一个资源，则不适用。
  - 默认值是 if 条件资源的资源组。
- **ExistenceScope**（可选）
  - 允许的值为 Subscription 和 ResourceGroup。
  - 设置从中获取相关资源以在其中进行匹配的范围。
  - 如果 type 是 if 条件资源下的一个资源，则不适用。
  - ResourceGroup 将限制在 if 条件资源的资源组或 ResourceGroupName 中指定的资源组。
  - 对于 Subscription，则查询全部订阅以获取相关资源。
  - 默认值是 ResourceGroup。
- **ExistenceCondition**（可选）
  - 如果未指定，任何 type 的相关资源均满足此效果，并且不会触发审核。
  - 使用与 if 条件的策略规则相同的语言，但会分别针对每个相关资源进行评估。
  - 如果任何匹配的相关资源评估结果为 true，该效果就会得到满足并且不会触发审核。
  - 可以使用 [field()] 检查 if 条件中的值的等效性。
  - 例如，可用于验证父资源（位于 if 条件中）与匹配的相关资源位于相同的资源位置。

### <a name="auditifnotexists-example"></a>AuditIfNotExists 示例

示例：评估虚拟机以确定是否存在反恶意软件扩展，然后在缺失时进行审核。

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

与 AuditIfNotExists 类似，DeployIfNotExists 在条件满足时执行模板部署。

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists 评估

DeployIfNotExists 也在资源提供程序处理针对资源的创建或更新请求并返回成功状态代码后运行。 如果没有相关资源或如果由 ExistenceCondition 定义的资源未评估为 true，则会触发此效果。 触发效果后，执行模板部署。

在评估周期中，具有与资源匹配的 DeployIfNotExists 效果的策略定义被标记为不符合，但不对该资源执行任何操作。

### <a name="deployifnotexists-properties"></a>DeployIfNotExists 属性

DeployIfNotExists 效果的“details”属性具有可定义要匹配的相关资源和要执行的模板部署的所有子属性。

- **Type** [必选]
  - 指定要匹配的相关资源的类型。
  - 首先尝试提取 if 条件资源下的资源，然后在与 if 条件资源相同的资源组中进行查询。
- **Name**（可选）
  - 指定要匹配的资源的确切名称，并使策略提取一个特定资源，而不是指定类型的所有资源。
- **ResourceGroupName**（可选）
  - 允许相关资源的匹配来自不同的资源组。
  - 如果 type 是 if 条件资源下的一个资源，则不适用。
  - 默认值是 if 条件资源的资源组。
  - 如果执行模板部署，则将其部署在此值的资源组中。
- **ExistenceScope**（可选）
  - 允许的值为 Subscription 和 ResourceGroup。
  - 设置从中获取相关资源以在其中进行匹配的范围。
  - 如果 type 是 if 条件资源下的一个资源，则不适用。
  - ResourceGroup 将限制在 if 条件资源的资源组或 ResourceGroupName 中指定的资源组。
  - 对于 Subscription，则查询全部订阅以获取相关资源。
  - 默认值是 ResourceGroup。
- **ExistenceCondition**（可选）
  - 如果未指定，任何 type 的相关资源均满足此效果，并且不会触发此部署。
  - 使用与 if 条件的策略规则相同的语言，但会分别针对每个相关资源进行评估。
  - 如果任何匹配的相关资源评估结果为 true，该效果就会得到满足并且不会触发此部署。
  - 可以使用 [field()] 检查 if 条件中的值的等效性。
  - 例如，可用于验证父资源（位于 if 条件中）与匹配的相关资源位于相同的资源位置。
- **Deployment** [必选]
  - 该属性应包含完整的模板部署，因为它将传递给 `Microsoft.Resources/deployments` PUT API。 有关详细信息，请参阅[部署 REST API](/rest/api/resources/deployments)。

  > [!NOTE]
  > Deployment 属性中的所有函数都将作为模板（而不是策略）的组件进行评估。 此异常是将值从策略传递到模板的 parameters 属性。 本节中模板参数名称下的 value 用于执行此值传递操作（请参阅 DeployIfNotExists 示例中的 _fullDbName_）。

### <a name="deployifnotexists-example"></a>DeployIfNotExists 示例

示例：评估 SQL Server 数据库以确定是否启用 transparentDataEncryption。 如果未启用，则执行启用它的部署。

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="layering-policies"></a>分层策略

资源可能会受到多个分配的影响。 这些分配可能处于相同范围（特定资源、资源组、订阅或管理组）或处于不同范围内。 这些分配中的每一个也可能具有不同的定义效果。 无论如何，每项策略的条件和效果（直接分配或作为初始分配的一部分）均为独立评估。 例如，如果策略 1 和策略 2 均已分配，其中策略 1 具有使用拒绝效果限制在“westus”中创建订阅 A 的位置的条件，策略 2 可使用审核效果限制在“eastus”中创建的资源组 B（位于订阅 A 中）中的资源，则所得到结果将是：

- “eastus”中资源组 B 中的任何资源都符合策略 2，但被标记为不符合策略 1。
- 任何在资源组 B 中不在“eastus”中的资源将被标记为不符合策略 2，并且如果不在“westus”中，还将被标记为不符合策略 1。
- 订阅 A 中任何不在“westus”中的新资源将会被策略 1 拒绝。
- “westus”中订阅 A/资源组 B 中的任何新资源将在策略 2 中标记为不符合，但会被创建（符合策略 1，策略 2 为审核而不是拒绝）。

如果策略 1 和策略 2 都具有拒绝效果，情况将变为：

- 在资源组 B 中而不在“eastus”中的任何资源将被标记为不符合策略 2。
- 在资源组 B 中而不在“westus”中的任何资源将被标记为不符合策略 1。
- 订阅 A 中任何不在“westus”中的新资源将会被策略 1 拒绝。
- 订阅 A/资源组 B 中的任何新资源都将被拒绝（因为其位置无法同时满足策略 1 和策略 2）。

由于每项任务都是单独评估的，不存在因范围差异致使资源溜过间隙的机会。 因此，我们认为分层策略或策略重叠的最终结果是**累积最多限制**。 换句话说，如果策略 1 和策略 2 都具有拒绝效果，则由于重叠和冲突策略（如上例），你希望创建的资源可能会被阻止。 如果你仍然需要在目标范围内创建资源，请查看每项分配的排除情况，以确保正确的策略影响正确的范围。

## <a name="next-steps"></a>后续步骤

现在你对策略定义效果有了更深入的了解，请查看策略示例：

- 在 [Azure 策略示例](json-samples.md)中查看更多示例。
