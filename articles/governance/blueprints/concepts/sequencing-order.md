---
title: 了解 Azure 蓝图中的部署排序
description: 了解有关蓝图经历的生命周期以及每个阶段的详细信息。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c09fb26d8375e08281241aaed3f6f6e30acc755b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955446"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>了解 Azure 蓝图中的部署排序

Azure 蓝图使用排序顺序来确定处理蓝图分配时资源创建的顺序。 本文介绍了使用的默认排序顺序、如何自定义顺序以及如何处理自定义的顺序。

JSON 示例中的有些变量需要用自己的值替换：

- `{YourMG}` - 替换为管理组的名称

## <a name="default-sequencing-order"></a>默认排序顺序

如果蓝图不包含用于部署项目的顺序的指令或指令为 NULL，则使用以下顺序：

- 订阅级别“角色分配”项目按项目名称排序
- 订阅级别“策略分配”项目按项目名称排序
- 订阅级别“Azure 资源管理器模板”项目按项目名称排序
- “资源组”项目（包括子项目）按占位符名称排序

在处理的每个“资源组”项目中，以下顺序用于排列在该资源组中创建的项目：

- 资源组子“角色分配”项目按项目名称排序
- 资源组子“策略分配”项目按项目名称排序
- 资源组子“Azure 资源管理器模板”项目按项目名称排序

## <a name="customizing-the-sequencing-order"></a>自定义排序顺序

在编撰大型蓝图时，可能需要以一资源与另一资源的关系采用特定顺序来创建资源。 最常使用此模式是在蓝图包含多个 Azure 资源管理器模板时。 蓝图通过允许定义排序顺序来处理此问题。

这通过在 JSON 中定义 `dependsOn` 属性来实现。 只有蓝图（用于资源组）和项目对象支持此属性。 `dependsOn` 是在创建特定项目之前需要创建的项目名称的字符串数组。

### <a name="example---blueprint-with-ordered-resource-group"></a>示例 - 具有已排序资源组的蓝图

此示例显示包含资源组的蓝图，该资源组通过声明 `dependsOn` 的值以及标准资源组来定义自定义排序顺序。 在这种情况下，名为“assignPolicyTags”的项目将在“ordered-rg”资源组之前进行处理。
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

这是依赖于 Azure 资源管理器模板的示例策略项目。 根据默认排序，策略项目将先于 Azure 资源管理器模板创建。 这允许策略项目等待 Azure 资源管理器模板得到创建。

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

## <a name="processing-the-customized-sequence"></a>处理自定义排序

创建过程中，拓扑排序用于创建蓝图及其项目的依赖项关系图。 这确保可以支持资源组和项目之间的多个依赖项级别。

如果依赖项在不会更改默认顺序的蓝图或项目上进行声明，则不会对排序顺序进行任何更改。 此类示例包括依赖于订阅级别策略的资源组或依赖于资源组“standard-rg”子角色分配的资源组“standard-rg”子策略分配。 这两种情况下，`dependsOn` 都不会更改默认的排序顺序且不会进行任何更改。

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](lifecycle.md)
- 了解如何使用[静态和动态参数](parameters.md)
- 了解如何使用[蓝图资源锁定](resource-locking.md)
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)
- 使用[常规故障排除](../troubleshoot/general.md)在蓝图分配期间解决问题