---
title: 将敏感度标签分配给组-Azure AD |Microsoft Docs
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
ms.openlocfilehash: 0e4dabad5057fda39fe3753c810a85e6aeb55b3a
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582956"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>将敏感度标签分配到 Azure Active Directory 中的 Office 365 组（预览）

Azure Active Directory （Azure AD）支持将[Microsoft 365 符合性中心](https://sip.protection.office.com/homepage)发布的敏感度标签应用到 Office 365 组。 敏感度标签适用于各种服务，例如 Outlook、Microsoft 团队和 SharePoint。 此功能目前处于公开预览状态。 有关 Office 365 应用支持的详细信息，请参阅[office 365 支持敏感度标签](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels)。

> [!IMPORTANT]
> 若要配置此功能，你的 Azure AD 组织中必须至少有一个活动 Azure Active Directory Premium P1 许可证。

## <a name="enable-sensitivity-label-support-in-powershell"></a>在 PowerShell 中启用敏感度标签支持

若要将已发布的标签应用于组，必须首先启用此功能。 这些步骤在 Azure AD 中启用该功能。

1. 在计算机上打开 Windows PowerShell 窗口。 无需提升的权限即可打开该窗口。
1. 运行以下命令，准备运行 cmdlet。

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    在 "**登录你的帐户**" 页上，输入你的管理员帐户和密码以连接到你的服务，然后选择 "**登录**"。
1. 提取 Azure AD 组织的当前组设置。

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > 如果没有为此 Azure AD 组织创建组设置，则必须先创建设置。 按照 Azure Active Directory cmdlet 中的步骤[配置组设置](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets)，为此 Azure AD 组织创建组设置。

1. 接下来，显示当前组设置。

    ```PowerShell
    $Setting.Values
    ```

1. 然后启用此功能：

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. 然后保存更改并应用设置：

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

就这么简单。 已启用该功能，可以将已发布的标签应用于组。

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>在 Azure 门户中为新组分配标签

1. 登录到[Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择 "**组**"，然后选择 "**新建组**"。
1. 在 "**新建组**" 页上，选择 " **Office 365**"，然后填写新组所需的信息，然后从列表中选择一个灵敏度标签。

   ![在 "新建组" 页中分配敏感度标签](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. 保存更改，然后选择 "**创建**"。

创建组后，将自动强制实施与选定标签关联的站点和组设置。

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>为 Azure 门户中的现有组分配标签

1. 使用组管理员帐户或组所有者登录到[Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择**组**。
1. 从 "**所有组**" 页中，选择要标记的组。
1. 在选定组的页上，选择 "**属性**"，然后从列表中选择一个灵敏度标签。

   ![在组的 "概述" 页上分配敏感度标签](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. 选择“保存”  以保存更改。

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>从 Azure 门户中的现有组中删除标签

1. 使用全局管理员帐户或组管理员帐户或作为组所有者登录到[Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择**组**。
1. 从 "**所有组**" 页中，选择要从中删除标签的组。
1. 在 "**组**" 页上，选择 "**属性**"。
1. 选择“删除”。
1. 单击“保存”应用所做的更改。****

## <a name="using-classic-azure-ad-classifications"></a>使用经典 Azure AD 分类

启用此功能后，组的 "经典" 分类将仅显示现有组和站点，并且仅当在不支持敏感度标签的应用中创建组时，才应将其用于新组。 如果需要，你的管理员可以稍后将其转换为敏感性标签。 经典分类是通过在 Azure AD PowerShell 中为`ClassificationList`设置定义值来设置的旧分类。 启用此功能后，这些分类将不会应用于组。

## <a name="troubleshooting-issues"></a>对问题进行故障排除

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>敏感度标签不适用于组

仅当满足以下所有条件时，才会为组显示敏感度标签选项：

1. 在此 Azure AD 组织的 Microsoft 365 符合性中心中发布标签。
1. 启用此功能后，PowerShell 中的 EnableMIPLabels 设置为 True。
1. 组是 Office 365 组。
1. 组织具有活动的 Azure Active Directory Premium P1 许可证。
1. 当前登录的用户具有足够的权限来分配标签。 用户必须是全局管理员、组管理员或组所有者。

请确保满足所有条件，以便将标签分配给组。

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>列表中没有要分配的标签

如果要查找的标签不在列表中，则可能是以下原因之一：

- 此标签可能不会在 Microsoft 365 相容性中心发布。 这也适用于不再发布的标签。 请咨询管理员以获取详细信息。
- 但可以发布标签，而不能用于已登录的用户。 有关如何访问标签的详细信息，请咨询管理员。

### <a name="how-to-change-the-label-on-a-group"></a>如何更改组中的标签

可以使用将标签分配给现有组的相同步骤随时交换标签，如下所示：

1. 使用全局或组管理员帐户或作为组所有者登录到[Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择**组**。
1. 从 "**所有组**" 页中，选择要标记的组。
1. 在选定组的页上，选择 "**属性**"，然后从列表中选择一个新的敏感度标签。
1. 选择“保存”  。

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>组设置对已发布标签所做的更改不会在组中更新

最佳做法是，我们不建议您在将标签应用于组之后更改标签的组设置。 当你对与[Microsoft 365 相容性中心](https://sip.protection.office.com/homepage)内的已发布标签关联的组设置进行更改时，这些策略更改不会自动应用于受影响的组。

如果必须进行更改，请使用[Azure AD PowerShell 脚本](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)将更新手动应用到受影响的组。 此方法可确保所有现有组都强制实施新的设置。

## <a name="next-steps"></a>后续步骤

- [结合使用敏感度标签和 Microsoft 团队、Office 365 组和 SharePoint 站点](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [标签策略后更新组 Azure AD PowerShell 脚本手动更改](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [编辑组设置](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [使用 PowerShell 命令管理组](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
