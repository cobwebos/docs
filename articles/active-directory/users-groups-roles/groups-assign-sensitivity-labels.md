---
title: 为组分配敏感度标签 - Azure AD |微软文档
description: 如何创建成员资格规则以自动填充组和规则引用。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/24/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 667fb39aabfec14cff01221b82a45ba8ad1d68d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329726"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>在 Azure 活动目录中为 Office 365 组分配敏感度标签（预览）

Azure 活动目录 （Azure AD） 支持将[Microsoft 365 合规性中心](https://sip.protection.office.com/homepage)发布的敏感度标签应用于 Office 365 组。 敏感度标签适用于跨服务（如 Outlook、Microsoft 团队和 SharePoint）的分组。 此功能目前处于公开预览状态。 有关支持 Office 365 应用的详细信息，请参阅[Office 365 支持敏感度标签](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels)。

> [!IMPORTANT]
> 要配置此功能，Azure AD 组织中必须至少有一个活动 Azure 活动目录高级 P1 许可证。

## <a name="enable-sensitivity-label-support-in-powershell"></a>在 PowerShell 中启用灵敏度标签支持

要将已发布的标签应用于组，必须首先启用该功能。 这些步骤启用 Azure AD 中的功能。

1. 在计算机上打开 Windows PowerShell 窗口。 无需提升的权限即可打开该窗口。
1. 运行以下命令，准备运行 cmdlet。

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    在"**登录到您的帐户**"页中，输入管理员帐户和密码以将您连接到服务，然后选择"**登录**"。
1. 获取 Azure AD 组织的当前组设置。

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > 如果尚未为此 Azure AD 组织创建组设置，则必须首先创建这些设置。 按照 Azure[活动目录 cmdlet 中的步骤配置组设置](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets)，为此 Azure AD 组织创建组设置。

1. 接下来，显示当前组设置。

    ```PowerShell
    $Setting.Values
    ```

1. 然后启用该功能：

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. 然后保存更改并应用设置：

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

就这么简单。 您已启用该功能，您可以将已发布的标签应用于组。

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>将标签分配给 Azure 门户中的新组

1. 登录到 Azure [AD 管理中心](https://aad.portal.azure.com)。
1. 选择**组**，然后选择 **"新建组**"。
1. 在新**组**页上，选择**Office 365，** 然后填写新组所需的信息，然后从列表中选择敏感度标签。

   ![在"新建组"页中分配敏感度标签](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. 保存更改并选择"**创建**"。

创建组，然后自动强制执行与所选标签关联的站点和组设置。

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>将标签分配给 Azure 门户中的现有组

1. 使用组管理员帐户或组所有者登录到[Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择**组**。
1. 在"**所有组"** 页上，选择要标记的组。
1. 在所选组的页面上，选择 **"属性"** 并从列表中选择敏感度标签。

   ![在组概览页上分配敏感度标签](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. 选择 **"保存"** 以保存更改。

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>从 Azure 门户中的现有组中删除标签

1. 使用全局管理员或组管理员帐户或组所有者登录到[Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择**组**。
1. 从"**所有组"** 页面中，选择要从中删除标签的组。
1. 在 **"组"** 页上，选择 **"属性**"。
1. 选择“删除”。
1. 单击“保存”应用所做的更改。****

## <a name="using-classic-azure-ad-classifications"></a>使用经典 Azure AD 分类

启用此功能后，组的"经典"分类将仅显示现有组和网站，并且仅当在不支持敏感度标签的应用中创建组时，才应将其用于新组。 如果需要，管理员可以稍后将其转换为敏感度标签。 经典分类是通过在 Azure AD PowerShell 中定义`ClassificationList`设置的值来设置的旧分类。 启用此功能后，这些分类将不会应用于组。

## <a name="troubleshooting-issues"></a>对问题进行故障排除

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>敏感度标签不适用于组上的分配

仅当满足以下所有条件时，才会为组显示敏感度标签选项：

1. 标签发布在 Microsoft 365 符合性中心，用于此租户。
1. 此功能已启用，启用MIP标签在 PowerShell 中设置为 True。
1. 该组是 Office 365 组。
1. 租户具有活动 Azure 活动目录高级 P1 许可证。
1. 当前登录用户具有分配标签的足够权限。 用户必须是全局管理员、组管理员或组所有者。

请确保满足所有条件，以便将标签分配给组。

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>要分配的标签不在列表中

如果您要查找的标签不在列表中，则可能出于以下原因之一：

- 该标签可能不会在 Microsoft 365 合规性中心发布。 这也适用于不再发布的标签。 有关详细信息，请咨询您的管理员。
- 标签可以发布，但是，它不适用于已登录的用户。 有关如何访问标签的详细信息，请咨询管理员。

### <a name="how-to-change-the-label-on-a-group"></a>如何更改组上的标签

标签可以随时使用与向现有组分配标签相同的步骤进行交换，如下所示：

1. 使用全局或组管理员帐户或组所有者登录到[Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择**组**。
1. 在"**所有组"** 页上，选择要标记的组。
1. 在所选组的页面上，选择 **"属性"** 并从列表中选择新的敏感度标签。
1. 选择“保存”。****

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>未在组上更新对已发布标签的组设置更改

最佳做法是，不建议在标签应用于组后更改标签的组设置。 当您对 Microsoft [365 合规性中心](https://sip.protection.office.com/homepage)中与已发布标签关联的组设置进行更改时，这些策略更改不会自动应用于受影响的组。

如果必须进行更改，请使用[Azure AD PowerShell 脚本](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)手动将更新应用于受影响的组。 此方法确保所有现有组强制执行新设置。

## <a name="next-steps"></a>后续步骤

- [将敏感度标签与 Microsoft 团队、Office 365 组和 SharePoint 网站一起使用](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [使用 Azure AD PowerShell 脚本手动更改标签策略后更新组](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [编辑组设置](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [使用 PowerShell 命令管理组](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
