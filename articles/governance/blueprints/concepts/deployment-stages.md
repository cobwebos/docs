---
title: 蓝图部署的阶段
description: 了解 Azure 蓝图服务在创建蓝图分配时经历的安全和项目相关步骤。
ms.date: 11/13/2019
ms.topic: conceptual
ms.openlocfilehash: daa7722fa37547929aa21b76b870f70143ae71ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76156618"
---
# <a name="stages-of-a-blueprint-deployment"></a>蓝图部署的阶段

部署蓝图时，Azure 蓝图服务会执行一系列操作来部署蓝图中定义的资源。 本文详细介绍了每个步骤涉及的内容。

蓝图部署是通过为订阅分配蓝图或[更新现有分配来触发的](../how-to/update-existing-assignments.md)。 在部署过程中，蓝图采取以下高级步骤：

> [!div class="checklist"]
> - 授予所有者权利的蓝图
> - 将创建蓝图分配对象
> - 可选 - 蓝图创建**系统分配的**托管标识
> - 托管标识部署蓝图工件
> - 蓝图服务和**系统分配的**托管标识权限被吊销

## <a name="blueprints-granted-owner-rights"></a>授予所有者权利的蓝图

使用[系统分配的托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)标识时，Azure 蓝图服务主体被授予已分配的订阅或订阅的所有者权限。 授予的角色允许蓝图创建系统**分配的**托管标识，然后撤消。 如果使用**用户分配的**托管标识，Azure 蓝图服务主体将得不到并且不需要订阅的所有者权限。

如果分配是通过门户完成的，则会自动授予权限。 但是，如果分配是通过 REST API 完成的，则授予权限需要通过单独的 API 调用来完成。 Azure 蓝图 AppId`f71766dc-90d9-4b7d-bd9d-4499c4331c3f`是 ，但服务主体因租户而异。 使用[Azure 活动目录图形 API](../../../active-directory/develop/active-directory-graph-api.md)和 REST 终结点[服务主体](/graph/api/resources/serviceprincipal)获取服务主体。 然后，通过[门户](../../../role-based-access-control/role-assignments-portal.md)[、Azure CLI、Azure](../../../role-based-access-control/role-assignments-cli.md) [PowerShell、REST](../../../role-based-access-control/role-assignments-powershell.md) [API](../../../role-based-access-control/role-assignments-rest.md)或[资源管理器模板](../../../role-based-access-control/role-assignments-template.md)授予 Azure 蓝图_所有者_角色。

蓝图服务不会直接部署资源。

## <a name="the-blueprint-assignment-object-is-created"></a>将创建蓝图分配对象

用户、组或服务主体为订阅分配蓝图。 分配对象存在于分配蓝图的订阅级别。 部署创建的资源不是在部署实体的上下文中完成的。

创建蓝图分配时，将选择[托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)的类型。 默认值为**系统分配的**托管标识。 可以选择**用户分配的**托管标识。 使用**用户分配的**托管标识时，必须在创建蓝图分配之前定义它并授予它权限。 [所有者](../../../role-based-access-control/built-in-roles.md#owner)和[蓝图操作员](../../../role-based-access-control/built-in-roles.md#blueprint-operator)内置角色都具有创建使用**用户分配的**托管`blueprintAssignment/write`标识的分配的必要权限。

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>可选 - 蓝图创建系统分配的托管标识

在分配期间选择[系统分配的托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)时，蓝图将创建标识，并授予托管标识[所有者](../../../role-based-access-control/built-in-roles.md#owner)角色。 如果[升级了现有分配](../how-to/update-existing-assignments.md)，蓝图将使用以前创建的托管标识。

与蓝图分配相关的托管标识用于部署或重新部署蓝图中定义的资源。 此设计避免了互不经意间干扰的任务。
此设计还通过控制蓝图中每个已部署资源的安全性支持[资源锁定](./resource-locking.md)功能。

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>托管标识部署蓝图工件

然后，托管标识按定义的[排序顺序](./sequencing-order.md)触发蓝图中工件的资源管理器部署。 可以调整顺序，以确保以正确的顺序部署依赖于其他工件的项目。

部署的访问失败通常是授予托管标识的访问权限级别的结果。 蓝图服务管理**系统分配的**托管标识的安全生命周期。 但是，用户负责管理**用户分配的**托管标识的权限和生命周期。

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>蓝图服务和系统分配的托管标识权限被吊销

部署完成后，蓝图将从订阅中撤消**系统分配的**托管标识的权限。 然后，蓝图服务从订阅中撤消其权限。 权限删除可防止蓝图成为订阅的永久所有者。

## <a name="next-steps"></a>后续步骤

- 了解如何使用[静态和动态参数](parameters.md)。
- 学习自定义[蓝图排序顺序](sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](resource-locking.md)。
- 了解如何[更新现有作业](../how-to/update-existing-assignments.md)。
- 在分配蓝图期间使用[常规故障排除时](../troubleshoot/general.md)解决问题。
