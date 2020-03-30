---
title: 标记资源的策略
description: 描述可以分配的 Azure 策略，以确保标记符合性。
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e7febe4c833cefd0ee9a5c49b4b70f4901fea8d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80147013"
---
# <a name="assign-policies-for-tag-compliance"></a>分配策略以符合标签

您可以使用[Azure 策略](../../governance/policy/overview.md)强制标记规则和约定。 通过创建策略，可以避免将资源部署到没有组织预期标记的订阅中。 您可以创建策略，在部署期间自动应用所需的标记，而不是手动应用标记或搜索不符合的资源。 现在，还可以通过新的 [Modify](../../governance/policy/concepts/effects.md#modify) 效果和[修正任务](../../governance/policy/how-to/remediate-resources.md)将标记应用于现有资源。 以下部分展示标记策略示例。

## <a name="policies"></a>策略

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-policies-tags.md)]

## <a name="next-steps"></a>后续步骤

* 若要了解如何标记资源，请参阅[使用标记来组织 Azure 资源](tag-resources.md)。
* 并非所有资源类型都支持标记。 若要确定是否可以将标记应用到资源类型，请参阅 [Azure 资源的标记支持](tag-support.md)。
