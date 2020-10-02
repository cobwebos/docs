---
title: 在管理单元中添加、删除和列出用户-Azure Active Directory |Microsoft Docs
description: 在 Azure Active Directory 的管理单元中管理用户及其角色权限
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
ms.openlocfilehash: 22f35d88f3bb36d63d533941d27f72336714077c
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630272"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>在 Azure Active Directory 的管理单元中添加和管理用户

在 Azure Active Directory (Azure AD) 中，你可以向管理单元 (AU) 添加用户，以便更精细地控制管理范围。

有关将 PowerShell 和 Microsoft Graph 用于管理单元的管理的准备步骤，请参阅[入门](roles-admin-units-manage.md#get-started)。

## <a name="add-users-to-an-au"></a>将用户添加到 AU

### <a name="azure-portal"></a>Azure 门户

可以通过两种方式将用户分配到管理单元。

1. 单独分配

    1. 可以在门户中转到 Azure AD，选择“用户”，然后选择要分配到管理单元的用户。 然后，可以在左侧面板中选择“管理单元”。 可以通过单击 "分配给管理单元"，然后选择要向其分配用户的管理单元，将用户分配给一个或多个管理单元。

       ![选择“添加”，然后输入管理单元的名称](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. 可以在门户中转到 Azure AD，并在左窗格中选择 "管理单元"，然后选择要在其中分配用户的管理单元。 选择 " **所有用户** "，然后选择 " **添加成员**"。 然后，可以从右侧窗格中选择一个或多个要分配到该管理单元的用户。

        ![选择一个管理单元，然后选择“添加成员”](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. 批量分配

    在门户中转到 Azure AD，并选择“管理单元”。 选择要在其中添加用户的管理单元。 单击“所有用户”->“从 .csv 文件添加成员”以继续。 然后，可以下载 CSV 模板并编辑该文件。 格式很简单，需要在每行中添加单个 UPN。 文件准备就绪后，将其保存在适当的位置，然后在步骤 3 中将其上传，如快照中所突出显示。

    ![向管理单元批量分配用户](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```

上面的示例使用 Add-AzureADAdministrativeUnitMember cmdlet 向管理单元添加用户。 用户要添加到的管理单元的对象 ID 和要添加的用户的对象 ID 用作参数。 可以根据特定环境的需要更改突出显示的部分。

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref
Request body
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{id}"
}
```

示例：

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/johndoe@fabidentity.com"
}
```

## <a name="list-administrative-units-for-a-user"></a>列出用户的管理单元

### <a name="azure-portal"></a>Azure 门户

在 Azure 门户可以通过以下方式打开用户的配置文件：

1. 打开**Azure AD**  >  **用户**。

1. 选择该用户以打开该用户的配置文件。

1. 选择 " **管理单位** " 以查看已分配用户的管理单元的列表。

   ![列出用户的管理单元](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```
注意： Add-azureadadministrativeunitmember 仅返回100成员。

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-au"></a>从 AU 中删除单个用户

### <a name="azure-portal"></a>Azure 门户

有两种方式可用于从管理单元删除用户。 在 Azure 门户中，可以通过转到“Azure AD” > “用户”打开用户的配置文件 。 选择该用户以打开该用户的配置文件。 选择要从中删除用户的管理单元，然后选择“从管理单元中删除”。

![从用户配置文件删除管理单元中的用户](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

还可以通过选择要从中删除用户的管理单元，在“Azure AD” > “管理单元”中删除用户 。 选择用户，然后选择“删除成员”。
  
![在管理单元级别删除用户](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/ $ref

## <a name="bulk-remove-more-than-one-user"></a>批量删除多个用户

可以转到“Azure AD”>“管理单元”，然后选择要从中删除用户的管理单元。 单击“批量删除成员”。 下载 CSV 模板，以便提供要删除的用户的列表。

编辑带有相关用户条目的已下载 CSV 模板。 请勿删除模板的前两行。 在每行中添加一个用户 UPN。

![编辑 CSV 文件以从管理单元批量删除用户](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

将条目保存到文件后，请上传文件，选择“提交”。

![提交批量上传文件](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>后续步骤

- [向管理单元分配角色](roles-admin-units-assign-roles.md)
- [向管理单元添加组](roles-admin-units-add-manage-groups.md)
