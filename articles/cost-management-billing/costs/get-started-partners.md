---
title: 面向合作伙伴的 Azure 成本管理入门
description: 本文介绍合作伙伴如何使用 Azure 成本管理功能，以及如何为客户启用成本管理访问。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: aparnag
ms.custom: secdec18
ms.openlocfilehash: f81ec71ffe5cdf7f70e309282307a1c4bb293219
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78245478"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>面向合作伙伴的 Azure 成本管理入门

Azure 成本管理原生可用于已将其客户加入 Microsoft 客户协议并已[购买 Azure 计划](/partner-center/purchase-azure-plan)的直接合作伙伴。 本文介绍合作伙伴如何使用 [Azure 成本管理](../index.yml)功能查看 Azure 计划中的订阅的成本。 此外，介绍合作伙伴如何为其客户启用成本管理访问。

就直接合作伙伴和间接提供商而言，间接提供商的全局管理员和管理员代理可以访问合作伙伴租户中的成本管理。 经销商和客户可以访问客户租户中的成本管理并查看订阅的成本，其中成本是按零售价计算和显示的。 但是，他们必须对客户租户中的订阅具有 RBAC 访问权限才能查看成本。 提供商必须为客户租户启用成本可见性策略。

在 CSP 合作伙伴为其客户启用成本管理功能后，客户即可使用该功能。

CSP 合作伙伴可以使用成本管理来实现以下目的：

- 了解已开票的成本，并将成本关联到客户、订阅、资源组和服务。
- 在[成本分析](quick-acm-cost-analysis.md)中获得 Azure 成本的直观视图，并使用其中的许多功能来按客户、订阅、资源组、资源、计量器、服务及其他许多维度分析成本。
- 在成本分析中查看已应用“合作伙伴赚取的返点”(PEC) 的资源成本。
- 使用编程[预算](tutorial-acm-create-budgets.md)设置通知和自动化，并在成本超出预算时发出警报。
- 启用 Azure 资源管理器策略，使客户能够访问成本管理数据。 然后，客户可以查看采用[即用即付费率](https://azure.microsoft.com/pricing/calculator/)的订阅的消耗成本数据。
- 将成本和使用情况数据导出到即用即付订阅的存储 Blob。

以下示例显示了所有客户的成本。
![显示所有客户的成本的示例](./media/get-started-partners/customer-costs1.png)

以下示例显示了单个客户的成本。
![显示单个客户的成本的示例](./media/get-started-partners/customer-costs2.png)

还可以通过 REST API 使用 Azure 成本管理提供的所有功能。 使用 API 可以自动化成本管理任务。

## <a name="prerequisites"></a>先决条件

对于合作伙伴而言，Azure 成本管理原生仅适用于 Azure 计划中的订阅。

若要在 Azure 门户中启用 Azure 成本管理，必须确认客户接受 Microsoft 客户协议（代表客户）并将客户转换到 Azure 计划。 Azure 成本管理中仅提供已转换到 Azure 计划的订阅的成本。

Azure 成本管理要求对计费帐户或订阅拥有读取访问权限。

有关为计费帐户启用 Azure 成本管理并分配其访问权限的详细信息，请参阅[分配用户角色和权限](/partner-center/permissions-overview)。 “全局管理员”和“管理员代理”角色可以管理计费帐户的成本。  

若要在订阅范围访问 Azure 成本管理，对某个订阅拥有 RBAC 访问权限的任何用户都可以查看零售（即用即付）费率的成本。 但是，必须为客户租户启用成本可见性策略。 若要查看受支持帐户类型的完整列表，请参阅[了解成本管理数据](understand-cost-mgt-data.md)。


## <a name="how-cost-management-uses-scopes"></a>成本管理如何使用范围

你将在特定的范围内管理计费数据、获取特定于付款的角色、查看发票，以及执行常规的帐户管理。 对计费和帐户角色的管理独立于用于资源管理的范围（使用 RBAC）的管理。 为了明确区分不同范围的意图（包括访问控制的差异），我们分别将其称作计费范围和 RBAC 范围。

若要了解计费范围和 RBAC 范围以及成本管理如何使用范围，请参阅[了解和使用范围](understand-work-scopes.md)。

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>使用合作伙伴租户计费范围管理成本

将客户加入 Microsoft 客户协议后，可在租户中使用以下计费范围。  在成本管理中使用范围来管理成本。

### <a name="billing-account-scope"></a>计费帐户范围

使用计费帐户范围可以查看所有客户和计费配置文件中的税前成本。 仅显示 Microsoft 客户协议中客户的基于消耗量的产品的发票成本。 但是，会显示 Microsoft 客户协议和 CSP 套餐中客户的基于购买项的产品的发票成本。 目前，在范围中查看成本的默认货币为美元。 为范围设置的预算也以美元为货币。

无论使用何种不同的客户计费货币，合作伙伴都可以使用计费帐户范围以美元为货币来为其各个客户、订阅、资源和资源组设置预算和管理成本。

合作伙伴还可以在成本分析视图中，筛选各个客户的采用特定计费货币的成本。 选择“实际成本”列表可以查看采用受支持客户计费货币的成本。 

![显示货币的“实际成本”选项的示例](./media/get-started-partners/actual-cost-selector.png)

在计费范围中使用[摊销成本视图](quick-acm-cost-analysis.md#customize-cost-views)可以查看整个预留期限内的预留实例摊销成本。

### <a name="billing-profile-scope"></a>计费配置文件范围

使用计费配置文件范围可以查看所有客户的、包含在发票中的所有产品和订阅的采用计费货币的税前成本。 可以使用 **InvoiceID** 筛选器筛选特定发票的计费配置文件中的成本。 该筛选器显示特定发票的消耗和产品购买成本。 还可以筛选发票中特定客户的成本，以查看税前成本。

将客户加入 Microsoft 客户协议后，你将收到一份发票，其中包含 Microsoft 客户协议中这些客户的所有产品（消耗、购买和权利）的所有费用。 以相同的货币计费时，这些发票还包含仍在 CSP 套餐中的客户的权利和购买产品（例如 SaaS、Azure 市场和预留项）的费用。

为了帮助根据客户发票核对费用，可以使用计费配置文件范围来查看客户发票的所有累积成本。 与发票一样，该范围将显示新 Microsoft 客户协议中每个客户的成本。 该范围还显示了仍在当前 CSP 套餐中的客户权利产品的每笔费用。

只有计费配置文件和计费帐户范围才是适合显示权利和基于购买的产品（例如 Azure 市场和预留项购买）费用的范围。

计费配置文件定义发票中包含的订阅。 计费配置文件在功能上等同于企业协议注册。 计费配置文件是生成发票的范围。

目前，在查看计费配置文件范围中的成本时，客户的计费货币是默认货币。 在计费配置文件范围设置的预算采用计费货币。

合作伙伴可以使用范围来核对发票。 此外，他们可以使用范围以计费货币为以下各项设置预算：

- 筛选出的特定发票
- 客户
- 订阅
- 资源组
- 资源
- Azure 服务
- 计量
- ResellerMPNID

### <a name="customer-scope"></a>客户范围

合作伙伴可以使用范围来管理与已加入 Microsoft 客户协议的客户关联的成本。 合作伙伴可以使用范围来查看特定客户的采用计费货币的税前成本。 还可以筛选特定订阅、资源组或资源的税前成本。

客户范围不包括当前 CSP 套餐中的客户。 该范围仅包括已签署 Microsoft 客户协议的客户。 应用客户筛选器时，将在计费帐户和计费配置文件范围提供当前 CSP 套餐客户的权利成本，而不提供 Azure 用量。 在此范围设置的预算采用计费货币。

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>合作伙伴在成本管理中访问计费范围

只有具有“全局管理员”和“管理员代理”角色的用户才能直接在合作伙伴的 Azure 租户中管理和查看计费帐户、计费配置文件与客户的成本。   有关合作伙伴中心角色的详细信息，请参阅[分配用户角色和权限](/partner-center/permissions-overview)。

## <a name="enable-cost-management-in-the-customer-tenant"></a>在客户租户中启用成本管理

将客户加入 Microsoft 客户协议后，合作伙伴可以启用对成本管理的访问。 然后，合作伙伴可以启用某个策略，使客户能够查看按即用即付零售费率计算的成本。 将在 RBAC 订阅和资源组范围，以客户的计费货币根据其消耗用量显示成本。

合作伙伴启用成本可见性策略后，可通过 Azure 资源管理器访问订阅的任何用户都可以管理和分析按即用即付费率计算的成本。 对 Azure 订阅拥有相应 RBAC 访问权限的经销商和客户实际上可以查看成本。

如果合作伙伴有权访问订阅和资源组，则无论采用哪种策略，他们也都可以查看成本。

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>启用查看 Azure 使用费的策略

合作伙伴可以使用以下信息来启用查看其客户的 Azure 使用费的策略。

在 Azure 门户中登录到合作伙伴租户，并选择“成本管理 + 计费”。  选择相关的 Microsoft 合作伙伴协议计费帐户，然后选择“客户”。  客户列表与计费帐户相关联。

在客户列表中，选择要允许其查看成本的客户。

![在成本管理中选择客户](./media/get-started-partners/customer-list.png)

在“设置”下，选择“策略”。  

将显示与所选客户的订阅相关联的“Azure 用量”费用的当前成本可见性策略。 
![允许客户查看即用即付费用的策略](./media/get-started-partners/cost-management-billing-policies.png)

将策略设置为“否”时，与该客户关联的订阅用户无法使用 Azure 成本管理。  除非已由合作伙伴启用，否则默认会对所有订阅用户禁用成本可见性策略。

将成本策略设置为“是”时，与客户租户关联的订阅用户可以查看按即用即付费率计算的使用费。 

启用成本可见性策略后，具有订阅用量的所有服务都会显示按即用即付费率计算的成本。 对于实际和摊销成本，预留用量会显示零费用。 购买项和权利不与特定的订阅相关联。 因此，不会在订阅范围显示购买项。


### <a name="view-customer-costs"></a>查看客户成本

若要查看客户租户的成本，请打开**成本管理 + 计费**。 选择“成本分析”  ，然后将范围更改为客户租户订阅以开始查看成本。

![以客户身份查看成本分析 ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

将以基于即用即付费率的成本，提供订阅和资源组 RBAC 范围的成本分析、预算和警报。

RBAC 范围内的预留实例的摊销视图和实际成本将显示零费用。 预留实例成本仅显示在购买时所在的计费范围内。

## <a name="analyze-costs-in-cost-analysis"></a>在成本分析中分析成本

有权访问合作伙伴租户中的计费范围的合作伙伴可以在不同客户的成本分析中，浏览和分析特定客户或发票的开票成本。 在[成本分析](quick-acm-cost-analysis.md)视图中，还可以[保存视图](quick-acm-cost-analysis.md#saving-and-sharing-customized-views)，以及将数据导出到 [CSV 和 PNG 文件](quick-acm-cost-analysis.md#automation-and-offline-analysis)。

有权访问客户租户中的订阅的 RBAC 用户还可以分析该客户租户中的订阅的零售成本、保存视图，以及将数据导出到 CSV 和 PNG 文件。

可以使用成本分析中的筛选和分组依据功能来按多个字段分析成本。 下一部分将介绍特定于合作伙伴的字段。

## <a name="data-fields"></a>数据字段

在使用情况详细信息文件和成本管理 API 中可以找到以下数据字段。 在适用的情况下，会显示合作伙伴中心内的同等信息。 对于下面以粗体显示的字段，合作伙伴可以使用成本分析中的筛选和分组依据功能来按多个字段分析成本。 以粗体显示的字段仅适用于合作伙伴支持的 Microsoft 客户协议。

| **字段名称** | **说明** | **合作伙伴中心内的同等信息** |
| --- | --- | --- |
| invoiceId | 特定交易的发票上显示的发票 ID。 | 显示交易的发票编号。 |
| previousInvoiceID | 有退款（负成本）时对原始发票的引用。 仅当有退款时才填充。 | 空值 |
| billingAccountName | 表示合作伙伴的计费帐户的名称。 其中累积了已加入 Microsoft 客户协议的客户，以及已购买 SaaS、Azure 市场和预留项等权利的 CSP 客户的成本。 | 空值 |
| billingAccountID | 表示合作伙伴的计费帐户的标识符。 | MCAPI 合作伙伴商业根 ID。 在请求中使用，但不包含在响应中。|
| billingProfileID | 以一种计费货币将已加入 Microsoft 客户协议的客户，以及已购买 SaaS、Azure 市场和预留项等权利的 CSP 客户的成本分组到不同发票的计费配置文件的标识符。 | MCAPI 合作伙伴计费组 ID。 在请求中使用，但不包含在响应中。 |
| billingProfileName | 以一种计费货币将已加入 Microsoft 客户协议的客户，以及已购买 SaaS、Azure 市场和预留项等权利的 CSP 客户的成本分组到不同发票的计费配置文件的名称。 | 空值 |
| invoiceSectionName | 在发票中计费的项目的名称。 不适用于合作伙伴加入的 Microsoft 客户协议。 | 空值 |
| invoiceSectionID | 在发票中计费的项目的标识符。 不适用于合作伙伴加入的 Microsoft 客户协议。 | 空值 |
| **CustomerTenantID** | 客户订阅的 Azure Active Directory 租户的标识符。 | 客户的组织 ID - 客户的 Azure Active Directory TenantID。 |
| **CustomerName** | 客户订阅的 Azure Active Directory 租户的名称。 | 合作伙伴中心内显示的客户组织名称。 根据系统信息核对发票时，此字段非常重要。 |
| **CustomerTenantDomainName** | 客户订阅的 Azure Active Directory 租户的域名。 | 客户的 Azure Active Directory 租户域。 |
| **PartnerTenantID** | 合作伙伴的 Azure Active Directory 租户的标识符。 | 合作伙伴的 Azure Active Directory 租户 ID，称作“合作伙伴 ID”，采用 GUID 格式。 |
| **PartnerName** | 合作伙伴 Azure Active Directory 租户的名称。 | 合作伙伴名称。 |
| **ResellerMPNID** | 与订阅关联的经销商的 MPNID。 | 订阅记录中的经销商的 MPN ID。 不适用于当前活动。 |
| costCenter | 与订阅关联的成本中心。 | 空值 |
| billingPeriodStartDate | 发票中显示的计费周期开始日期。 | 空值 |
| billingPeriodEndDate | 发票中显示的计费周期结束日期。 | 空值 |
| servicePeriodStartDate | 为服务用量分级费用时分级时段的开始日期。 将根据分级时段确定 Azure 服务的价格。 | 合作伙伴中心内的 ChargeStartDate。 计费周期的开始日期，不包括上一个计费周期内的之前未付款的潜在使用数据的显示日期。 该时间始终为一天的起点时间，即 0:00。 |
| servicePeriodEndDate | 为服务用量分级费用时的时段结束日期。 将根据分级时段确定 Azure 服务的价格。 | 空值 |
| date | 对于 Azure 消耗数据，会显示分级用量的日期。 对于预留实例，会显示购买日期。 对于重复性费用和一次性费用（例如市场和支持服务），会显示购买日期。 | 空值 |
| productID | 已按消耗或购买量累积了费用的产品的标识符。 它是合作伙伴中心内显示的 productID 和 SKuID 的串联键。 | 产品的 ID。 |
| product | 在发票中显示的已按消耗或购买量累积了费用的产品的名称。 | 目录中的产品名称。 |
| serviceFamily | 显示购买或收费的产品的服务系列。 例如“存储”或“计算”。 | 空值 |
| productOrderID | 订阅所属的资产或 Azure 计划名称的标识符。 例如“Azure 计划”。 | CommerceSubscriptionID |
| productOrderName | 订阅所属的 Azure 计划的名称。 例如“Azure 计划”。 | 空值|
| consumedService | 旧版 EA 使用情况详细信息中使用的消耗服务（旧式分类）。 | 合作伙伴中心内显示的服务。 例如 Microsoft.Storage、Microsoft.Compute 和 microsoft.operationalinsights。 |
| meterID | 度量的消耗项的计量标识符。 | 所用计量器的 ID。 |
| meterName | 标识度量消耗项的计量器名称。 | 所用计量器的名称。 |
| meterCategory | 标识用量对应的顶级服务。 | 用量对应的顶级服务。 |
| meterSubCategory | 定义可能影响费率的 Azure 服务的类型或子类别。 | 可能影响费率的 Azure 服务的类型。|
| meterRegion | 指明某些服务的数据中心的位置，这些服务根据数据中心位置进行定价。 | 服务数据中心的区域位置（如果适用且已填充）。 |
| 订阅 ID | Microsoft 为 Azure 订阅生成的唯一标识符。 | EntitlementID |
| subscriptionName | Azure 订阅的名称。 | 空值 |
| 术语 | 显示套餐的有效期限。 例如，预留实例显示 12 个月表示一年的期限。 对于 SaaS、Azure 市场和支持服务的一次性购买或重复性购买，期限会显示一个月。 不适用于 Azure 消耗计划。 | 空值 |
| publisherType（firstParty、thirdPartyReseller、thirdPartyAgency） | 发布者的类型，将发布者标识为第一方、第三方经销商或第三方代理商。 | 空值 |
| partNumber | 未使用的预留实例和 Azure 市场服务的部件号。 | 空值 |
| publisherName | 服务发布者（包括 Microsoft 或第三方发布者）的名称。 | 产品发布者的名称。|
| reservationId | 预留实例购买项的标识符。 | 空值 |
| reservationName | 预留实例的名称。 | 空值 |
| reservationOrderId | 预留实例的 OrderID。 | 空值 |
| 频率 | 预留实例的付款频率。 | 空值 |
| resourceGroup | 用于生命周期资源管理的 Azure 资源组的名称。 | 资源组的名称。 |
| instanceID 或 ResourceID | 资源实例的标识符。 | 显示为包含完整资源属性的 ResourceURI。 |
| resourceLocation | 资源位置的名称。 | 资源的位置。 |
| 位置 | 资源的规范化位置。 | 空值 |
| effectivePrice | 服务的有效单价，采用定价货币。 每个产品、服务系列、计量器和套餐采用唯一的单价。 与计费帐户的价目表中的定价配合使用。 如果存在分层定价或包含的数量，则会显示混合使用价格。 | 调整后的单价。 |
| 数量 | 购买或消耗的度量数量。 计费周期内使用的计量器数量。 | 单位数量。 请确保它与对帐期间计费系统中的信息相匹配。 |
| unitOfMeasure | 指明服务的计价单位。 例如，GB 和小时。 | 指明服务的计价单位。 例如，GB、小时和 10,000 秒。 |
| pricingCurrency | 定义单价的货币。 | 价目表中的货币。|
| billingCurrency | 定义计费成本的货币。 | 客户地理区域的货币。 |
| chargeType | 定义成本在 Azure 成本管理中所代表的费用类型，例如购买和退款。 | 费用或调整的类型。 不适用于当前活动。 |
| costinBillingCurrency | 采用计费货币的 ExtendedCost 或混合税前成本。 | 空值 |
| costinPricingCurrency | 与价格关联的采用定价货币的 ExtendedCost 或混合税前成本。 | 空值 |
| **costinUSD** | 以美元为货币的估算 ExtendedCost 或混合税前成本。 | 空值 |
| **paygCostInBillingCurrency** | 定价为零售价时显示成本。 以计费货币显示即用即付价格。 仅在 RBAC 范围适用。 | 空值 |
| **paygCostInUSD** | 定价为零售价时显示成本。 以美元为货币显示即用即付价格。 仅在 RBAC 范围适用。 | 空值 |
| exchangeRate | 从定价货币转换为计费货币时使用的汇率。 | 在合作伙伴中心内称为 PCToBCExchangeRate。 定价货币兑换为计费货币的汇率。|
| exchangeRateDate | 从定价货币转换为计费货币时使用的汇率的日期。 | 在合作伙伴中心内称为 PCToBCExchangeRateDat。 定价货币兑换为计费货币的汇率日期。|
| isAzureCreditEligible | 指示该成本是否适合以 Azure 额度付款。 | 空值 |
| serviceInfo1 | 旧字段，用于捕获可选的服务特定元数据。 | 内部 Azure 服务元数据。 |
| serviceInfo2 | 旧字段，用于捕获可选的服务特定元数据。 | 服务信息。 例如，虚拟机的映像类型和 ExpressRoute 的 ISP 名称。|
| additionalInfo | 服务特定的元数据。 例如，虚拟机的映像类型。 | 其他列中未包括的任何其他信息。 特定于服务的元数据。 例如，虚拟机的映像类型。|
| 标记 | 分配给计量器的标记。 使用标记对计费记录进行分组。 例如，可以使用标记按使用测定仪的部门分配费用。 | 客户添加的标记。|
| **partnerEarnedCreditRate** | 存在基于合作伙伴管理员链接访问的合作伙伴赚取的返点 (PEC) 时应用的折扣率。 | 合作伙伴赚取的返点 (PEC) 率。 例如 0% 或 15%。 |
| **partnerEarnedCreditApplied** | 指示是否已应用合作伙伴赚取的返点。 | 空值 |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>查看合作伙伴赚取的返点 (PEC) 资源成本

在 Azure 成本管理中，合作伙伴可以使用成本分析来查看收到了 PEC 权益的成本。

在 Azure 门户中登录到合作伙伴租户，并选择“成本管理 + 计费”。  在“成本管理”下，选择“成本分析”。  

成本分析视图将显示合作伙伴计费帐户的成本。 根据需要选择合作伙伴、特定客户或计费配置文件的“范围”来核对发票。 

在圆环图中选择下拉列表，然后选择“PartnerEarnedCreditApplied”钻取到 PEC 成本。 

![演示如何查看合作伙伴赚取的返点的示例](./media/get-started-partners/cost-analysis-pec1.png)

如果 **PartnerEarnedCreditApplied** 属性为 _True_，则关联的成本将提供合作伙伴赚取的权益的管理访问权限。

如果 **PartnerEarnedCreditApplied** 属性为 _False_，则关联的成本不符合所需的返点条件。 或者，购买的服务不符合合作伙伴赚取的返点的条件。

服务使用情况数据通常需要在 8-24 小时后才显示在成本管理中。 有关详细信息，请参阅[使用情况数据的更新频率存在变化](understand-cost-mgt-data.md#usage-data-update-frequency-varies)。 从访问时间开始算起的 48 小时内，PEC 返点将显示在 Azure 成本管理中。


还可以使用“分组依据”选项，按 **PartnerEarnedCreditApplied** 属性进行分组和筛选。  使用这些选项可以检查包含和不包含 PEC 的成本。

![按合作伙伴赚取的返点进行分组或筛选](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>将成本数据导出到 Azure 存储

有权访问合作伙伴租户中的计费范围的合作伙伴可将其成本和使用情况数据导出到 Azure 存储 Blob。 该 Blob 必须位于合作伙伴租户的某个订阅中，且该订阅不是共享的服务订阅或客户的订阅。 若要启用成本数据导出，我们建议在合作伙伴租户中设置一个独立的即用即付订阅来托管导出的成本数据。 导出存储帐户将在即用即付订阅中托管的 Azure 存储 Blob 上创建。 根据伙伴创建导出时所在的范围，会定期自动将关联的数据导出到存储帐户。

对订阅拥有 RBAC 访问权限的用户还可以将成本数据导出到客户租户的任何订阅中托管的 Azure 存储 Blob。

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>在合作伙伴租户或客户租户中创建导出

在 Azure 门户中登录到合作伙伴租户或客户租户，并选择“成本管理 + 计费”。  选择适当的范围（例如 Microsoft 合作伙伴协议计费帐户），然后选择“成本分析”。  加载页面后，选择“导出”。  选择“计划导出”下的“查看所有导出”。 

![选择“导出”和“查看所有导出”](./media/get-started-partners/export01.png)

接下来，选择“添加”并键入名称，然后选择导出类型。  选择“存储”选项卡并输入所需的信息。 

![添加新导出并选择“存储”选项卡](./media/get-started-partners/export02.png)

在合作伙伴租户中创建导出时，请选择合作伙伴租户中的即用即付订阅。 使用该订阅创建 Azure 存储帐户。

对于客户租户中的 RBAC 用户，请选择客户租户中的订阅。 使用该订阅创建 Azure 存储帐户。

查看内容，然后选择“创建”以计划导出。 

若要验证导出列表中的数据，请选择存储帐户名称。 在存储帐户页上选择“容器”，然后选择容器。  导航到相应的文件夹，并选择 CSV 文件。 选择“下载”以获取 CSV 文件并将其打开。  导出的数据类似于 Azure 门户中的成本数据和使用情况详细信息。

![导出数据的示例](./media/get-started-partners/example-export-data.png)

## <a name="cost-management-rest-apis"></a>成本管理 REST API

合作伙伴和客户可以使用以下部分中所述的成本管理 API 来完成常见任务。

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure 成本管理 API - 直接和间接提供程序

有权访问合作伙伴租户中的计费范围的合作伙伴可以使用以下 API 来查看开票成本。

如果合作伙伴有权访问订阅，则无论成本策略是什么，他们都可以调用订阅范围的 API。 有权访问订阅的其他用户（例如客户或经销商）只有在合作伙伴为客户租户启用了成本策略之后，才能调用 API。


#### <a name="to-get-a-list-of-billing-accounts"></a>获取计费帐户的列表

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>获取客户的列表

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>获取订阅的列表

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>获取一段时间的发票列表

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

API 调用将返回发票数组，其中包含类似于以下 JSON 代码的元素。

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

使用前面返回的 ID 字段值，并在以下示例中将它替换为范围，以查询使用情况详细信息。

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

该示例返回与特定发票关联的使用情况记录。


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>获取客户的策略以查看成本

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>设置客户的策略以查看成本

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>获取计费帐户的 Azure 服务使用情况

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>下载客户的 Azure 服务使用情况

以下 get 调用是一个异步操作。

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

调用响应中返回的 `Location` URI 来检查操作状态。 当状态为 *Completed* 时，`downloadUrl` 属性将包含可用于下载生成的报表的链接。


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>获取或下载所用 Azure 服务的价目表

首先使用以下 post 调用。

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

然后调用异步操作属性值。 例如：

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
以上 get 调用返回包含价目表的下载链接。


#### <a name="to-get-aggregated-costs"></a>获取聚合成本

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>为合作伙伴创建预算

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>为客户创建预算

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>删除预算

```
DELETE
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>后续步骤
- 在成本管理中[开始分析成本](quick-acm-cost-analysis.md)
- 在成本管理中[创建和管理预算](tutorial-acm-create-budgets.md)
