---
title: 了解 Azure 蓝图中的资源锁定
description: 了解用于在分配蓝图时保护资源的锁定选项。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 718c23b806da5058c042b961077e51ba0d4b6245
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973245"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>了解 Azure 蓝图中的资源锁定

仅当存在一个可以确保保持一致性的机制时，大规模创建一致的环境才会真正有价值。 本文介绍 Azure 蓝图中的资源锁定的工作原理。

## <a name="locking-modes-and-states"></a>锁定模式和状态

锁定模式适用于蓝图分配，并且只有两个选项：“无”或“所有资源”。 在蓝图分配期间配置锁定模式，并在成功将分配应用于订阅后无法更改该锁定模式。

由分配到订阅的蓝图中的项目定义创建的资源有以下三种状态：“未锁定”、“只读”或“无法编辑/删除”。 任何类型的项目都可以具有“未锁定”状态。 但是，非资源组项目为“只读”，资源组为“无法编辑/删除”。 这是如何管理这些资源的重要区别。

“只读”状态完全按如下所示定义：以任何方式都无法更改资源 -- 无更改且无法将其删除。 由于资源组的“容器”性质，“无法编辑/删除”更为微妙。 资源组对象为只读，但可以创建、更新和删除资源组中的资源 -- 只要它们不属于具有“只读”锁定状态的任何蓝图分配。

## <a name="overriding-locking-states"></a>重写锁定状态

虽然对订阅具有相应的[基于角色的访问控制](../../../role-based-access-control/overview.md) (RBAC) 的人员（如“所有者”角色）通常能够更改或删除任何资源，但在已部署的分配过程中蓝图应用锁定时并非如此。 如果已使用“锁定”选项设置分配，则甚至订阅所有者也不能更改已包含的资源。

这样可以保护已定义的蓝图与设计用于通过意外或以编程方式删除或更改创建的环境之间的一致性。

## <a name="removing-locking-states"></a>删除锁定状态

如果有必要删除由分配创建的资源，则将其删除的唯一方法是先删除分配。 删除分配后，将删除由蓝图创建的锁定。 但是，资源保留了下来，然后需要具有相应权限的人员通过正常方式将其删除。

## <a name="how-blueprint-locks-work"></a>蓝图锁定的工作原理

如果蓝图分配选择了“锁定”选项，则会在分配期间将 RBAC 角色 `denyAssignments` 应用于项目资源。 该角色由蓝图分配的托管标识添加，并且只能通过同一托管标识从项目资源中删除。 这将强制实施锁定机制，并防止尝试删除蓝图之外的蓝图锁定。 只有删除蓝图分配才能删除角色和锁定，并且只能通过具有相应权限的人员来执行该操作。

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](lifecycle.md)
- 了解如何使用[静态和动态参数](parameters.md)
- 了解如何自定义[蓝图排序顺序](sequencing-order.md)
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)
- 使用[常规故障排除](../troubleshoot/general.md)在蓝图分配期间解决问题