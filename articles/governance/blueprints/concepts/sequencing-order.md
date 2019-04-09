---
title: 了解部署排序顺序
description: 了解有关蓝图定义所经历的生命周期和有关每个阶段的详细信息。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 5552e44fcca056bd4fd5b4fd19559adfbd005444
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266182"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>了解 Azure 蓝图中的部署排序

Azure 蓝图使用**序列化顺序**处理蓝图定义的分配时确定的顺序创建资源。 本文解释了以下概念：

- 使用的默认序列化顺序
- 如何自定义顺序
- 自定义顺序是如何处理的

JSON 示例中的有些变量需要用自己的值替换：

- `{YourMG}` -替换为管理组的名称

## <a name="default-sequencing-order"></a>默认排序顺序

如果蓝图定义包含顺序，以便将项目部署任何指令或指令为 null，则使用以下顺序：

- 订阅级别“角色分配”项目按项目名称排序
- 订阅级别“策略分配”项目按项目名称排序
- 订阅级别“Azure 资源管理器模板”项目按项目名称排序
- “资源组”项目（包括子项目）按占位符名称排序

在每个**资源组**项目中，将按照以下顺序排列在该资源组中创建的项目：

- 资源组子“角色分配”项目按项目名称排序
- 资源组子“策略分配”项目按项目名称排序
- 资源组子“Azure 资源管理器模板”项目按项目名称排序

## <a name="customizing-the-sequencing-order"></a>自定义排序顺序

当撰写大型蓝图定义时，可能有必要为按特定顺序创建资源。 蓝图定义包含多个 Azure 资源管理器模板时，此方案的最常见的使用模式。 蓝图通过允许定义排序顺序来处理此模式。

排序是通过在 JSON 中定义 `dependsOn` 属性来实现的。 蓝图定义中，为资源组和项目对象支持此属性。 `dependsOn` 是特定项目需要它创建之前创建的项目名称的字符串数组。

### <a name="example---ordered-resource-group"></a>示例-排序资源组

此示例蓝图定义具有已通过声明的值来定义自定义排序顺序的资源组`dependsOn`，以及标准的资源组。 在这种情况下，名为“assignPolicyTags”的项目将在“ordered-rg”资源组之前进行处理。
standard-rg 将按默认排序顺序进行处理。

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint",
    "type": "Microsoft.Blueprint/blueprints",
    "name": "mySequencedBlueprint"
}
```

### <a name="example---artifact-with-custom-order"></a>示例 - 使用自定义顺序的项目

此示例是一个策略项目，它依赖于一个 Azure 资源管理器模板。 根据默认排序，策略项目将先于 Azure 资源管理器模板创建。 此排序允许策略项目等待 Azure 资源管理器模板完成创建。

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/assignPolicyTags",
    "type": "Microsoft.Blueprint/artifacts",
    "name": "assignPolicyTags"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>示例-具体取决于资源组的订阅级别模板项目

此示例适用于在订阅级别，以依赖于资源组部署的资源管理器模板。 在默认排序，将任何资源组和资源组中的子项目之前创建的订阅级别项目。 蓝图定义此类中定义的资源组：

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

具体取决于订阅级别模板项目**等待-对-我**资源组定义如下：

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/subtemplateWaitForRG",
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "name": "subtemplateWaitForRG"
}
```

## <a name="processing-the-customized-sequence"></a>处理自定义排序

在创建过程中，将使用拓扑排序来创建蓝图项目的依赖项关系图。 此检查可确保资源组与项目之间每个级别的依赖项都得到支持。

如果声明了不会更改默认顺序的依赖项，则不会进行任何更改。 一个示例是依赖于订阅级策略的资源组。 另一个示例是资源组“standard-rg”子策略分配，它依赖于资源组“standard-rg”子角色分配。 这两种情况下，`dependsOn` 都不会更改默认的排序顺序且不会进行任何更改。

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](lifecycle.md)。
- 了解如何使用[静态和动态参数](parameters.md)。
- 了解如何利用[蓝图资源锁定](resource-locking.md)。
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。