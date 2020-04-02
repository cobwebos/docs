---
title: 配置最终用户如何同意使用 Azure AD 的应用程序
description: 了解如何管理用户如何以及何时同意可以访问组织数据的应用程序。
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
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519624"
---
# <a name="configure-how-end-users-consent-to-applications"></a>配置最终用户如何同意应用程序

应用程序可以与 Microsoft 标识平台集成，以允许用户在 Azure 活动目录 （Azure AD） 中使用其工作或学校帐户登录，并访问组织的数据以提供丰富的数据驱动体验。 不同的权限允许应用程序对用户和组织数据进行不同级别的访问。

默认情况下，用户可以同意访问组织数据的应用程序，尽管仅限于某些权限。 例如，默认情况下，用户可以同意允许应用访问其自己的邮箱或用户所属团队的 Teams 对话，但不能同意允许无人参与的应用访问读取和写入组织中的所有 SharePoint 网站。 虽然允许用户自行同意，但允许用户轻松获取与 Microsoft 365、Azure 和其他服务集成的有用应用程序，但如果不仔细使用和监视，则可能会构成风险。

Microsoft 建议禁用将来的用户同意操作，以帮助减少您的表面积并减轻此风险。 如果禁用用户同意，以前的同意授予仍将得到遵守，但管理员必须执行以后的所有同意操作。 用户可以通过集成[的管理员同意请求工作流](configure-admin-consent-workflow.md)或通过您自己的支持流程请求租户范围的管理员同意。 有关详细信息[，请参阅保护标识基础结构的五个步骤](../../security/fundamentals/steps-secure-identity.md)。

## <a name="configure-user-consent-to-applications"></a>配置用户对应用程序的同意
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>禁用或启用 Azure 门户的用户同意

可以使用 Azure 门户禁用或启用用户同意访问组织数据的应用程序的权限：

1. 以[全局管理员](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)身份登录到[Azure 门户](https://portal.azure.com)。
2. 选择**Azure 活动目录**，然后选择**企业应用程序**，然后**选择用户设置**。
3. 启用或禁用用户同意与控件标记**用户可以同意应用程序访问公司数据代表他们**。
4. （可选）配置[管理员同意请求工作流](configure-admin-consent-workflow.md)，以确保不允许同意应用的用户可以请求批准。

> [!TIP]
> 允许用户请求管理员审阅不允许用户同意的应用程序（例如，因为用户同意已被禁用，或者因为应用程序请求不允许用户授予的权限），请考虑[配置管理员同意工作流](configure-admin-consent-workflow.md)。

### <a name="disable-or-enable-user-consent-using-powershell"></a>使用 PowerShell 禁用或启用用户同意

您可以使用 Azure AD PowerShell v1 模块[（MSOnline](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)） 来启用或禁用用户同意访问组织数据的应用程序的能力。

1. 通过运行此 cmdlet 登录到您的组织：

    ```powershell
    Connect-MsolService
    ```

2. 通过运行此 cmdlet 检查是否启用了用户同意：

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. 启用或禁用用户同意。 例如，要禁用用户同意，请运行此 cmdlet：

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>配置组所有者同意访问组数据的应用

> [!IMPORTANT]
> 以下信息用于即将推出的功能，该功能将允许组所有者授予应用程序对其组数据的访问。 释放此功能时，默认情况下将启用此功能。 尽管此功能尚未广泛发布，但您可以使用这些说明在功能发布之前禁用该功能。

组所有者可以授权应用程序（例如，由第三方供应商发布的应用程序）访问组织与组关联的数据。 例如，团队所有者（是团队 Office 365 组的所有者）可以允许应用读取团队中的所有 Teams 消息，或列出组成员的基本配置文件。

> [!NOTE]
> 与此设置无关，始终允许组所有者将其他用户或应用直接添加为组所有者。

### <a name="configure-group-owner-consent-using-powershell"></a>使用 PowerShell 配置组所有者同意

您可以使用 Azure AD PowerShell 预览模块[（AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)） 来启用或禁用组所有者同意访问组织为其拥有的组访问组织数据的能力。

1. 确保使用[AzureAD 预览](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)模块（如果同时安装了[AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0)模块和[AzureAD 预览](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)模块，则此步骤非常重要）。

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. 连接到 Azure AD 电源外壳。

   ```powershell
   Connect-AzureAD
   ```

3. 检索租户中的 *"同意策略设置"目录设置*的当前值。 这要求检查是否创建了此功能的目录设置，如果没有，则使用相应目录设置模板中的值。

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

4. 了解设置值。 有两个设置值定义哪些用户能够允许应用访问其组的数据：

    | 设置       | 类型         | 说明  |
    | ------------- | ------------ | ------------ |
    | _启用组特定同意_   | Boolean |  指示是否允许组所有者授予特定于组的权限的标志。 |
    | _约束组特定同意组成员组 ID_ | Guid | 如果_启用组特定同意_设置为"True"，并且此值设置为组的对象 ID，则标识的组成员将有权向其拥有的组授予特定于组的权限。 |

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

## <a name="configure-risk-based-step-up-consent"></a>配置基于风险的逐步同意

基于风险的介入同意有助于减少用户接触恶意应用程序发出[非法同意请求](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)的风险。 如果 Microsoft 检测到有风险的最终用户同意请求，则请求将需要"逐步"才能进行管理员同意。 默认情况下启用此功能，但仅在启用最终用户同意时才会导致行为更改。

当检测到有风险的同意请求时，同意提示将显示一条消息，指示需要管理员批准。 如果启用[了管理员同意请求工作流](configure-admin-consent-workflow.md)，用户可以直接从同意提示将请求发送给管理员进行进一步审阅。 如果未启用，将显示以下消息：

* **AADSTS90094：**&lt;客户端AppDisplayName&gt;需要权限才能访问组织中只有管理员才能授予的资源。 请让管理员授予访问此应用的权限，否则你将无法使用该应用。

在这种情况下，还将记录审核事件，并记录"应用程序管理"类别、"同意应用程序"的活动类型和"检测到的 Risky 应用程序"的状态原因。

> [!IMPORTANT]
> 管理员应在批准之前仔细[评估所有同意请求](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent)，尤其是在 Microsoft 检测到风险时。

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>使用 PowerShell 禁用或重新启用基于风险的逐步同意

您可以使用 Azure AD PowerShell 预览模块[（AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)） 来禁用在 Microsoft 检测到风险的情况下所需的管理员同意的升步，或者如果以前已禁用风险，则重新启用该模块。

这可以使用与上面所示相同的步骤来[使用 PowerShell 配置组所有者同意](#configure-group-owner-consent-using-powershell)，但替换不同的设置值。 步骤有三个差异： 

1. 了解基于风险的升步同意的设置值：

    | 设置       | 类型         | 说明  |
    | ------------- | ------------ | ------------ |
    | _阻止用户同意为RiskyApps_   | Boolean |  指示检测到有风险请求时是否将阻止用户同意的标志。 |

2. 在步骤 3 中替换以下值：

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
3. 在步骤 5 中替换以下之一：

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>后续步骤

[配置管理员同意工作流](configure-admin-consent-workflow.md)

[了解如何管理对应用程序的同意和评估同意请求](manage-consent-requests.md)

[向应用程序授予租户范围的管理员许可](grant-admin-consent.md)

[Microsoft 标识平台中的权限和许可](../develop/active-directory-v2-scopes.md)

[StackOverflow 上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
