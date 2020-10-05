---
title: 以编程方式创建 Azure 订阅
description: 了解如何以编程方式创建其他 Azure 订阅。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/26/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 62989c21333e53fcb58b4b637802c8b697ae970e
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371433"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>以编程方式创建 Azure 订阅（预览版）

具有[企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)、[Microsoft 客户协议 (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) 或 [Microsoft 合作伙伴协议 (MPA)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) 计费帐户的 Azure 客户可以通过编程方式创建订阅。 本文介绍如何使用 Azure 资源管理器以编程方式创建订阅。

以编程方式创建 Azure 订阅时，该订阅受你从 Microsoft 或授权经销商处获取 Azure 服务时所签署协议的约束。 有关详细信息，请参阅 [Microsoft Azure 法律信息](https://azure.microsoft.com/support/legal/)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>为 EA 计费帐户创建订阅

使用以下各部分中的信息创建 EA 订阅。

### <a name="prerequisites"></a>先决条件

必须在注册帐户上具有所有者角色才能创建订阅。 可通过两种方式获取角色：

* 注册的企业管理员可以[将你设为帐户所有者](https://ea.azure.com/helpdocs/addNewAccount)（需要登录），这使得你成为注册帐户的所有者。

* 注册帐户的现有所有者可以[向你授予访问权限](grant-access-to-create-subscription.md)。 类似地，若要使用某个服务主体来创建 EA 订阅，则必须[向该服务主体授予创建订阅的权限](grant-access-to-create-subscription.md)。

### <a name="find-accounts-you-have-access-to"></a>查找有访问权限的帐户

将你添加到与帐户所有者关联的注册帐户后，Azure 通过帐户到注册关系确定订阅收费对象。 在该帐户下创建的所有订阅均向该帐户所在的 EA 注册收费。 若要创建订阅，必须传入拥有订阅所需的合约帐户和用户主体的相关值。

若要运行以下命令，必须登录到帐户所有者的主目录（默认在该目录中创建订阅）。**

### <a name="rest"></a>[REST](#tab/rest)

请求列出你有权访问的所有注册帐户：

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

API 响应列出你有权访问的所有注册帐户：

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

使用 `principalName` 属性标识想要对其收取订阅费用的帐户。 复制该帐户的 `name`。 例如，如果要在 SignUpEngineering@contoso.com 注册帐户下创建订阅，则复制 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 此标识符是注册帐户的对象 ID。 将该值粘贴到某个位置，以便在下一步中将其用作 `enrollmentAccountObjectId`。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

打开 [Azure Cloud Shell](https://shell.azure.com/) 并选择 PowerShell。

使用 [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) cmdlet 列出你可以访问的所有注册帐户。

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure 使用你有权访问的注册帐户列表做出响应：

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
使用 `principalName` 属性标识想要对其收取订阅费用的帐户。 复制该帐户的 `ObjectId`。 例如，如果要在 SignUpEngineering@contoso.com 注册帐户下创建订阅，则复制 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 将该对象 ID 粘贴到某个位置，以便在下一步中将其用作 `enrollmentAccountObjectId`。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) 命令列出有访问权限的所有注册帐户。

```azurecli-interactive
az billing enrollment-account list
```

Azure 使用你有权访问的注册帐户列表做出响应：

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

使用 `principalName` 属性标识想要对其收取订阅费用的帐户。 复制该帐户的 `name`。 例如，如果要在 SignUpEngineering@contoso.com 注册帐户下创建订阅，则复制 ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```。 此标识符是注册帐户的对象 ID。 将该值粘贴到某个位置，以便在下一步中将其用作 `enrollmentAccountObjectId`。

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>在特定注册帐户下创建订阅

以下示例在上一步选择的注册帐户中创建名为 Dev Team Subscription 的订阅。 订阅产品/服务为 MS-AZR-0017P（常规 Microsoft 企业协议）。 它也选择性地添加两个用户作为订阅的 Azure RBAC 所有者。

### <a name="rest"></a>[REST](#tab/rest)

发出以下请求，将 `<enrollmentAccountObjectId>` 替换为从第一步复制的 `name` (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)。 如果要指定所有者，请了解[如何获取用户对象 ID](grant-access-to-create-subscription.md#userObjectId)。

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

| 元素名称  | 必选 | 类型   | 说明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | 否      | 字符串 | 订阅的显示名称。 如果未指定，则将其设置为产品/服务名称，例如“Microsoft Azure Enterprise”。                                 |
| `offerType`   | 是      | 字符串 | 订阅的套餐。 EA 的两个选项是 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)（生产用）和 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)（开发/测试用，需要[使用 EA 门户启用](https://ea.azure.com/helpdocs/DevOrTestOffer)）。                |
| `owners`      | 否       | 字符串 | 希望在订阅创建时作为 Azure RBAC 所有者添加到订阅上的任意用户的对象 ID。  |

在响应中，作为标头 `Location` 的一部分，你返回一个可以查询订阅创建操作状态的 URL。 完成订阅创建后，`Location` URL 上的 GET 将返回 `subscriptionLink` 对象，该对象具有订阅 ID。 有关更多详细信息，请参阅[订阅 API 文档](/rest/api/subscription/)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要安装包含 `New-AzSubscription` cmdlet 的模块的最新版本，请运行 `Install-Module Az.Subscription`。 若要安装最新版本的 PowerShellGet，请参阅[获取 PowerShellGet 模块](/powershell/scripting/gallery/installing-psget)。

运行以下 [New-AzSubscription](/powershell/module/az.subscription) 命令，并将 `<enrollmentAccountObjectId>` 替换为第一步中收集的 `ObjectId` (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)。 如果要指定所有者，请了解[如何获取用户对象 ID](grant-access-to-create-subscription.md#userObjectId)。

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| 元素名称  | 必选 | 类型   | 说明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | 否      | 字符串 | 订阅的显示名称。 如果未指定，则将其设置为产品/服务名称，例如“Microsoft Azure Enterprise”。                                 |
| `OfferType`   | 是      | 字符串 | 订阅的套餐。 EA 的两个选项是 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)（生产用）和 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)（开发/测试用，需要[使用 EA 门户启用](https://ea.azure.com/helpdocs/DevOrTestOffer)）。                |
| `EnrollmentAccountObjectId`      | 是       | 字符串 | 注册帐户的对象 ID，在该帐户下创建订阅并对其计费。 此值为从 `Get-AzEnrollmentAccount` 获取的 GUID。 |
| `OwnerObjectId`      | 否       | 字符串 | 希望在订阅创建时作为 Azure RBAC 所有者添加到订阅上的任意用户的对象 ID。  |
| `OwnerSignInName`    | 否       | 字符串 | 希望在订阅创建时作为 Azure RBAC 所有者添加到订阅上的任意用户的电子邮件地址。 可以使用此参数，而不是 `OwnerObjectId`。|
| `OwnerApplicationId` | 否       | 字符串 | 希望在订阅创建时作为 Azure RBAC 所有者添加到订阅上的任意服务主体的应用程序 ID。 可以使用此参数，而不是 `OwnerObjectId`。 使用此参数时，服务主体必须具有[对该目录的读取访问权限](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)。|

要查看所有参数的完整列表，请参阅 [New-AzSubscription](/powershell/module/az.subscription/New-AzSubscription)。


### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

首先，通过运行 `az extension add --name subscription` 安装此预览版扩展。

运行以下 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) 命令，并将 `<enrollmentAccountObjectId>` 替换为第一步中收集的 `name` (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)。 如果要指定所有者，请了解[如何获取用户对象 ID](grant-access-to-create-subscription.md#userObjectId)。

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| 元素名称  | 必选 | 类型   | 说明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | 否      | 字符串 | 订阅的显示名称。 如果未指定，则将其设置为产品/服务名称，例如“Microsoft Azure Enterprise”。                                 |
| `offer-type`   | 是      | 字符串 | 订阅的套餐。 EA 的两个选项是 [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/)（生产用）和 [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/)（开发/测试用，需要[使用 EA 门户启用](https://ea.azure.com/helpdocs/DevOrTestOffer)）。                |
| `enrollment-account-object-id`      | 是       | 字符串 | 注册帐户的对象 ID，在该帐户下创建订阅并对其计费。 此值为从 `az billing enrollment-account list` 获取的 GUID。 |
| `owner-object-id`      | 否       | 字符串 | 希望在订阅创建时作为 Azure RBAC 所有者添加到订阅上的任意用户的对象 ID。  |
| `owner-upn`    | 否       | 字符串 | 希望在订阅创建时作为 Azure RBAC 所有者添加到订阅上的任意用户的电子邮件地址。 可以使用此参数，而不是 `owner-object-id`。|
| `owner-spn` | 否       | 字符串 | 希望在订阅创建时作为 Azure RBAC 所有者添加到订阅上的任意服务主体的应用程序 ID。 可以使用此参数，而不是 `owner-object-id`。 使用此参数时，服务主体必须具有[对该目录的读取访问权限](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole)。|

要查看所有参数的完整列表，请参阅 [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create)。

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>对创建 Azure Enterprise 订阅的 API 限制

- 仅 Azure Enterprise 订阅可以使用此 API 进行创建。
- 每个注册帐户的订阅限制为 2000 个。 超过此限制后，只能在 Azure 门户中为该帐户创建更多订阅。 如果想要通过 API 创建更多订阅，请创建另一个注册帐户。
- 不是帐户所有者但通过 Azure RBAC 添加到注册帐户的用户不能在 Azure 门户中创建订阅。
- 不能选择要在其中创建订阅的租户。 订阅始终在帐户所有者的主租户中进行创建。 若要将订阅移到不同的租户，请参阅[更改订阅租户](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。


## <a name="create-subscriptions-for-an-mca-account"></a>为 MCA 帐户创建订阅

### <a name="prerequisites"></a>先决条件

必须在发票科目上具有所有者、参与者或 Azure 订阅创建者角色，或者在计费对象信息或计费帐户上具有所有者或参与者角色才能创建订阅。 有关详细信息，请参阅[订阅计费角色和任务](understand-mca-roles.md#subscription-billing-roles-and-tasks)。

下面显示的示例使用 REST API。 目前，PowerShell 和 Azure CLI 不受支持。

### <a name="find-billing-accounts-that-you-have-access-to"></a>查找你有权访问的计费帐户

发出以下请求，以列出所有计费帐户。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
API 响应列出你有权访问的计费帐户。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
使用 `displayName` 属性来标识要为其创建订阅的计费帐户。 确保帐户的 agreeementType 为 MicrosoftCustomerAgreement。 复制帐户的 `name`。  例如，如果要为 `Contoso` 计费帐户创建订阅，则复制 `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`。 将该值粘贴到某个位置，以便在下一步使用它。

### <a name="find-invoice-sections-to-create-subscriptions"></a>查找发票科目以创建订阅

订阅费用在计费对象信息发票的某个科目中显示。 使用以下 API 获取你有权在其上创建 Azure 订阅的发票科目和计费对象信息的列表。

发出以下请求，将 `<billingAccountName>` 替换为从第一步复制的 `name` (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```)。

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
API 响应列出你有权在其上创建订阅的所有发票科目及其计费对象信息：

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}

```

使用 `invoiceSectionDisplayName` 属性来标识要为其创建订阅的发票科目。 复制发票科目的 `invoiceSectionId`、`billingProfileId` 和其中一个 `skuId`。 例如，如果要为 `Development` 发票科目创建类型为 `Microsoft Azure plan` 的订阅，则复制 `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`、`/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` 和 `0001`。 将这些值粘贴到某个位置，以便在下一步中使用它们。

### <a name="create-a-subscription-for-an-invoice-section"></a>为发票科目创建订阅

以下示例为 Development 发票科目创建类型为 Microsoft Azure 计划的名为 Dev Team subscription 的订阅。 订阅将向 Contoso finance 的计费对象信息收费，并在发票的 Development 科目显示。

发出以下请求，将 `<invoiceSectionId>` 替换为从第二步复制的 `invoiceSectionId` (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```)。 需要将第二步中复制的 `billingProfileId` 和 `skuId` 传递到 API 的请求参数中。 如果要指定所有者，请了解[如何获取用户对象 ID](grant-access-to-create-subscription.md#userObjectId)。

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| 元素名称  | 必选 | 类型   | 说明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | 是      | 字符串 | 订阅的显示名称。|
| `billingProfileId`   | 是      | 字符串 | 用于计收订阅使用费的计费对象信息的 ID。  |
| `skuId` | 是      | 字符串 | 确定 Azure 计划类型的 SKU ID。 |
| `owners`      | 否       | 字符串 | 希望在订阅创建时作为 Azure RBAC 所有者添加到订阅上的任何用户或服务主体的对象 ID。  |
| `costCenter` | 否      | 字符串 | 与订阅关联的成本中心。 它在使用情况 csv 文件中显示。 |
| `managementGroupId` | 否      | 字符串 | 订阅将添加到其中的管理组的 ID。 若要获取管理组列表，请参阅[管理组 - 列表 API](/rest/api/resources/managementgroups/list)。 使用 API 中管理组的 ID。 |

在响应中，返回 `subscriptionCreationResult` 对象进行监视。 完成订阅创建后，`subscriptionCreationResult` 对象将返回有订阅 ID 的 `subscriptionLink` 对象。

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>为 MPA 计费帐户创建订阅

### <a name="prerequisites"></a>先决条件

必须在组织的云解决方案提供商帐户中具有全局管理员或管理员代理角色，才能为计费帐户创建订阅。 有关详细信息，请参阅[合作伙伴中心 - 为用户分配角色和权限](/partner-center/permissions-overview)。

下面显示的示例使用 REST API。 目前，PowerShell 和 Azure CLI 不受支持。

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>查找你有权访问的计费帐户

发出以下请求，以列出你有权访问的所有计费帐户。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
API 响应列出计费帐户。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
使用 `displayName` 属性来标识要为其创建订阅的计费帐户。 确保帐户的 agreeementType 为 MicrosoftPartnerAgreement。 复制帐户的 `name`。 例如，如果要为 `Contoso` 计费帐户创建订阅，则复制 `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`。 将该值粘贴到某个位置，以便在下一步使用它。

### <a name="find-customers-that-have-azure-plans"></a>查找具有 Azure 计划的客户

发出以下请求，将 `<billingAccountName>` 替换为从第一步复制的 `name` (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```)，以列出你可以为其创建 Azure 订阅的计费帐户中的所有客户。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
API 响应列出具有 Azure 计划的计费帐户中的客户。 你可以为这些客户创建订阅。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}

```

使用 `displayName` 属性来标识要为其创建订阅的客户。 复制客户的 `id`。 例如，如果要为 `Fabrikam toys` 创建订阅，则复制 `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`。 将该值粘贴到某个位置，以便在后面的步骤中使用它。

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>对于间接提供商可选：获取客户的经销商

如果你是 CSP 双层模型中的间接提供商，则可以在为客户创建订阅时指定经销商。

发出以下请求，将 `<customerId>` 替换为从第二步复制的 `id` (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)，以列出客户可用的所有经销商。

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
API 响应列出客户的经销商：

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```
使用 `description` 属性来标识将与订阅关联的经销商。 复制经销商的 `resellerId`。 例如，如果要关联 `Wingtip`，则复制 `3xxxxx`。 将该值粘贴到某个位置，以便在下一步使用它。

### <a name="create-a-subscription-for-a-customer"></a>为客户创建订阅

以下示例为 Fabrikam Toys 创建名为 Dev Team subscription 的订阅，并将 Wingtip 经销商与该订阅相关联。 T

发出以下请求，将 `<customerId>` 替换为从第二步复制的 `id` (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```)。 将从第二步中复制的可选 resellerId 传递到 API 的请求参数中。

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| 元素名称  | 必选 | 类型   | 说明                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | 是      | 字符串 | 订阅的显示名称。|
| `skuId` | 是      | 字符串 | Azure 计划的 SKU ID。 将 0001 用于 Microsoft Azure 计划类型的订阅 |
| `resellerId`      | 否       | 字符串 | 将与订阅关联的经销商的 MPN ID。  |

在响应中，返回 `subscriptionCreationResult` 对象进行监视。 完成订阅创建后，`subscriptionCreationResult` 对象将返回有订阅 ID 的 `subscriptionLink` 对象。

## <a name="next-steps"></a>后续步骤

* 有关使用 .NET 创建企业协议 (EA) 订阅的示例，请参阅 [GitHub 上的示例代码](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core)。
* 创建订阅以后，即可将该权限授予其他用户和服务主体。 有关详细信息，请参阅[授予创建 Azure Enterprise 订阅（预览版）所需的访问权限](grant-access-to-create-subscription.md)。
* 要详细了解如何使用管理组管理大量订阅，请参阅[使用 Azure 管理组整理资源](../../governance/management-groups/overview.md)