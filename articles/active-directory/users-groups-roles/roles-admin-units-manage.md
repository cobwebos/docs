---
title: 添加和删除管理单位（预览） - Azure 活动目录 |微软文档
description: 使用管理单位限制 Azure 活动目录中的角色权限范围
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
ms.openlocfilehash: 351cbee9cbc7cc02c7f20553b6fc8c3f861541df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428155"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>管理 Azure 活动目录中的管理单位

要在 Azure 活动目录 （Azure AD） 中进行更精细的管理控制，可以将用户分配给范围仅限于一个或多个管理单位 （A） 的 Azure AD 角色。

## <a name="getting-started"></a>入门

1. 要通过[图形资源管理器](https://aka.ms/ge)从以下说明运行查询，请确保执行以下操作：

    1. 转到门户中的 Azure AD，然后在应用程序中选择图形资源管理器，并提供图形资源管理器的管理员同意。

        ![选择图形资源管理器并在此页面上提供管理员同意](./media/roles-admin-units-manage/select-graph-explorer.png)

    1. 在图形资源管理器中，请确保选择测试版。

        ![在 POST 操作之前选择测试版](./media/roles-admin-units-manage/select-beta-version.png)

1. 请使用 Azure AD 电源外壳的预览版本。 详细说明在这里。

## <a name="add-an-administrative-unit"></a>添加管理单位

### <a name="azure-portal"></a>Azure 门户

1. 转到门户中的"活动目录"，然后选择左侧面板中的"管理单位"。

    ![导航到 Azure 活动目录中的管理单位](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. 选择 **"添加**" 并提供管理单位的名称，可以选择为管理单位添加说明。

    ![选择"添加"，然后输入管理单位的名称](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. 选择 **"添加"** 以最终确定管理单元。

### <a name="powershell"></a>PowerShell

在尝试执行以下操作之前，请安装 Azure AD 电源外壳（预览版本）：

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

可以根据需要修改上面突出显示的值。

### <a name="microsoft-graph"></a>Microsoft Graph

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>删除管理单位

在 Azure 活动目录 （Azure AD） 中，可以删除不再需要作为管理角色范围单元的管理员单元。

### <a name="azure-portal"></a>Azure 门户

转到**Azure AD >门户中的管理单位**。 选择要删除的管理单位，然后选择 **"删除**"。 确认**为"是"** 后，行政单位将被删除。

![选择要删除的管理单位](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="powershell"></a>PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

突出显示的部分可以根据需要更改特定环境。

### <a name="graph-api"></a>图形 API

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>后续步骤

[管理行政单位](roles-admin-units-add-manage-users.md)
[管理组](roles-admin-units-add-manage-groups.md)中的用户
