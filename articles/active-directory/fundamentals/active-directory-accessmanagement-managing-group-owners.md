---
title: 添加或删除组所有者 - Azure Active Directory | Microsoft Docs
description: 有关如何使用 Azure Active Directory 添加或删除组所有者的说明。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ee4b452d087bfef0e9f5f7c820870da0df8dc3e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74683936"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>在 Azure Active Directory 中添加或删除组所有者
Azure Active Directory (Azure AD) 组由组所有者拥有和管理。 组所有者可以是用户或服务主体，并且能够管理包括成员身份的组。 只有现有组所有者或组管理管理员才能分配组所有者。 组所有者无需成为组成员。

如果组没有所有者，组管理管理员仍可以管理该组。

## <a name="add-an-owner-to-a-group"></a>向组添加所有者
下面是使用 Azure AD 门户将用户作为组添加到组的说明。 若要将服务主体添加为组的所有者，请按照使用[PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0)的说明进行操作。

### <a name="to-add-a-group-owner"></a>添加组所有者
1. 使用目录的全局管理员帐户登录到 [Azure 门户](https://portal.azure.com)。

2. 依次选择“Azure Active Directory”、“组”以及要添加所有者的组（例如此示例为“MDM 策略 - 西部”）。

3. 在“MDM 策略 - 西部概述”页面上，选择“所有者”。

    ![“MDM 策略 - 西部概述”页面，其中突出显示了“所有者”选项](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. 在“MDM 策略 - 西部 - 所有者”页面上，选择“添加所有者”，然后搜索并选择将成为新的组所有者的用户，然后选中“选择”。

    ![“MDM 策略 - 西部 - 所有者”页面，其中突出显示了“添加所有者”选项](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    选择新的所有者后，你可以刷新“所有者”页面并查看添加到所有者列表中的名字。

## <a name="remove-an-owner-from-a-group"></a>删除组所有者
使用 Azure AD 从组中删除所有者。

### <a name="to-remove-an-owner"></a>删除所有者
1. 使用目录的全局管理员帐户登录到 [Azure 门户](https://portal.azure.com)。

2. 依次选择“Azure Active Directory”、“组”以及要删除所有者的组（对于此示例为“MDM 策略 - 西部”）。

3. 在“MDM 策略 - 西部概述”页面上，选择“所有者”。

    ![“MDM 策略 - 西部概述”页面，其中突出显示了“所有者”选项](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. 在“MDM 策略 - 西部 - 所有者”页面上，选择要删除的身份为组所有者的用户，从用户的信息页面中选择“删除”，然后选择“是”以确认你的决定。

    ![用户的信息页面，其中突出显示了“删除”选项](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    删除所有者后，可以返回到“所有者”页面，看到该名称已从所有者列表中删除。

## <a name="next-steps"></a>后续步骤
- [使用 Azure Active Directory 组管理对资源的访问](active-directory-manage-groups.md)

- [用于配置组设置的 Azure Active Directory cmdlet](../users-groups-roles/groups-settings-cmdlets.md)

- [使用组分配集成式 SaaS 应用程序的访问权限](../users-groups-roles/groups-saasapps.md)

- [将本地标识与 Azure Active Directory 集成](../hybrid/whatis-hybrid-identity.md)

- [用于配置组设置的 Azure Active Directory cmdlet](../users-groups-roles/groups-settings-v2-cmdlets.md)
