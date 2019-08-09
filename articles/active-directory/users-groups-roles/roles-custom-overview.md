---
title: 预览具有自定义权限的 Azure 管理员角色-Azure Active Directory |Microsoft Docs
description: 预览用于委派标识管理的自定义 Azure AD 角色。 管理 Azure 门户、PowerShell 或图形 API 中的 Azure 角色。
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: eabf29b10814d19e89c21f27ec66fce5355c9bfb
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880696"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Azure Active Directory 中的自定义管理员角色 (预览)

本文介绍如何了解 Azure Active Directory (Azure AD) 中的新的基于自定义角色的访问控制 (RBAC) 和资源范围。 自定义 RBAC 角色显示[内置角色](directory-assign-admin-roles.md)的基础权限, 因此可以创建和组织自己的自定义角色。 此方法允许你在需要时以更精细的方式授予访问权限。 此自定义 RBAC 角色的第一版包含创建角色的功能, 用于分配管理应用注册的权限。 随着时间的推移, 将添加组织资源 (如企业应用程序、用户和设备) 的其他权限。  

此外, 自定义 RBAC 角色还支持每个资源的分配, 还支持更传统的组织范围内的分配。 此方法使你能够授予访问权限, 以管理某些资源 (例如, 一个应用注册), 而无需授予对所有资源 (所有应用注册) 的访问权限。

Azure AD 基于角色的访问控制是 Azure AD 的公共预览功能, 适用于任何付费的 Azure AD 许可计划。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="understand-azure-ad-role-based-access-control"></a>了解 Azure AD 基于角色的访问控制

使用自定义 RBAC 角色授予权限的过程分为两个步骤: 创建自定义角色定义, 然后使用角色分配进行分配。 自定义角色定义是从预设列表添加的权限的集合。 这些权限与内置角色中使用的权限相同。  

创建角色定义后, 可以通过创建角色分配将其分配给某人。 角色分配授予某人特定范围内角色定义中的权限。 此两步过程允许你创建一个角色定义, 并在不同的范围内将其分配多次。 作用域定义角色成员有权访问的资源集。 最常见的作用域是组织范围 (组织范围) 范围。 可以在组织范围的范围内分配自定义角色, 这意味着角色成员对组织中的所有资源具有角色权限。 还可以在对象范围内分配自定义角色。 对象范围的一个示例就是单个应用程序。 这样一来, 就可以在组织中的所有应用程序上将同一角色分配给 Sally, 然后只 Naveen Contoso Expense 报表应用程序。  

Azure AD RBAC 在类似于[Azure 基于角色的访问控制](../../role-based-access-control/overview.md)的概念上操作。 不同之处在于, Azure RBAC 控制对 Azure 资源 (例如虚拟机和网站) 的访问, Azure AD RBAC 控制对 Azure AD 的访问。 这两个系统都利用了角色定义和角色分配的概念。

### <a name="role-assignments"></a>角色分配

角色分配是将角色定义附加到特定范围内的用户的过程, 目的是为了授予访问权限。 通过创建角色分配来授予访问权限，通过删除角色分配来撤销访问权限。 角色分配由三个元素组成:
- 用户
- 角色定义
- 资源范围

你可以使用 Azure 门户、Azure AD PowerShell 或图形 API[创建角色分配](roles-create-custom.md)。 你还可以[查看自定义角色的分配](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)。

下图显示了角色分配的示例。 在此示例中, 已在 Contoso 小组件生成器应用注册范围内为丽丽绿色分配了应用注册管理员自定义角色。 此分配仅向 Chris 授予此特定应用注册上的应用注册管理员角色的权限。

![角色分配是指如何强制实施权限, 并且有三个部分](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>安全主体

安全主体表示要分配对 Azure AD 资源的访问权限的用户。 *用户*是在 Azure Active Directory 中具有用户配置文件的个人。

### <a name="role"></a>Role

角色定义 (或角色) 是权限的集合。 角色定义列出了可以对 Azure AD 资源执行的操作, 例如创建、读取、更新和删除。 Azure AD 中有两种类型的角色:

- Microsoft 创建的无法更改的内置角色。 全局管理员内置角色具有所有 Azure AD 资源的所有权限。
- 由你的组织创建和管理的自定义角色。

### <a name="scope"></a>范围

作用域是指作为角色分配的一部分, 特定 Azure AD 资源的允许操作限制。 分配角色时, 可以指定限制管理员对特定资源的访问权限的范围。 例如, 如果你想要向开发人员授予自定义角色, 但仅要管理特定的应用程序注册, 可以在角色分配中包括特定的应用程序注册作为范围。

  > [!Note]
  > 可以在目录范围和资源范围内分配自定义角色。 它们还不能在管理单元范围内进行分配。
  > 可以在目录范围内分配内置角色, 也可以在某些情况下分配管理单元范围。 它们尚未在对象范围内分配。

## <a name="required-license-plan"></a>所需的许可计划

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>后续步骤

- 使用[Azure 门户、Azure AD PowerShell 和图形 API](roles-create-custom.md)创建自定义角色分配
- [查看自定义角色的分配](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)
