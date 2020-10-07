---
title: 在 Azure AD 中管理应用许可策略
description: 了解如何管理内置和自定义应用许可策略，以控制何时可授予同意。
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
ms.openlocfilehash: fee727e16909355fe614b875ff61d4d38f7a98ce
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804304"
---
# <a name="manage-app-consent-policies"></a>管理应用许可策略

利用 Azure AD PowerShell，你可以查看和管理应用许可策略。

应用许可策略包括一个或多个 "包含" 条件集以及零个或多个 "排除" 条件集。 对于在应用许可策略中考虑的事件，该事件必须与 *至少* 一个 "包含" 条件集匹配，而且不能 *有* 多个 "排除" 条件集。

每个条件集包含多个条件。 对于与条件集匹配的事件，必须满足条件集中的 *所有* 条件。

ID 以 "microsoft-" 开头的应用许可策略是内置策略。 其中的某些内置策略用于现有的内置目录角色。 例如， `microsoft-application-admin` 应用许可策略描述允许应用程序管理员和云应用程序管理员角色向租户范围内的管理员授予许可的条件。 内置策略可用于自定义目录角色和配置用户同意设置，但不能进行编辑或删除。

## <a name="pre-requisites"></a>先决条件

1. 请确保使用 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) 模块。 如果同时安装了 [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0&preserve-view=true) 模块和 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) 模块，则此步骤非常重要。

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. 连接到 Azure AD PowerShell。

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>列出现有应用许可策略

最好先熟悉组织中现有的应用许可策略，这是一个好主意：

1. 列出所有应用许可政策：

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. 查看策略的 "包括" 条件集：

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -Id "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. 查看 "排除" 条件集：

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -Id "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>创建自定义应用许可策略

按照以下步骤创建自定义应用许可策略：

1. 创建新的空应用许可策略。

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. 添加 "包括" 条件集。

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   重复此步骤以添加其他 "include" 条件集。

1. （可选）添加 "排除" 条件集。

   ```powershell
   # Retrieve the service principal for the Azure Management API
   $azureApi = Get-AzureADServicePrincipal -Filter "servicePrincipalNames/any(n:n eq 'https://management.azure.com/')"

   # Exclude delegated permissions for the Azure Management API
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "excludes" `
       -PermissionType "delegated" `
       -ResourceApplication $azureApi.AppId
   ```

   重复此步骤以添加其他 "排除" 条件集。

创建应用许可策略后，你可以 [允许用户同意](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy) 使用此策略。

## <a name="delete-a-custom-app-consent-policy"></a>删除自定义应用许可策略

1. 下面演示了如何删除自定义应用许可策略。 **此操作不可撤消。**

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

> [!WARNING]
> 无法还原已删除的应用许可策略。 如果意外删除了自定义应用许可策略，你将需要重新创建该策略。

---

### <a name="supported-conditions"></a>支持的条件

下表提供了应用许可策略支持的条件的列表。

| 条件 | 说明|
|:---------------|:----------|
| PermissionClassification | 要授予的权限的 [权限分类](configure-permission-classifications.md) ，或 "全部" 与任何权限分类匹配 (包括未分类) 的权限。 默认值为 "all"。 |
| PermissionType | 要授予的权限的权限类型。 使用 "应用程序" 作为委托权限的应用程序权限 (例如) 或 "委派" 的应用程序角色。 <br><br>**注意**：值 "delegatedUserConsentable" 表示委派的权限，而 API 发布者未将其配置为需要管理员同意，此值可用于内置权限授予策略，但不能在自定义权限授予策略中使用。 必需。 |
| ResourceApplication | 资源应用程序的 **AppId** (例如，要为其授予权限的 API) ，或与任何资源应用程序或 API 匹配的 "任何"。 默认值为 "any"。 |
| 权限 | 与任何权限匹配的特定权限的权限 Id 列表，或单个值为 "全部" 的列表。 默认值为单一值 "all"。 <ul><li>可在 API 的 ServicePrincipal 对象的 **OAuth2Permissions** 属性中找到委托权限 id。</li><li>可在 API 的 ServicePrincipal 对象的 **AppRoles** 属性中找到应用程序权限 id。</li></ol> |
| ClientApplicationIds | 要与匹配的客户端应用程序的 **AppId** 值列表，或单个值为 "全部" 的列表，以匹配任何客户端应用程序。 默认值为单一值 "all"。 |
| ClientApplicationTenantIds | 在其中注册了客户端应用程序的 Azure Active Directory 租户 Id 的列表，或单个值为 "全部" 的列表与在任何租户中注册的客户端应用匹配的列表。 默认值为单一值 "all"。 |
| ClientApplicationPublisherIds | 已验证的客户端应用程序 [发布者](../develop/publisher-verification-overview.md) 的 MICROSOFT 合作伙伴网络 (MPN) id 的列表，或单个值为 "全部" 的列表，以与任何发布者的客户端应用匹配。 默认值为单一值 "all"。 |
| ClientApplicationsFromVerifiedPublisherOnly | 设置为 `$true` 仅在客户端应用程序中与 [已验证的发布服务器](../develop/publisher-verification-overview.md)匹配。 设置为 `$false` 可以匹配任何客户端应用，即使它没有经过验证的发布者。 默认值为 `$false`。 |

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请访问以下链接：

* [配置用户同意设置](configure-user-consent.md)
* [配置管理员同意工作流](configure-admin-consent-workflow.md)
* [了解如何管理对应用程序的同意并评估同意请求](manage-consent-requests.md)
* [向应用程序授予租户范围的管理许可](grant-admin-consent.md)
* [Microsoft 标识平台中的权限和许可](../develop/active-directory-v2-scopes.md)

获取帮助或查找问题的答案：
* [StackOverflow 上的 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
