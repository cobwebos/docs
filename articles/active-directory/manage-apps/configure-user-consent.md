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
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 0c9844d5e3f65dba5e51170367cfd16715a08883
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763459"
---
# <a name="configure-how-end-users-consent-to-applications"></a>配置最终用户如何对应用程序表示同意

你可以将应用程序与 Microsoft 标识平台集成，以允许用户使用其工作或学校帐户登录和访问你组织的数据，从而提供丰富的数据驱动体验。

用户必须先授权应用程序访问，然后应用程序才可访问你组织的数据。 权限不同，访问级别就不同。 默认情况下，允许所有用户同意应用程序获取无需管理员同意的权限。 例如，默认情况下，用户可同意允许应用访问其邮箱，但无法同意允许应用具有不受限制的访问权限来读写你组织中的所有文件。

通过允许用户向应用授予对数据的访问权限，用户可轻松获取有用的应用程序并提高工作效率。 但在某些情况下，如果不仔细监视和控制，该配置可能会带来风险。

## <a name="user-consent-settings"></a>用户同意设置

若要控制用户在哪些情况下可同意应用程序，请选择将应用于所有用户的同意策略。 下面是 3 个同意策略选项：

* **禁止用户同意** - 用户无法向应用程序授予权限。 用户可继续登录到他们之前同意或管理员代表他们同意的应用，但不可自行同意新权限或新应用。 只有被授予目录角色（该角色具有授予同意的权限）的用户才能同意新权限或新应用。

* **用户可同意已验证的发布者提供的应用，但应用仅能获取你选择的权限（预览）** - 所有用户只能同意由[已验证的发布者](../develop/publisher-verification-overview.md)发布的应用以及已在你的租户中注册的应用。 用户只能同意被分类为“影响较低”的权限。

  确保[对权限进行分类](#configure-permission-classifications-preview)，选择允许用户同意哪些权限。

* **用户可同意所有应用** - 此选项允许所有用户同意任意应用程序的任意权限，而无需管理员同意。 

   为了降低恶意应用程序企图欺骗用户向其授予对你组织数据的访问权限的风险，我们建议仅允许用户同意[已验证的发布者](../develop/publisher-verification-overview.md)发布的应用程序。

### <a name="configure-user-consent-settings-from-the-azure-portal"></a>通过 Azure 门户配置用户同意设置

若要通过 Azure 门户配置用户同意设置：

1. 以[全局管理员](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)的身份登录 [Azure 门户](https://portal.azure.com)。
1. 选择“Azure Active Directory” > “企业应用程序” > “同意和权限” > “用户同意设置”   。
1. 在“针对应用程序的用户同意”下，选择想要为所有用户配置的同意设置。
1. 选择“保存”以保存设置。

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="用户同意设置":::

> [!TIP]
> 请考虑[启用管理员同意工作流](configure-admin-consent-workflow.md)，以允许用户请求管理员评审和批准不允许用户同意的应用程序，例如当用户同意已被禁用或应用程序请求用户无法授予的权限时。

### <a name="configure-user-consent-settings-using-powershell"></a>使用 PowerShell 配置用户同意设置

可使用最新的 Azure AD PowerShell 预览版模块 [AzureADPreview](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)，选择使用哪个同意策略来控制用户对应用程序的同意。

* **禁止用户同意** - 若要禁止用户同意，请将控制用户同意的同意策略设置为空：

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

* **允许用户同意已验证的发布者提供的应用，但应用所获权限有限（预览）** - 若要允许使用有限制性的用户同意，仅同意已验证的发布者提供的应用以及在你的租户中注册的应用，并且应用仅获得你分类为“影响较低”的权限，那么请配置名为 `microsoft-user-default-low` 的内置同意策略：

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-low")
  ```

   请记得[对权限进行分类](#configure-permission-classifications-preview)，以选择允许用户同意哪些权限。

* **允许用户同意所有应用** - 若要允许用户同意所有应用：

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-legacy")
  ```

   此选项允许所有用户同意任意应用程序的任意权限，而无需管理员同意。 建议仅允许用户同意已验证的发布者提供的应用。

## <a name="configure-permission-classifications-preview"></a>配置权限分类（预览）

通过权限分类，可根据组织的策略和风险评估确定不同权限的影响。 例如，可使用同意策略中的权限分类来确定允许用户同意哪一组权限。

> [!NOTE]
> 目前仅支持“影响较低”这一权限分类。 只有无需管理员同意的委托权限可被分类为“影响较低”。

### <a name="classify-permissions-using-the-azure-portal"></a>使用 Azure 门户对权限进行分类

1. 以[全局管理员](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)的身份登录 [Azure 门户](https://portal.azure.com)。
1. 选择“Azure Active Directory” > “企业应用程序” > “同意和权限” > “权限分类”   。
1. 选择“添加权限”，再将一个权限分类为“影响较小”。 
1. 选择 API，然后选择委托的权限。

在下例中，我们对单一登录所需的最小一组权限进行了分类：

:::image type="content" source="media/configure-user-consent/permission-classifications.png" alt-text="权限分类":::

> [!TIP]
> 对于 Microsoft Graph API，执行基本单一登录所需的最低权限为 `openid`、`profile`、`User.Read` 和 `offline_access`。 通过这些权限，应用可读取已登录用户的个人资料详细信息，即使用户不再使用该应用，也可保留此访问。

### <a name="classify-permissions-using-powershell"></a>使用 PowerShell 对权限进行分类

可使用最新的 Azure AD PowerShell 预览版模块 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) 对权限进行分类。 权限分类是在发布权限的 API 的 ServicePrincipal 对象上配置的。

#### <a name="to-read-the-current-permission-classifications-for-an-api"></a>若要读取 API 的当前权限分类：

1. 检索 API 的 ServicePrincipal 对象。 在这里，我们将检索 Microsoft Graph API 的 ServicePrincipal 对象：

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. 读取 API 的委托权限分类：

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="to-classify-a-permission-as-low-impact"></a>若要将权限分类为“影响较低”：

1. 检索 API 的 ServicePrincipal 对象。 在这里，我们将检索 Microsoft Graph API 的 ServicePrincipal 对象：

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. 查找要对其分类的委托的权限：

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. 使用权限名称和 ID 设置权限分类：

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="to-remove-a-delegated-permission-classification"></a>若要删除委托权限分类：

1. 检索 API 的 ServicePrincipal 对象。 在这里，我们将检索 Microsoft Graph API 的 ServicePrincipal 对象：

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. 若要查找要删除的委托权限分类：

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. 请删除权限分类：

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>配置组所有者对应用访问组数据的同意

组所有者可授权应用程序（如第三方供应商发布的应用程序）访问你的组织中与组关联的数据。 例如，Microsoft Teams 中的团队所有者可允许应用读取团队中的所有 Teams 消息，或允许列出组成员的基本个人资料。

可配置允许哪些用户同意应用访问其组数据，你也可禁用此功能。

### <a name="configure-group-owner-consent-using-the-azure-portal"></a>使用 Azure 门户配置组所有者同意

1. 以[全局管理员](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)的身份登录 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory” > “企业应用程序” > “同意和权限” > “用户同意设置”   。
3. 在“针对应用访问数据的组所有者同意”下，选择要启用的选项。
4. 选择“保存”以保存设置。

在下例中，所有组所有者都可同意应用访问其组数据：

:::image type="content" source="media/configure-user-consent/group-owner-consent.png" alt-text="组所有者同意设置":::

### <a name="configure-group-owner-consent-using-powershell"></a>使用 PowerShell 配置组所有者同意

可使用 Azure AD PowerShell 预览版模块 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) 允许或禁止组所有者同意应用程序访问你组织的数据来查找他们拥有的组。

1. 请确保使用 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) 模块。 如果同时安装了 [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) 模块和 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) 模块，则此步骤非常重要。

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

## <a name="configure-risk-based-step-up-consent"></a>配置基于风险的升级同意

基于风险的升级同意有助于减少用户遭受发出[非法同意请求](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)的恶意应用的攻击。 如果 Microsoft 检测到最终用户同意请求存在风险，该请求将需要“升级”，改为管理员同意。 此功能默认启用，但如果启用了最终用户同意，则它只会导致行为的更改。

当检测到同意请求存在风险时，同意提示将显示一条消息，指示需要管理员批准。 如果已启用[管理员同意请求工作流](configure-admin-consent-workflow.md)，则用户可将请求发送给管理员，使其直接从同意提示中进一步评审。 如果未启用，则将显示以下消息：

* **AADSTS90094：** &lt;clientAppDisplayName&gt; 需要访问你组织中只有管理员可批准的资源。 使用应用前，请先向管理员请求授予此应用的权限。

在这种情况下，还将记录一个审核事件，其类别为“ApplicationManagement”、活动类型为“同意应用程序”，状态原因为“检测到存在风险的应用程序”。

> [!IMPORTANT]
> 批准请求之前，管理员应仔细[评估所有同意请求](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent)，尤其是在 Microsoft 检测到风险时。

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>使用 PowerShell 禁用或重新启用基于风险的升级同意

可使用 Azure AD PowerShell 预览版模块 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)，以禁用在 Microsoft 检测到风险时需要获得管理员同意的升级，或重新启用此功能（如果之前已禁用）。

为此，可按照与上述相同的步骤[使用 PowerShell 配置组所有者同意](#configure-group-owner-consent-using-powershell)，但替换为其他设置值。 步骤中有 3 点不同： 

1. 了解基于风险的升级同意的设置值：

    | 设置       | 类型         | 说明  |
    | ------------- | ------------ | ------------ |
    | BlockUserConsentForRiskyApps   | Boolean |  指示检测到请求存在风险时是否阻止用户同意的标志。 |

1. 在步骤 3 中替换为以下值：

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
    
1. 在步骤 5 中替换为以下值之一：

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请访问以下链接：

* [配置管理员同意工作流](configure-admin-consent-workflow.md)
* [了解如何管理对应用程序的同意并评估同意请求](manage-consent-requests.md)
* [向应用程序授予租户范围的管理许可](grant-admin-consent.md)
* [Microsoft 标识平台中的权限和许可](../develop/active-directory-v2-scopes.md)

获取帮助或查找问题的答案：
* [StackOverflow 上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
