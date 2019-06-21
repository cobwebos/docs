---
title: LinkedIn 帐户连接-Azure Active Directory 的管理员同意 |Microsoft Docs
description: 介绍如何启用或禁用 LinkedIn 集成帐户连接 Azure Active Directory 中的 Microsoft 应用中
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/29/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ca46d6df9c32f23d3051d1205c3c6b39e69f5a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164726"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>集成 Azure Active Directory 中的 LinkedIn 帐户连接

您可以允许用户在你的组织能够访问其某些 Microsoft 应用中的 LinkedIn 连接。 直到用户同意连接其帐户不共享任何数据。 您可以将你的组织中 Azure Active Directory (Azure AD) 集成[管理中心](https://aad.portal.azure.com)。

> [!IMPORTANT]
> LinkedIn 帐户连接设置是目前正在向 Azure AD 组织推出。 当它推出到你的组织时，它是默认情况下启用。
> 
> 异常：
> * 此设置不适用于使用以下软件的客户：美国政府版 Microsoft 云、德国 Microsoft 云或者在中国通过 21Vianet 运营的 Azure 和 Office 365。
> * 对于在德国预配的租户，此设置默认为关闭。 请注意，此设置不适用于使用德国 Microsoft 云的客户。
> * 对于在法国预配的租户，此设置默认为关闭。
>
> 一旦你的组织启用了 LinkedIn 帐户连接，帐户连接工作后用户同意应用代表他们的公司数据访问。 有关用户同意的情况下设置的信息，请参阅[如何删除用户的访问权限的应用程序](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment)。

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>启用在 Azure 门户中的 LinkedIn 帐户连接

可以启用想要具有访问权限，只有你的组织中的所选用户到整个组织中用户的 LinkedIn 帐户连接。

1. 登录到[Azure AD 管理中心](https://aad.portal.azure.com/)与 Azure AD 组织的全局管理员帐户。
1. 选择“用户”  。
1. 在“用户”  边栏选项卡中，选择“用户设置”  。
1. 下**LinkedIn 帐户连接**，允许用户连接其帐户以访问其某些 Microsoft 应用中的 LinkedIn 连接。 直到用户同意连接其帐户不共享任何数据。

    * 选择**是**若要启用你的组织中的所有用户服务
    * 选择**所选组**以启用该服务仅在你的组织中的所选用户的组
    * 选择**否**若要从你的组织中的所有用户撤销许可

    ![将集成在组织中的 LinkedIn 帐户连接](./media/linkedin-integration/linkedin-integration.png)

1. 完成后，选择**保存**以保存设置。

> [!Important]
> LinkedIn 集成完全才会启用你的用户他们同意连接其帐户。 没有数据时为用户启用帐户的连接共享。

### <a name="assign-selected-users-with-a-group"></a>使用组分配所选的用户
我们已经取代了用于选择一组用户，以便你可以启用连接 LinkedIn 和 Microsoft 帐户而不是许多单个用户的单个组的功能的选项指定的用户列表的所选选项。 如果没有为所选的单个用户启用 LinkedIn 帐户连接，不需要执行任何操作。 如果以前已启用 LinkedIn 帐户连接，为所选的单个用户，您应该：

1. 获取单个用户的当前列表
1. 将当前已启用单个用户移动到组
1. 从上一组用作在 Azure AD 管理员中心中设置的 LinkedIn 帐户连接中的所选组。

> [!NOTE]
> 即使不将你当前所选的单个用户移动到一个组，他们仍可以看到在 Microsoft 应用中的 LinkedIn 信息。

### <a name="get-the-current-list-of-selected-users"></a>获取当前所选用户的列表

1. 使用你的管理员帐户登录到 Microsoft 365。
1. 转到  https://linkedinselectedusermigration.azurewebsites.net/ 。 您将看到为 LinkedIn 帐户连接选择的用户的列表。
1. 将列表导出到 CSV 文件。

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>将当前所选的单个用户移动到组

1. 启动 PowerShell
1. 通过运行安装 Azure AD 模块 `Install-Module AzureAD`
1. 运行以下脚本：

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

若要将用作在 Azure AD 管理员中心中设置的 LinkedIn 帐户连接中的所选组的步骤 2 中的组，请参阅[在 Azure 门户中的启用 LinkedIn 帐户连接](#enable-linkedin-account-connections-in-the-azure-portal)。

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>使用组策略启用 LinkedIn 帐户连接

1. 下载 [Office 2016 管理模板文件 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. 提取 ADMX 文件然后将其复制到中央存储  。
1. 打开组策略管理。
1. 使用以下设置创建一个组策略对象：“用户配置” > “管理模板” > “Microsoft Office 2016” > “杂项” > “在 Office 应用程序中显示 LinkedIn 的功能”      。
1. 选择“已启用”或“已禁用”。  
  
   状态 | 效果
   ------ | ------
   **已启用** | Office 2016“选项”中的“在 Office 应用程序中显示领英功能”设置已启用。  你的组织中的用户可以在其 Office 2016 应用程序中使用 LinkedIn 功能。
   **已禁用** | Office 2016“选项”中的“在 Office 应用程序中显示领英功能”设置已禁用，最终用户不能更改此设置。  组织中的用户无法在其 Office 2016 应用程序中使用 LinkedIn 功能。

此组策略只会影响本地计算机上的 Office 2016 应用。 如果用户在其 Office 2016 应用中禁用 LinkedIn，他们仍可以看到在 Office 365 中的 LinkedIn 功能。

## <a name="next-steps"></a>后续步骤

* [用户同意和 linkedin 共享的数据](linkedin-user-consent.md)

* [Microsoft 应用中的 LinkedIn 信息和功能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 帮助中心](https://www.linkedin.com/help/linkedin)

* [在 Azure 门户中查看当前的领英集成设置](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
