---
title: 计划定义结构的详细信息
description: 描述如何使用策略计划定义对策略定义进行分组，以便部署到组织中的 Azure 资源。
ms.date: 10/07/2020
ms.topic: conceptual
ms.openlocfilehash: 8f9c6146e1dde5b5a7f6595c61638319de60a82d
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876169"
---
# <a name="azure-policy-initiative-definition-structure"></a>Azure Policy 计划定义结构

使用计划可组合多个相关策略定义，以简化分配和管理，因为可将组作为单个项使用。 例如，可以将相关标记策略组合为单个计划。 将应用计划，而非单独分配每个策略。

使用 JSON 创建策略计划定义。 策略计划定义包含以下各项的元素：

- 显示名称
- description
- metadata
- parameters
- 策略定义
- 此属性 (的策略组是合规 [性 (预览版) 功能](./regulatory-compliance.md) 的一部分) 

下面的示例演示如何创建用于处理 `costCenter` 和 `productName` 这两个标记的计划。 它使用两个内置策略来应用默认标记值。

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "metadata": {
            "version": "1.0.0",
            "category": "Tags"
        },
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
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
    }
}
```

Azure Policy 内置项和模式位于 [Azure Policy 示例](../samples/index.md)。

## <a name="metadata"></a>Metadata

可选 `metadata` 属性存储关于策略计划定义的信息。
客户可在 `metadata` 中定义对其组织有用的任何属性和值。 但是，Azure Policy 和内置项使用一些常见属性。

### <a name="common-metadata-properties"></a>常见元数据属性

- `version`（字符串）：跟踪有关策略计划定义的内容版本的详细信息。
- `category`（字符串）：确定在 Azure 门户中的哪个类别下显示策略定义。

  > [!NOTE]
  > 对于 [法规遵从性](./regulatory-compliance.md) 计划， `category` 必须 **符合法规要求**。

- `preview`（布尔值）：如果策略计划定义为“预览版”，则为 true 或 false 标志。
- `deprecated`（布尔值）：如果策略计划定义被标记为“已弃用”，则为 true 或 false 标志。

> [!NOTE]
> Azure Policy 服务会使用 `version`、`preview` 和 `deprecated` 属性，将变更级别传达给内置策略定义或计划和状态。 `version` 的格式为：`{Major}.{Minor}.{Patch}`。 特定状态（例如“弃用”或“预览版”）会附加至 `version` 属性，或另一个属性中附加为“布尔值”。 有关 Azure Policy 内置项版本控制方式的详细信息，请参阅[内置项版本控制](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md)。

## <a name="parameters"></a>parameters

参数可减少策略定义的数量，有助于简化策略管理。 使用类似窗体中字段的参数 - `name`、`address`、`city`、`state`。 这些参数始终不变，但其值会基于窗体中的各填写内容变化。
构建策略计划时，参数同样适用。 通过在策略计划定义中包含参数，可以在包含的策略中重复使用该参数。

> [!NOTE]
> 分配计划后，不能更改计划级别参数。 因此，建议在定义参数时设置 defaultValue。

### <a name="parameter-properties"></a>参数属性

参数有下述可以在策略计划定义中使用的属性：

- `name`：参数的名称。 由策略规则中的 `parameters` 部署函数使用。 有关详细信息，请参阅[使用参数值](#passing-a-parameter-value-to-a-policy-definition)。
- `type`：确定参数是“字符串”、“数组”、“对象”、“布尔值”、“整数”、“浮点数”，还是“日期/时间”。
- `metadata`：定义主要由 Azure 门户用来显示用户友好信息的子属性：
  - `description`：说明参数的用途。 可以用来提供可接受值的示例。
  - `displayName`：在门户中显示的用于参数的友好名称。
  - `strongType`：（可选）通过门户分配策略定义时使用。 提供上下文感知列表。 有关详细信息，请参阅 [strongType](#strongtype)。
- `defaultValue`：（可选）设置分配的参数的值（如果值未给定）。
- `allowedValues`：（可选）提供参数在分配过程中所接受值的数组。

例如，可以定义策略计划定义，以限制各种包含的策略定义中的资源位置。 allowedLocations 可以是该策略计划定义的一个参数。 然后，该参数可用于每个包含的策略定义，并在分配策略计划期间定义。

```json
"parameters": {
    "init_allowedLocations": {
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

### <a name="passing-a-parameter-value-to-a-policy-definition"></a>将参数值传递到策略定义

声明要将哪些计划参数传递到计划定义的 [policyDefinitions](#policy-definitions) 数组中的哪些包含的策略定义。 尽管参数名称可以是相同的，但在计划中使用与策略定义中不同的名称可以简化代码以提升可读性。

例如，可以将之前定义的 init_allowedLocations 计划参数传递到多个包含的策略定义及其参数 sql_locations 和 vm_locations，如下所示  ：

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

此示例引用 init_allowedLocations 参数，该参数已在[参数属性](#parameter-properties)中演示过。

### <a name="strongtype"></a>strongType

在 `metadata` 属性中，可以使用 **strongType** 提供 Azure 门户中的选项多选列表。 strongType 可以是受支持的资源类型，也可以是允许值。 若要确定资源类型是否对 strongType有效，请使用 [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider)。

支持部分不是由 Get-AzResourceProvider 返回的资源类型。 这些资源类型为：

- `Microsoft.RecoveryServices/vaults/backupPolicies`

strongType 的非资源类型允许值有：

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="policy-definitions"></a>策略定义

计划定义的 `policyDefinitions` 部分是一个数组，其中现有策略定义包含在该计划中。 如[将参数值传递到策略定义](#passing-a-parameter-value-to-a-policy-definition)中所述，此属性是将[计划参数](#parameters)传递到策略定义的位置。

### <a name="policy-definition-properties"></a>策略定义属性

每个表示策略定义的数组元素具有以下属性：

- `policyDefinitionId`（字符串）：要包含的自定义或内置策略定义的 ID。
- `policyDefinitionReferenceId`（字符串）：包含的策略定义的短名称。
- `parameters`：（可选）用于将计划参数作为该策略定义中的属性传递到包含的策略定义的名称/值对。 有关详细信息，请参阅[参数](#parameters)。
- `groupNames` (字符串数组) ： (可选) 策略定义是其成员的组。 有关详细信息，请参阅 [策略组](#policy-definition-groups)。

下面是 `policyDefinitions` 的一个示例，，它有两个包含的策略定义，会向这两个策略定义分别传递相同的计划参数：

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

## <a name="policy-definition-groups"></a>策略定义组

可对计划定义中的策略定义进行分组和分类。 Azure 策略的 [法规遵从性](./regulatory-compliance.md) (预览版) 功能使用此属性将定义分组到 **控件** 和 **符合性域**中。 此信息在 `policyDefinitionGroups` _array_ 属性中定义。 可以在 Microsoft 创建的 **policyMetadata** 对象中找到其他分组详细信息。 有关信息，请参阅 [元数据对象](#metadata-objects)。

### <a name="policy-definition-groups-parameters"></a>策略定义组参数

中的每个 _数组_ 元素 `policyDefinitionGroups` 必须具有以下两个属性：

- `name` (字符串) \[ 必需 \] ： **组**的短名称。 在法规遵从性中， **控件**。 此属性的值由 `groupNames` 在中使用 `policyDefinitions` 。
- `category` (字符串) ：该组所属的层次结构。 在法规遵从性中，是控件的 **符合性域** 。
- `displayName` (字符串) ： **组** 或 **控件**的友好名称。 供门户使用。
- `description` (字符串) ：有关 **组** 或 **控件** 所涵盖内容的说明。
- `additionalMetadataId` (字符串) ：包含有关**控件**和**符合性域**的其他详细信息的[policyMetadata](#metadata-objects)对象的位置。

  > [!NOTE]
  > 客户可能会指向现有的 [policyMetadata](#metadata-objects) 对象。 但是，这些对象是 _只读_ 的，并且仅由 Microsoft 创建。

`policyDefinitionGroups`NIST 内置计划定义的属性示例如下所示：

```json
"policyDefinitionGroups": [
    {
        "name": "NIST_SP_800-53_R4_AC-1",
        "additionalMetadataId": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1"
    }
]
```

### <a name="metadata-objects"></a>元数据对象

Microsoft 创建的法规遵从性内置包含有关每个控件的其他信息。
此信息为：

- 在 Azure 门户上显示的有关合规性计划 **控制** 的概述。
- 通过 REST API 提供。 请参阅 `Microsoft.PolicyInsights` 资源提供程序和 [policyMetadata 操作组](/rest/api/policy-insights/policymetadata/getresource)。
- 通过 Azure CLI 提供。 请参阅 [az policy metadata](/cli/azure/policy/metadata) 命令。

> [!IMPORTANT]
> 合规性的元数据对象是 _只读_ 的，不能由客户创建。

策略分组的元数据在节点中包含以下信息 `properties` ：

- `metadataId`：与分组相关的 **控件 ID** 。
- `category` (必需) ：**控件**所属的**符合性域**。
- `title` (必需) ： **控件 ID**的友好名称。
- `owner` (所需) ：标识在 Azure 中负责控制的人员： _客户_、 _Microsoft_、 _共享_。
- `description`：有关控件的其他信息。
- `requirements`：有关控件实现的责任的详细信息。
- `additionalContentUrl`：指向有关控件的详细信息的链接。 此属性通常是指向文档的部分的链接，该文档涵盖符合性标准中的此控件。

下面是 **policyMetadata** 对象的示例。 此示例元数据属于 _NIST SP 800-53 R4_ 。

```json
{
  "properties": {
    "metadataId": "NIST SP 800-53 R4 AC-1",
    "category": "Access Control",
    "title": "Access Control Policy and Procedures",
    "owner": "Shared",
    "description": "**The organization:**    \na. Develops, documents, and disseminates to [Assignment: organization-defined personnel or roles]:  \n1. An access control policy that addresses purpose, scope, roles, responsibilities, management commitment, coordination among organizational entities, and compliance; and  \n2. Procedures to facilitate the implementation of the access control policy and associated access controls; and  \n  
\nb. Reviews and updates the current:  \n1. Access control policy [Assignment: organization-defined frequency]; and  \n2. Access control procedures [Assignment: organization-defined frequency].",
    "requirements": "**a.**  The customer is responsible for developing, documenting, and disseminating access control policies and procedures. The customer access control policies and procedures address access to all customer-deployed resources and customer system access (e.g., access to customer-deployed virtual machines, access to customer-built applications).  \n**b.**  The customer is responsible for reviewing and updating access control policies and procedures in accordance with FedRAMP requirements.",
    "additionalContentUrl": "https://nvd.nist.gov/800-53/Rev4/control/AC-1"
  },
  "id": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1",
  "name": "NIST_SP_800-53_R4_AC-1",
  "type": "Microsoft.PolicyInsights/policyMetadata"
}
```

## <a name="next-steps"></a>后续步骤

- 请参阅[定义结构](./definition-structure.md)
- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看[了解策略效果](effects.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取符合性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
