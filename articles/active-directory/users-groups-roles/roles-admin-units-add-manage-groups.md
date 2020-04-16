---
title: 在管理单元（预览）中添加、删除和列出组 - Azure 活动目录 |微软文档
description: 在 Azure 活动目录中的管理单元中管理组及其角色权限
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
ms.openlocfilehash: 78f45d9e1ca5b8237779fe096154c83749aa08fe
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428428"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>在 Azure 活动目录中在管理单元中添加和管理组

在 Azure 活动目录 （Azure AD） 中，您可以将组添加到管理单位 （AU） 以进行更精细的管理控制范围。

有关准备使用 PowerShell 和 Microsoft 图形进行管理单元管理的步骤，请参阅[入门](roles-admin-units-manage.md#getting-started)。

## <a name="add-groups-to-an-au"></a>将组添加到 AU

### <a name="azure-portal"></a>Azure 门户

在预览中，只能将组单独分配给管理单位。 没有将组批量分配给管理单位的选项。 您可以通过门户中的两种方式之一将组分配给管理单位：

1. 从**Azure AD >组**"页

    打开 Azure AD 中的"组概览"页，然后选择要分配给管理单位的组。 在左侧，选择 **"管理单位"** 列出组分配到的管理单位。 在上面，你会发现选项"分配给管理单位"，单击它将在右侧提供一个面板来选择管理单元。

    ![将组单独分配给管理单位](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. 从**Azure AD >管理单位>所有组**页面

    打开 Azure AD 中的所有组边栏选项卡>管理单位。 如果已分配给管理单位的组，则它们将显示在右侧。 选择 **"在顶部添加**"，右侧面板将滑动列出 Azure AD 组织中可用的组。 选择要分配给管理单位的一个或多个组。

    ![选择管理单位，然后选择"添加成员"](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

在此示例中，cmdlet Add-AzureAD管理单位成员用于将组添加到管理单位。 要添加的管理单元的对象 ID 和要添加的组的对象 ID 被视为参数。 突出显示的部分可以根据需要更改特定环境。

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

转到**Azure AD >门户中的管理单位**。 选择要为其列出用户的管理单位。 默认情况下，已在左侧面板上选择了**所有用户**。 选择 **"所有组**"，在右侧，您将找到作为所选管理单位成员的组的列表。

![选择要删除的管理单位](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

这将有助于您获取管理单位的所有成员。 如果要显示管理单位的所有组，可以使用以下代码段：

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

## <a name="list-aus-for-a-group"></a>列出组的 A

### <a name="azure-portal"></a>Azure 门户

在 Azure AD 门户中，可以通过打开组来打开**组**的详细信息。 选择组以打开组的配置文件。 选择 **"管理单位"** 以列出组是成员的所有管理单位。

![列出组的行政单位](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>从 AU 中删除组

### <a name="azure-portal"></a>Azure 门户

有两种方法可以从 Azure 门户中的管理单位中删除组。

打开**Azure AD** > **组**，并打开要从管理单位中删除的组配置文件。 选择左侧面板中的 **"管理单位**"以列出组是成员的所有管理单位。 选择要从中删除组的管理单位，然后选择**从管理单位中删除**。

![从管理单位中删除组](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

或者，您可以转到**Azure AD** > **管理单位**，并选择组是成员的管理单位。 选择左侧面板中的**组**以列出成员组。 选择要从管理单位中删除的组，然后选择 **"删除组**"。

![列出管理单元中的组](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>后续步骤

- [将角色分配给管理单位](roles-admin-units-assign-roles.md)
- [管理管理单元中的用户](roles-admin-units-add-manage-users.md)
