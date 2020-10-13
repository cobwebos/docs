---
title: 添加角色分配的步骤-Azure RBAC
description: 了解使用 azure RBAC)  (将 Azure 角色分配给用户、组、服务主体或托管标识的步骤。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.openlocfilehash: badf10da8af0ed3829deb2498b51b5d5c8ce6a93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91604260"
---
# <a name="steps-to-add-a-role-assignment"></a>角色分配的添加步骤

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] 本文介绍了使用 [Azure 门户](role-assignments-portal.md)、 [Azure PowerShell](role-assignments-powershell.md)、 [Azure CLI](role-assignments-cli.md)或 [REST API](role-assignments-rest.md)添加角色分配的高级步骤。

## <a name="step-1-determine-who-needs-access"></a>步骤1：确定谁需要访问权限

首先需要确定谁需要访问权限。 可以将角色分配给用户、组、服务主体或托管标识。 这也称为“安全主体”。

![角色分配的安全主体](./media/shared/rbac-security-principal.png)

- 用户 - 在 Azure Active Directory 中具有配置文件的人员。 也可以将角色分配到其他租户中的用户。 有关其他组织中的用户的信息，请参阅 [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md)。
- 组 - 在 Azure Active Directory 中创建的一组用户。 将某个角色分配到某个组时，该组中的所有用户都拥有该角色。 
- 服务主体 - 应用程序或服务用来访问特定 Azure 资源的安全标识。 可将服务主体视为应用程序的用户标识（用户名和密码或证书）。
- 托管标识 - Azure Active Directory 中由 Azure 自动托管的标识。 在开发云应用程序时，通常使用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)来管理用于向 Azure 服务进行身份验证的凭据。

## <a name="step-2-find-the-appropriate-role"></a>步骤2：查找适当的角色

权限会组合到一个 *角色定义*中。 它通常直接称为“角色”。 可以从多个内置角色的列表中选择。 如果内置角色不能满足组织的特定需求，则可自行创建自定义角色。

![角色分配的角色定义](./media/shared/rbac-role-definition.png)

下面列出了四个基本的内置角色。 前三个角色适用于所有资源类型。

- [所有者](built-in-roles.md#owner) - 拥有对所有资源的完全访问权限，包括将访问权限委派给其他用户的权限。
- [参与者](built-in-roles.md#contributor) - 可以创建和管理所有类型的 Azure 资源，但无法将访问权限授予其他用户。
- [读取者](built-in-roles.md#reader) - 可以查看现有的 Azure 资源。
- [用户访问管理员](built-in-roles.md#user-access-administrator) - 可以管理用户对 Azure 资源的访问。

剩余的内置角色允许管理特定的 Azure 资源。 例如，[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色允许用户创建和管理虚拟机。

1. 请从综合性文章 [Azure 内置角色](built-in-roles.md)开始。 本文顶部的表是对本文后面的详细信息的索引。

1. 在此文章中，导航到要向其授予权限的资源的服务类别 (如计算、存储和数据库) 。 查找所需内容的最简单方法通常是在页面上搜索相关的关键字，如“blob”、“虚拟机”等。

1. 查看服务类别列出的角色，并确定所需的特定操作。 同样，始终以最严格的角色开始。

    例如，如果安全主体需要读取 Azure 存储帐户中的 blob，但不需要写入访问权限，请选择 " [存储 Blob 数据读取器](built-in-roles.md#storage-blob-data-reader) " 而不是 " [存储 blob 数据参与者](built-in-roles.md#storage-blob-data-contributor) " (并且一定不能) 管理员级别的 [存储 blob 数据所有者](built-in-roles.md#storage-blob-data-owner) 角色。 稍后，你可以根据需要随时更新角色分配。

1. 如果找不到合适的角色，则可以创建 [自定义角色](custom-roles.md)。

## <a name="step-3-identify-the-needed-scope"></a>步骤3：确定所需的范围

范围是访问权限适用于的资源集。 在 Azure 中，可以指定以下四个级别的作用域： [管理组](../governance/management-groups/overview.md)、订阅、 [资源组](../azure-resource-manager/management/overview.md#resource-groups)和资源。 范围采用父子关系结构。 层次结构的每个级别都使范围更具针对性。 可以在其中任何一个范围级别分配角色。 你选择的级别决定了角色的应用范围。 较低级别继承更高级别的角色权限。 

![角色分配的范围](./media/shared/rbac-scope.png)

在父作用域分配角色时，这些权限将继承到子作用域。 例如：

- 如果将 " [读取](built-in-roles.md#reader) 者" 角色分配给管理组范围内的用户，则该用户可以读取管理组中所有订阅中的所有内容。
- 如果将 [计费读取](built-in-roles.md#billing-reader) 者角色分配给订阅范围内的组，则该组的成员可以读取订阅中每个资源组和资源的计费数据。
- 如果在资源组范围向某个应用程序分配了[参与者](built-in-roles.md#contributor)角色，则该应用程序可以管理该资源组中所有类型的资源，但不能管理订阅中其他资源组的资源。

 有关详细信息，请参阅 [了解作用域](scope-overview.md)。

## <a name="step-4-check-your-prerequisites"></a>步骤 4. 检查先决条件

若要分配角色，你必须使用分配有角色分配的角色的用户（例如，你正在尝试分配角色的作用域中的 " [所有者](built-in-roles.md#owner) " 或 " [用户访问管理员](built-in-roles.md#user-access-administrator) "）登录。 同样，若要删除角色分配，你必须拥有角色分配 "删除" 权限。

- `Microsoft.Authorization/roleAssignments/write`
- `Microsoft.Authorization/roleAssignments/delete`

如果用户帐户无权在订阅内分配角色，则将显示错误消息“你的帐户无权执行操作 'Microsoft.Authorization/roleAssignments/write'”。在这种情况下，请与你的订阅管理员联系，因为他们可以代表你分配权限。

## <a name="step-5-add-role-assignment"></a>步骤 5。 添加角色分配

了解安全主体、角色和作用域后，可以分配角色。 可以使用 Azure 门户、Azure PowerShell、Azure CLI、Azure Sdk 或 REST Api 添加角色分配。 每个订阅中最多可以包含 **2000** 个角色分配。 此限制包括订阅、资源组和资源范围内的角色分配。 每个管理组中最多可以有 500 个角色分配。

有关如何添加角色分配的详细步骤，请参阅以下文章。

- [使用 Azure 门户添加或删除 Azure 角色分配](role-assignments-portal.md)
- [使用 Azure PowerShell 添加或删除 Azure 角色分配](role-assignments-powershell.md)
- [使用 Azure CLI 添加或删除 Azure 角色分配](role-assignments-cli.md)
- [使用 REST API 添加或删除 Azure 角色分配](role-assignments-rest.md)

## <a name="next-steps"></a>后续步骤

- [教程：使用 Azure 门户授予用户对 Azure 资源的访问权限](quickstart-assign-role-user-portal.md)
