---
title: 添加和删除管理单元-Azure Active Directory |Microsoft Docs
description: 在 Azure Active Directory 中使用管理单元来限制角色权限的范围。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1166d2ad17aea97a4dd7fdda53c42d6b3df75936
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450362"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>在 Azure Active Directory 中管理管理单元

若要在 Azure Active Directory (Azure AD) 中实现更精细的管理控制，可将用户分配到作用域限定为一个或多个管理单元 (AU) 的 Azure AD 角色。

## <a name="get-started"></a>入门

1. 若要通过 [图形资源管理器](https://aka.ms/ge)从以下说明运行查询，请执行以下操作：

    a. 在 Azure 门户中，转到 Azure AD。 在应用程序列表中，选择 " **图形资源管理器**"，然后选择 " **向图形资源管理器授予管理员许可**"。

    ![显示指向 "授予管理员许可" 的链接的屏幕截图](./media/roles-admin-units-manage/select-graph-explorer.png)


1. 使用 Azure AD PowerShell 的预览版本。

## <a name="add-an-administrative-unit"></a>添加管理单元

### <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 在 Azure 门户中转到 Azure AD，然后在左窗格中选择“管理单元”。

    ![Azure AD 中的管理 unitslink 的屏幕截图](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. 选择“添加”，然后输入管理单元的名称。 （可选）添加管理单元的说明。

    ![用于输入管理单元名称的“添加”按钮和文本框的屏幕截图](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. 选择“添加”以完成管理单元。

### <a name="use-powershell"></a>使用 PowerShell

在尝试运行以下命令之前，请安装 Azure AD PowerShell（预览版）：

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

可以根据需要修改用引号引起来的值。

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

```http
Http Request
POST /administrativeUnits
Request body
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>删除管理单元

在 Azure AD 中，可以删除不再需要作为管理角色的作用域的管理单元。

### <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 在 Azure 门户中，转到“Azure AD” > “管理单元”。 
1. 键入要删除的管理单元，然后选择“删除”。 
1. 若要确认是否要删除管理单元，请选择“是”。 此时会删除管理单元。

![管理单元的“删除”按钮和确认窗口的屏幕截图](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>使用 PowerShell

```powershell
$delau = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -ObjectId $delau.ObjectId
```

可以根据特定环境的需要修改用引号引起来的值。

### <a name="use-the-graph-api"></a>使用 Graph API

```http
HTTP request
DELETE /administrativeUnits/{Admin id}
Request body
{}
```

## <a name="next-steps"></a>后续步骤

* [对管理单元中的用户的管理](roles-admin-units-add-manage-users.md)
* [管理管理单元中的组](roles-admin-units-add-manage-groups.md)
