---
title: 以编程方式创建 Azure Enterprise 订阅 | Microsoft Docs
description: 了解如何以编程方式创建其他 Azure Enterprise 或 Enterprise 开发/测试订阅。
services: azure-resource-manager
author: jlian
manager: jlian
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/30/2018
ms.author: jlian
ms.openlocfilehash: f55f878d53b3813ea2ff2510998d47820de76a6a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>以编程方式创建 Azure Enterprise 订阅（预览版）

Azure [企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 客户可以以编程方式创建 EA (MS-AZR-0017P) 和 EA 开发/测试 (MS-AZR-0148P) 订阅。 若要向另一个用户或服务主体授予创建对你的帐户收费的订阅的权限，请授予其对注册帐户的[基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-configure.md) 访问权限。 

> [!IMPORTANT]
> 通过此 API 创建的 Azure 订阅按该协议执行，根据该协议，你已从 Microsoft 或授权经销商处获取 Microsoft Azure 服务。 有关详细信息，请参阅 [Microsoft Azure 法律信息](https://azure.microsoft.com/support/legal/)。

本文内容：

> [!div class="checklist"]
> * 了解如何使用 Azure 资源管理器以编程方式创建订阅
> * 了解如何使用 RBAC 共享创建对你的 EA 帐户收费的订阅的能力

另请参阅 [GitHub 上的 .NET 示例代码](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)。

## <a name="ask-your-ea-enrollment-admin-to-add-you-as-account-owner"></a>请求 EA 注册管理员将你添加为帐户所有者

首先，请求注册管理员[将你添加为使用 EA 门户的帐户所有者](https://ea.azure.com/helpdocs/addNewAccount)（需要登录）。 按照收到的邀请电子邮件中的说明手动创建初始订阅。

> [!IMPORTANT]
> 必须先确认帐户所有权并手动创建初始 EA 订阅，然后才继续下一步操作。 只将帐户添加到注册是不够的。

## <a name="find-accounts-you-have-access-to"></a>查找有访问权限的帐户

将你作为帐户所有者添加到 Azure EA 注册后，Azure 使用帐户到注册的关系来确定订阅收费对象。 要创建订阅，请首先找到你拥有访问权限的注册帐户。 如果你当前是 EA 帐户所有者并且尝试使用此 API，Azure 会检查你是否满足以下条件：

- 你的帐户已添加到 EA 注册
- 你拥有一个或多个 EA 或 EA 开发/测试订阅，这意味着你已经至少经历过一次手动注册
- 你已登录到帐户所有者的主目录，默认在该目录创建订阅

如果满足上述三个条件，则会返回 `enrollmentAccount` 资源，并且你可以开始在该帐户下创建订阅。 在该帐户下创建的所有订阅均针对该帐户所在的 EA 注册进行计费。

# <a name="resttabrest"></a>[REST](#tab/rest)

列出所有注册帐户的请求：

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure 使用有访问权限的所有注册帐户列表做出响应：

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

使用 [Get-AzureRmEnrollmentAccount 命令](/powershell/module/azurerm.billing/get-azurermenrollmentaccount)列出你可以访问的所有注册帐户。

```azurepowershell-interactive
Get-AzureRmEnrollmentAccount
```

Azure 使用对象 ID 和帐户电子邮件地址列表做出响应。

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

使用 [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) 命令列出有访问权限的所有注册帐户。

```azurecli-interactive 
az billing enrollment-account list
```
Azure 使用对象 ID 和帐户电子邮件地址列表做出响应。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

---

使用 `principalName` 属性标识想要对其收取订阅费用的帐户。 将 `id` 用作在下一步骤中创建订阅的 `enrollmentAccount` 值。

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>在特定注册帐户下创建订阅 

下面的示例创建请求创建名为“开发团队订阅”的订阅，订阅产品为 MS-AZR-0017P（常规 EA）。 注册帐户是 `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx`（占位符值，这是一个 GUID），这是 SignUpEngineering@contoso.com 的注册帐户。它也选择性地添加两个用户作为订阅的 RBAC 所有者。

# <a name="resttabrest"></a>[REST](#tab/rest)

使用请求路径中 `enrollmentAccount` 的 `id` 创建订阅。

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| 元素名称  | 必选 | Type   | 说明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | 否      | String | 订阅的显示名称。 如果未指定，则设置为产品名称，例如“Microsoft Azure Enterprise”。                                 |
| `offerType`   | 是      | String | 订阅的产品。 EA 的两个选项是 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)（生产用）和 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)（开发/测试用，需要[使用 EA 门户启用](https://ea.azure.com/helpdocs/DevOrTestOffer)）。                |
| `owners`      | 否       | String | 希望在订阅创建时作为 RBAC 所有者添加到订阅上的任意用户的对象 ID。  |

在响应中，返回 `subscriptionOperation` 对象进行监视。 完成订阅创建后，`subscriptionOperation` 对象将返回有订阅 ID 的 `subscriptionLink` 对象。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

要使用此预览版模块，请先运行 `Install-Module AzureRM.Subscription -AllowPrerelease` 进行安装。 为了确保 `-AllowPrerelease` 有效，请从[获取 PowerShellGet 模块](/powershell/gallery/psget/get_psget_module)安装 PowerShellGet 最新版本。

使用 [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview) 并将 `enrollmentAccount` 对象 ID 作为创建新订阅的 `EnrollmentAccountObjectId` 参数。 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| 元素名称  | 必选 | Type   | 说明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | 否      | String | 订阅的显示名称。 如果未指定，则设置为产品名称，例如“Microsoft Azure Enterprise”。                                 |
| `OfferType`   | 是      | String | 订阅的产品。 EA 的两个选项是 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)（生产用）和 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)（开发/测试用，需要[使用 EA 门户启用](https://ea.azure.com/helpdocs/DevOrTestOffer)）。                |
| `EnrollmentAccountObjectId`      | 是       | String | 注册帐户的对象 ID，在该帐户下创建订阅并对其计费。 这是一个 GUID 值，可从 `Get-AzureRmEnrollmentAccount` 获取。 |
| `OwnerObjectId`      | 否       | String | 希望在订阅创建时作为 RBAC 所有者添加到订阅上的任意用户的对象 ID。  |
| `OwnerSignInName`    | 否       | String | 希望在订阅创建时作为 RBAC 所有者添加到订阅上的任意用户的电子邮件地址。 可以使用此参数，而不是 `OwnerObjectId`。|
| `OwnerApplicationId` | 否       | String | 希望在订阅创建时作为 RBAC 所有者添加到订阅上的任意服务主体的应用程序 ID。 可以使用此参数，而不是 `OwnerObjectId`。| 

要查看所有参数的完整列表，请参阅 [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview)。

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

要使用此预览版扩展，请先运行 `az extension add --name subscription` 进行安装。

使用 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) 并将 `enrollmentAccount` 对象 ID 作为创建新订阅的 `enrollment-account-object-id` 参数。

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| 元素名称  | 必选 | Type   | 说明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | 否      | String | 订阅的显示名称。 如果未指定，则设置为产品名称，例如“Microsoft Azure Enterprise”。                                 |
| `offer-type`   | 是      | String | 订阅的产品。 EA 的两个选项是 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)（生产用）和 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)（开发/测试用，需要[使用 EA 门户启用](https://ea.azure.com/helpdocs/DevOrTestOffer)）。                |
| `enrollment-account-object-id`      | 是       | String | 注册帐户的对象 ID，在该帐户下创建订阅并对其计费。 这是一个 GUID 值，可从 `az billing enrollment-account list` 获取。 |
| `owner-object-id`      | 否       | String | 希望在订阅创建时作为 RBAC 所有者添加到订阅上的任意用户的对象 ID。  |
| `owner-upn`    | 否       | String | 希望在订阅创建时作为 RBAC 所有者添加到订阅上的任意用户的电子邮件地址。 可以使用此参数，而不是 `owner-object-id`。|
| `owner-spn` | 否       | String | 希望在订阅创建时作为 RBAC 所有者添加到订阅上的任意服务主体的应用程序 ID。 可以使用此参数，而不是 `owner-object-id`。| 

要查看所有参数的完整列表，请参阅 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)。

----

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>委托访问使用 RBAC 的注册帐户的权限

若要为另一个用户或服务主体提供能够创建针对特定帐户的订阅的能力，请[提供他们在注册帐户范围内的 RBAC 所有者角色](../active-directory/role-based-access-control-manage-access-rest.md)。 下面的示例向 `principalId` 为 `<userObjectId>`（针对 SignUpEngineering@contoso.com）的租户用户提供注册帐户的所有者角色。 

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>"
  }
}
```
所有者角色在注册帐户范围成功分配时，Azure 使用角色分配信息做出响应：

```json
{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>",
    "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "createdOn": "2018-03-05T08:36:26.4014813Z",
    "updatedOn": "2018-03-05T08:36:26.4014813Z",
    "createdBy": "<assignerObjectId>",
    "updatedBy": "<assignerObjectId>"
  },
  "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "<roleAssignmentGuid>"
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 [New-AzureRmRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) 为其他用户所有者提供访问注册帐户的权限。

```azurepowershell-interactive
New-AzureRmRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az role assignment create](../active-directory/role-based-access-control-manage-access-azure-cli.md) 为其他用户所有者提供访问注册帐户的权限。

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

用户成为注册帐户的 RBAC 所有者之后，他们即可以编程方式在该帐户下创建订阅。 委托用户创建的订阅仍有作为服务管理员的原始帐户所有者，但它默认情况下也有作为所有者的委托用户。 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>使用活动日志审核订阅的创建者

若要通过此 API 跟踪创建的订阅，请使用[租户活动日志 API](/rest/api/monitor/tenantactivitylogs)。 目前不可以使用 PowerShell、CLI 或 Azure 门户跟踪订阅创建信息。

1. Azure AD 租户的租户管理员可以[提升访问权限](../active-directory/role-based-access-control-tenant-admin-access.md)，然后将一个“读者”角色分配给 `/providers/microsoft.insights/eventtypes/management` 范围内的审核用户。
1. 审核用户可以调用[租户活动日志 API](/rest/api/monitor/tenantactivitylogs) 查看订阅创建活动。 示例：

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> 若要快速从命令行调用此 API，请尝试使用 [ARMClient](https://github.com/projectkudu/ARMClient)。

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>对创建 Azure Enterprise 订阅的 API 限制

- 仅 Azure Enterprise 订阅可以使用此 API 进行创建。
- 每个帐户的订阅限制为 50 个。 超出此限制后，只能使用帐户中心创建订阅。
- 帐户下至少需要一个 EA 或 EA 开发/测试，这意味着帐户所有者至少经历过一次手动注册。
- 不是帐户所有者但通过 RBAC 添加到注册帐户的用户不能使用帐户中心创建订阅。
- 不能选择要在其中创建订阅的租户。 订阅始终在帐户所有者的主租户中进行创建。 若要将订阅移到不同的租户，请参阅[更改订阅租户](..\active-directory\active-directory-how-subscriptions-associated-directory.md)。

## <a name="next-steps"></a>后续步骤

* 有关使用 .NET 创建订阅的示例，请参阅 [GitHub 上的示例代码](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)。
* 若要了解有关 Azure 资源管理器及其 API 的详细信息，请参阅 [Azure 资源管理器概述](resource-group-overview.md)。
* 若要了解有关使用管理组管理大量订阅的详细信息，请参阅[使用 Azure 管理组组织资源](management-groups-overview.md)
* 若要查看有关大型组织订阅管理的综合最佳做法指南，请参阅 [Azure 企业基架 - 规范性订阅管理](resource-manager-subscription-governance.md)
