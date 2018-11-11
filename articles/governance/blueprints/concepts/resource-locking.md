---
title: 了解 Azure 蓝图中的资源锁定
description: 了解用于在分配蓝图时保护资源的锁定选项。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 4e71797837927fe5f5233bcf88d35fef98f504e9
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139436"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>了解 Azure 蓝图中的资源锁定

仅当存在一个可以维护该一致性的机制时，大规模创建一致的环境才会真正有价值。 本文介绍 Azure 蓝图中的资源锁定的工作原理。

## <a name="locking-modes-and-states"></a>锁定模式和状态

锁定模式适用于蓝图分配，并且只有两个选项：“无”或“所有资源”。 锁定模式是在分配蓝图期间配置的，在成功将分配应用于订阅后无法更改锁定模式。

由蓝图分配中的项目创建的资源有三种状态：“未锁定”、“只读”或“无法编辑/删除”。 每个项目都可以处于“未锁定”状态。 但是，非资源组项目具有“只读”状态，资源组具有“无法编辑/删除”状态。 此差异是这些资源的管理方式的一个重要区别。

“只读”状态完全按如下所示定义：以任何方式都无法更改资源 -- 无更改且无法将其删除。 由于资源组的“容器”性质，“无法编辑/删除”更为微妙。 资源组对象是只读的，但可以对资源组中的非锁定资源进行更改。

## <a name="overriding-locking-states"></a>重写锁定状态

通常可以允许在订阅上具有合适的[基于角色的访问控制](../../../role-based-access-control/overview.md) (RBAC) 的某人（例如“所有者”角色）更改或删除任何资源。 当蓝图在已部署的分配中应用了锁定时，无法进行此访问。 如果已使用“锁定”选项设置分配，则甚至订阅所有者也不能更改已包含的资源。

此安全措施可以保护已定义的蓝图与设计用于通过意外或以编程方式删除或更改创建的环境之间的一致性。

## <a name="removing-locking-states"></a>删除锁定状态

如果有必要删除由分配创建的资源，则将其删除的方法是先删除分配。 删除分配后，将删除由蓝图创建的锁定。 但是，资源会留在原地，需要通过正常方式删除。

## <a name="how-blueprint-locks-work"></a>蓝图锁定的工作原理

如果蓝图分配选择了“锁定”选项，则会在分配期间将 RBAC 角色 `denyAssignments` 应用于项目资源。 该角色由蓝图分配的托管标识添加，并且只能通过同一托管标识从项目资源中删除。 此安全措施将强制实施锁定机制，并防止在蓝图外部删除蓝图锁定。 只有删除蓝图分配才能删除角色和锁定，并且只能通过具有相应权限的人员来执行该操作。

> [!IMPORTANT]
> Azure 资源管理器可以将角色分配详细信息缓存最多 30 分钟。 因此，蓝图资源上的 `denyAssignments` 可能不会立即完全生效。 在此时间段内，可能无法删除将由蓝图锁保护的资源。

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](lifecycle.md)
- 了解如何使用[静态和动态参数](parameters.md)
- 了解如何自定义[蓝图排序顺序](sequencing-order.md)
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)
- 使用[常规疑难解答](../troubleshoot/general.md)在蓝图分配期间解决问题