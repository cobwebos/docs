---
title: 如何更新现有 Azure 蓝图分配
description: 了解如何更新 Azure 蓝图中现有分配的机制。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: ecac0fb21a6691874d5e8db49eadd7114d41845f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956194"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>如何更新现有蓝图分配

分配蓝图时可以更新分配。 有多个更新现有分配的原因，包括：

- 添加或删除[资源锁定](../concepts/resource-locking.md)
- 更改[动态参数](../concepts/parameters.md#dynamic-parameters)的值
- 将分配升级到新发布的蓝图版本

## <a name="updating-assignments"></a>更新分配

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择左侧窗口中的“策略”，启动 Azure 蓝图服务。 在“策略”页上，单击“蓝图”。

1. 从左侧页面选择“分配的蓝图”。

1. 在蓝图列表中，左键单击蓝图分配，然后单击“更新分配”按钮，或右键单击蓝图分配并选择“更新分配”。

   ![更新分配](../media/update-existing-assignments/update-assignment.png)

1. “分配蓝图”页将加载预先填写的来自原始分配的所有值。 可以更改“蓝图定义版本”、“锁定分配”状态，以及蓝图定义上存在的任何动态参数。 完成更改时，单击“分配”。

1. 在更新后的分配详细信息页上，查看新状态。 在此示例中，我们向分配添加了“锁定”。

   ![更新的分配 - 已锁定](../media/update-existing-assignments/updated-assignment.png)

1. 使用下拉列表了解有关其他“分配操作”的详细信息。 “受管理资源”表通过选择的分配操作进行更新。

   ![分配操作](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>更新分配规则

更新分配部署遵循几个重要规则。 这些规则决定了现有资源的情况，具体取决于请求的更改和正在部署或更新的项目资源的类型。

- 角色分配
  - 如果角色或角色代理人（用户、组或应用程序）发生更改，则创建新的角色分配。 将保留以前部署的角色分配。
- 策略分配
  - 如果策略分配的参数已发生更改，则更新现有分配。
  - 如果策略分配的定义已发生更改，则创建新的策略分配。 将保留以前部署的策略分配。
  - 如果从蓝图中删除策略分配项目，将保留以前部署的策略分配。
- Azure 资源管理器模板
  - 该模板通过资源管理器作为 PUT 处理。 由于每个资源类型的处理方式不同，因此要查看每个包含的资源的文档，以确定在蓝图运行时该操作的影响。

## <a name="possible-errors-on-updating-assignments"></a>更新分配上可能出现的错误

更新分配时，在执行期间进行更改可能会导致中断。 完成部署后，此示例将更改资源组的位置。 [Azure 资源管理器](../../../azure-resource-manager/resource-group-overview.md)支持的任何更改都可以进行，但是任何会在 Azure 资源管理器中导致错误的更改也将导致分配失败。

可以更新分配的次数没有限制。 因此，如果发生错误，要么是由于错误参数（已经存在的对象），要么是 Azure 资源管理器不允许更改，请确定错误，然后对分配进行另一次更新。

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](../concepts/lifecycle.md)
- 了解如何使用[静态和动态参数](../concepts/parameters.md)
- 了解如何自定义[蓝图排序顺序](../concepts/sequencing-order.md)
- 了解如何使用[蓝图资源锁定](../concepts/resource-locking.md)
- 使用[常规故障排除](../troubleshoot/general.md)在蓝图分配期间解决问题
