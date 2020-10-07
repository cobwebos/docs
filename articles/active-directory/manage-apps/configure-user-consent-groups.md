---
title: 使用 Azure AD 配置组所有者同意访问组数据的应用
description: 了解如何管理组和团队所有者是否可以同意将有权访问组或团队数据的应用程序。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: e590981fabcd20f23f25d12b4176b6730cb0fc3c
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804290"
---
# <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>配置组所有者对应用访问组数据的同意

组和团队所有者可以授权应用程序（如第三方供应商发布的应用程序）访问组织的与组关联的数据。 例如，Microsoft Teams 中的团队所有者可允许应用读取团队中的所有 Teams 消息，或允许列出组成员的基本个人资料。 若要了解详细信息，请参阅 [Microsoft 团队中的资源特定同意](https://docs.microsoft.com/microsoftteams/resource-specific-consent) 。

## <a name="manage-group-owner-consent-to-apps"></a>管理组所有者同意应用

你可以配置允许哪些用户同意访问其组或团队数据的应用，也可以为所有用户禁用此设置。

# <a name="portal"></a>[Portal](#tab/azure-portal)

请按照以下步骤管理对访问组数据的应用的组所有者同意：

1. 以[全局管理员](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)的身份登录 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory” > “企业应用程序” > “同意和权限” > “用户同意设置”   。
3. 在“针对应用访问数据的组所有者同意”下，选择要启用的选项。
4. 选择“保存”以保存设置。

在下例中，所有组所有者都可同意应用访问其组数据：

:::image type="content" source="media/configure-user-consent-groups/group-owner-consent.png" alt-text="组所有者同意设置":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

可使用 Azure AD PowerShell 预览版模块 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) 允许或禁止组所有者同意应用程序访问你组织的数据来查找他们拥有的组。

1. 请确保使用 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) 模块。 如果同时安装了 [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0&preserve-view=true) 模块和 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) 模块，则此步骤非常重要。

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. 连接到 Azure AD PowerShell。

   ```powershell
   Connect-AzureAD
   ```

1. 检索租户中“同意策略设置”目录设置的当前值。 这需要检查是否已创建此功能的目录设置；如果没有，则使用相应目录设置模板中的值。

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

1. 了解设置值。 有两个设置值可用于定义哪些用户能够允许应用访问其组的数据：

    | 设置       | 类型         | 说明  |
    | ------------- | ------------ | ------------ |
    | EnableGroupSpecificConsent   | Boolean | 指示是否允许组所有者授予特定于组的权限的标志。 |
    | ConstrainGroupSpecificConsentToMembersOfGroupId | Guid | 如果 EnableGroupSpecificConsent 设置为“True”，并且此值设置为组的对象 ID，则将授权已标识组的成员向其拥有的组授予特定于组的权限。 |

1. 更新所需配置的设置值：

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

1. 保存设置。

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

---

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请访问以下链接：

* [配置用户同意设置](configure-user-consent.md)
* [配置管理员同意工作流](configure-admin-consent-workflow.md)
* [了解如何管理对应用程序的同意并评估同意请求](manage-consent-requests.md)
* [向应用程序授予租户范围的管理许可](grant-admin-consent.md)
* [Microsoft 标识平台中的权限和许可](../develop/active-directory-v2-scopes.md)

获取帮助或查找问题的答案：
* [StackOverflow 上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
