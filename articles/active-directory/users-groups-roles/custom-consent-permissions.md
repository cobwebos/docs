---
title: Azure Active Directory 中自定义角色的应用同意权限 | Microsoft Docs
description: 在 Azure 门户、PowerShell 或 Graph API 中预览自定义 Azure AD 角色的应用同意权限。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: overview
ms.date: 10/06/2020
ms.author: curtand
ms.reviewer: psignoret
ms.custom: it-pro
ms.openlocfilehash: dbaf49d6be547137e4b082e2fc2a9c65d734907b
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804413"
---
# <a name="app-consent-permissions-for-custom-roles-in-azure-active-directory"></a>Azure Active Directory 中自定义角色的应用同意权限

本文包含当前可用于 Azure Active Directory (Azure AD) 中的自定义角色定义的应用同意权限。 本文介绍与应用同意和权限相关的一些常见方案所需的权限。

## <a name="required-license-plan"></a>所需许可证计划

使用此功能需要 Azure AD 组织的 Azure AD Premium P1 许可证。 若要根据需要查找合适的许可证，请参阅[比较免费版、基本版和高级版的正式发布功能](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="app-consent-permissions"></a>应用同意权限

使用本文中列出的权限来管理应用同意策略，并使用向应用授予同意的权限。

> [!NOTE]
> Azure AD 管理门户尚不支持将本文中列出的权限添加到自定义目录角色定义中。 你必须[使用 Azure AD PowerShell 来创建具有本文所列权限的自定义目录角色](roles-create-custom.md#create-a-role-using-powershell)。

### <a name="granting-delegated-permissions-to-apps-on-behalf-of-self-user-consent"></a>代表自己（用户同意）向应用授予委托的权限

根据应用同意策略，允许用户代表自己（用户同意）向应用程序授予同意。

- microsoft.directory/servicePrincipals/managePermissionGrantsForSelf.{id}

其中，`{id}` 替换为[应用同意策略](../manage-apps/manage-app-consent-policies.md)的 ID，该策略将设置激活此权限所需满足的条件。

例如，若要根据 ID 为 `microsoft-user-default-low` 的内置应用同意策略，允许用户代表自己授予同意，应使用权限 `...managePermissionGrantsForSelf.microsoft-user-default-low`。

### <a name="granting-permissions-to-apps-on-behalf-of-all-admin-consent"></a>代表所有人（管理员同意）向应用授予权限

将租户范围内的管理员同意委托给应用，以同时获得委托的权限和应用程序权限（应用角色）：

- microsoft.directory/servicePrincipals/managePermissionGrantsForAll.{id}

其中，`{id}` 替换为[应用同意策略](../manage-apps/manage-app-consent-policies.md)的 ID，该策略将设置激活此权限所需满足的条件。

例如，若要根据 ID 为 `low-risk-any-app` 的自定义[应用同意策略](../manage-apps/manage-app-consent-policies.md)，允许角色被分派人向应用授予租户范围内的管理员同意，应使用权限 `microsoft.directory/servicePrincipals/managePermissionGrantsForAll.low-risk-any-app`。

### <a name="managing-app-consent-policies"></a>管理应用同意策略

委托[应用同意策略](../manage-apps/manage-app-consent-policies.md)的创建、更新和删除。

- microsoft.directory/permissionGrantPolicies/create
- microsoft.directory/permissionGrantPolicies/standard/read
- microsoft.directory/permissionGrantPolicies/basic/update
- microsoft.directory/permissionGrantPolicies/delete

## <a name="full-list-of-permissions"></a>权限的完整列表

权限 | 说明
---------- | -----------
microsoft.directory/servicePrincipals/managePermissionGrantsForSelf.{id} | 根据应用同意策略 `{id}`，授予代表自己同意（用户同意）应用的能力。
microsoft.directory/servicePrincipals/managePermissionGrantsForAll.{id} | 根据应用同意策略 `{id}`，授予代表所有人同意（租户范围内的管理员同意）应用的权限。
microsoft.directory/permissionGrantPolicies/standard/read | 授予阅读应用同意策略的能力。
microsoft.directory/permissionGrantPolicies/basic/update | 授予更新现有应用同意策略的基本属性的能力。
microsoft.directory/permissionGrantPolicies/create | 授予创建应用同意策略的能力。
microsoft.directory/permissionGrantPolicies/delete | 授予删除应用同意策略的能力。

## <a name="next-steps"></a>后续步骤

- [使用 Azure 门户、Azure AD PowerShell 或图形 API 创建自定义角色](roles-create-custom.md)
- [查看自定义角色的分配](roles-view-assignments.md)
