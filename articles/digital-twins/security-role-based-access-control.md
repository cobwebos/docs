---
title: 了解基于角色的访问控制-Azure 数字孪生 |Microsoft Docs
description: 了解基于角色的访问控制和管理 Azure 数字孪生中的权限。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 1d4243457f51f11db5bc9681b29d27293fbc6250
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949024"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Azure 数字孪生中基于角色的访问控制

Azure 数字孪生支持对空间图中特定数据、资源和操作进行精确的访问控制。 它通过精细的角色和权限管理（称为基于角色的访问控制 (RBAC)）实现。 RBAC 包括“角色”和“角色分配”。 角色标识权限的级别。 角色分配将角色与用户或设备相关联。

使用 RBAC 可将权限授予：

- 用户。
- 设备。
- 服务主体。
- 用户定义的函数。
- 属于某个域的所有用户。
- 租户。

还可以微调访问权限的程度。

RBAC 具有唯一性，原因在于权限沿着空间图继承。

## <a name="what-can-i-do-with-rbac"></a>RBAC 的作用是什么？

开发人员可以使用 RBAC 来实现以下目的：

- 授予用户管理权限，使其能够管理整个建筑内或仅特定房间或楼层内的设备。
- 授予管理员全局访问权限，使其能够访问整个图或某部分图的所有空间图节点。
- 向支持专家授予图的读取访问权限，但访问密钥除外。
- 向域的每个成员授予所有图对象的读取访问权限。

## <a name="rbac-best-practices"></a>RBAC 最佳做法

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>角色

### <a name="role-definitions"></a>角色定义

角色定义是组成权限和构成角色的其他属性的集合。 角色定义列出了允许的操作，包括具有该角色的任何对象可执行的创建、读取、更新和删除操作。 它还指定权限应用到的对象类型。

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> 若要检索上述角色的完整定义，请查询系统/角色 API。
> 阅读[创建和管理角色分配](./security-create-manage-role-assignments.md#retrieve-all-roles)了解详细信息。

### <a name="object-identifier-types"></a>对象标识符类型

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> 阅读[创建和管理角色分配](./security-create-manage-role-assignments.md#grant-permissions-to-your-service-principal)了解如何向服务主体授予权限。

以下参考文档介绍了：

- 如何[查询用户的对象 ID](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)。
- 如何[获取服务主体的对象 ID](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)。
- 如何[检索 Azure AD 租户的对象 ID](../active-directory/develop/quickstart-create-new-tenant.md)。

## <a name="role-assignments"></a>角色分配

Azure 数字孪生角色分配将某个对象（例如用户或 Azure AD 租户）与角色和空间相关联。 向属于该空间的所有对象授予权限。 空间包括其下的整个空间图。

例如，向某用户分配某空间图（代表建筑）的根节点的 `DeviceInstaller` 角色。 然后，该用户可以读取和更新该节点的设备，以及读取和更新建筑中所有其他子空间的设备。

若要向接收方授予权限，请创建角色分配。 若要撤消权限，请删除角色分配。

>[!IMPORTANT]
> 阅读[创建和管理角色分配](./security-create-manage-role-assignments.md)了解关于角色分配的详细信息。

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何创建和管理 Azure 数字孪生角色分配，请阅读[创建和管理角色分配](./security-create-manage-role-assignments.md)。