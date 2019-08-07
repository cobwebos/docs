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
ms.openlocfilehash: 82638e3e102f7b8e39cd797960a11f3193132bc1
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779396"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Azure Active Directory 中的自定义管理员角色 (预览)

本文介绍如何了解 Azure Active Directory (Azure AD) 中新的自定义 RBAC (基于角色的访问控制) 和资源范围。 自定义 RBAC 角色显示[内置角色](directory-assign-admin-roles.md)的基础权限, 因此可以创建和组织自己的自定义角色。 资源范围提供了一种方法, 用于将自定义角色分配给管理某些资源 (例如一个应用程序), 而无需授予对所有资源 (所有应用程序) 的访问权限。

使用自定义 RBAC 角色授予权限的过程分为两个步骤。 首先, 创建一个自定义角色定义, 并从 "预设" 列表向其添加权限。 这些是在内置角色中使用的相同权限。 创建角色后, 可以通过创建角色分配将其分配给某人。 此两步过程允许你创建一个角色并在不同的范围中多次分配该角色。 可以在目录范围内分配自定义角色, 也可以在对象范围内对其进行分配。 对象范围的一个示例就是单个应用程序。 这样一来, 就可以将相同的角色分配给 Sally 中的所有应用程序, 然后只 Naveen Contoso Expense 报表应用程序。

此自定义 RBAC 角色的第一版包含创建角色的功能, 用于分配管理应用注册的权限。 随着时间的推移, 将添加组织资源 (如企业应用程序、用户和设备) 的其他权限。

预览功能:

- 用于创建和管理自定义角色并将其分配给组织范围内的用户的门户 UI 更新
- 带有新 cmdlet 的预览 PowerShell 模块:
  - 创建和管理自定义角色
  - 将自定义角色分配到组织范围或每应用注册范围
  - 在组织范围内分配内置角色 (与 GA cmdlet 的奇偶校验)
  - Azure AD 图形 API 支持

Azure AD 基于角色的访问控制是 Azure AD 的公共预览功能, 适用于任何付费的 Azure AD 许可计划。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="understand-azure-ad-role-based-access-control"></a>了解 Azure AD 基于角色的访问控制

Azure AD 基于角色的访问控制允许您将自定义的角色分配给只允许对单个 Azure AD 资源类型允许的操作。 基于角色的访问 Azure AD 在类似于 Azure 基于角色的访问控制 (适用于 azure 资源访问的[AZURE RBAC](../../role-based-access-control/overview.md) , 但 Azure AD 基于角色的访问 Microsoft Graph 控制基于 azure 资源管理器的概念上运作。 但是, 这两个系统将其功能基于角色分配。

### <a name="role-assignments"></a>角色分配

使用 Azure AD 基于角色的访问控制来控制访问的方式是创建**角色分配**, 用于强制执行权限。 角色分配由三个元素组成:

- 安全主体
- 角色定义
- 资源范围

通过创建角色分配来授予访问权限，通过删除角色分配来撤销访问权限。 你可以使用 Azure 门户、Azure AD PowerShell 和图形 API[创建角色分配](roles-create-custom.md)。 您可以单独[查看自定义角色的分配](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)。

下图显示了角色分配的示例。 在此示例中, 已向 Chris 绿色分配了 SalesForce 应用程序范围内的 "[应用程序管理员](directory-assign-admin-roles.md#application-administrator)" 角色。 丽丽无权管理任何其他应用程序, 除非它们属于不同的角色分配。

![角色分配是指如何强制实施权限, 并且有三个部分](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>安全主体

安全主体表示要分配 Azure AD 资源访问权限的用户或服务主体。 *用户*是在 Azure Active Directory 中具有用户配置文件的个人。 *服务主体*是应用程序或服务用来访问特定 Azure AD 资源的安全标识。

安全主体与用户标识类似, 因为它表示用户名、密码或证书, 但对于应用程序或服务而不是用户。

### <a name="role"></a>Role

角色定义 (或角色) 是权限的集合。 角色定义列出了可以对 Azure AD 资源执行的操作, 例如创建、读取、更新和删除。 Azure AD 中有两种类型的角色:

- Microsoft 创建的无法更改的内置角色。 全局管理员内置角色具有所有 Azure AD 资源的所有权限。
- 由你的组织创建和管理的自定义角色。

### <a name="scope"></a>范围

作用域是特定 Azure AD 资源的允许操作的限制。 分配角色时, 可以指定将管理员允许的操作限制为特定资源的作用域。 例如, 如果你希望向开发人员授予自定义角色, 但仅用于管理特定的应用程序注册, 则可以将特定的应用程序注册包含为角色分配中的作用域。

  > [!Note]
  > 可以在目录范围和资源范围内分配自定义角色。 它们还不能在管理单元范围内进行分配。
  > 可以在目录范围内分配内置角色, 也可以在某些情况下分配管理单元范围。 它们尚未在对象范围内分配。

## <a name="required-license-plan"></a>所需的许可计划

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>后续步骤

- 使用[Azure 门户、Azure AD PowerShell 和图形 API](roles-create-custom.md)创建自定义角色分配
- [查看自定义角色的分配](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)
