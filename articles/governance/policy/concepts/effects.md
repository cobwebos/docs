---
title: 了解效果的工作原理
description: Azure Policy 定义具有各种效果，用来确定如何对符合性进行管理和报告。
ms.date: 10/05/2020
ms.topic: conceptual
ms.openlocfilehash: 19811eca33be7dff4d9bee5b8bd89dd38f185a57
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873942"
---
# <a name="understand-azure-policy-effects"></a>了解 Azure Policy 效果

Azure Policy 中的每个策略定义都有单一效果。 该效果确定了在评估匹配的策略规则时发生的情况。 如果这些效果适用于新资源、更新的资源或现有资源，则它们的行为会有所不同。

目前策略定义中支持以下效果：

- [附加](#append)
- [审核](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [拒绝](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [已禁用](#disabled)
- [修改](#modify)

_弃用_了以下效果：

- [EnforceOPAConstraint](#enforceopaconstraint)
- [EnforceRegoPolicy](#enforceregopolicy)

> [!IMPORTANT]
> 使用 "_审核_" 和 "_拒绝_" 作为 "资源提供程序" 模式，而不是**EnforceOPAConstraint**或**EnforceRegoPolicy**影响 `Microsoft.Kubernetes.Data` 。 已更新内置策略定义。 当修改这些内置策略定义的现有策略分配时，必须将 _effect_ 参数更改为 "已更新 _allowedValues_ " 列表中的值。

## <a name="order-of-evaluation"></a>评估顺序

Azure Policy 首先评估创建或更新资源的请求。 Azure Policy 会创建将应用于资源的所有分配列表，然后根据每个定义评估资源。 对于[资源管理器模式](./definition-structure.md#resource-manager-modes)，Azure Policy 在将请求转交给相应的资源提供程序之前处理多个效果。 此顺序可以防止资源提供程序在资源不符合 Azure Policy 的设计治理控制时进行不必要的处理。 使用[资源提供程序模式](./definition-structure.md#resource-provider-modes)，资源提供程序管理评估和结果，并将结果报告回 Azure Policy。

- 首先检查**已禁用**效果以确定是否应评估策略规则。
- 然后评估“附加”和“修改”。  由于这两个效果可能会改变请求，因此所做的更改可能会阻止“审核”或“拒绝”效果的触发。 这些效果仅在资源管理器模式下可用。
- 然后评估“拒绝”。 通过在“审核”之前评估“拒绝”，可以防止两次记录不需要的资源。
- 最后评估审核。

资源提供程序针对资源管理器模式请求返回成功代码后，AuditIfNotExists 和 DeployIfNotExists 将进行评估以确定是否需要其他符合性日志记录或操作 。

## <a name="append"></a>附加

附加用于在创建或更新期间向请求的资源添加其他字段。 常见的示例是为存储资源指定允许的 Ip。

> [!IMPORTANT]
> 附加用于非标记的属性。 尽管附加可以在创建或更新请求期间将标记添加到资源，但建议使用[修改](#modify)效果取代标记。

### <a name="append-evaluation"></a>“附加”评估

在创建或更新资源期间，会在资源提供程序处理请求之前进行“附加”评估。 当满足策略规则的 **if** 条件时，“附加”会向资源添加字段。 如果“附加”效果使用其他值替代原始请求中的值，则它会充当拒绝效果并拒绝该请求。 若要将新值附加到现有数组，请使用 \[\*\] 版本的别名。

当使用附加效果的策略定义作为评估周期的一部分运行时，它不会更改已存在的资源。 相反，它会将符合 if 条件的任意资源标记为不符合。

### <a name="append-properties"></a>“附加”属性

附加效果只有“详细信息”数组，它是必需的。 因为“详细信息”是一个数组，它可能需要单个或多个字段/值对。 请参阅[定义结构](definition-structure.md#fields)，获取可接受的字段列表。

### <a name="append-examples"></a>“附加”示例

示例 1：单个“字段/值”对使用具有数组值的非 \[\*\] [别名](definition-structure.md#aliases)在存储帐户上设置 IP 规则。   如果非 **\[\*\] 别名是数组，则附加效果会将值附加为整个数组** 。 如果数组已存在，该冲突会导致拒绝事件发生。

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

示例 2：单个“字段/值”对使用具有数组值的 \[\*\] [别名](definition-structure.md#aliases)在存储帐户上设置 IP 规则。   通过使用 \[\*\] 别名，附加效果会将值附加到可能预先存在的数组。  如果数组尚不存在，将会创建它。

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="audit"></a>审核

“审核”用于评估不合规资源时在活动日志中创建警告事件，但不会停止请求。

### <a name="audit-evaluation"></a>“审核”评估

“审核”是 Azure Policy 在创建或更新资源期间检查的最后一个效果。 对于资源管理器模式，Azure Policy 会将资源发送到资源提供程序。 “审核”对于资源请求和评估周期的工作方式相同。 对于新资源和更新的资源，Azure 策略将 `Microsoft.Authorization/policies/audit/action` 操作添加到活动日志，并将资源标记为不合规。

### <a name="audit-properties"></a>“审核”属性

对于资源管理器模式，Audit 效果没有任何其他属性可用于策略定义的 then 条件。

对于 `Microsoft.Kubernetes.Data` 的资源提供程序模式，Audit 效果具有以下 details 的附加子属性。

- constraintTemplate（必选）
  - 约束模板 CustomResourceDefinition (CRD) 定义新约束。 该模板定义 Rego 逻辑、约束架构和通过 Azure Policy 的值传递的约束参数。
- constraint（必选）
  - 约束模板的 CRD 实现。 使用通过值传递的参数，如 `{{ .Values.<valuename> }}`。 在下面的示例 2 中，这些值为 `{{ .Values.excludedNamespaces }}` 和 `{{ .Values.allowedContainerImagesRegex }}`。
- values（可选）
  - 定义要传递给约束的任何参数和值。 每个值都必须在约束模板 CRD 中存在。

### <a name="audit-example"></a>“审核”示例

示例 1：对资源管理器模式使用 Audit 效果。

```json
"then": {
    "effect": "audit"
}
```

示例 2：对 `Microsoft.Kubernetes.Data` 的资源提供程序模式使用 Audit 效果。 details 中的附加信息定义了要在 Kubernetes 中使用以限制允许的容器映像的约束模板和 CRD。

```json
"then": {
    "effect": "audit",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists 对与匹配 if 条件的资源相关的资源启用审核，但没有在 then 条件的 details 中指定的属性  。

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists 评估

AuditIfNotExists 在资源提供程序处理资源创建或更新请求并返回成功状态代码后运行。 如果没有相关资源或如果由 **ExistenceCondition** 定义的资源未评估为 true，则会发生审核。 对于新资源和更新的资源，Azure 策略将 `Microsoft.Authorization/policies/audit/action` 操作添加到活动日志，并将资源标记为不合规。 触发后，满足 if 条件的资源是标记为不符合的资源。

### <a name="auditifnotexists-properties"></a>AuditIfNotExists 属性

AuditIfNotExists 效果的“details”属性具有定义要匹配的相关资源的所有子属性。

- Type（必选）
  - 指定要匹配的相关资源的类型。
  - 如果 details.type 是 if 条件资源下的一个资源类型，则策略会在已评估资源范围内查询此“类型”的资源。   否则，策略会在与已评估资源同一资源组范围内查询。
- **Name**（可选）
  - 指定要匹配的资源的确切名称，并使策略提取一个特定资源，而不是指定类型的所有资源。
  - 当 if.field.type 和 then.details.type 的条件值匹配时，Name 将变为必需且必须为 `[field('name')]` 或子资源的 `[field('fullName')]`  。
    但是，应考虑改用[审核](#audit)效果。
- **ResourceGroupName**（可选）
  - 允许相关资源的匹配来自不同的资源组。
  - 如果 **type** 是 **if** 条件资源下的一个资源，则不适用。
  - 默认值是 if 条件资源的资源组。
- **ExistenceScope**（可选）
  - 允许的值为 Subscription 和 ResourceGroup。
  - 设置从中获取相关资源以在其中进行匹配的范围。
  - 如果 **type** 是 **if** 条件资源下的一个资源，则不适用。
  - ResourceGroup 将限制在 if 条件资源的资源组或 ResourceGroupName 中指定的资源组。
  - 对于 Subscription，则查询全部订阅以获取相关资源。
  - 默认值是 ResourceGroup。
- **ExistenceCondition**（可选）
  - 如果未指定，任何 **type** 的相关资源均满足此效果，并且不会触发审核。
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

## <a name="deny"></a>拒绝

“拒绝”用于通过策略定义防止与定义的标准不匹配的资源请求，并使请求失败。

### <a name="deny-evaluation"></a>“拒绝”评估

在资源管理器模式下创建或更新匹配的资源时，Deny 会在发送给资源提供程序之前阻止请求。 该请求返回为 `403 (Forbidden)`。 在门户中，可以将 Forbidden（禁止）视为策略分配阻止的部署状态。 对于资源提供程序模式，资源提供程序管理资源的评估。

在评估现有资源期间，与“拒绝”策略定义匹配的资源将标记为不合规。

### <a name="deny-properties"></a>“拒绝”属性

对于资源管理器模式，Deny 效果没有任何其他属性可用于策略定义的 then 条件。

对于 `Microsoft.Kubernetes.Data` 的资源提供程序模式，Deny 效果具有以下 details 的附加子属性。

- constraintTemplate（必选）
  - 约束模板 CustomResourceDefinition (CRD) 定义新约束。 该模板定义 Rego 逻辑、约束架构和通过 Azure Policy 的值传递的约束参数。
- constraint（必选）
  - 约束模板的 CRD 实现。 使用通过值传递的参数，如 `{{ .Values.<valuename> }}`。 在下面的示例 2 中，这些值为 `{{ .Values.excludedNamespaces }}` 和 `{{ .Values.allowedContainerImagesRegex }}`。
- values（可选）
  - 定义要传递给约束的任何参数和值。 每个值都必须在约束模板 CRD 中存在。

### <a name="deny-example"></a>“拒绝”示例

示例 1：对资源管理器模式使用 Deny 效果。

```json
"then": {
    "effect": "deny"
}
```

示例 2：对 `Microsoft.Kubernetes.Data` 的资源提供程序模式使用 Deny 效果。 details 中的附加信息定义了要在 Kubernetes 中使用以限制允许的容器映像的约束模板和 CRD。

```json
"then": {
    "effect": "deny",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

与 AuditIfNotExists 类似，DeployIfNotExists 策略定义在条件满足时将执行模板部署。

> [!NOTE]
> **deployIfNotExists** 支持[嵌套模板](../../../azure-resource-manager/templates/linked-templates.md#nested-template)，但目前不支持[链接模版](../../../azure-resource-manager/templates/linked-templates.md#linked-template)。

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists 评估

DeployIfNotExists 将在资源提供程序处理创建或更新资源请求并返回成功状态代码后运行约 15 分钟。 如果没有相关资源或如果由 **ExistenceCondition** 定义的资源未评估为 true，则会发生模板部署。
部署持续时间取决于模板中包含资源的复杂性。

在评估周期中，具有与资源匹配的 DeployIfNotExists 效果的策略定义被标记为不合规，但不对该资源执行任何操作。 使用[修正任务](../how-to/remediate-resources.md)来修正现有不符合资源。

### <a name="deployifnotexists-properties"></a>DeployIfNotExists 属性

DeployIfNotExists 效果的“details”属性具有定义要匹配的相关资源和要执行的模板部署的所有子属性。

- Type（必选）
  - 指定要匹配的相关资源的类型。
  - 首先尝试提取 if 条件资源下的资源，然后在与 if 条件资源相同的资源组中进行查询。
- **Name**（可选）
  - 指定要匹配的资源的确切名称，并使策略提取一个特定资源，而不是指定类型的所有资源。
  - 当 if.field.type 和 then.details.type 的条件值匹配时，Name 将变为必需且必须为 `[field('name')]` 或子资源的 `[field('fullName')]`  。
- **ResourceGroupName**（可选）
  - 允许相关资源的匹配来自不同的资源组。
  - 如果 **type** 是 **if** 条件资源下的一个资源，则不适用。
  - 默认值是 if 条件资源的资源组。
  - 如果执行模板部署，则将其部署在此值的资源组中。
- **ExistenceScope**（可选）
  - 允许的值为 Subscription 和 ResourceGroup。
  - 设置从中获取相关资源以在其中进行匹配的范围。
  - 如果 **type** 是 **if** 条件资源下的一个资源，则不适用。
  - ResourceGroup 将限制在 if 条件资源的资源组或 ResourceGroupName 中指定的资源组。
  - 对于 Subscription，则查询全部订阅以获取相关资源。
  - 默认值是 ResourceGroup。
- **ExistenceCondition**（可选）
  - 如果未指定，任何 **type** 的相关资源均满足此效果，并且不会触发部署。
  - 使用与 if 条件的策略规则相同的语言，但会分别针对每个相关资源进行评估。
  - 如果任何匹配的相关资源评估结果为 true，该效果就会得到满足并且不会触发部署。
  - 可以使用 [field()] 检查 if 条件中的值的等效性。
  - 例如，可用于验证父资源（位于 if 条件中）与匹配的相关资源位于相同的资源位置。
- roleDefinitionIds（必选）
  - 此属性必须包含与可通过订阅访问的基于角色的访问控制角色 ID 匹配的字符串数组。 有关详细信息，请参阅[修正 - 配置策略定义](../how-to/remediate-resources.md#configure-policy-definition)。
- **DeploymentScope**（可选）
  - 允许的值为 Subscription 和 ResourceGroup。
  - 设置要触发的部署类型。 _Subscription_ 指示[在订阅级别部署](../../../azure-resource-manager/templates/deploy-to-subscription.md)，_ResourceGroup_ 指示部署到资源组。
  - 使用订阅级别部署时，必须在 _Deployment_ 中指定 _location_ 属性。
  - 默认值是 ResourceGroup。
- Deployment（必选）
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
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="disabled"></a>已禁用

对于测试情况以及在策略定义已参数化效果时，此效果很有用。 借助这种灵活性可以禁用单个分配，而无需禁用该策略的所有分配。

Disabled 效果的替代方法是 enforcementMode，可在策略分配上设置。
enforcementMode 已禁用时，仍可评估资源。 日志（例如活动日志）和策略效果不会出现。 有关详细信息，请参阅[策略分配 - 强制模式](./assignment-structure.md#enforcement-mode)。

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

此效果与 `Microsoft.Kubernetes.Data` 的策略定义模式一起使用。 它用于对 Azure 上的 Kubernetes 群集将使用 [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) 定义的 Gatekeeper v3 许可控制规则传递到 [Open Policy Agent](https://www.openpolicyagent.org/) (OPA)。

> [!IMPORTANT]
> 不_推荐_使用**EnforceOPAConstraint**效果和相关**Kubernetes 服务**类别的受限预览策略定义。 请改用 "使用 _审核_ 和 _拒绝_ " 作为资源提供程序模式 `Microsoft.Kubernetes.Data` 。

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint 评估

Open Policy Agent 许可控制器会实时评估群集上的任何新请求。
每隔 15 分钟，完成一次对群集的完全扫描，并将结果报告给 Azure Policy。

### <a name="enforceopaconstraint-properties"></a>EnforceOPAConstraint 属性

EnforceOPAConstraint 效果的 Details 属性具有描述 Gatekeeper v3 许可控制规则的子属性。

- constraintTemplate（必选）
  - 约束模板 CustomResourceDefinition (CRD) 定义新约束。 该模板定义 Rego 逻辑、约束架构和通过 Azure Policy 的值传递的约束参数。
- constraint（必选）
  - 约束模板的 CRD 实现。 使用通过值传递的参数，如 `{{ .Values.<valuename> }}`。 在下面的示例中，这些值为 `{{ .Values.cpuLimit }}` 和 `{{ .Values.memoryLimit }}`。
- values（可选）
  - 定义要传递给约束的任何参数和值。 每个值都必须在约束模板 CRD 中存在。

### <a name="enforceopaconstraint-example"></a>EnforceOPAConstraint 示例

示例：Gatekeeper v3 许可控制规则用于在 Kubernetes 中设置容器 CPU 和内存资源限制。

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

此效果与 `Microsoft.ContainerService.Data` 的策略定义模式一起使用。 它用于在 [Azure Kubernetes](../../../aks/intro-kubernetes.md) 服务上将使用 [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) 定义的 Gatekeeper v2 许可控制规则传递到 [Open Policy Agent](https://www.openpolicyagent.org/) (OPA)。

> [!IMPORTANT]
> 具有“EnforceRegoPolicy”效果和相关“Kubernetes 服务”的有限预览策略定义已被弃用。 请改用 "使用 _审核_ 和 _拒绝_ " 作为资源提供程序模式 `Microsoft.Kubernetes.Data` 。

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy 评估

Open Policy Agent 许可控制器会实时评估群集上的任何新请求。
每隔 15 分钟，完成一次对群集的完全扫描，并将结果报告给 Azure Policy。

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy 属性

EnforceRegoPolicy 效果的 Details 属性具有描述 Gatekeeper v2 许可控制规则的子属性。

- **policyId** (必需) 
  - 作为参数传递给 Rego 许可控制规则的唯一名称。
- **策略** (必需) 
  - 指定 Rego 许可控制规则的 URI。
- **policyParameters** (可选) 
  - 定义要传递给 Rego 策略的任何参数和值。

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy 示例

示例：Gatekeeper v2 许可控制规则仅允许在 AKS 中指定的容器映像。

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="modify"></a>修改

Modify 用于在创建或更新期间在资源中添加、更新或删除属性或标记。
常见的示例是在 costCenter 等资源上更新标记。 使用[修正任务](../how-to/remediate-resources.md)来修正现有不符合资源。 单个修改规则可以有任意数量的操作。

Modify 支持以下操作：

- 添加、替换或删除资源标记。 对于标记，除非目标资源是资源组，否则 Modify 策略的 `mode` 应设置为 Indexed。
- 添加或替换虚拟机和虚拟机规模集的托管标识类型 (`identity.type`) 的值。
- 添加或替换某些别名的值（预览）。
  - 使用 `Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }`
    在 Azure PowerShell 4.6.0 或更高版本中，获取可与 Modify 一起使用的别名列表。

> [!IMPORTANT]
> 如果你正在管理标记，我们建议使用 Modify 而不要使用 Append，因为 Modify 提供更多的操作类型，且能够修正现有的资源。 但如果无法创建托管标识或 Modify 尚不支持资源属性的别名，则建议使用 Append。

### <a name="modify-evaluation"></a>修改评估

在创建或更新资源期间，修改会在资源提供程序处理请求之前进行评估。 如果策略规则的 if 条件得到满足，则 Modify 操作将应用于请求内容。 每个 Modify 操作可以指定一个条件来决定何时应用它。 条件评估为 false 的操作将被跳过。

别名指定后，执行以下附加检查，确保 Modify 操作更改请求内容后不会导致资源提供程序拒绝该请求：

- 在请求 API 版本中，别名映射到的属性标记为“可修改”。
- Modify 操作中的令牌类型与请求 API 版本中的属性的预期令牌类型匹配。

如果这些检查中的任何一个失败，策略评估将回退到指定的 conflictEffect。

> [!IMPORTANT]
> 建议包含别名的 Modify 定义采用 Audit 冲突效果，避免使用 API 版本（其中映射的属性不是“可修改”）的请求失败。 如果同一别名在不同 API 版本中的行为不同，可以使用 Conditional Modify 操作来确定对每个 API 版本使用的 Modify 操作。

当使用修改效果的策略定义作为评估周期的一部分运行时，它不会更改已存在的资源。 相反，它会将符合 if 条件的任意资源标记为不符合。

### <a name="modify-properties"></a>修改属性

修改效果的“Details”属性包含定义修正所需权限以及用于添加、更新或删除标记值操作的所有子属性。 

- roleDefinitionIds（必选）
  - 此属性必须包含与可通过订阅访问的基于角色的访问控制角色 ID 匹配的字符串数组。 有关详细信息，请参阅[修正 - 配置策略定义](../how-to/remediate-resources.md#configure-policy-definition)。
  - 定义的角色必须包括所有授予[参与者](../../../role-based-access-control/built-in-roles.md#contributor)角色的操作。
- conflictEffect（可选）
  - 确定在多个策略定义修改同一属性的情况下，或在 Modify 操作不适用于指定别名的情况下，哪个策略定义“胜出”。
    - 对于新的或更新的资源，具有 Deny 的策略定义优先。 具有 Audit 的策略定义会跳过所有操作。 如果多个策略定义具有 Deny，则该请求作为冲突被拒绝。 如果所有策略定义都具有 Audit，则不处理冲突策略定义的任何操作。
    - 对于现有资源，如果多个策略定义具有 Deny，则符合性状态为“冲突” 。 如果一个或更少的策略定义具有 Deny，则每个分配都返回“不符合”的符合性状态 。
  - 可用值：audit、deny、disabled  。
  - 默认值为 deny。
- operations（必选）
  - 要在匹配资源上完成的所有标记操作的数组。
  - 属性：
    - operation（必选）
      - 定义要在匹配资源上执行的操作。 选项为：_addOrReplace_ _添加_ _删除_。 _添加_行为与 [附加](#append)效果类似。
    - field（必选）
      - 要添加、替换或删除的标记。 对于其他[字段](./definition-structure.md#fields)，标记名称必须遵循相同的命名约定。
    - **值** (可选)
      - 要设置标记的值。
      - 如果**操作**是 _addOrReplace_ 或_添加_，则需要此属性。
    - condition（可选）
      - 一个字符串，其中包含使用 [Policy 函数](./definition-structure.md#policy-functions)的 Azure Policy 语言表达式，该表达式计算结果为 true 或 false 。
      - 不支持以下 Policy 函数：`field()`、`resourceGroup()`、`subscription()`。

### <a name="modify-operations"></a>修改操作

**操作**属性数组能够以不同的方式从单个策略定义中更改多个标记。 每个操作都由**操作** **字段**和**值**属性组成。 操作确定修正任务对标记执行的操作，字段确定更改的标记，值定义该标记的新设置。 下面的示例进行了以下标记更改：

- 将 `environment` 标记设置为“Test”，即使它已存在且具有不同的值。
- 删除标记 `TempResource`。
- 将 `Dept` 标记设置为在策略分配上配置的策略参数  _DeptName_。

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

此**操作**属性具有以下选项：

|Operation |说明 |
|-|-|
|addOrReplace |将定义的属性或标记和值添加到资源，即使该属性或标记已存在并使用不同的值。 |
|添加 |将定义的属性或标记和值添加到资源。 |
|删除 |从资源中删除定义的属性或标记。 |

### <a name="modify-examples"></a>修改示例

示例 1：添加 `environment` 标记并将现有 `environment` 标记替换为“Test”：

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

示例 2：删除 `env` 标记并添加 `environment` 标记，或将现有 `environment` 标记替换为参数化的值：

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "conflictEffect": "deny",
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

示例 3：确保存储帐户不允许 blob 公共访问，仅在评估所用 API 版本大于或等于“2019-04-01”的请求时，才应用 Modify 操作：

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/microsoft.authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab"
        ],
        "conflictEffect": "audit",
        "operations": [
            {
                "condition": "[greaterOrEquals(requestContext().apiVersion, '2019-04-01')]",
                "operation": "addOrReplace",
                "field": "Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
                "value": false
            }
        ]
    }
}
```

## <a name="layering-policy-definitions"></a>策略定义分层

资源可能会受到多个分配的影响。 这些分配可能处于相同或不同的范围。 这些分配中的每一个也可能具有不同的定义效果。 将单独评估每个策略的条件和效果。 例如：

- 策略 1
  - 将资源位置限制为“westus”
  - 分配到订阅 A
  - “拒绝”效果
- 策略 2
  - 将资源位置限制为“eastus”
  - 分配到订阅 A 中的资源组 B
  - “审核”效果
  
此设置将产生以下结果：

- 位于“eastus”的资源组 B 中的任何现有资源都符合策略 2，但不符合策略 1
- 不位于“eastus”的资源组 B 中的任何现有资源都不符合策略 2，并且如果它们不在“westus”中，则也不符合策略 1
- 订阅 A 中任何不在“westus”中的任何新资源将被策略 1 拒绝
- 位于“westus”的订阅 A 和资源组 B 中的任何新资源将会创建，但不符合策略 2

如果策略 1 和策略 2 都具有“拒绝”效果，则情况变为：

- 不位于“eastus”的资源组 B 中的任何现有资源不符合策略 2
- 不位于“westus”的资源组 B 中的任何现有资源不符合策略 1
- 订阅 A 中任何不在“westus”中的任何新资源将被策略 1 拒绝
- 订阅 A 的资源组 B 中的任何新资源将被拒绝

单独评估每个分配。 因此，不存在因范围差异致使资源溜过间隙的可能性。 我们认为分层策略的最终结果是**累积最多限制**。 例如，如果策略 1 和策略 2 都具有“拒绝”效果，则重叠和冲突策略定义会阻止资源。 如果仍然需要在目标范围内创建资源，请查看每项分配的排除项，以验证策略分配是否正在影响相应的范围。

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看 [Azure Policy 定义结构](definition-structure.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取符合性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。