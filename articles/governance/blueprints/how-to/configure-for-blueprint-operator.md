---
title: 为蓝图操作员设置环境
description: 了解如何配置 Azure 环境以与蓝图操作员内置基于角色的访问控制 （RBAC） 角色一起使用。
ms.date: 08/26/2019
ms.topic: how-to
ms.openlocfilehash: fba0dd3f2eeb69f768800d1d04640510462d3c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873210"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>为蓝图操作员配置环境

可以将蓝图定义和蓝图分配的管理分配给不同的团队。 架构师或治理团队通常负责蓝图定义的生命周期管理，而运营团队则负责管理这些集中控制的蓝图定义的分配。

**蓝图操作员**内置基于角色的访问控制 （RBAC） 专为此类方案而设计。 该角色允许操作类型团队管理组织蓝图定义的分配，但不能修改它们。 这样做需要在 Azure 环境中进行一些配置，本文将介绍必要的步骤。

## <a name="grant-permission-to-the-blueprint-operator"></a>授予蓝图操作员权限

第一步是将**蓝图操作员**角色授予要分配蓝图的帐户或安全组（建议）。 此操作应在管理组层次结构中的最高级别执行，该层次结构包含操作团队应有权访问的所有管理组和订阅。 建议在授予这些权限时遵循最低特权原则。

1. （推荐）[创建安全组并添加成员](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. 将**蓝图操作员**[的角色分配](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment)添加到帐户或安全组

## <a name="user-assign-managed-identity"></a>用户分配托管标识

蓝图定义可以使用系统分配或用户分配的托管标识。 但是，使用**蓝图操作员**角色时，需要将蓝图定义配置为使用用户分配的托管标识。 此外，需要授予**蓝图操作员**角色的帐户或安全组在用户分配的托管标识上被授予**托管标识操作员**角色。 如果没有此权限，蓝图分配将因缺少权限而失败。

1. [创建用户分配的托管标识](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)，供分配的蓝图使用

1. 将**托管标识操作员**[的角色分配](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment)添加到帐户或安全组。 将角色分配范围限定为新用户分配的托管标识。

1. 作为**蓝图运算符**，分配使用新用户分配的托管标识的[蓝图](../create-blueprint-portal.md#assign-a-blueprint)。

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../concepts/parameters.md)。
- 学习自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 在分配蓝图期间使用[常规故障排除时](../troubleshoot/general.md)解决问题。