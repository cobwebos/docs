---
title: 蓝图部署的阶段
description: 了解 Azure 蓝图服务在创建蓝图分配时所经历的与安全和项目相关的步骤。
ms.date: 05/06/2020
ms.topic: conceptual
ms.openlocfilehash: 9efc66baa262e004a8beea5295e8567f4ab119dd
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82863988"
---
# <a name="stages-of-a-blueprint-deployment"></a>蓝图部署的阶段

部署蓝图后，Azure 蓝图服务将执行一系列操作来部署蓝图中定义的资源。 本文详细介绍了每个步骤涉及的内容。

通过向订阅分配蓝图或[更新现有分配](../how-to/update-existing-assignments.md)来触发蓝图部署。 在部署期间，Azure 蓝图会执行以下高级步骤：

> [!div class="checklist"]
> - Azure 蓝图已授予所有者权限
> - 已创建蓝图赋值对象
> - 可选-Azure 蓝图创建**系统分配的**托管标识
> - 托管标识部署蓝图项目
> - 吊销 Azure 蓝图服务和**系统分配的**托管标识权限

## <a name="azure-blueprints-granted-owner-rights"></a>Azure 蓝图已授予所有者权限

使用[系统分配的托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)托管标识时，将向 Azure 蓝图服务主体授予对分配的订阅或订阅的所有者权限。 授予的角色允许 Azure 蓝图创建并稍后撤消**系统分配**的托管标识。 如果使用**用户分配**的托管标识，Azure 蓝图服务主体不会获得订阅的所有者权限，也不需要对订阅拥有所有者权限。

如果分配是通过门户完成的，则会自动授予权限。 但是，如果通过 REST API 完成分配，则需要使用单独的 API 调用来授予权限。 Azure 蓝图 AppId 为`f71766dc-90d9-4b7d-bd9d-4499c4331c3f`，但服务主体因租户而异。 使用[Azure Active Directory 图形 API](../../../active-directory/develop/active-directory-graph-api.md)和 REST 终结点[服务主体](/graph/api/resources/serviceprincipal)以获取服务主体。 然后，通过[门户](../../../role-based-access-control/role-assignments-portal.md)、 [Azure CLI](../../../role-based-access-control/role-assignments-cli.md)、 [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md)、 [REST API](../../../role-based-access-control/role-assignments-rest.md)或[资源管理器模板](../../../role-based-access-control/role-assignments-template.md)授予 Azure 蓝图_所有者_角色。

Azure 蓝图服务不会直接部署资源。

## <a name="the-blueprint-assignment-object-is-created"></a>已创建蓝图赋值对象

用户、组或服务主体将蓝图分配给订阅。 分配对象存在于指定了蓝图的订阅级别。 部署创建的资源不在部署实体的上下文中完成。

创建蓝图分配时，将选择[托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)的类型。 默认值为**系统分配**的托管标识。 可以选择**用户分配**的托管标识。 使用**用户分配**的托管标识时，必须在创建蓝图分配之前定义和授予权限。 [所有者](../../../role-based-access-control/built-in-roles.md#owner)和[蓝图运算符](../../../role-based-access-control/built-in-roles.md#blueprint-operator)内置角色都具有创建使用**用户分配**的`blueprintAssignment/write`托管标识的分配所必需的权限。

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>可选-Azure 蓝图创建系统分配的托管标识

在分配过程中选择[系统分配的托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)时，Azure 蓝图会创建标识，并向托管标识授予[所有者](../../../role-based-access-control/built-in-roles.md#owner)角色。 如果[升级了现有分配](../how-to/update-existing-assignments.md)，Azure 蓝图将使用之前创建的托管标识。

与蓝图分配相关的托管标识用于部署或重新部署在蓝图中定义的资源。 此设计避免了意外干扰的赋值。
此设计还通过从蓝图控制每个已部署资源的安全性来支持[资源锁定](./resource-locking.md)功能。

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>托管标识部署蓝图项目

然后，托管标识会按定义的[顺序顺序](./sequencing-order.md)触发蓝图内项目的资源管理器部署。 可以调整顺序，以确保依赖于其他项目的项目按正确的顺序进行部署。

部署的访问失败通常是向托管标识授予的访问级别。 Azure 蓝图服务管理**系统分配**的托管标识的安全生命周期。 但是，用户负责管理**用户分配**的托管标识的权限和生命周期。

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>已撤销蓝图服务和系统分配的托管标识权限

部署完成后，Azure 蓝图会撤消订阅中**系统分配**的托管标识的权限。 然后，Azure 蓝图服务将撤消其对订阅的权限。 权限删除可防止 Azure 蓝图成为订阅的永久所有者。

## <a name="next-steps"></a>后续步骤

- 了解如何使用[静态和动态参数](parameters.md)。
- 了解如何自定义[蓝图排序顺序](sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](resource-locking.md)。
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。
