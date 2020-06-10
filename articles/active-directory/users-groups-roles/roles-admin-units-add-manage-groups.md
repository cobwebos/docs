---
title: 在管理单元中添加、删除和列出组（预览版）- Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中对管理单元中的组及其角色权限的管理
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2547a0fdbfbcc143ff822de333b41198f469375c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649315"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>在 Azure Active Directory 中对管理单元中的组的添加和管理

在 Azure Active Directory (Azure AD) 中，你可以向管理单元 (AU) 添加组，以获得更精细的管理控制范围。

有关将 PowerShell 和 Microsoft Graph 用于管理单元的管理的准备步骤，请参阅[入门](roles-admin-units-manage.md#get-started)。

## <a name="add-groups-to-an-au"></a>向 AU 添加组

### <a name="azure-portal"></a>Azure 门户

在预览版中，只能将组一个个地分配给管理单元。 无法将组批量地分配给管理单元。 在门户中，可通过以下两种方式之一，将向管理单元分配组：

1. 通过“Azure AD”>“组”页

    在 Azure AD 中打开“组”概述页面，然后选择需要向管理单元分配的组。 在左侧，选择“管理单元”以列出给组分配的管理单元。 可在顶部看到“分配到管理单元”选项，单击它，屏幕右侧就会显示一个用于选择管理单元的面板。

    ![将组一个一个地分配给管理单元](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. 通过“Azure AD”>“管理单元”>“所有组”页

    通过“Azure AD”>“管理单元”打开“所有组”边栏选项卡。 如果已有组分配给了管理单元，它们将显示在右侧。 在顶部选择“添加”，右面板就会滑入，上面列出你所在 Azure AD 组织中可用的组。 选择要分配给管理单元的一个或多个组。

    ![选择一个管理单元，然后选择“添加成员”](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

此示例使用 Add-AzureADAdministrativeUnitMember cmdlet 向管理单元添加组。 管理单元的对象 ID 和要添加的组的对象 ID 用作参数。 可以根据特定环境的需要更改突出显示的部分。

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref

    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
    }

示例：

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
    }

## <a name="list-groups-in-an-au"></a>列出 AU 中的组

### <a name="azure-portal"></a>Azure 门户

在门户中，转到“Azure AD”>“管理单元”。 选择要列出其中的用户的管理单元。 左面板上默认选中“所有用户”。 选择“所有组”，右侧就会显示一列组，这些组都是所选管理单元的成员。

![列出管理单元中的组](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

这将帮助你获取管理单元的所有成员。 如果要显示属于管理单元的成员的所有组，可以使用以下代码片段：

    foreach ($member in (Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
    {
    if($member.ObjectType -eq "Group")
    {
    Get-AzureADGroup -ObjectId $member.ObjectId
    }
    }

### <a name="microsoft-graph"></a>Microsoft Graph

    HTTP request
    GET /administrativeUnits/{Admin id}/members/$/microsoft.graph.group
    Request body
    {}

## <a name="list-aus-for-a-group"></a>列出组的 AU

### <a name="azure-portal"></a>Azure 门户

在 Azure AD 门户中，打开“组”即可展开组的详细信息。 选择一个组可打开该组的配置文件。 选择“管理单元”以列出该组所属的所有管理单元。

![列出组的管理单元](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>从 AU 删除组

### <a name="azure-portal"></a>Azure 门户

在 Azure 门户中，有两种方式可以从管理单元删除组。

打开“Azure AD” > “组”，然后打开要从管理单元删除的组的配置文件 。 在左面板中选择“管理单元”以列出该组所属的所有管理单元。 选择要从中删除组的管理单元，然后选择“从管理单元中删除”。

![从管理单元删除组](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

另一种方式是：转到“Azure AD” > “管理单元”，然后选择组所属的管理单元 。 在左面板中选择“组”以列出成员组。 选择要从管理单元中删除的组，然后选择“删除组”。

![列出管理单元中的组](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>后续步骤

- [向管理单元分配角色](roles-admin-units-assign-roles.md)
- [对管理单元中的用户的管理](roles-admin-units-add-manage-users.md)
