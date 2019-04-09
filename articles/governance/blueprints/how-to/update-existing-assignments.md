---
title: 如何更新现有分配从门户
description: 了解如何更新现有分配在门户中 Azure 蓝图的机制。
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: c75bd8c3831bad0c8217f16315843cbe3824fe4d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59276828"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>如何更新现有蓝图分配

分配蓝图时可以更新分配。 众多原因导致要更新现有分配，其中包括：

- 添加或删除[资源锁定](../concepts/resource-locking.md)
- 更改[动态参数](../concepts/parameters.md#dynamic-parameters)的值
- 将分配升级到新发布的蓝图版本

## <a name="updating-assignments"></a>更新分配

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 从左侧页面选择“分配的蓝图”。

1. 在蓝图列表中，左键单击蓝图分配。 然后单击**更新分配**按钮，或右键单击蓝图分配并选择**更新分配**。

   ![更新现有的蓝图分配](../media/update-existing-assignments/update-assignment.png)

1. **分配蓝图**页将加载预先填入从原始分配的所有值。 可以更改“蓝图定义版本”、“锁定分配”状态，以及蓝图定义上存在的任何动态参数。 完成更改时，单击“分配”。

1. 在更新后的分配详细信息页上，查看新状态。 在此示例中，我们向分配添加了“锁定”。

   ![更新现有的蓝图分配的锁模式发生更改](../media/update-existing-assignments/updated-assignment.png)

1. 了解有关其他详细信息**赋值运算**使用下拉列表。 表**托管资源**按选择的分配操作的更新。

   ![赋值运算的蓝图分配](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>更新分配规则

更新分配部署遵循几个重要规则。 这些规则决定了已部署的资源会发生什么情况。 所请求的更改和要部署或更新的项目资源决定了要采取的操作。

- 角色分配
  - 如果角色或角色代理人（用户、组或应用程序）发生更改，则创建新的角色分配。 以前部署的角色分配将会保留。
- 策略分配
  - 如果策略分配的参数已发生更改，则更新现有分配。
  - 如果策略分配的定义已发生更改，则会创建一个新的策略分配。 以前部署的策略分配将会保留。
  - 如果从蓝图中删除策略分配项目，已部署的策略分配将会保留。
- Azure 资源管理器模板
  - 该模板通过资源管理器作为 PUT 处理。 由于每个资源类型以不同的方式处理此操作，因此请查看包含的每个资源的文档，以确定在蓝图运行时该操作的影响。

## <a name="possible-errors-on-updating-assignments"></a>更新分配上可能出现的错误

更新分配时，在执行期间进行更改可能会导致中断。 一个示例是在部署完成后更改资源组的位置。 [Azure 资源管理器](../../../azure-resource-manager/resource-group-overview.md)支持的任何更改都可以进行，但是任何会在 Azure 资源管理器中导致错误的更改也将导致分配失败。

可以更新分配的次数没有限制。 如果发生错误，请确定该错误并对分配进行其他更新。  示例错误场景：

- 不正确的参数
- 已经存在的对象
- Azure 资源管理器不支持的更改

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。