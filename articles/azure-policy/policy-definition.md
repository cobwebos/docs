---
title: Azure 策略定义结构 | Microsoft Docs
description: 介绍 Azure 策略如何使用资源策略定义，通过描述何时强制实施策略和要执行的操作为组织中的资源建立约定。
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/17/2018
ms.topic: article
ms.service: azure-policy
ms.custom: ''
ms.openlocfilehash: 50965010d821d4edf94e2f5727546cb56f61f5db
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="azure-policy-definition-structure"></a>Azure 策略定义结构

Azure 策略使用的资源策略定义，可使你通过描述何时强制实施策略和要执行的操作为组织中的资源建立约定。 通过定义约定，可以控制成本并更轻松地管理资源。 例如，可指定仅允许特定类型的虚拟机。 或者，可要求所有资源都拥有特定标记。 策略由所有子资源继承。 因此，如果将策略应用到资源组，则会将其应用到该资源组中的所有资源。

使用 JSON 创建策略定义。 策略定义包含以下项的元素：

* mode
* parameters
* 显示名称
* description
* 策略规则
  * 逻辑评估
  * 效果

例如，以下 JSON 说明限制资源部署位置的策略：

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
        }
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

所有 Azure 策略模板示例位于 [Azure 策略模板](json-samples.md)。

## <a name="mode"></a>Mode

**模式**确定将对策略评估哪些资源类型。 支持的模式包括：
* `all`：评估资源组和所有资源类型 
* `indexed`：仅评估支持标记和位置的资源类型

大多数情况下，建议将“mode”设置为 `all`。 通过门户创建的所有策略定义使用 `all` 模式。 如果使用 PowerShell 或 Azure CLI，则需要手动指定 mode 参数。

在创建强制执行标记或位置的策略时，应该使用 `indexed`。 这并不是必须的，但是它会阻止不支持标记和位置的资源，使其不会在符合性结果中显示为不兼容。 在这一点上，资源组是一个例外。 尝试在资源组上强制执行位置或标记的策略应将“mode”设为 `all`，并专门针对 `Microsoft.Resources/subscriptions/resourceGroup` 类型。 请在[强制执行资源组标记](scripts/enforce-tag-rg.md)查看相关示例。

## <a name="parameters"></a>parameters

参数可减少策略定义的数量，有助于简化策略管理。 使用类似窗体中字段的参数 - `name`、`address`、`city`、`state`。 这些参数始终不变，但其值会基于窗体中的各填写内容变化。 构建策略时，参数同样适用。 如果在策略定义中包括参数，就可以通过使用不同的值重新使用策略以执行不同方案。

例如，可以定义资源属性策略来限制资源的部署位置。 在这种情况下，创建策略时需声明以下参数：


```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations",
      "strongType": "location"
    }
  }
}
```

参数类型可以是字符串，也可以是数组。 Azure 门户等工具使用元数据属性显示用户友好信息。

在元数据属性中，可以使用 **strongType** 提供 Azure 门户中的选项多选列表。  **strongType** 的允许值目前包括：

* `"location"`
* `"resourceTypes"`
* `"storageSkus"`
* `"vmSKUs"`

在策略规则中，使用下列语法引用参数：

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>显示名称和说明

可使用“显示名称”和“说明”来标识策略定义，并提供其使用情景。

## <a name="policy-rule"></a>策略规则

策略规则包括 **If** 和 **Then** 块。 在 **If** 块中，定义强制执行策略时指定的一个或多个条件。 可以对这些条件应用逻辑运算符，以精确定义策略的方案。

在 **Then** 块中，定义满足 **If** 条件时产生的效果。

```json
{
  "if": {
    <condition> | <logical operator>
  },
  "then": {
    "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists"
  }
}
```

### <a name="logical-operators"></a>逻辑运算符

支持的逻辑运算符为：

* `"not": {condition  or operator}`
* `"allOf": [{condition or operator},{condition or operator}]`
* `"anyOf": [{condition or operator},{condition or operator}]`

**not** 语法反转条件的结果。 **allOf** 语法（与逻辑 **And** 操作相似）要求所有条件为 true。 **anyOf** 语法（与逻辑 **Or** 操作相似）要求一个或多个条件为 true。

可以嵌套逻辑运算符。 以下示例显示了嵌套在 allOf 操作中的 not 操作。

```json
"if": {
  "allOf": [
    {
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

条件评估字段是否符合特定的准则。 支持的条件有：

* `"equals": "value"`
* `"notEquals": "value"`
* `"like": "value"`
* `"notLike": "value"`
* `"match": "value"`
* `"notMatch": "value"`
* `"contains": "value"`
* `"notContains": "value"`
* `"in": ["value1","value2"]`
* `"notIn": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"notContainsKey": "keyName"`
* `"exists": "bool"`

使用 like 和 notLike 条件时，可以在值中提供通配符 (*)。

当使用 match 和 notMatch 条件时，请提供 `#` 来表示数字，提供 `?` 来表示字母，提供任何其他字符来表示该实际字符。 有关示例，请参阅[批准的 VM 映像](scripts/allowed-custom-images.md)。

### <a name="fields"></a>字段
使用字段构成条件。 字段显示用于描述资源状态的资源请求负载属性。  

支持以下字段：

* `name`
* `fullName`
  * 返回资源的全名，包括任何父级信息（例如：myServer/myDatabase）
* `kind`
* `type`
* `location`
* `tags`
* `tags.tagName`
* `tags[tagName]`
  * 括号语法支持包含句点的标记名称
* 属性别名 - 有关列表，请参阅[别名](#aliases)。

### <a name="alternative-accessors"></a>可供选择的取值函数
“Field”是策略规则中使用的主要取值函数。 它会直接检查被评估的资源。 而策略支持另一个取值函数“source”。

```json
"source": "action",
"equals": "Microsoft.Compute/virtualMachines/write"
```

“source”只支持一个值，就是“action”。 “action”返回被评估请求的授权操作。 [活动日志](../monitoring-and-diagnostics/monitoring-activity-log-schema.md)中的授权部分公开了授权操作。

当策略在后台评估现有资源时，它将“action”设为对此资源类型的 `/write` 授权操作。

### <a name="effect"></a>效果
策略支持以下类型的效果：

* **Deny**：会在审核日志中生成一个事件，并使请求失败
* **Audit**：会在审核日志中生成一个警告事件，但不会使请求失败
* **Append**：会将定义的字段集添加到请求
* **AuditIfNotExists**：如果资源不存在，则启用审核
* **DeployIfNotExists**：如果资源不存在，则部署一个资源。 目前，只支持通过内置的策略实现这种效果。

对于 **append**，必须提供以下详细信息：

```json
"effect": "append",
"details": [
  {
    "field": "field name",
    "value": "value of the field"
  }
]
```

值可以是字符串或 JSON 格式对象。

借助 AuditIfNotExists 和 DeployIfNotExists，可以评估相关资源是否存在，并在该资源不存在时应用规则和相应的作用。 例如，可以要求为所有虚拟网络部署网络观察程序。
有关未部署虚拟机扩展时的审核示例，请参阅[如果扩展不存在，则进行审核](scripts/audit-ext-not-exist.md)。


## <a name="aliases"></a>别名

可以使用属性别名来访问资源类型的特定属性。 通过别名，可限制允许用于特定资源属性的值和条件。 每个别名会映射到给定资源类型不同 API 版本的路径。 在策略评估期间，策略引擎会获取该 API 版本的属性路径。

**Microsoft.Cache/Redis**

| 别名 | 说明 |
| ----- | ----------- |
| Microsoft.Cache/Redis/enableNonSslPort | 设置是否启用非 ssl Redis 服务器端口 (6379)。 |
| Microsoft.Cache/Redis/shardCount | 设置要在高级群集缓存上创建的分片数。  |
| Microsoft.Cache/Redis/sku.capacity | 设置要部署的 Redis 缓存的大小。  |
| Microsoft.Cache/Redis/sku.family | 设置要使用的 SKU 系列。 |
| Microsoft.Cache/Redis/sku.name | 设置要部署的 Redis 缓存类型。 |

**Microsoft.Cdn/profiles**

| 别名 | 说明 |
| ----- | ----------- |
| Microsoft.CDN/profiles/sku.name | 设置定价层的名称。 |

**Microsoft.Compute/disks**

| 别名 | 说明 |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | 设置用于创建虚拟机的平台映像或应用商店映像的产品/服务。 |
| Microsoft.Compute/imagePublisher | 设置用于创建虚拟机的平台映像或应用商店映像的发布者。 |
| Microsoft.Compute/imageSku | 设置用于创建虚拟机的平台映像或应用商店映像的 SKU。 |
| Microsoft.Compute/imageVersion | 设置用于创建虚拟机的平台映像或应用商店映像的版本。 |


**Microsoft.Compute/virtualMachines**

| 别名 | 说明 |
| ----- | ----------- |
| Microsoft.Compute/imageId | 设置用于创建虚拟机的映像的标识符。 |
| Microsoft.Compute/imageOffer | 设置用于创建虚拟机的平台映像或应用商店映像的产品/服务。 |
| Microsoft.Compute/imagePublisher | 设置用于创建虚拟机的平台映像或应用商店映像的发布者。 |
| Microsoft.Compute/imageSku | 设置用于创建虚拟机的平台映像或应用商店映像的 SKU。 |
| Microsoft.Compute/imageVersion | 设置用于创建虚拟机的平台映像或应用商店映像的版本。 |
| Microsoft.Compute/licenseType | 设置本地许可的映像或磁盘。 此值仅用于包含 Windows Server 操作系统的映像。  |
| Microsoft.Compute/virtualMachines/imageOffer | 设置用于创建虚拟机的平台映像或应用商店映像的产品/服务。 |
| Microsoft.Compute/virtualMachines/imagePublisher | 设置用于创建虚拟机的平台映像或应用商店映像的发布者。 |
| Microsoft.Compute/virtualMachines/imageSku | 设置用于创建虚拟机的平台映像或应用商店映像的 SKU。 |
| Microsoft.Compute/virtualMachines/imageVersion | 设置用于创建虚拟机的平台映像或应用商店映像的版本。 |
| Microsoft.Compute/virtualMachines/osDisk.Uri | 设置 vhd URI。 |
| Microsoft.Compute/virtualMachines/sku.name | 设置虚拟机的大小。 |
| Microsoft.Compute/virtualMachines/availabilitySet.id | 为虚拟机设置可用性集 ID。 |

**Microsoft.Compute/virtualMachines/extensions**

| 别名 | 说明 |
| ----- | ----------- |
| Microsoft.Compute/virtualMachines/extensions/publisher | 设置扩展的发布者名称。 |
| Microsoft.Compute/virtualMachines/extensions/type | 设置扩展的类型。 |
| Microsoft.Compute/virtualMachines/extensions/typeHandlerVersion | 设置扩展的版本。 |

**Microsoft.Compute/virtualMachineScaleSets**

| 别名 | 说明 |
| ----- | ----------- |
| Microsoft.Compute/imageId | 设置用于创建虚拟机的映像的标识符。 |
| Microsoft.Compute/imageOffer | 设置用于创建虚拟机的平台映像或应用商店映像的产品/服务。 |
| Microsoft.Compute/imagePublisher | 设置用于创建虚拟机的平台映像或应用商店映像的发布者。 |
| Microsoft.Compute/imageSku | 设置用于创建虚拟机的平台映像或应用商店映像的 SKU。 |
| Microsoft.Compute/imageVersion | 设置用于创建虚拟机的平台映像或应用商店映像的版本。 |
| Microsoft.Compute/licenseType | 设置本地许可的映像或磁盘。 此值仅用于包含 Windows Server 操作系统的映像。 |
| Microsoft.Compute/VirtualMachineScaleSets/computerNamePrefix | 设置规模集中所有虚拟机的计算机名前缀。 |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl | 设置用户映像的 blob URI。 |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.vhdContainers | 设置用于存储规模集的操作系统磁盘的容器 URL。 |
| Microsoft.Compute/VirtualMachineScaleSets/sku.name | 设置规模集中虚拟机的大小。 |
| Microsoft.Compute/VirtualMachineScaleSets/sku.tier | 设置规模集中虚拟机的层级。 |

**Microsoft.Network/applicationGateways**

| 别名 | 说明 |
| ----- | ----------- |
| Microsoft.Network/applicationGateways/sku.name | 设置网关的大小。 |

**Microsoft.Network/virtualNetworkGateways**

| 别名 | 说明 |
| ----- | ----------- |
| Microsoft.Network/virtualNetworkGateways/gatewayType | 设置此虚拟网关的类型。 |
| Microsoft.Network/virtualNetworkGateways/sku.name | 配置网关 SKU 名称。 |

**Microsoft.Sql/servers**

| 别名 | 说明 |
| ----- | ----------- |
| Microsoft.Sql/servers/version | 设置服务器的版本。 |

**Microsoft.Sql/databases**

| 别名 | 说明 |
| ----- | ----------- |
| Microsoft.Sql/servers/databases/edition | 设置数据库的版本。 |
| Microsoft.Sql/servers/databases/elasticPoolName | 设置数据库所处弹性池的名称。 |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveId | 设置数据库的已配置服务级别目标 ID。 |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveName | 设置数据库的已配置服务级别目标的名称。  |

**Microsoft.Sql/elasticpools**

| 别名 | 说明 |
| ----- | ----------- |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | 设置数据库弹性池的共享 DTU 总数。 |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/edition | 设置弹性池的版本。 |

**Microsoft.Storage/storageAccounts**

| 别名 | 说明 |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | 设置用于计费的访问层。 |
| Microsoft.Storage/storageAccounts/accountType | 设置 SKU 名称。 |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | 设置在将数据存储于 blob 存储服务中时，服务是否对数据进行加密。 |
| Microsoft.Storage/storageAccounts/enableFileEncryption | 设置在将数据存储于文件存储服务中时，服务是否对数据进行加密。 |
| Microsoft.Storage/storageAccounts/sku.name | 设置 SKU 名称。 |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | 设置为仅允许 https 流入存储服务。 |
| Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id | 检查是否启用了虚拟网络服务终结点。 |

## <a name="initiatives"></a>计划

计划可组合多个相关策略定义，简化分配和管理，因为可将组作为单个项使用。 例如，可以将所有相关标记策略组合为单个计划。 将应用计划，而非单独分配每个策略。

下面的示例演示如何创建用于处理 `costCenter` 和 `productName` 这两个标记的计划。 它使用两个内置策略来应用默认标记值。


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
        "policyDefinitions": [
            {
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

- 可在 [Azure 策略模板](json-samples.md)中查看 Azure 策略模板示例。
