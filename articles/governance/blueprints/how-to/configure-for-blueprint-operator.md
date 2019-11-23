---
title: Setup your environment for Blueprint Operator
description: Learn how to configure your Azure environment for use with the Blueprint Operator built-in role-based access control (RBAC) role.
ms.date: 08/26/2019
ms.topic: conceptual
ms.openlocfilehash: 671ac3aaf42bddb3e775baec6838e4c271c4e855
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406378"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>为蓝图操作员配置环境

The management of your blueprint definitions and blueprint assignments can be assigned to different teams. It's common for an architect or governance team to be responsible for the lifecycle management of your blueprint definitions while an operations team is responsible for managing assignments of those centrally controlled blueprint definitions.

The **Blueprint Operator** built-in role-based access control (RBAC) is designed specifically for use in this type of scenario. The role allows for operations type teams to manage the assignment of the organizations blueprint definitions, but not the ability to modify them. Doing so requires some configuration in your Azure environment and this article explains the necessary steps.

## <a name="grant-permission-to-the-blueprint-operator"></a>Grant permission to the Blueprint Operator

The first step is to grant the **Blueprint Operator** role to the account or security group (recommended) that is going to be assigning blueprints. This action should be done at the highest level in the management group hierarchy that encompasses all of the management groups and subscriptions the operations team should have blueprint assignment access to. It's recommended to follow the principle of least privilege when granting these permissions.

1. (Recommended) [Create a security group and add members](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. [Add a role assignment](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) of **Blueprint Operator** to the account or security group

## <a name="user-assign-managed-identity"></a>User-assign managed identity

A blueprint definition can use either system-assigned or user-assigned managed identities. However, when using the **Blueprint Operator** role, the blueprint definition needs to be configured to use a user-assigned managed identity. Additionally, the account or security group being granted the **Blueprint Operator** role needs to be granted the **Managed Identity Operator** role on the user-assigned managed identity. Without this permission, blueprint assignments fail because of lack of permissions.

1. [Create a user-assigned managed identity](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) for use by an assigned blueprint

1. [Add a role assignment](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) of **Managed Identity Operator** to the account or security group. Scope the role assignment to the new user-assigned managed identity.

1. As the **Blueprint Operator**, [assign a blueprint](../create-blueprint-portal.md#assign-a-blueprint) that uses the new user-assigned managed identity.

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。