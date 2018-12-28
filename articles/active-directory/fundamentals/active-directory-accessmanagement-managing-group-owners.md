---
title: 添加或删除组所有者 - Azure Active Directory | Microsoft Docs
description: 有关如何使用 Azure Active Directory 添加或删除组所有者的说明。
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.openlocfilehash: 8c70378993e0155cbe730a5a351848ca1a1daa0a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53087144"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>在 Azure Active Directory 中添加或删除组所有者
Azure Active Directory (Azure AD) 组由组所有者拥有和管理。 组所有者被分配为由资源所有者（管理员）管理组及其成员。 组所有者无需成为组成员。 在分配组所有者之后，仅资源所有者可以添加或删除所有者。

在某些情况下，你作为管理员可以决定不分配组所有者。 在这种情况下，你将成为组所有者。 此外，除非在组设置中进行了限制，否则所有者可以将其他所有者分配给自己的组。

## <a name="add-an-owner-to-a-group"></a>向组添加所有者
使用 Azure AD 向组添加其他组所有者。

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

- [使用组分配对集成式 SaaS 应用的访问权限](../users-groups-roles/groups-saasapps.md)

- [将本地标识与 Azure Active Directory 集成](../hybrid/whatis-hybrid-identity.md)

- [用于配置组设置的 Azure Active Directory cmdlet](../users-groups-roles/groups-settings-v2-cmdlets.md)
