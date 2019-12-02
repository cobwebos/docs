---
title: 教程：管理标记治理
description: 本教程使用 Azure Policy 的修改效果来创建和执行新资源和现有资源上的标记治理模型。
ms.date: 11/25/2019
ms.topic: tutorial
ms.openlocfilehash: e3d6e279b293ea8063c690f9fb69a6f183b2838d
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482261"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>教程：通过 Azure Policy 管理标记治理

[标记](../../../azure-resource-manager/resource-group-using-tags.md)是将 Azure 资源组整理到分类中的关键部分。 遵循[标记管理最佳做法](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)时，标记可以作为使用 Azure Policy 应用业务策略或[使用成本管理跟踪成本](../../../cost-management/cost-mgt-best-practices.md#organize-and-tag-your-resources)的基础。
无论你使用标记的方式和原因是什么，重要的是你可以在 Azure 资源上快速添加、更改和删除这些标记。

Azure Policy 的[修改](../concepts/effects.md#modify)效果旨在帮助管理标记，而无论你处于资源调控的哪个阶段。 “修改”在以下情况下有帮助  ：

- 你不熟悉云，并且没有标记治理经验
- 已经拥有数以千计的资源，但没有标记治理经验
- 已经具有需要更改的现有分类

在本教程中，你将完成以下任务：

> [!div class="checklist"]
> - 确定业务要求
> - 将每个要求映射到策略定义
> - 将标记策略分组为一个计划

## <a name="prerequisites"></a>先决条件

需要一个 Azure 订阅才能完成此教程。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="identify-requirements"></a>确定要求

与任何良好的治理控制实现一样，要求应源自业务需求，并且在创建技术控制之前应该得到充分理解。 对于本方案教程，以下各项是我们的业务要求：

- 所有资源上有两个所需标记：CostCenter 和 Env  
- “CostCenter”必须在所有容器和单个资源上 
  - 资源继承自它们所在的容器，但可以单独重写
- “Env”必须在所有容器和单个资源上 
  - 资源根据容器命名方案确定环境，并且不能重写
  - 容器中的所有资源都属于相同环境

## <a name="configure-the-costcenter-tag"></a>配置 CostCenter 标记

就特定于由 Azure Policy 管理的 Azure 环境而言，CostCenter 标记要求如下  ：

- 拒绝缺少 CostCenter 标记的资源组 
- 修改资源以在缺少 CostCenter 标记时从父资源组添加此标记 

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>拒绝缺少 CostCenter 标记的资源组

由于资源组的“CostCenter”不能由资源组的名称确定，因此必须在创建资源组的请求中定义标记  。 具有[拒绝](../concepts/effects.md#deny)效果的以下策略规则会阻止创建或更新没有 CostCenter 标记的资源组  ：

```json
"if": {
    "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "tags['CostCenter']",
            "exists": false
        }
    ]
},
"then": {
    "effect": "deny"
}
```

> [!NOTE]
> 由于此策略规则以资源组为目标，策略定义上的“模式”必须是“全部”，而不是“已编入索引”  。

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>修改资源以在缺少 CostCenter 标记时继承此标记

第二个“CostCenter”需要的是任何资源在缺少标记时从父资源组继承标记  。 如果已在资源上定义了标记，则即使该标记与父资源组不同，也必须将其单独保留。 以下策略规则使用[修改](../concepts/effects.md#modify)：

```json
"policyRule": {
    "if": {
        "field": "tags['CostCenter']",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "add",
                "field": "tags['CostCenter']",
                "value": "[resourcegroup().tags['CostCenter']]"
            }]
        }
    }
}
```

此策略规则使用“add”操作，而不是“addOrReplace”，因为我们不想要在[修正](../how-to/remediate-resources.md)现有资源时更改标记值   。 它还使用 `[resourcegroup()]` 模板函数从父资源组获取标记值。

> [!NOTE]
> 由于此策略规则以支持标记的资源为目标，因此策略定义上的模式必须为“已编入索引”  。 此配置还确保此策略跳过资源组。

## <a name="configure-the-env-tag"></a>配置 Env 标记

就特定于由 Azure Policy 管理的 Azure 环境而言，Env 标记要求如下  ：

- 根据资源组的命名方案修改资源组上的 Env 标记 
- 将资源组中所有资源的 Env 标记修改为与父资源组相同 

### <a name="modify-resource-groups-env-tag-based-on-name"></a>基于名称修改资源组 Env 标记

对于在 Azure 环境中存在的每个环境，需要[修改](../concepts/effects.md#modify)策略。 每个策略的“修改”策略类似于以下策略定义：

```json
"policyRule": {
    "if": {
        "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "name",
            "like": "prd-*"
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "Production"
            }]
        }
    }
}
```

> [!NOTE]
> 由于此策略规则以资源组为目标，策略定义上的“模式”必须是“全部”，而不是“已编入索引”  。

此策略只将资源组与用于 `prd-` 生产资源的示例命名方案匹配。 更复杂的命名方案可以通过几个“匹配”条件而不是本例中的单个“类似”条件来实现   。

### <a name="modify-resources-to-inherit-the-env-tag"></a>修改资源以继承 Env 标记

业务需求要求所有资源都具有与其父资源组相同的 Env 标记  。 无法重写此标记，因此我们将使用具有[修改](../concepts/effects.md#modify)效果的 addOrReplace 操作  。 示例“修改”策略类似于以下规则：

```json
"policyRule": {
    "if": {
        "anyOf": [{
            "field": "tags['Env']",
            "notEquals": "[resourcegroup().tags['Env']]"
        },
        {
            "field": "tags['Env']",
            "exists": false
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "[resourcegroup().tags['Env']]"
            }]
        }
    }
}
```

> [!NOTE]
> 由于此策略规则以支持标记的资源为目标，因此策略定义上的模式必须为“已编入索引”  。 此配置还确保此策略跳过资源组。

此策略规则查找没有 Env 标记的父资源组值或缺少 Env 标记的任何资源   。 匹配资源的 Env 标记设置为了父资源组值，即使标记已在资源上但具有不同的值也是如此  。

## <a name="assign-the-initiative-and-remediate-resources"></a>分配计划并修正资源

创建上述标记策略后，将它们加入到标记治理的单个计划中，并将其分配给管理组或订阅。 该计划和包含的策略随后会评估现有资源的合规性，并改变与策略规则中的“if”属性相匹配的新资源或更新资源的请求  。 但是，该策略不会自动使用定义的标记更改更新现有的不合规资源。

与 [deployIfNotExists](../concepts/effects.md#deployifnotexists) 策略一样，“修改”策略使用修正任务来更改现有的不合规资源  。 按照有关[如何修正资源](../how-to/remediate-resources.md)的说明来识别不合规的“修改”资源，然后将标记更正为已定义的分类  。

## <a name="clean-up-resources"></a>清理资源

如果今后不再使用本教程中的资源，请使用以下步骤删除前面创建的所有分配或定义：

1. 在“Azure Policy”页左侧的“创作”下选择“定义”（如果尝试删除分配，则选择“分配”）    。

1. 搜索要删除的新计划或策略定义（或分配）。

1. 右键单击定义（或分配）对应的行或选择其末尾的省略号，然后选择“删除定义”（或“删除分配”）。  

## <a name="review"></a>审阅

本教程介绍了以下任务：

> [!div class="checklist"]
> - 确定了业务要求
> - 将每个要求映射到策略定义
> - 将标记策略分组为一个计划

## <a name="next-steps"></a>后续步骤

若要了解有关策略定义结构的详细信息，请查看以下文章：

> [!div class="nextstepaction"]
> [Azure Policy 定义结构](../concepts/definition-structure.md)