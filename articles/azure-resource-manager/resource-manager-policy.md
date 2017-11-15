---
title: "Azure 资源策略 | Microsoft Docs"
description: "介绍如何使用 Azure 资源管理器策略，以确保部署期间资源属性设置的一致性。 可在订阅或资源组中应用策略。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: tomfitz
ms.openlocfilehash: 6faa8b4d259eddb1b26345d8900e829f6ca4dba8
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2017
---
# <a name="resource-policy-overview"></a>资源策略概述
通过资源策略，可在组织中建立资源约定。 通过定义约定，可以控制成本并更轻松地管理资源。 例如，可指定仅允许特定类型的虚拟机。 或者，可要求所有资源都拥有特定标记。 策略由所有子资源继承。 因此，如果将策略应用到资源组，则会将其应用到该资源组中的所有资源。

了解策略的两个概念：

* 策略定义 - 描述何时强制执行策略，以及要采取的操作
* 策略分配 - 应用策略定义的范围（订阅或资源组）

本主题重点介绍策略定义。 有关策略分配的信息，请参阅[使用 Azure 门户分配和管理资源策略](resource-manager-policy-portal.md)或[通过脚本分配和管理策略](resource-manager-policy-create-assign.md)。

在创建和更新资源（PUT 和 PATCH 操作）时评估策略。

## <a name="how-is-it-different-from-rbac"></a>策略与 RBAC 有什么不同？
策略和基于角色的访问控制 (RBAC) 之间存在一些主要区别。 RBAC 关注不同范围内的**用户**操作。 例如，你将添加到所需范围的资源组的参与者角色后，可对该资源组做出更改。 策略关注部署期间的“资源”属性。 例如，可通过策略控制能够预配的资源类型。 或者，可限制能够预配资源的位置。 不同于 RBAC，策略是默认的允许和明确拒绝系统。 

若要使用策略，必须通过 RBAC 完成身份验证。 具体而言，帐户需要：

* `Microsoft.Authorization/policydefinitions/write` 定义策略的权限
* `Microsoft.Authorization/policyassignments/write` 分配策略的权限 

**参与者**角色中未包括这些权限。

## <a name="built-in-policies"></a>内置策略

Azure 提供了一些可降低必须要定义的策略数目的内置策略定义。 继续进行策略定义之前，应考虑内置策略是否已提供所需的定义。 内置策略定义：

* 允许的位置
* 允许的资源类型
* 允许的存储帐户 SKU
* 允许的虚拟机 SKU
* 应用标记和默认值
* 强制实施标记和值
* 不允许的资源类型
* 需要 SQL Server 版本 12.0
* 需要存储帐户加密

## <a name="policy-definition-structure"></a>策略定义结构
使用 JSON 创建策略定义。 策略定义包含以下项的元素：

* mode
* parameters
* 显示名称
* description
* 策略规则
  * 逻辑评估
  * 效果

以下示例说明限制资源部署位置的策略：

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

## <a name="mode"></a>Mode

建议将 `mode` 设置为 `all`。 将其设置为 all 时，系统会评估该策略的资源组和所有资源类型。 门户对所有策略使用 all。 如果使用 PowerShell 或 Azure CLI，则需要指定 `mode` 参数并将其设置为 all。
 
以前，仅对支持标记和位置的资源类型评估策略。 `indexed` 模式会继续此行为。 如果使用 all 模式，则还对不支持标记和位置的资源类型评估策略。 [虚拟网络子网](https://github.com/Azure/azure-policy-samples/tree/master/samples/Network/enforce-nsg-on-subnet)是新添加类型的示例。 此外，模式设置为 all 时会评估资源组。 例如，可以[对资源组强制实施标记](https://github.com/Azure/azure-policy-samples/tree/master/samples/ResourceGroup/enforce-resourceGroup-tags)。 

## <a name="parameters"></a>parameters
使用参数可减少策略定义的数量，有助于简化策略管理。 为资源属性定义策略（如限制资源部署的位置），并在定义中包含参数。 然后，通过在分配策略时传递不同的值（例如为订阅指定一组位置），针对不同的方案重复使用该策略定义。

在创建策略定义时声明参数。

```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations"
    }
  }
}
```

参数类型可以是字符串，也可以是数组。 Azure 门户等工具使用元数据属性显示用户友好信息。 

在策略规则中，使用下列语法引用参数： 

```json
{ 
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>显示名称和说明

使用**显示名称**和**说明**来标识策略定义，并提供其使用情景。

## <a name="policy-rule"></a>策略规则

策略规则包括 **If** 和 **Then** 块。 在 **If** 块中，定义强制执行策略时指定的一个或多个条件。 可以对这些条件应用逻辑运算符，以精确定义策略的方案。 在 **Then** 块中，定义满足 **If** 条件时产生的效果。

```json
{
  "if": {
    <condition> | <logical operator>
  },
  "then": {
    "effect": "deny | audit | append"
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
条件评估**字段**是否符合特定的准则。 支持的条件有：

* `"equals": "value"`
* `"like": "value"`
* `"match": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

在使用 **like** 条件时，可以在值中提供通配符 (*)。

当使用 **match** 条件时，请提供 `#` 来表示数字，提供 `?` 来表示字母，提供任何其他字符来表示该实际字符。 有关示例，请参阅[应用针对名称和文本的资源策略](resource-manager-policy-naming-convention.md)。

### <a name="fields"></a>字段
使用字段构成条件。 字段显示用于描述资源状态的资源请求负载属性。  

支持以下字段：

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*` 
* 属性别名 - 有关列表，请参阅[别名](#aliases)。

### <a name="effect"></a>效果
策略支持三种类型的效果 - `deny`、`audit`、`append`、`AuditIfNotExists` 和 `DeployIfNotExists`。 

* **Deny** 会在审核日志中生成一个事件，并使请求失败
* **Audit** 会在审核日志中生成一个警告事件，但不会使请求失败
* **Append** 会将定义的字段集添加到请求 
* **AuditIfNotExists** - 如果资源不存在则启用审核
* **DeployIfNotExists** - 如果资源不存在则部署一个资源。 目前，只支持通过内置的策略实现这种效果。

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

借助 AuditIfNotExists 和 DeployIfNotExists，可以评估子资源是否存在，并在该资源不存在时应用规则。 例如，可以要求为所有虚拟网络部署网络观察程序。

有关未部署虚拟机扩展时的审核示例，请参阅[审核 VM 扩展](https://github.com/Azure/azure-policy-samples/blob/master/samples/Compute/audit-vm-extension/azurepolicy.json)。

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

## <a name="policy-sets"></a>策略集

策略集使你可以对几个相关的策略定义进行分组。 策略集可简化分配和管理，因为你会以单个项目的形式处理组。 例如，可以对单个策略集中的所有相关标记策略进行分组。 将应用策略集，而非单独分配每个策略。
 
下面的示例演示如何创建用于处理 costCenter 和 productName 这两个标记的策略集。 该示例使用两个内置策略用于应用默认的标记值，以及强制实施标记值。 为实现可重用性，策略集声明了两个参数 costCenterValue 和 productNameValue。 它使用不同参数多次引用两个内置的策略定义。 对于每个参数，可以提供一个固定值（如 tagName 的值），也可以提供策略集的参数（如 tagValue 的值）。

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

通过 New-AzureRMPolicySetDefinition PowerShell 命令添加策略集。

对于 REST 操作，使用 2017-06-01-preview API 版本，如下面的示例所示：

```
PUT /subscriptions/<subId>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicySet?api-version=2017-06-01-preview
```

## <a name="next-steps"></a>后续步骤
* 定义策略规则之后，将其分配到某一范围。 若要通过门户分配策略，请参阅[使用 Azure 门户分配和管理资源策略](resource-manager-policy-portal.md)。 若要通过 REST API、PowerShell 或 Azure CLI 分配策略，请参阅[通过脚本分配和管理策略](resource-manager-policy-create-assign.md)。
* 有关示例策略，请参阅 [Azure 资源策略 GitHub 存储库](https://github.com/Azure/azure-policy-samples)。
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。
* 该策略架构在 [http://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json](http://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json) 中发布。 

