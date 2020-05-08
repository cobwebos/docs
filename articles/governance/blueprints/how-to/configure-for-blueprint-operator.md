---
title: 设置蓝图操作员的环境
description: 了解如何配置 Azure 环境，以便与蓝图运算符内置的基于角色的访问控制（RBAC）角色一起使用。
ms.date: 05/06/2020
ms.topic: how-to
ms.openlocfilehash: a61997f5e3247e5b42d5e2f07c0fc38cc1b5114d
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864005"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>为蓝图操作员配置环境

可以将蓝图定义和蓝图分配的管理分配给不同的团队。 在运营团队负责管理这些集中控制的蓝图定义的分配时，架构师或调控团队通常负责管理蓝图定义的生命周期管理。

**蓝图运算符**内置的基于角色的访问控制（RBAC）专用于在此类方案中使用。 角色允许操作类型团队管理组织蓝图定义的分配，但不允许对其进行修改。 执行此操作需要在 Azure 环境中进行一些配置，本文介绍必要的步骤。

## <a name="grant-permission-to-the-blueprint-operator"></a>向蓝图运算符授予权限

第一步是向要分配蓝图的帐户或安全组（推荐）授予**蓝图操作员**角色。 应在管理组层次结构中的最高级别完成此操作，该管理组层次结构包含操作团队应对其进行蓝图分配访问的所有管理组和订阅。 建议在授予这些权限时遵循最低权限原则。

1. 您[创建安全组并添加成员](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. 向帐户或安全组添加**蓝图操作员**[角色分配](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment)

## <a name="user-assign-managed-identity"></a>用户-分配托管标识

蓝图定义可以使用系统分配的或用户分配的托管标识。 但是，在使用**蓝图运算符**角色时，需要将蓝图定义配置为使用用户分配的托管标识。 此外，要向其授予**蓝图操作员**角色的帐户或安全组需要向用户分配的托管标识授予**托管标识操作员**角色。 如果没有此权限，则蓝图分配由于缺少权限而失败。

1. [创建用户分配的托管标识](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)，供分配的蓝图使用

1. 向帐户或安全组添加**托管标识操作员**的[角色分配](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment)。 将角色分配的范围限定为新的用户分配的托管标识。

1. 作为**蓝图运算符**，[分配一个](../create-blueprint-portal.md#assign-a-blueprint)使用新的用户分配的托管标识的蓝图。

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。