---
title: 添加和删除管理单位（预览） - Azure 活动目录 |微软文档
description: 使用管理单位限制 Azure 活动目录中的角色权限范围。
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
ms.openlocfilehash: 7a4a0dfaeda18b3f68ddc3c7cc7333b8c994d174
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684914"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>管理 Azure 活动目录中的管理单位

要在 Azure 活动目录 （Azure AD） 中进行更精细的管理控制，可以将用户分配给范围为一个或多个管理单位 （A） 的 Azure AD 角色。

## <a name="get-started"></a>入门

1. 要通过[图形资源管理器](https://aka.ms/ge)从以下说明运行查询，执行以下操作：

    a. 在 Azure 门户中，转到 Azure AD。 在应用程序列表中，选择**图形资源管理器**，然后选择**授予管理员同意图形资源管理器**。

    ![显示指向"授予管理员同意"链接的屏幕截图](./media/roles-admin-units-manage/select-graph-explorer.png)

    b. 在图形资源管理器中，选择**测试版**。

    ![显示所选测试版的屏幕截图](./media/roles-admin-units-manage/select-beta-version.png)

1. 使用 Azure AD 电源外壳的预览版本。

## <a name="add-an-administrative-unit"></a>添加管理单位

### <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 在 Azure 门户中，转到 Azure AD，然后在左侧窗格中选择 **"管理单位**"。

    ![Azure AD 中管理单元（预览）链接的屏幕截图](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. 选择 **"添加**"，然后输入管理单位的名称。 或者，添加管理单位的说明。

    ![用于输入管理单位名称的"添加"按钮和文本框的屏幕截图](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. 选择 **"添加"** 以最终确定管理单元。

### <a name="use-powershell"></a>使用 PowerShell

在尝试运行以下命令之前，请安装 Azure AD 电源外壳（预览）：

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

您可以根据需要修改引号中包含的值。

### <a name="use-microsoft-graph"></a>使用微软图形

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>删除管理单位

在 Azure AD 中，可以删除不再需要作为管理角色范围单元的管理单位。

### <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 在 Azure 门户中，转到**Azure AD** > **管理单位**。 
1. 选择要删除的管理单位，然后选择 **"删除**"。 
1. 要确认要删除管理单位，请选择"**是**"。 行政单位将被删除。

![管理单位删除按钮和确认窗口的屏幕截图](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>使用 PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

您可以根据需要修改引号中包含的值。

### <a name="use-the-graph-api"></a>使用图形 API

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>后续步骤

* [管理管理单元中的用户](roles-admin-units-add-manage-users.md)
* [管理管理单元中的组](roles-admin-units-add-manage-groups.md)
