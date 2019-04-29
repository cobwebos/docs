---
title: 蓝图部署的阶段
description: 了解 Azure Blueprint 服务在部署过程中将经历的步骤。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d7000813b51fb9c9aae9a21cbded3ae0028e83f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684686"
---
# <a name="stages-of-a-blueprint-deployment"></a>蓝图部署的阶段

获取部署蓝图，一系列操作均由 Azure 蓝图服务部署蓝图中定义的资源。 本文提供了有关每个步骤涉及的详细信息。

通过将蓝图分配给订阅触发蓝图部署或[更新现有分配](../how-to/update-existing-assignments.md)。 部署过程中，蓝图采用以下高级步骤：

> [!div class="checklist"]
> - 蓝图授予所有者权限
> - 创建蓝图分配对象
> - 可选-蓝图创建**系统分配**托管标识
> - 托管的标识部署蓝图项目
> - 蓝图服务和**系统分配**托管的标识权限被吊销

## <a name="blueprints-granted-owner-rights"></a>蓝图授予所有者权限

Azure 蓝图服务主体授予对已分配的订阅或订阅的所有者权限。 授予的角色允许创建，并在以后撤销，蓝图[系统分配的托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)。

如果通过门户完成分配自动授予权限。 但是，如果分配通过 REST API 中，授予权限需要能够完成与一个单独的 API 调用。 Azure Blueprint AppId 是`f71766dc-90d9-4b7d-bd9d-4499c4331c3f`，但因租户而异的服务主体。 使用[Azure Active Directory 图形 API](../../../active-directory/develop/active-directory-graph-api.md)和 REST 终结点[servicePrincipals](/graph/api/resources/serviceprincipal)获取服务主体。 然后，授予 Azure 蓝图_所有者_通过角色[门户](../../../role-based-access-control/role-assignments-portal.md)， [Azure CLI](../../../role-based-access-control/role-assignments-cli.md)， [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md)， [RESTAPI](../../../role-based-access-control/role-assignments-rest.md)，或[资源管理器模板](../../../role-based-access-control/role-assignments-template.md)。

蓝图服务不会直接部署的资源。

## <a name="the-blueprint-assignment-object-is-created"></a>创建蓝图分配对象

用户、 组或服务主体将蓝图分配到订阅。 在订阅级别分配蓝图其中存在分配对象。 由部署创建的资源不是在部署实体的上下文中完成的。

创建蓝图分配的类型时[托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)处于选中状态。 默认值是**系统分配**托管标识。 一个**用户分配**，可以选择托管的标识。 使用时**用户分配**托管标识，必须定义和之前创建蓝图分配，授予权限。

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>可选-蓝图创建系统分配给托管的标识

当[系统分配的托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)处于选中状态期间分配，蓝图创建标识，并授予托管的标识[所有者](../../../role-based-access-control/built-in-roles.md#owner)角色。 如果[升级现有分配](../how-to/update-existing-assignments.md)，蓝图使用以前创建的托管的标识。

与蓝图分配相关的托管的标识用于部署或重新部署蓝图中定义的资源。 这种设计避免了无意中彼此间相互影响的分配。
这种设计还支持[资源锁定](./resource-locking.md)通过控制每个已部署资源的蓝图的安全功能。

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>托管的标识部署蓝图项目

托管的标识，然后触发中定义的蓝图中的项目资源管理器部署[序列化顺序](./sequencing-order.md)。 可以调整顺序以确保按正确的顺序部署依赖于其他项目的项目。

部署了访问错误通常是访问的授予给托管标识权限级别的结果。 该蓝图服务管理的安全生命周期**系统分配**托管标识。 但是，用户负责管理的权限和生命周期**用户分配**托管标识。

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>蓝图服务和系统分配的托管的标识权限被吊销

完成部署后，蓝图，撤消的权限**系统分配**从订阅中托管标识。 然后，蓝图服务撤消其从订阅的权限。 权限删除阻止蓝图变得对某一订阅的永久所有者。

## <a name="next-steps"></a>后续步骤

- 了解如何使用[静态和动态参数](parameters.md)。
- 了解如何自定义[蓝图排序顺序](sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](resource-locking.md)。
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。