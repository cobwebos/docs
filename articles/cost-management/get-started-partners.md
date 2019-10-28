---
title: 适用于合作伙伴的 Azure 成本管理入门
description: 本文介绍合作伙伴如何使用 Azure 成本管理功能，以及如何为客户提供成本管理访问权限。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/22/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 6d59964013a2631430ecd7e46d1ce0f6be60a05f
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802038"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>适用于合作伙伴的 Azure 成本管理入门

对于已将客户载入到 Microsoft 客户协议的合作伙伴，Azure 成本管理是本机可用的。 本文介绍合作伙伴如何使用[Azure 成本管理](https://docs.microsoft.com/azure/cost-management/)功能。 还介绍了合作伙伴如何为其客户实现成本管理访问权限。 客户可在其 CSP 合作伙伴启用后使用成本管理功能。

CSP 合作伙伴使用成本管理来：

- 了解已开票的成本，并将成本与客户、订阅、资源组和服务相关联。
- 在[成本分析](quick-acm-cost-analysis.md)中获得 Azure 成本的直观视图，并通过功能按客户、订阅、资源组、资源、计量、服务及许多其他维度分析成本。
- 查看在成本分析中应用了合作伙伴挣贷款（PEC）的资源成本。
- 当成本超出预算时，使用编程[预算](tutorial-acm-create-budgets.md)和警报设置通知和自动化。
- 启用 Azure 资源管理器策略，以提供对成本管理数据的客户访问。 然后，客户可以使用即用即[付费率](https://azure.microsoft.com/pricing/calculator/)查看其订阅的消耗成本数据。

以下示例显示了所有客户的成本。
显示所有客户的成本的 ![示例](./media/get-started-partners/customer-costs1.png)

下面是一个显示单个客户的成本的示例。
显示单个客户的成本的 ![示例](./media/get-started-partners/customer-costs2.png)

还可通过 REST Api 使用 Azure 成本管理中提供的所有功能。 使用 Api 自动执行成本管理任务。

## <a name="prerequisites"></a>必备组件

Azure 成本管理要求对计费帐户或订阅具有 "读取" 权限。 可以在资源的任何级别授予访问权限，将计费帐户或管理组向下授予管理应用的各个资源组。 有关为计费帐户启用和分配对 Azure 成本管理的访问的详细信息，请参阅[分配用户角色和权限](/partner-center/permissions-overview)。 **全局管理员**和**管理代理**角色可以管理计费帐户的成本。

若要查看受支持的帐户类型的完整列表，请参阅[了解成本管理数据](understand-cost-mgt-data.md)。


## <a name="how-cost-management-uses-scopes"></a>成本管理如何使用作用域

作用域是你在其中管理计费数据、拥有特定于付款的角色、查看发票和执行常规帐户管理的位置。 计费和帐户角色与使用 RBAC 的资源管理的范围分开管理。 若要明确区分不同范围的意图，包括访问控制差异，它们分别称为计费范围和 RBAC 范围。

若要了解计费范围和 RBAC 范围以及成本管理如何与作用域一起使用，请参阅[了解和使用作用域](understand-work-scopes.md)。

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>通过合作伙伴租户计费范围管理成本

将客户载入到 Microsoft 客户协议后，你的租户中将提供以下_计费范围_。 使用范围来管理成本管理中的成本。

### <a name="billing-account-scope"></a>计费帐户范围

使用计费帐户范围查看所有客户和计费配置文件中的税前成本。 仅在 Microsoft 客户协议上为客户的基于消耗的产品显示发票成本。 但是，对于在 Microsoft 客户协议和 CSP 提议上购买的基于购买的产品，会显示发票成本。 目前，在范围内查看成本的默认货币为美元。 作用域的预算集也以 USD 为单位。

无论使用哪种不同的客户计费货币，合作伙伴都使用计费帐户范围来设置预算，并跨其客户、订阅、资源和资源组来管理成本。

合作伙伴还可以在成本分析视图中通过特定的计费币种来筛选客户的成本。 选择 "**实际成本**" 列表以查看受支持的客户计费币种的成本。

![显示货币的实际成本选择的示例](./media/get-started-partners/actual-cost-selector.png)

使用计费范围中的[摊销成本视图](quick-acm-cost-analysis.md#customize-cost-views)来查看预订期限内的预留实例摊销成本。

### <a name="billing-profile-scope"></a>计费配置文件范围

使用计费配置文件范围，以在所有产品和发票中包含的订阅的所有客户的计费币种内查看税前成本。 您可以使用**InvoiceID**筛选器来筛选特定发票的计费配置文件中的成本。 筛选器显示特定发票的消耗和产品采购成本。 您还可以筛选发票上特定客户的成本，以查看税前成本。

将客户加入 Microsoft 客户协议后，您将收到一张发票，其中包含这些客户在 Microsoft 客户协议上的所有产品（消耗、购买和权利）的所有费用。 如果以相同的货币进行计费，这些发票还包括有关授权和购买产品的费用，如 SaaS、Azure Marketplace 以及仍处于 CSP 提供的客户的预订。

为了帮助根据客户发票来协调费用，可以使用计费配置文件范围来查看为客户的发票支付的所有成本。 与发票一样，范围显示新的 Microsoft 客户协议中每个客户的成本。 范围还显示了仍在当前 CSP 提议中的客户授权产品的每个费用。

计费配置文件和计费帐户范围是唯一适用的作用域，这些范围显示了授权和基于采购的产品（如 Azure Marketplace 和预订购买）的费用。

计费配置文件定义发票中包含的订阅。 计费配置文件在功能上等同于企业协议注册。 计费配置文件是生成发票的作用域。

目前，客户的计费币种是在计费配置文件范围内查看成本时的默认货币。 计费配置文件范围内的预算设置为计费货币。

合作伙伴可以使用范围来协调发票。 而且，它们使用范围为以下各项设置计费货币中的预算：

- 特定筛选的发票
- 客户
- Subscription
- Resource group
- 资源
- Azure 服务
- 计量
- ResellerMPNID

### <a name="customer-scope"></a>客户范围

合作伙伴使用范围来管理与载入 Microsoft 客户协议的客户关联的成本。 范围允许合作伙伴查看特定客户的税前成本。 你还可以为特定的订阅、资源组或资源筛选税前成本。

客户范围不包括当前 CSP 提供的客户。 此范围仅包括具有 Microsoft 客户协议的客户。 在应用客户筛选器时，适用于当前 CSP 的授权成本（而非 Azure 使用情况）提供了计费帐户和计费配置文件范围内的客户。

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>在成本管理中合作伙伴访问计费范围

只有拥有**全局管理员**和**管理代理**角色的用户才能直接在合作伙伴的 Azure 租户中管理和查看计费帐户、计费配置文件和客户的成本。 有关合作伙伴中心角色的详细信息，请参阅[分配用户角色和权限](/partner-center/permissions-overview)。

## <a name="enable-cost-management-in-the-customer-tenant"></a>启用客户租户中的成本管理

合作伙伴可以在客户载入 Microsoft 客户协议后启用对成本管理的访问。 然后，合作伙伴可以启用允许客户查看按现用现付零售费率计算的成本的策略。 在 RBAC 订阅和资源组范围内，按客户的计费币种显示成本。

当伙伴启用了成本可见性策略时，拥有 Azure 资源管理器访问权限的任何用户都可以按现用现付费率管理和分析成本。 有效地，具有对 Azure 订阅的合适 RBAC 访问权限的分销商和客户可以查看成本。

无论采用哪种策略，合作伙伴都可以查看订阅和资源组的访问权限。

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>启用策略以查看 Azure 使用费用

合作伙伴可以使用以下信息来为其客户查看 Azure 使用情况收费。

在 Azure 门户中，登录到合作伙伴租户，然后单击 "**成本管理 + 计费**"。 选择计费帐户，然后单击 "**客户**"。 客户列表与计费帐户关联。

在 "客户" 列表中，选择要允许其查看成本的客户。

![选择成本管理中的客户](./media/get-started-partners/customer-list.png)

在 "**设置**" 下，单击 "**策略**"。

对于与所选客户的订阅相关联的**Azure 使用**费用，将显示当前成本可见性策略。
![策略以允许客户查看即用即付费用](./media/get-started-partners/cost-management-billing-policies.png)

当策略设置为 "**否**" 时，Azure 成本管理不适用于与客户关联的订阅用户。 默认情况下，默认情况下，为所有订阅用户禁用成本可见性策略。

当成本策略设置为 **"是"** 时，与客户租户关联的订阅用户可以按即用即付费率查看使用量。

启用成本可见性策略后，具有订阅使用情况的所有服务都按现用现付费率显示成本。 预订使用情况在实际和摊销成本上显示为零。 采购和权利不关联到特定订阅。 因此，不会在订阅范围中显示购买。

若要查看客户租户的成本，请打开成本管理 + 计费，然后单击 "计费帐户"。 在计费帐户列表中，单击计费帐户。

![选择计费帐户](./media/get-started-partners/select-billing-account.png)

在 "**计费**" 下，单击 " **Azure 订阅**"，然后单击客户。

![选择 Azure 订阅客户](./media/get-started-partners/subscriptions-select-customer.png)

单击 "**成本分析**" 并开始查看成本。
现在，按即用即付费率的成本对订阅和资源组 RBAC 范围提供成本分析、预算和警报。

![以客户身份查看成本分析 ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

RBAC 范围内的预留实例的分期查看和实际成本显示零收费。 预订实例成本仅显示在进行购买的计费范围中。

## <a name="analyze-costs-in-cost-analysis"></a>分析成本分析中的成本

合作伙伴可以探索和分析客户对于特定客户或发票的成本分析成本。 使用筛选器和分组依据功能可以按多个字段来分析成本，包括：

| **字段** | **说明** |
| --- | --- |
| PartnerTenantID | 合作伙伴的 Azure Active Directory 租户的标识符 |
| PartnerName | Azure Active Directory 租户的合作伙伴的名称 |
| CustomerTenantID | 客户订阅的 Azure Active Directory 租户的标识符 |
| CustomerName | 包含客户订阅的 Azure Active Directory 租户的名称 |
| ResellerMPNID | 与订阅关联的分销商的 MPNID |
| 订阅 ID | Azure 订阅的唯一 Microsoft 生成的标识符 |
| subscriptionName | Azure 订阅的名称 |
| billingProfileID | 计费配置文件的标识符。 它采用单个计费货币跨客户对发票中的成本进行分组。
| invoiceID | 显示特定交易记录的发票上的发票 ID |
| resourceGroup | Azure 资源组的名称。 用于资源生命周期管理。 |
| partnerEarnedCreditRate | 基于合作伙伴管理员链接访问权限的合作伙伴获得的信用额度（PEC）时应用的折扣率。 |
| partnerEarnedCreditApplied | 指示是否应用了合作伙伴挣贷记。 |

在 "[成本分析](quick-acm-cost-analysis.md)" 视图中，还可以[保存视图](quick-acm-cost-analysis.md#saving-and-sharing-customized-views)并将数据导出到[CSV 和 PNG 文件](quick-acm-cost-analysis.md#automation-and-offline-analysis)。

## <a name="view-partner-earned-credit-pec-resource-costs"></a>查看合作伙伴获得的信用（PEC）资源成本

在 Azure 成本管理中，合作伙伴可以使用成本分析来查看收到 PEC 权益的成本。

在 Azure 门户中，登录到合作伙伴租户，并选择 "**成本管理 + 计费**"。 在 "**成本管理**" 下，单击 "**成本分析**"。

成本分析视图显示合作伙伴的计费帐户的成本。 根据需要为合作伙伴、特定客户或计费配置文件选择**范围**，以对发票进行协调。

在环形图中，单击下拉列表并选择 " **PartnerEarnedCreditApplied** " 以深化到 "PEC 成本"。

![示例演示如何查看合作伙伴获得的信用额度](./media/get-started-partners/cost-analysis-pec1.png)

当**PartnerEarnedCreditApplied**属性为_True_时，关联的成本具有合作伙伴获得管理访问权限。

如果**PartnerEarnedCreditApplied**属性为_False_，则关联的成本不满足信用要求的资格。 或者，购买的服务不适用于合作伙伴获得的信用额度。

服务使用情况数据通常需要8-24 小时才能出现在成本管理中。 有关详细信息，请参阅[使用情况数据更新频率有所不同](understand-cost-mgt-data.md#usage-data-update-frequency-varies)。 从 Azure 成本管理中的访问时间开始，PEC 信用将在48小时内显示。


还可以使用 "**分组依据**" 选项按**PartnerEarnedCreditApplied**属性进行分组和筛选。 使用这些选项可以检查有和没有 PEC 的成本。

![按合作伙伴所获得信用额度分组或筛选](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="cost-management-rest-apis"></a>成本管理 REST Api

合作伙伴和客户可以使用以下部分中所述的成本管理 Api 来执行常见任务。

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure 成本管理 Api-直接和间接提供程序

具有合作伙伴租户中计费范围访问权限的合作伙伴可以使用以下 Api 来查看开票成本。

如果合作伙伴有权访问订阅，则可以调用订阅范围内的 Api，而无需考虑成本策略。 有权访问订阅的其他用户（如客户或经销商）只有在合作伙伴启用了客户租户的成本策略之后，才能调用 Api。


#### <a name="to-get-a-list-of-billing-accounts"></a>获取计费帐户列表

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>获取客户列表

```
armclient get "providers/Microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers?api-version=2019-10-01-preview"
```
#### <a name="to-get-a-list-of-subscriptions"></a>获取订阅列表

```
armclient get "/providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/billingSubscriptions?api-version=2019-10-01-preview"
```

#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>获取客户的策略以查看成本

```
armclient get "providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/policies/default?api-version=2019-10-01-preview"
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>设置客户要查看成本的策略

```
armclient put "providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/policies/default?api-version=2019-10-01-preview" @policy.json
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>获取计费帐户的 Azure 服务使用情况

```
armclient GET /providers/Microsoft.Billing/BillingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>下载客户的 Azure 服务使用情况

以下 get 调用是一个异步操作。

```
armclient get providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

调用在响应中返回的 `Location` URI 以检查操作状态。 当状态为 "*已完成*" 时，`downloadUrl` 属性包含一个链接，可用于下载生成的报表。


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>获取或下载已使用的 Azure 服务的价目表

首先，使用以下文章。

```
armclient post "/providers/Microsoft.Billing/BillingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/billingProfiles/YYYY-YYYY-YYY-YYYY-YYY/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

然后，调用异步操作属性值。 例如：

```
armclient get "providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/billingProfiles/YYYY-YYYY-YYY-YYYY-YYY/pricesheetDownloadOperations/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX?sessiontoken=0:11186&api-version=2019-10-01-preview"
```
前面的 get 调用返回包含价目表的下载链接。

#### <a name="to-get-customer-costs-for-the-last-two-months-sorted-by-month"></a>若要获取过去两个月的客户成本，按月排序

```
armclient post providers/microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryCustomer.json
```

#### <a name="to-get-azure-subscription-costs-for-the-last-two-months-sorted-by-month"></a>若要获取过去两个月的 Azure 订阅成本，按月排序

```
armclient post providers/microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQuerySubscription.json
```

#### <a name="to-get-daily-costs-for-the-current-month"></a>获取当月的每日成本

```
armclient post providers/microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

#### <a name="create-a-budget-for-a-partner"></a>为合作伙伴创建预算

```
armclient put providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01 @budgetCreate.json
```


#### <a name="create-a-budget-for-a-customer"></a>为客户创建预算

```
armclient put providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/providers/Microsoft.Consumption/budgets/test-partner-demo?api-version=2019-10-01 @budgetCreate.json
```
#### <a name="delete-a-budget"></a>删除预算

```
armclient delete providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```


## <a name="next-steps"></a>后续步骤
- [开始分析](quick-acm-cost-analysis.md)成本管理中的成本
- 在成本管理中[创建和管理预算](tutorial-acm-create-budgets.md)
