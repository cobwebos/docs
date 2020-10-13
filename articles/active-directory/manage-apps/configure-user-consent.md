---
title: 使用 Azure AD 配置最终用户如何对应用程序表示同意
description: 对于将有权访问你组织数据的应用程序，了解管理用户如何及何时对这些应用表示同意的方式。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: cff2af745e9b79f573aba02e0a9baefe4a5e45a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819260"
---
# <a name="configure-how-end-users-consent-to-applications"></a>配置最终用户如何对应用程序表示同意

你可以将应用程序与 Microsoft 标识平台集成，以允许用户使用其工作或学校帐户登录和访问你组织的数据，从而提供丰富的数据驱动体验。

用户必须先授权应用程序访问，然后应用程序才可访问你组织的数据。 权限不同，访问级别就不同。 默认情况下，允许所有用户同意应用程序获取无需管理员同意的权限。 例如，默认情况下，用户可同意允许应用访问其邮箱，但无法同意允许应用具有不受限制的访问权限来读写你组织中的所有文件。

通过允许用户向应用授予对数据的访问权限，用户可轻松获取有用的应用程序并提高工作效率。 但在某些情况下，如果不仔细监视和控制，该配置可能会带来风险。

> [!IMPORTANT]
> 为了降低恶意应用程序企图欺骗用户向其授予对你组织数据的访问权限的风险，我们建议仅允许用户同意[已验证的发布者](../develop/publisher-verification-overview.md)发布的应用程序。

## <a name="user-consent-settings"></a>用户同意设置

应用许可策略介绍了在应用可以许可之前必须满足的条件。 这些策略可能包括请求访问的应用程序的条件，以及该应用程序请求的权限。

通过选择适用于所有用户的应用许可策略，你可以设置限制，允许最终用户向应用授予许可的时间，以及需要他们请求管理员审查和批准的时间：

* **禁止用户同意** - 用户无法向应用程序授予权限。 用户可继续登录到他们之前同意或管理员代表他们同意的应用，但不可自行同意新权限或新应用。 只有已被授予权限的目录角色的用户才能同意新应用程序。

* **用户可以同意已验证的发布者或你的组织中的应用，但仅适用于你选择的权限** -所有用户只能同意已 [验证的发布者](../develop/publisher-verification-overview.md) 发布的应用和已在你的租户中注册的应用。 用户只能同意已分类为 "影响较小" 的权限。 您必须对 [权限进行分类](configure-permission-classifications.md) 以选择允许用户同意的权限。

* **用户可以同意所有应用** -此选项允许所有用户同意任何不需要管理员同意的任何应用程序的权限。

* **自定义应用同意策略** -若要更好地控制用户同意的条件，你可以 [创建自定义应用许可策略](manage-app-consent-policies.md#create-a-custom-app-consent-policy)，并将其配置为适用于用户同意。

# <a name="portal"></a>[门户](#tab/azure-portal)

若要通过 Azure 门户配置用户同意设置：

1. 以[全局管理员](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)的身份登录 [Azure 门户](https://portal.azure.com)。
1. 选择“Azure Active Directory” > “企业应用程序” > “同意和权限” > “用户同意设置”   。
1. 在“针对应用程序的用户同意”下，选择想要为所有用户配置的同意设置。
1. 选择“保存”以保存设置。

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="用户同意设置":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

你可以使用最新的 Azure AD PowerShell 预览版模块 [AzureADPreview](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)，以选择哪个应用许可策略控制用户对应用程序的同意。

#### <a name="disable-user-consent"></a>禁用用户同意

若要禁用用户同意，请设置控制用户同意为空的同意策略：

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

#### <a name="allow-user-consent-subject-to-an-app-consent-policy"></a>允许用户同意使用应用许可策略

若要允许用户许可，请选择应将哪个应用许可策略控制用户授权，以将同意授予应用：

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.{consent-policy-id}")
  ```

将替换 `{consent-policy-id}` 为要应用的策略的 ID。 你可以选择已创建的 [自定义应用许可策略](manage-app-consent-policies.md#create-a-custom-app-consent-policy) ，也可以从以下内置策略中进行选择：

| ID | 说明 |
|:---|:------------|
| microsoft-用户-默认-低 | **允许用户同意已验证的发布者对所选权限的应用**<br /> 仅允许对已验证的发布者和应用程序中已注册的应用程序的应用进行有限的用户同意，并且仅适用于分类为 "影响较低" 的权限。  (不要忘记对 [权限进行分类](configure-permission-classifications.md) ，以选择允许用户同意的权限。 )  |
| microsoft-用户-默认-旧式 | **允许用户同意应用**<br /> 此选项允许所有用户同意任何不需要管理员同意的任何应用程序的权限 |
  
例如，若要允许用户同意使用内置策略，请 `microsoft-user-default-low` 执行以下操作：

```powershell
Set-AzureADMSAuthorizationPolicy `
   -Id "authorizationPolicy" `
   -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.microsoft-user-default-low")
```

---

> [!TIP]
> [启用管理员许可工作流](configure-admin-consent-workflow.md) ，允许用户请求管理员审阅和批准用户不允许同意的应用程序，例如，当用户同意已禁用或应用程序请求用户不允许授予的权限时。

## <a name="risk-based-step-up-consent"></a>基于风险的逐步同意

基于风险的升级同意有助于减少用户遭受发出[非法同意请求](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)的恶意应用的攻击。 如果 Microsoft 检测到最终用户同意请求存在风险，该请求将需要“升级”，改为管理员同意。 此功能默认启用，但如果启用了最终用户同意，则它只会导致行为的更改。

当检测到同意请求存在风险时，同意提示将显示一条消息，指示需要管理员批准。 如果已启用[管理员同意请求工作流](configure-admin-consent-workflow.md)，则用户可将请求发送给管理员，使其直接从同意提示中进一步评审。 如果未启用，则将显示以下消息：

* **AADSTS90094：** &lt;clientAppDisplayName&gt; 需要访问你组织中只有管理员可批准的资源。 使用应用前，请先向管理员请求授予此应用的权限。

在这种情况下，还将记录一个审核事件，其类别为“ApplicationManagement”、活动类型为“同意应用程序”，状态原因为“检测到存在风险的应用程序”。

> [!IMPORTANT]
> 批准请求之前，管理员应仔细[评估所有同意请求](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent)，尤其是在 Microsoft 检测到风险时。

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>使用 PowerShell 禁用或重新启用基于风险的升级同意

可使用 Azure AD PowerShell 预览版模块 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true)，以禁用在 Microsoft 检测到风险时需要获得管理员同意的升级，或重新启用此功能（如果之前已禁用）。

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

    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```

1. 了解设置值：

    | 设置       | 类型         | 说明  |
    | ------------- | ------------ | ------------ |
    | BlockUserConsentForRiskyApps   | Boolean |  指示检测到请求存在风险时是否阻止用户同意的标志。 |

1. 更新所需配置的设置值：

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
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

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请访问以下链接：

* [配置用户同意设置](configure-user-consent.md)
* [管理应用同意策略](manage-app-consent-policies.md)
* [配置管理员同意工作流](configure-admin-consent-workflow.md)
* [了解如何管理对应用程序的同意并评估同意请求](manage-consent-requests.md)
* [向应用程序授予租户范围的管理许可](grant-admin-consent.md)
* [Microsoft 标识平台中的权限和许可](../develop/active-directory-v2-scopes.md)

获取帮助或查找问题的答案：
* [StackOverflow 上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
