---
title: 以编程方式创建 Azure Enterprise 订阅 | Microsoft Docs
description: 了解如何以编程方式创建其他 Azure Enterprise 或 Enterprise 开发/测试订阅。
services: azure-resource-manager
author: jureid
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: jureid
ms.openlocfilehash: cf325b93c626e0c7f9584449154e2d531995cdc5
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204345"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>以编程方式创建 Azure Enterprise 订阅（预览版）

Azure [企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 客户可以以编程方式创建 EA (MS-AZR-0017P) 和 EA 开发/测试 (MS-AZR-0148P) 订阅。 本文介绍如何使用 Azure 资源管理器以编程方式创建订阅。

通过此 API 创建 Azure 订阅时，该订阅受你从 Microsoft 或授权经销商处获取 Microsoft Azure 服务时所签署协议的约束。 有关详细信息，请参阅 [Microsoft Azure 法律信息](https://azure.microsoft.com/support/legal/)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备组件

你想要创建下的订阅注册帐户，必须具有的所有者角色。 可通过两种方式来获取这些角色：

* 你的注册管理员可以[将你的帐户所有者](https://ea.azure.com/helpdocs/addNewAccount)（登录所需） 这使你的注册帐户所有者。 按照收到的邀请电子邮件中的说明手动创建初始订阅。 请先确认帐户所有权并手动创建初始 EA 订阅，然后继续下一步操作。 只将帐户添加到合约是不够的。

* 注册帐户的现有所有者可以[向你授予访问权限](grant-access-to-create-subscription.md)。 类似地，若要使用某个服务主体来创建 EA 订阅，必须[向该服务主体授予创建订阅的权限](grant-access-to-create-subscription.md)。

## <a name="find-accounts-you-have-access-to"></a>查找有访问权限的帐户

将你作为帐户所有者添加到 Azure EA 注册后，Azure 使用帐户到注册的关系来确定订阅收费对象。 在该帐户下创建的所有订阅均针对该帐户所在的 EA 注册进行计费。 若要创建订阅，必须传入拥有订阅所需的合约帐户和用户主体的相关值。 

若要运行以下命令，必须登录到帐户所有者的主目录（默认在该目录中创建订阅）。 

## <a name="resttabrest"></a>[REST](#tab/rest)

若要列出有权访问的所有注册帐户的请求：

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

使用 `principalName` 属性标识想要对其收取订阅费用的帐户。 复制`name`的该帐户。 例如，如果你想要创建下的订阅SignUpEngineering@contoso.com注册帐户，会将复制```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 这是注册帐户的对象 ID。 将此值粘贴到某个位置，以便可以使用它作为在下一步`enrollmentAccountObjectId`。

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

打开[Azure Cloud Shell](https://shell.azure.com/)并选择 PowerShell。

使用 [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) cmdlet 列出你可以访问的所有注册帐户。

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure 使用有权访问注册帐户列表做出响应：

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
使用 `principalName` 属性标识想要对其收取订阅费用的帐户。 复制`ObjectId`的该帐户。 例如，如果你想要创建下的订阅SignUpEngineering@contoso.com注册帐户，会将复制```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 将某个位置，以便可以使用它作为下一步中粘贴此对象 ID `enrollmentAccountObjectId`。

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) 命令列出有访问权限的所有注册帐户。

```azurecli-interactive 
az billing enrollment-account list
```

Azure 使用有权访问注册帐户列表做出响应：

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]
```

使用 `principalName` 属性标识想要对其收取订阅费用的帐户。 复制`name`的该帐户。 例如，如果你想要创建下的订阅SignUpEngineering@contoso.com注册帐户，会将复制```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 这是注册帐户的对象 ID。 将此值粘贴到某个位置，以便可以使用它作为在下一步`enrollmentAccountObjectId`。

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>在特定注册帐户下创建订阅

下面的示例创建名为的订阅*开发团队订阅*在上一步中选择的注册帐户。 订阅产品/服务*MS-条-0017 P* （常规 Microsoft 企业协议）。 它也选择性地添加两个用户作为订阅的 RBAC 所有者。

# <a name="resttabrest"></a>[REST](#tab/rest)

下面的请求，请替换`<enrollmentAccountObjectId>`与`name`复制的第一步 (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)。 如果你想要指定所有者，了解[如何获取用户对象 Id](grant-access-to-create-subscription.md#userObjectId)。

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

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

| 元素名称  | 必选 | Type   | 描述                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | 否      | String | 订阅的显示名称。 如果未指定，则将其设置为产品/服务名称，例如“Microsoft Azure Enterprise”。                                 |
| `offerType`   | 是      | String | 订阅的套餐。 EA 的两个选项是 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)（生产用）和 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)（开发/测试用，需要[使用 EA 门户启用](https://ea.azure.com/helpdocs/DevOrTestOffer)）。                |
| `owners`      | 否       | String | 希望在订阅创建时作为 RBAC 所有者添加到订阅上的任意用户的对象 ID。  |

在响应中，返回 `subscriptionOperation` 对象进行监视。 完成订阅创建后，`subscriptionOperation` 对象将返回有订阅 ID 的 `subscriptionLink` 对象。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

首先，通过运行安装此预览版模块`Install-Module Az.Subscription -AllowPrerelease`。 为了确保 `-AllowPrerelease` 有效，请从[获取 PowerShellGet 模块](/powershell/gallery/installing-psget)安装 PowerShellGet 最新版本。

运行[新建 AzSubscription](/powershell/module/az.subscription)下面，替换命令`<enrollmentAccountObjectId>`与`ObjectId`收集的第一步中 (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)。 如果你想要指定所有者，了解[如何获取用户对象 Id](grant-access-to-create-subscription.md#userObjectId)。

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| 元素名称  | 必选 | Type   | 描述                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | 否      | String | 订阅的显示名称。 如果未指定，则将其设置为产品/服务名称，例如“Microsoft Azure Enterprise”。                                 |
| `OfferType`   | 是      | String | 订阅的套餐。 EA 的两个选项是 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)（生产用）和 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)（开发/测试用，需要[使用 EA 门户启用](https://ea.azure.com/helpdocs/DevOrTestOffer)）。                |
| `EnrollmentAccountObjectId`      | 是       | String | 注册帐户的对象 ID，在该帐户下创建订阅并对其计费。 此值为从 `Get-AzEnrollmentAccount` 获取的 GUID。 |
| `OwnerObjectId`      | 否       | String | 希望在订阅创建时作为 RBAC 所有者添加到订阅上的任意用户的对象 ID。  |
| `OwnerSignInName`    | 否       | String | 希望在订阅创建时作为 RBAC 所有者添加到订阅上的任意用户的电子邮件地址。 可以使用此参数，而不是 `OwnerObjectId`。|
| `OwnerApplicationId` | 否       | String | 希望在订阅创建时作为 RBAC 所有者添加到订阅上的任意服务主体的应用程序 ID。 可以使用此参数，而不是 `OwnerObjectId`。 使用此参数时，服务主体必须具有[对该目录的读取访问权限](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)。| 

要查看所有参数的完整列表，请参阅 [New-AzSubscription](/powershell/module/az.subscription.preview)。

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

首先，通过运行安装此预览版扩展`az extension add --name subscription`。

运行[az 帐户创建](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)下面，替换命令`<enrollmentAccountObjectId>`与`name`第一步中复制 (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)。 如果你想要指定所有者，了解[如何获取用户对象 Id](grant-access-to-create-subscription.md#userObjectId)。

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| 元素名称  | 必选 | Type   | 描述                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | 否      | String | 订阅的显示名称。 如果未指定，则将其设置为产品/服务名称，例如“Microsoft Azure Enterprise”。                                 |
| `offer-type`   | 是      | String | 订阅的套餐。 EA 的两个选项是 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)（生产用）和 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)（开发/测试用，需要[使用 EA 门户启用](https://ea.azure.com/helpdocs/DevOrTestOffer)）。                |
| `enrollment-account-object-id`      | 是       | String | 注册帐户的对象 ID，在该帐户下创建订阅并对其计费。 此值为从 `az billing enrollment-account list` 获取的 GUID。 |
| `owner-object-id`      | 否       | String | 希望在订阅创建时作为 RBAC 所有者添加到订阅上的任意用户的对象 ID。  |
| `owner-upn`    | 否       | String | 希望在订阅创建时作为 RBAC 所有者添加到订阅上的任意用户的电子邮件地址。 可以使用此参数，而不是 `owner-object-id`。|
| `owner-spn` | 否       | String | 希望在订阅创建时作为 RBAC 所有者添加到订阅上的任意服务主体的应用程序 ID。 可以使用此参数，而不是 `owner-object-id`。 使用此参数时，服务主体必须具有[对该目录的读取访问权限](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)。| 

要查看所有参数的完整列表，请参阅 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)。

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>对创建 Azure Enterprise 订阅的 API 限制

- 仅 Azure Enterprise 订阅可以使用此 API 进行创建。
- 一个初始限制为 50 个订阅每个注册帐户，但你可以[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)若要将限制增加到 200。 之后，可以仅创建订阅通过帐户中心。
- 帐户下至少需要一个 EA 或 EA 开发/测试，这意味着帐户所有者至少经历过一次手动注册。
- 不是帐户所有者但通过 RBAC 添加到合约帐户的用户不能使用帐户中心创建订阅。
- 不能选择要在其中创建订阅的租户。 订阅始终在帐户所有者的主租户中进行创建。 若要将订阅移到不同的租户，请参阅[更改订阅租户](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。

## <a name="next-steps"></a>后续步骤

* 有关使用 .NET 创建订阅的示例，请参阅 [GitHub 上的示例代码](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)。
* 创建订阅以后，即可将该权限授予其他用户和服务主体。 有关详细信息，请参阅[授予创建 Azure Enterprise 订阅（预览版）所需的访问权限](grant-access-to-create-subscription.md)。
* 要详细了解如何使用管理组管理大量订阅，请参阅[使用 Azure 管理组整理资源](management-groups-overview.md)
