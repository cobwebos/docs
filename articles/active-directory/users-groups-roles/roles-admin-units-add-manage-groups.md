---
title: 在管理单元中添加、删除和列出组（预览）-Azure Active Directory |Microsoft Docs
description: 在 Azure Active Directory 的管理单元中管理组及其角色权限
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
ms.openlocfilehash: 1f0b0c0a7b666aed56ad24625c80c0a57683b998
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683275"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>在 Azure Active Directory 中的管理单元中添加和管理组

在 Azure Active Directory （Azure AD）中，可以将组添加到管理单元（AU），以便控制更精细的管理作用域。

有关准备使用 PowerShell 和 Microsoft Graph 管理单元管理的步骤，请参阅[开始](roles-admin-units-manage.md#get-started)使用。

## <a name="add-groups-to-an-au"></a>将组添加到 AU

### <a name="azure-portal"></a>Azure 门户

在预览中，只能将组单独分配给管理单元。 没有将组大容量分配给管理单元的选项。 可以通过以下两种方式之一，在门户中将组分配到管理单元：

1. 从 " **Azure AD > 组**" 页

    在 Azure AD 中打开 "组概述" 页，并选择需要分配到管理单元的组。 在左侧，选择 "**管理单元**" 列出分配给组的管理单元。 在顶部，你会看到 "分配到管理单元" 选项，单击它将提供一个右侧面板来选择管理单元。

    ![将组单独分配给管理单元](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. 从 " **Azure AD > 管理单元 > 所有组**" 页

    打开 "所有组" 边栏选项卡中 Azure AD > 管理单元 "。 如果有组已分配到管理单元，它们将显示在右侧。 选择顶部的 "**添加**"，右侧面板会滑入 Azure AD 组织中的可用组。 选择要分配给管理单元的一个或多个组。

    ![选择管理单元，然后选择 "添加成员"](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

在此示例中，使用 cmdlet Add-azureadadministrativeunitmember 将组添加到管理单元。 要添加的管理单元的对象 ID 和要添加的组的对象 ID 作为参数。 突出显示部分可能根据特定环境的需要进行更改。

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref

    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
    }

例如：

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
    }

## <a name="list-groups-in-an-au"></a>在 AU 中列出组

### <a name="azure-portal"></a>Azure 门户

请在门户中中转到**Azure AD > 管理单元**。 选择要为其列出用户的管理单元。 默认情况下，在左侧面板中选择 "**所有用户**"。 选择 "**所有组**"，然后在右侧找到作为所选管理单元的成员的组列表。

![选择要删除的管理单元](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

这将帮助你获取管理单元的所有成员。 如果要显示属于管理单元成员的所有组，可以使用以下代码片段：

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

## <a name="list-aus-for-a-group"></a>列出组的澳大利亚

### <a name="azure-portal"></a>Azure 门户

在 Azure AD 门户中，可以通过打开 "**组**" 打开组的详细信息。 选择组以打开组的配置文件。 选择 "**管理单元**" 可列出该组所属的所有管理单元。

![列出组的管理单元](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>从 AU 中删除组

### <a name="azure-portal"></a>Azure 门户

可以通过两种方式从 Azure 门户中的管理单元中删除组。

打开**Azure AD** > **组**，然后打开要从管理单元中删除的组的配置文件。 在左侧面板中选择 "**管理单元**"，以列出该组所属的所有管理单元。 选择要从中删除组的管理单元，然后选择 "**从管理单元中删除**"。

![从管理单元中删除组](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

或者，你可以前往**Azure AD** > **管理单元**，并选择该组所属的管理单元。 在左面板中选择 "**组**" 以列出成员组。 选择要从管理单元中删除的组，然后选择 "**删除组**"。

![列出管理单元中的组](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>后续步骤

- [向管理单元分配角色](roles-admin-units-assign-roles.md)
- [管理单元中的用户](roles-admin-units-add-manage-users.md)
