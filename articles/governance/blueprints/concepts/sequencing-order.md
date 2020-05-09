---
title: 了解部署排序顺序
description: 了解在蓝图分配过程中部署蓝图项目的默认顺序，以及如何自定义部署顺序。
ms.date: 05/06/2020
ms.topic: conceptual
ms.openlocfilehash: 91e11f8127ba2532ad48362de1689f4be2b6f935
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864515"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>了解 Azure 蓝图中的部署排序

在处理蓝图定义的分配时，Azure 蓝图使用**排序顺序**来确定创建资源的顺序。 本文解释了以下概念：

- 使用的默认序列化顺序
- 如何自定义顺序
- 自定义顺序是如何处理的

JSON 示例中的有些变量需要用自己的值替换：

- `{YourMG}` - 替换为管理组的名称

## <a name="default-sequencing-order"></a>默认排序顺序

如果蓝图定义为部署项目的顺序不包含指令，或者指令为 null，则使用以下顺序：

- 订阅级别“角色分配”项目按项目名称排序****
- 订阅级别“策略分配”项目按项目名称排序****
- 订阅级别“Azure 资源管理器模板”项目按项目名称排序****
- “资源组”项目（包括子项目）按占位符名称排序****

在每个**资源组**项目中，将按照以下顺序排列在该资源组中创建的项目：

- 资源组子“角色分配”项目按项目名称排序****
- 资源组子“策略分配”项目按项目名称排序****
- 资源组子“Azure 资源管理器模板”项目按项目名称排序****

> [!NOTE]
> 使用[伪像（）](../reference/blueprint-functions.md#artifacts)可对所引用的项目创建隐式依赖项。

## <a name="customizing-the-sequencing-order"></a>自定义排序顺序

编写大型蓝图定义时，可能需要按特定顺序创建资源。 此方案的最常见使用模式是蓝图定义包含多个 Azure 资源管理器模板。 Azure 蓝图通过允许定义序列顺序来处理此模式。

排序是通过在 JSON 中定义 `dependsOn` 属性来实现的。 资源组和项目对象的蓝图定义支持此属性。 `dependsOn` 是在创建特定项目之前需要创建的项目名称的字符串数组。

> [!NOTE]
> 创建蓝图对象时，如果使用[REST API](/rest/api/blueprints/artifacts/createorupdate)，则每个项目资源都将从文件名中获取其名称（如果使用[POWERSHELL](/powershell/module/az.blueprint/new-azblueprintartifact)）或 URL 端点。 项目中的 ResourceGroup 引用必须与蓝图定义中定义的_资源_组引用匹配。

### <a name="example---ordered-resource-group"></a>示例-有序资源组

此示例蓝图定义具有一个资源组，该资源组通过声明的值`dependsOn`以及标准资源组定义了自定义的排序顺序。 在这种情况下，名为“assignPolicyTags”的项目将在“ordered-rg”资源组之前进行处理********。
standard-rg 将按默认排序顺序进行处理****。

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
    "type": "Microsoft.Blueprint/blueprints"
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
    "type": "Microsoft.Blueprint/artifacts"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>示例-根据资源组的订阅级别模板项目

此示例适用于在订阅级别部署的资源管理器模板，以依赖于资源组。 默认排序中，将在这些资源组中的任何资源组和子项目之前创建订阅级别项目。 资源组在蓝图定义中定义，如下所示：

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

根据 "**等待我**" 资源组的定义，订阅级别模板项目的定义如下所示：

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
    "type": "Microsoft.Blueprint/blueprints/artifacts"
}
```

## <a name="processing-the-customized-sequence"></a>处理自定义排序

在创建过程中，将使用拓扑排序来创建蓝图项目的依赖项关系图。 此检查可确保资源组与项目之间每个级别的依赖项都得到支持。

如果声明了不会更改默认顺序的依赖项，则不会进行任何更改。
一个示例是依赖于订阅级策略的资源组。 另一个示例是资源组“standard-rg”子策略分配，它依赖于资源组“standard-rg”子角色分配。 这两种情况下，`dependsOn` 都不会更改默认的排序顺序且不会进行任何更改。

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](lifecycle.md)。
- 了解如何使用[静态和动态参数](parameters.md)。
- 了解如何利用[蓝图资源锁定](resource-locking.md)。
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。