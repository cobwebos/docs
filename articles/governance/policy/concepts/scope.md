---
title: 了解 Azure 策略中的作用域
description: 介绍 Azure 资源管理器中范围的概念，以及如何将其应用于 Azure 策略来控制 Azure 策略评估的资源。
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 83eda330a35b007abfa37046a202b14728726849
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984440"
---
# <a name="understand-scope-in-azure-policy"></a>了解 Azure 策略中的作用域

有许多设置可确定哪些资源可以被评估，哪些资源由 Azure 策略评估。 这些控件的主要概念是 _范围_。
有关高级概述，请参阅 [Azure 资源管理器中的作用域](../../../azure-resource-manager/management/overview.md#understand-scope)。
本文介绍了在 Azure 策略中的 _作用域_ 及其相关对象和属性的影响。

## <a name="definition-location"></a>定义位置

Azure 策略使用的第一个实例范围是在创建策略定义时。 定义可以保存在管理组或订阅中。 位置确定可向其分配方案或策略的范围。 资源必须在定义位置的资源层次结构内，才能分配到目标。

如果定义位置是：

- 只能**为订阅中**的资源分配策略定义。
- 仅限**管理组**-可以为子管理组和子订阅中的资源分配策略定义。 如果计划将策略定义应用于多个订阅，则该位置必须是包含每个订阅的管理组。

此位置应为你要在其上使用策略定义的所有资源所共享的资源容器。 此资源容器通常是根管理组附近的管理组。

## <a name="assignment-scopes"></a>分配范围

赋值具有几个设置作用域的属性。 使用这些属性可确定要评估的 Azure 策略的资源以及哪些资源会达到符合性。 这些属性映射到以下概念：

- 包含-应通过定义评估资源层次结构或单个资源的符合性。 `properties.scope`赋值对象上的属性确定要包含的内容并评估其符合性。 有关详细信息，请参阅 [分配定义](./assignment-structure.md)。

- 排除-不应根据定义评估资源层次结构或单个资源的符合性。 `properties.notScopes`赋值对象的_数组_属性确定要排除的内容。 不会在符合性计数中评估或包含这些范围内的资源。 有关详细信息，请参阅 [分配定义-排除的作用域](./assignment-structure.md#excluded-scopes)。

除了策略分配上的属性，还是 [策略例外](./exemption-structure.md) 对象。 免除通过提供一种方法来识别不计算的赋值部分，从而增强范围。

- 豁免 (**预览版** 功能) -资源层次结构或单个资源应该按定义评估符合性，但不会因某个原因（例如，通过另一种方法进行弃权或通过其他方法缓解）进行评估。 处于此状态的资源显示为在相容性报告中 **免除** ，以便可以对其进行跟踪。 豁免对象在资源层次结构或单个资源上创建为子对象，该对象确定免除的范围。 资源层次结构或单个资源可以免除多个分配。 可以通过使用属性将豁免配置为按计划过期 `expiresOn` 。 有关详细信息，请参阅 [例外定义](./exemption-structure.md)。

  > [!NOTE]
  > 由于免除为资源层次结构或单个资源授予豁免的影响，例外情况还具有其他安全措施。 除了要求对 `Microsoft.Authorization/policyExemptions/write` 资源层次结构或单个资源进行操作外，例外的创建者还必须在 `exempt/Action` 目标分配上具有谓词。

## <a name="scope-comparison"></a>范围比较

下表比较了作用域选项：

| | 进 | 排除 (notScopes)  | 例外 |
|---|:---:|:---:|:---:|
|**计算资源** | &#10004; | - | - |
|**资源管理器对象** | - | - | &#10004; |
|**需要修改策略分配对象** | &#10004; | &#10004; | - |

## <a name="next-steps"></a>后续步骤

- 了解[策略定义结构](./definition-structure.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取符合性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。