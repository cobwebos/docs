---
title: 用于标记资源的策略
description: 介绍可分配的用于确保标记符合性的 Azure 策略。
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e3eeb28ea23b18c3492f68d2fac294fc014420c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82147865"
---
# <a name="assign-policies-for-tag-compliance"></a>分配用于确保标记符合性的策略

使用 [Azure Policy](../../governance/policy/overview.md) 强制实施标记规则和约定。 通过创建策略，可以避免将没有组织所需标记的资源部署到订阅。 不要手动应用标记或搜索不符合的资源，请创建一个策略，用于在部署期间自动应用所需标记。 现在，还可以通过新的 [Modify](../../governance/policy/concepts/effects.md#modify) 效果和[修正任务](../../governance/policy/how-to/remediate-resources.md)将标记应用于现有资源。 以下部分展示标记策略示例。

## <a name="policies"></a>策略

[!INCLUDE [Tag policies](../../../includes/policy/samples/bycat/policies-tags.md)]

## <a name="next-steps"></a>后续步骤

* 若要了解如何标记资源，请参阅[使用标记来组织 Azure 资源](tag-resources.md)。
* 并非所有资源类型都支持标记。 若要确定是否可以将标记应用到资源类型，请参阅 [Azure 资源的标记支持](tag-support.md)。
