---
title: LinkedIn 帐户连接的管理员许可-Azure AD |Microsoft Docs
description: 说明如何在 Azure Active Directory 中启用或禁用 Microsoft 应用中的 LinkedIn 集成帐户连接
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bf65f69d9dcaf6de2236c98b56b58ec7e021099
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025409"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>在 Azure Active Directory 中集成 LinkedIn 帐户连接

你可以允许组织中的用户在某些 Microsoft 应用中访问其 LinkedIn 连接。 在用户同意连接其帐户之前，不会共享数据。 你可以在 Azure Active Directory （Azure AD）[管理中心](https://aad.portal.azure.com)内集成你的组织。

> [!IMPORTANT]
> LinkedIn 帐户连接设置当前正在 Azure AD 组织中推出。 在将其推出给你的组织时，它会默认启用。
> 
> 异常：
> * 此设置不适用于使用以下软件的客户：美国政府版 Microsoft 云、德国 Microsoft 云或者在中国通过 21Vianet 运营的 Azure 和 Office 365。
> * 对于在德国预配的租户，此设置默认为关闭。 请注意，此设置不适用于使用德国 Microsoft 云的客户。
> * 对于在法国预配的租户，此设置默认为关闭。
>
> 为你的组织启用 LinkedIn 帐户连接后，帐户连接将在用户同意应用代表他们访问公司数据的应用后工作。 有关用户同意设置的信息，请参阅[如何删除用户对应用程序的访问权限](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment)。

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>在 Azure 门户中启用 LinkedIn 帐户连接

你可以仅为你想要访问的用户（从整个组织到你组织中的选定用户）启用 LinkedIn 帐户连接。

1. 使用 Azure AD 组织的全局管理员帐户登录到[Azure AD 管理中心](https://aad.portal.azure.com/)。
1. 选择“用户”。
1. 在“用户”边栏选项卡中，选择“用户设置”。
1. 在 " **LinkedIn 帐户连接**" 下，允许用户连接其帐户以在某些 Microsoft 应用中访问其 LinkedIn 连接。 在用户同意连接其帐户之前，不会共享数据。

    * 选择 **"是"** 可为组织中的所有用户启用该服务
    * 选择 "**所选组**" 以仅为组织中的一组选定用户启用该服务
    * 选择 "**否**" 以撤消组织中所有用户的同意

    ![在组织中集成 LinkedIn 帐户连接](./media/linkedin-integration/linkedin-integration.png)

1. 完成后，选择 "**保存**" 以保存设置。

> [!Important]
> 如果用户同意连接帐户，则不会为用户完全启用 LinkedIn 集成。 为用户启用帐户连接时，不会共享数据。

### <a name="assign-selected-users-with-a-group"></a>向选定用户分配组
我们替换了 "选定" 选项，该选项指定一个用户列表，其中包含选择一组用户的选项，以便您可以为单个组（而不是多个单独的用户）连接 LinkedIn 和 Microsoft 帐户。 如果没有为选定的单个用户启用 LinkedIn 帐户连接，则无需执行任何操作。 如果以前为所选的单个用户启用了 LinkedIn 帐户连接，则应执行以下操作：

1. 获取各个用户的当前列表
1. 将当前启用的单个用户移动到组
1. 使用 "Azure AD 管理中心" 中 "LinkedIn 帐户连接" 设置中的 "组" 作为所选组。

> [!NOTE]
> 即使不将当前选定的单个用户移至某个组，他们仍可以在 Microsoft 应用中看到 LinkedIn 信息。

### <a name="get-the-current-list-of-selected-users"></a>获取所选用户的当前列表

1. 用管理员帐户登录 Microsoft 365。
1. 转到 https://linkedinselectedusermigration.azurewebsites.net/。 你将看到为 LinkedIn 帐户连接选择的用户列表。
1. 将列表导出到 CSV 文件。

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>将当前选择的单个用户移动到组

1. 启动 PowerShell
1. 通过运行 `Install-Module AzureAD` 安装 Azure AD 模块
1. 运行以下脚本：

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

若要使用步骤2中的组作为 Azure AD 管理中心中 "LinkedIn 帐户连接" 设置中的选定组，请参阅[在 Azure 门户中启用 LinkedIn 帐户连接](#enable-linkedin-account-connections-in-the-azure-portal)。

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>使用组策略启用 LinkedIn 帐户连接

1. 下载 [Office 2016 管理模板文件 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. 提取 ADMX 文件然后将其复制到中央存储。
1. 打开组策略管理。
1. 使用以下设置创建组策略对象：“用户配置” **“管理模板”** “Microsoft Office 2016” > “杂项” **“在 Office 应用程序中显示 LinkedIn 的功能”**  >  >  > 。
1. 选择“已启用”或“已禁用”。
  
   状态 | 效果
   ------ | ------
   **已启用** | Office 2016“选项”中的“在 Office 应用程序中显示领英功能”设置已启用。 你的组织中的用户可以在其 Office 2016 应用程序中使用 LinkedIn 功能。
   **已禁用** | Office 2016“选项”中的“在 Office 应用程序中显示领英功能”设置已禁用，最终用户不能更改此设置。 组织中的用户无法在其 Office 2016 应用程序中使用 LinkedIn 功能。

此组策略只会影响本地计算机上的 Office 2016 应用。 如果用户在其 Office 2016 应用中禁用 LinkedIn，则他们仍可在 Office 365 中看到 LinkedIn 功能。

## <a name="next-steps"></a>后续步骤

* [LinkedIn 的用户同意和数据共享](linkedin-user-consent.md)

* [Microsoft 应用中的 LinkedIn 信息和功能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 帮助中心](https://www.linkedin.com/help/linkedin)

* [在 Azure 门户中查看当前的领英集成设置](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
