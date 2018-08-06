---
title: Azure 中基于角色的访问控制 (RBAC) 是什么？ | Microsoft Docs
description: 获取 Azure 中基于角色的访问控制 (RBAC) 的概述。 使用角色分配可以控制对 Azure 中的资源的访问。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: role-based-access-control
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a2e0bf35f73a355197f821f7cce12294f7b35576
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344743"
---
# <a name="what-is-role-based-access-control-rbac"></a>什么是基于角色的访问控制 (RBAC)？

对于任何使用云的组织而言，云资源的访问权限管理都是一项重要功能。 基于角色的访问控制 (RBAC) 可帮助你管理谁有权访问 Azure 资源、他们可以对这些资源执行哪些操作以及他们有权访问哪些区域。

RBAC 是在 [Azure 资源管理器](../azure-resource-manager/resource-group-overview.md)基础上构建的授权系统，针对 Azure 中的资源提供精细的访问权限管理。

## <a name="what-can-i-do-with-rbac"></a>RBAC 的作用是什么？

下面是 RBAC 的作用的一些示例：

- 允许一个用户管理订阅中的虚拟机，并允许另一个用户管理虚拟网络
- 允许 DBA 组管理订阅中的 SQL 数据库
- 允许某个用户管理资源组中的所有资源，例如虚拟机、网站和子网
- 允许某个应用程序访问资源组中的所有资源

## <a name="best-practice-for-using-rbac"></a>使用 RBAC 的最佳做法

使用 RBAC，可以在团队中实现职责分离，仅向用户授予执行作业所需的访问权限。 无需向每个人授予 Azure 订阅或资源的无限制权限，可以仅允许在特定的范围执行某些操作。

规划访问控制策略时，最佳做法是授予用户完成工作所需的最低权限。 下图显示了与 RBAC 使用有关的建议模式。

![RBAC 和最小特权](./media/overview/rbac-least-privilege.png)

## <a name="how-rbac-works"></a>RBAC 的工作原理

使用 RBAC 控制资源访问权限的方式是创建角色分配。 这是一个需要理解的重要概念 — 它涉及到如何强制实施权限。 角色分配包含三个要素：安全主体、角色订阅和范围。

### <a name="security-principal"></a>安全主体

安全主体是一个对象，表示请求访问 Azure 资源的用户、组或服务主体。

![角色分配的安全主体](./media/overview/rbac-security-principal.png)

- 用户 - 在 Azure Active Directory 中具有配置文件的人员。 也可以将角色分配到其他租户中的用户。 有关其他组织中的用户的信息，请参阅 [Azure Active Directory B2B](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)。
- 组 - 在 Azure Active Directory 中创建的一组用户。 将某个角色分配到某个组时，该组中的所有用户都拥有该角色。 
- 服务主体 - 应用程序或服务用来访问特定 Azure 资源的安全标识。 可将服务主体视为应用程序的用户标识（用户名和密码或证书）。

### <a name="role-definition"></a>角色定义

角色定义是权限的集合。 它有时简称为“角色”。 角色定义列出可以执行的操作，例如读取、写入和删除。 角色可以是高级别的（例如所有者），也可以是特定的（例如虚拟机读取者）。

![角色分配的角色定义](./media/overview/rbac-role-definition.png)

Azure 包含多个可用的[内置角色](built-in-roles.md)。 下面列出了四个基本的内置角色。 前三个角色适用于所有资源类型。

- [所有者](built-in-roles.md#owner) - 拥有对所有资源的完全访问权限，包括将访问权限委派给其他用户的权限。
- [参与者](built-in-roles.md#contributor) - 可以创建和管理所有类型的 Azure 资源，但无法将访问权限授予其他用户。
- [读取者](built-in-roles.md#reader) - 可以查看现有的 Azure 资源。
- [用户访问管理员](built-in-roles.md#user-access-administrator) - 允许你管理用户对 Azure 资源的访问。

剩余的内置角色允许管理特定的 Azure 资源。 例如，[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色允许用户创建和管理虚拟机。 如果内置角色不能满足组织的特定需求，则你可以创建自己的[自定义角色](custom-roles.md)。

Azure 引入了数据操作（目前以预览版提供），用于授予对对象中数据的访问权限。 例如，如果某个用户对某个存储帐户拥有读取数据的访问权限，则该用户可以读取该存储帐户中的 Blob 或消息。 有关详细信息，请参阅[了解角色定义](role-definitions.md)。

### <a name="scope"></a>范围

范围是访问权限适用的边界。 分配角色时，可以通过定义范围来进一步限制允许的操作。 如果你想要将某人分配为[网站参与者](built-in-roles.md#website-contributor)，但只针对一个资源组执行此分配，则使用范围就很有帮助。

在 Azure 中，可在多个级别指定范围：订阅、资源组或资源。 范围以父子关系构建，其中的每个子级只有一个父级。

![角色分配的范围](./media/overview/rbac-scope.png)

在父范围分配的访问权限将继承到子范围。 例如：

- 如果在订阅范围向某个组分配了[读取者](built-in-roles.md#reader)角色，则该组的成员可以查看订阅中的每个资源组和资源。
- 如果在资源组范围向某个应用程序分配了[参与者](built-in-roles.md#contributor)角色，则该应用程序可以管理该资源组中所有类型的资源，但不能管理订阅中的其他资源组资源。

Azure 还包含一个高于订阅的、称作[管理组](../azure-resource-manager/management-groups-overview.md)的范围（目前以预览版提供）。 管理组是管理多个订阅的一种方式。 指定 RBAC 的范围时，可以指定管理组，或者指定订阅、资源组或资源层次结构。

### <a name="role-assignment"></a>角色分配

角色分配是出于授予访问权限的目的，在特定的范围将角色定义绑定到用户、组或服务主体的过程。 通过创建角色分配来授予访问权限，通过删除角色分配来撤销访问权限。

下图显示了角色分配的示例。 在此示例中，为“营销”组分配了医药销售资源组的[参与者](built-in-roles.md#contributor)角色。 这意味着，“营销”组中的用户可以在医药销售资源组中创建或管理任何 Azure 资源。 “营销”用户无权访问医药销售资源组外部的资源，除非他们属于另一个角色分配。

![用于控制访问权限的角色分配](./media/overview/rbac-overview.png)

可以使用 Azure 门户、Azure CLI、Azure PowerShell、Azure SDK 或 REST API 创建角色分配。 每个订阅中最多可以包含 2000 个角色分配。 若要创建和删除角色分配，必须拥有 `Microsoft.Authorization/roleAssignments/*` 权限。 此权限是通过[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator)角色授予的。

## <a name="next-steps"></a>后续步骤

- [快速入门：使用 RBAC 和 Azure 门户授予用户的访问权限](quickstart-assign-role-user-portal.md)
- [使用 RBAC 和 Azure 门户管理访问权限](role-assignments-portal.md)
- [了解 Azure 中的不同角色](rbac-and-directory-admin-roles.md)
