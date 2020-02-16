---
title: 模式：使用策略定义部署资源
description: 此 Azure Policy 模式提供了有关如何使用策略定义部署资源的示例。
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: a8b6528afbd21c7c667e48965574c9b48c403654
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169985"
---
# <a name="azure-policy-pattern-deploy-resources"></a>Azure Policy 模式：部署资源

[deployIfNotExists](../concepts/effects.md#deployifnotexists) 效果使得在创建或更新不符合要求的资源时能够部署 [Azure 资源管理器模板](../../../azure-resource-manager/templates/overview.md)。 与使用 [deny](../concepts/effects.md#deny) 效果相比，此方法更好，因为它允许继续创建资源，并确保进行更改以使资源符合要求。

## <a name="sample-policy-definition"></a>示例策略定义

此策略定义使用 **field** 运算符来计算创建或更新的资源的 `type`。 当资源是 _Microsoft.Network/virtualNetworks_ 时，策略将在新资源或已更新资源的位置中查找网络观察程序。 如果找不到匹配的网络观察程序，则会部署资源管理器模板来创建缺少的资源。

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json":::

### <a name="explanation"></a>说明

#### <a name="existencecondition"></a>existenceCondition

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="18-23":::

**properties.policyRule.then.details** 块告诉 Azure Policy 要在 **properties.policyRule.if** 块中查找与创建或更新的资源相关的哪些内容。 在此示例中，资源组 **networkWatcherRG** 中必须存在 **field** `location` 等于新资源或已更新资源的位置的一个网络观察程序。 使用 `field()` 函数将允许 **existenceCondition** 访问新的或已更新资源上的属性，具体而言是 `location` 属性。

#### <a name="roledefinitionids"></a>roleDefinitionIds

:::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="24-26":::

**properties.policyRule.then.details** 块中的 **roleDefinitionIds** _array_ 属性告诉策略定义托管标识需要使用哪些权限来部署所包括的资源管理器模板。 必须设置此属性来包括具有模板部署所需权限的角色，但应使用“最小权限原则”的概念，只应包括必需的操作，不包括任何其他内容。

#### <a name="deployment-template"></a>部署模板

策略定义的 **deployment** 部分有一个 **properties** 块，其中定义了三个核心组件：

- **mode** - 此属性设置模板的[部署模式](../../../azure-resource-manager/templates/deployment-modes.md)。

- **template** - 此属性包括了模板本身。 在此示例中，**location** 模板参数设置新的网络观察程序资源的位置。

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="30-44":::
  
- **parameters** - 此属性定义提供给 **template** 的参数。 参数名称必须与在 **template** 中定义的名称匹配。 在此示例中，将此参数命名为 **location** 以便匹配。 **location** 的值再次使用 `field()` 函数来获取所计算资源（即 **policyRule.if** 块中的虚拟网络）的值。

  :::code language="json" source="~/policy-templates/patterns/pattern-deploy-resources.json" range="45-49":::

## <a name="next-steps"></a>后续步骤

- 查看其他[模式和内置定义](./index.md)。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。