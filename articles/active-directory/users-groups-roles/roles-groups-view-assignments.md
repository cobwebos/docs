---
title: 在 Azure Active Directory 中查看分配给组的角色 |Microsoft Docs
description: 预览用于委托标识管理的自定义 Azure AD 角色。 在 Azure 门户、PowerShell 或图形 API 中管理 Azure 角色。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b7613fb70299a70e4389b97c2647a26cb7c3374
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475980"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>在 Azure Active Directory 中查看分配给组的角色

本部分介绍如何使用 Azure AD 管理中心来查看分配给组的角色。 查看组和分配的角色是默认的用户权限。

1. 用任何非管理员或管理员凭据登录到[Azure AD 管理中心](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。

1. 选择你感兴趣的组。

1. 选择 "**分配的角色**"。 你现在可以查看分配给此组的所有 Azure AD 角色。

   ![查看分配给所选组的所有角色](./media/roles-groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>使用 PowerShell

### <a name="get-object-id-of-the-group"></a>获取组的对象 ID

```powershell
Get-AzureADMSGroup -SearchString “Contoso_Helpdesk_Administrators”
```

### <a name="view-role-assignment-to-a-group"></a>查看组的角色分配

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>使用 Microsoft Graph API

### <a name="get-object-id-of-the-group"></a>获取组的对象 ID

```powershell
GET https://graph.microsoft.com/beta/groups?$filter displayName eq ‘Contoso_Helpdesk_Administrator’ 
```

### <a name="get-role-assignments-to-a-group"></a>获取组的角色分配

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>后续步骤

- [使用云组来管理角色分配](roles-groups-concept.md)
- [分配给云组的角色疑难解答](roles-groups-faq-troubleshooting.md)
