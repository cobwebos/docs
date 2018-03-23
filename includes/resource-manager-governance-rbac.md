---
title: include 文件
description: include 文件
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 24863e239620f84a57c631b3ecf5b08997de31c5
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
你希望确保你的组织中的用户对这些资源具有合适级别的访问权限。 你不希望向用户授予不受限的访问权限，但还需要确保他们可以执行其工作。 使用基于角色的访问控制 (RBAC)，你可以管理哪些用户有权在某个范围内完成特定操作。 一个角色定义一组允许的操作。 将角色分配至某一范围，并指定哪些用户在此范围内属于该角色。

规划访问控制策略时，请授予用户完成工作所需的最低权限。 下图显示了分配 RBAC 的建议模式。

![范围](./media/resource-manager-governance-rbac/role-examples.png)

有三个适用于所有资源的角色 - “所有者”、“参与者”和“读取者”。 分配到“所有者”角色的所有帐户都应是严格控制且很少使用的。 只需查看解决方案状态的用户应被授予“读取者”角色。

在订阅级别或资源组级别，大多数用户都被授予[特定于资源的角色](../articles/active-directory/role-based-access-built-in-roles.md)或[自定义角色](../articles/active-directory/role-based-access-control-custom-roles.md)。 这些角色严格定义了允许的操作。 通过将用户分配到这些角色，可以为用户提供必要的访问权限，同时不会让他们得到过多的控制权限。 可以向一个帐户分配多个角色，此用户就能获取各个角色综合的权限。 在资源级别授予访问权限对用户而言通常过于受限，但可能适用于为特定任务设计的自动化流程。

### <a name="who-can-assign-roles"></a>谁能分配角色

若要创建和删除角色分配，用户必须具有 `Microsoft.Authorization/roleAssignments/*` 访问权限。 此访问权限是通过“所有者”或“用户访问”管理员角色授权的。