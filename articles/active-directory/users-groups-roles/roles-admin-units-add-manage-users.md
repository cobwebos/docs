---
title: 在管理单元（预览）中添加、删除和列出用户 - Azure 活动目录 |微软文档
description: 在 Azure 活动目录中的管理单元中管理用户及其角色权限
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
ms.openlocfilehash: 2b2b901f652564c47ca35cb0f75a69f26fa2fa71
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533218"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>在 Azure 活动目录中的管理单元中添加和管理用户

在 Azure 活动目录 （Azure AD） 中，您可以将用户添加到管理单位 （AU） 以进行更精细的管理控制范围。

有关准备使用 PowerShell 和 Microsoft 图形进行管理单元管理的步骤，请参阅[入门](roles-admin-units-manage.md#getting-started)。

## <a name="add-users-to-an-au"></a>将用户添加到 AU

### <a name="azure-portal"></a>Azure 门户

您可以通过两种方式将用户分配给管理单位。

1. 个人分配

    1. 您可以转到门户中的 Azure AD 并选择"用户"并选择要分配给管理单元的用户。 然后，您可以在左侧面板中选择"管理单位"。 单击"分配给管理单位"并选择要分配用户的管理单位，可以将用户分配给一个或多个管理单位。

       ![选择"添加"，然后输入管理单位的名称](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. 您可以转到门户中的 Azure AD，然后在左侧窗格中选择管理单位，然后选择要分配用户的管理单位。 选择左侧窗格上的所有用户，然后选择"添加成员"。 然后，您可以继续从右侧窗格选择要分配给管理单位的一个或多个用户。

        ![选择管理单位，然后选择"添加成员"](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. 批量分配

    转到门户中的 Azure AD 并选择管理单位。 选择要添加用户的管理单位。 继续单击所有用户 -> 从 .csv 文件添加成员。 然后，您可以下载 CSV 模板并编辑该文件。 格式很简单，需要在每行中添加单个 UPN。 文件准备就绪后，将其保存在适当的位置，然后在步骤 3 中上载，如快照中突出显示的那样。

    ![批量将用户分配给管理单位](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrativeunitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId

在上面的示例中，cmdlet Add-AzureAD管理单位成员用于将用户添加到管理单位。 要添加用户的管理单元的对象 ID 和要添加的用户的对象 ID 被视为参数。 突出显示的部分可以根据需要更改特定环境。

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref
    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/users/{id}"
    }

示例：

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/johndoe@fabidentity.com"
    }

## <a name="list-administrative-units-for-a-user"></a>列出用户的管理单位

### <a name="azure-portal"></a>Azure 门户

在 Azure 门户中，可以通过访问 Azure AD >用户来打开用户的配置文件。 单击用户以打开用户的配置文件。

![在 Azure 活动目录中打开用户的配置文件](./media/roles-admin-units-add-manage-users/user-profile-admin-units.png)

选择左侧面板上的**管理单位**以查看已分配用户的管理单位列表。

![列出用户的管理单位](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $userObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/users//memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-single-user-from-an-au"></a>从 AU 中删除单个用户

### <a name="azure-portal"></a>Azure 门户

有两种方法可以从管理单位中删除用户。 在 Azure 门户中，可以通过访问**Azure AD** > **用户**来打开用户的配置文件。 选择用户以打开用户的配置文件。 选择要从中删除用户的管理单位，然后选择**从管理单元中删除**。

![从管理单位中删除用户配置文件](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

还可以通过选择要从中删除用户的管理单位来删除**Azure AD** > **管理单元**中的用户。 选择用户并选择 **"删除成员**"。
  
![在管理单位级别删除用户](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberUserObjId

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<user-id>/$ref

## <a name="bulk-remove-more-than-one-user"></a>批量删除多个用户

您可以转到 Azure AD >管理单元，并选择要从中删除用户的管理单位。 单击批量删除成员。 下载 CSV 模板，以提供要删除的用户列表。

使用相关用户条目编辑下载的 CSV 模板。 不要删除模板的前两行。 在每行中添加一个用户 UPN。

![编辑 CSV 文件，以便从管理单位中删除大量用户](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

将条目保存在文件中后，上载文件，选择"**提交**"。

![提交批量上传文件](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>后续步骤

- [将角色分配给管理单位](roles-admin-units-assign-roles.md)
- [将组添加到管理单位](roles-admin-units-add-manage-groups.md)
