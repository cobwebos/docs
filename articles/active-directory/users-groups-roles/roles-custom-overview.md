---
title: 预览具有可自定义权限的 Azure 管理员角色 - Azure Active Directory | Microsoft Docs
description: 预览用于委托标识管理权的自定义 Azure AD 角色。 在 Azure 门户、PowerShell 或图形 API 中管理 Azure 角色。
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 09/04/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c26e77fdf6e10e53f5acc0271986700c98fa690d
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772549"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Azure Active Directory 中的自定义管理员角色（预览）

本文介绍如何使用基于角色的访问控制和资源范围在 Azure Active Directory （Azure AD）中了解 Azure AD 自定义角色。 自定义 Azure AD 角色表示[内置角色](directory-assign-admin-roles.md)的基础权限，因此可以创建和组织自己的自定义角色。 此方法允许你在需要时，以比内置角色更精细的方式授予访问权限。 Azure AD 自定义角色的第一个版本包括创建角色以分配管理应用注册的权限的能力。 随着时间的推移，我们将为企业应用程序、用户和设备等组织资源添加更多的权限。  

此外，Azure AD 自定义角色还支持按资源进行分配，还支持更传统的组织范围内的分配。 此方法可让你授予管理某些资源（例如一个应用注册）的访问权限，而无需授予对所有资源（所有应用注册）的访问权限。

Azure AD 基于角色的访问控制是 Azure AD 的一项公共预览版功能，可通过任何付费 Azure AD 许可计划获得。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="understand-azure-ad-role-based-access-control"></a>了解 Azure AD 基于角色的访问控制

使用自定义 Azure AD 角色授予权限的过程分为两个步骤：创建自定义角色定义，然后使用角色分配来分配。 自定义角色定义是从预设列表添加的权限集合。 这些权限与内置角色中使用的权限相同。  

创建角色定义后，可以通过创建角色分配将其分配给用户。 角色分配向用户授予指定范围内角色定义中的权限。 使用此两步过程可以创建单个角色定义，并在不同的范围内将其分配多次。 作用域定义角色成员有权访问的一组 Azure AD 资源。 最常见的范围是组织范围。 可以在组织范围分配自定义角色，这意味着，该角色成员对该组织中的所有资源拥有角色权限。 还可以在对象范围分配自定义角色。 对象范围的示例是单个应用程序。 对于组织中的所有应用程序，可以将同一角色分配给一个用户，然后将该角色分配给另一个用户，该用户的作用域只包含 Contoso 费用报表应用程序。  

Azure AD 的内置角色和自定义角色操作类似于[Azure 基于角色的访问控制](../../role-based-access-control/overview.md)。 [这两个基于角色的访问控制系统之间的区别](../../role-based-access-control/rbac-and-directory-admin-roles.md)在于，azure RBAC 使用 Azure 资源管理来控制对 Azure 资源（例如虚拟机或存储）的访问，并 Azure AD 自定义角色控制对 Azure AD 资源的访问使用图形 API。 这两个系统都利用角色定义和角色分配的概念。

### <a name="role-assignments"></a>角色分配

角色分配是将角色定义附加到特定范围内的用户以授予 Azure AD 资源访问权限的对象。 通过创建角色分配来授予访问权限，通过删除角色分配来撤销访问权限。 角色分配的核心包括三个元素：

- 用户（Azure Active Directory 中具有用户配置文件的个人）
- 角色定义
- 资源范围

可以使用 Azure 门户、Azure AD PowerShell 或图形 API [创建角色分配](roles-create-custom.md)。 还可以[查看自定义角色的分配](roles-view-assignments.md#view-the-assignments-of-a-role)。

下图显示了角色分配的示例。 在此示例中，在 Contoso Widget Builder 应用注册范围为 Chris Green 分配了“应用注册管理员”自定义角色。 分配仅向 Chris 授予此特定应用注册的应用注册管理员角色权限。

![角色分配定义了权限的强制实施方式，由三个部分组成](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>安全主体

安全主体表示有权访问 Azure AD 资源的用户。 “用户”是在 Azure Active Directory 中具有用户配置文件的个人。

### <a name="role"></a>Role

角色定义（或角色）是权限的集合。 角色定义列出可对 Azure AD 资源执行的操作，例如读取、写入、更新和删除。 Azure AD 中有两种类型的角色：

- Microsoft 创建的内置角色（不可更改）。
- 由组织创建和管理的自定义角色。

### <a name="scope"></a>范围

范围是指允许对角色分配中的特定 Azure AD 资源执行的操作的限制。 分配角色时，可以指定一个范围来限制管理员对特定资源的访问。 例如，若要为开发人员授予某个自定义角色，但仅允许该开发人员管理特定的应用程序注册，则你可以在角色分配中包含特定的应用程序注册作为范围。

  > [!Note]
  > 可以在目录范围和资源范围分配自定义角色。 目前无法在“管理单元”范围分配自定义角色。
  > 在某些情况下，可以在目录范围内分配内置角色，也可以在管理单元范围内进行分配。 它们不能在 Azure AD 资源范围内进行分配。

## <a name="required-license-plan"></a>所需的许可计划

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>后续步骤

- 使用 [Azure 门户、Azure AD PowerShell 和图形 API](roles-create-custom.md) 创建自定义角色分配
- [查看自定义角色的分配](roles-view-assignments.md#view-assignments-of-single-application-scope)
