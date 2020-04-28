---
title: 使用 Azure AD 配置最终用户同意应用程序的方式
description: 了解如何以及何时让用户能够同意将有权访问组织数据的应用程序。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42337fe958a881ee263d16c866dda69f13fe09c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519624"
---
# <a name="configure-how-end-users-consent-to-applications"></a>配置最终用户同意应用程序的方式

应用程序可以与 Microsoft 标识平台集成，以允许用户使用其工作或学校帐户在 Azure Active Directory （Azure AD）中登录，并访问组织的数据，提供丰富的数据驱动体验。 不同的权限允许应用程序对用户和组织数据的不同级别的访问权限。

默认情况下，用户可以同意访问你的组织数据的应用程序，但仅适用于某些权限。 例如，默认情况下，用户可以同意允许应用访问其自己的邮箱或用户拥有的团队的团队会话，但不允许应用以无人参与的方式访问你组织中的所有 SharePoint 站点。 尽管允许用户自行同意，但允许用户轻松获取与 Microsoft 365、Azure 和其他服务集成的有用应用程序，如果不小心使用和监视，则可能会造成风险。

Microsoft 建议禁用未来的用户同意操作，以帮助减少您的 surface 区域并降低这种风险。 如果禁用了用户许可，则仍将接受以前的许可授权，但所有未来的同意操作都必须由管理员执行。 用户可以通过集成的[管理员同意请求工作流](configure-admin-consent-workflow.md)或通过你自己的支持过程来请求租户范围内的管理员许可。 有关更多详细信息，请参阅用于[保护标识基础结构的五个步骤](../../security/fundamentals/steps-secure-identity.md)。

## <a name="configure-user-consent-to-applications"></a>配置用户同意应用程序
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>禁用或启用 Azure 门户的用户同意

你可以使用 Azure 门户禁用或允许用户同意访问组织数据的应用程序：

1. 以[全局管理员身份](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)登录到[Azure 门户](https://portal.azure.com)。
2. 依次选择 " **Azure Active Directory**"、"**企业应用程序**" 和 "**用户设置**"。
3. 使用标记为 "**用户可以同意应用代表他们访问公司数据**" 的控件启用或禁用用户同意。
4. 可有可无配置[管理员同意请求工作流](configure-admin-consent-workflow.md)，以确保不允许同意应用的用户可以请求批准。

> [!TIP]
> 若要允许用户请求管理员查看不允许用户同意的应用程序（例如，由于用户同意已禁用，或者因为应用程序正在请求用户不允许授予的权限），请考虑[配置管理员许可工作流](configure-admin-consent-workflow.md)。

### <a name="disable-or-enable-user-consent-using-powershell"></a>使用 PowerShell 禁用或启用用户许可

可以使用 Azure AD PowerShell v1 模块（[MSOnline](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)）来启用或禁用用户同意访问组织数据的应用程序的能力。

1. 通过运行以下 cmdlet 登录到你的组织：

    ```powershell
    Connect-MsolService
    ```

2. 通过运行以下 cmdlet 检查用户同意是否已启用：

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. 启用或禁用用户同意。 例如，若要禁用用户同意，请运行以下 cmdlet：

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>配置组所有者同意访问组数据的应用

> [!IMPORTANT]
> 以下信息适用于即将发布的功能，使组所有者能够向应用程序授予对其组数据的访问权限。 此功能在发布后会默认启用。 尽管此功能尚未广泛发布，但你可以使用这些说明来禁用其版本的功能。

组所有者可以授权应用程序（例如，由第三方供应商发布的应用程序）访问组织的与组关联的数据。 例如，团队所有者（团队的 Office 365 组的所有者）可以允许应用读取团队中的所有团队消息，或列出组成员的基本配置文件。

> [!NOTE]
> 与此设置无关，将始终允许组所有者直接将其他用户或应用添加为组所有者。

### <a name="configure-group-owner-consent-using-powershell"></a>使用 PowerShell 配置组所有者同意

你可以使用 Azure AD PowerShell 预览版模块（[AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)）来启用或禁用组所有者同意应用程序访问其拥有的组的数据的权限。

1. 确保使用的是[AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)模块（如果已安装[AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0)模块和[AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)模块，则此步骤非常重要。）

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. 连接到 Azure AD PowerShell。

   ```powershell
   Connect-AzureAD
   ```

3. 检索租户中 "同意"*策略设置*目录设置的当前值。 这需要检查是否已创建此功能的目录设置，如果没有，则使用相应的目录设置模板中的值。

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

4. 了解设置值。 有两个设置值可用于定义哪些用户可以允许应用访问其组的数据：

    | 设置       | 类型         | 说明  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Boolean |  指示是否允许组所有者授予组特定权限的标志。 |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | 如果将_EnableGroupSpecificConsent_设置为 "True"，并将此值设置为组的对象 ID，则标识的组的成员将有权向其拥有的组授予特定于组的权限。 |

5. 更新所需配置的设置值：

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

6. 保存设置。

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="configure-risk-based-step-up-consent"></a>配置基于风险的单步执行许可

基于风险的逐步同意有助于减少用户对发出[非法同意请求](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)的恶意应用程序的攻击。 如果 Microsoft 检测到有风险的最终用户同意请求，则该请求将要求 "单步执行" 管理许可。 此功能在默认情况下是启用的，但在启用最终用户许可时，它只会导致行为发生更改。

当检测到有风险的许可请求时，同意提示将显示一条消息，指示需要管理员批准。 如果启用了[管理员同意请求工作流](configure-admin-consent-workflow.md)，则用户可以将请求发送到管理员，以便直接从同意提示进行复查。 如果未启用，将显示以下消息：

* **AADSTS90094：** &lt;clientAppDisplayName&gt;需要权限才能访问组织中只有管理员才能授予的资源。 请让管理员授予访问此应用的权限，否则你将无法使用该应用。

在这种情况下，还将记录审核事件，其中包含 "ApplicationManagement" 类别、"同意应用程序" 活动类型和 "检测到有风险的应用程序" 的状态描述。

> [!IMPORTANT]
> 管理员应在批准之前仔细[评估所有同意请求](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent)，尤其是当 Microsoft 检测到风险时。

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>使用 PowerShell 禁用或重新启用基于风险的步骤许可

你可以使用 Azure AD PowerShell 预览版模块（[AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)）来禁用 Microsoft 检测到风险或重新启用此功能（如果以前已禁用）的情况下所需的管理员许可。

为此，可以使用前面所示的相同步骤，使用[PowerShell 配置组所有者同意](#configure-group-owner-consent-using-powershell)，但使用不同的设置值。 步骤有三个不同之处： 

1. 了解基于风险的逐步同意的设置值：

    | 设置       | 类型         | 说明  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boolean |  指示检测到有风险的请求时是否阻止用户同意的标志。 |

2. 在步骤3：

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
3. 在步骤5：

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>后续步骤

[配置管理员许可工作流](configure-admin-consent-workflow.md)

[了解如何管理对应用程序的同意并评估同意请求](manage-consent-requests.md)

[向应用程序授予租户范围的管理员许可](grant-admin-consent.md)

[Microsoft 标识平台中的权限和许可](../develop/active-directory-v2-scopes.md)

[StackOverflow 上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
