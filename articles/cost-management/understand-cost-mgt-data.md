---
title: 了解 Azure 成本管理数据 | Microsoft Docs
description: 本文可帮助你更好地了解 Azure 成本管理中包含的数据，以及处理、收集、显示和关闭数据的频率。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 681ccc768b1fa3d5a968847d11987fbd83898b59
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721372"
---
# <a name="understand-cost-management-data"></a>了解成本管理数据

本文可帮助你更好地了解 Azure 成本管理中包含的 Azure 成本和使用情况数据。 它说明了处理、收集、显示和关闭数据的频率。 你需要按月支付 Azure 使用费。 尽管计费周期为每月期间，但周期开始日期和结束日期因订阅类型而异。 成本管理接收用量数据的频率根据不同的因素而异。 此类因素包括处理数据所花费的时间，以及 Azure 服务向计费系统发出用量数据的频率。

成本管理包括所有使用情况和购买情况，其中包括企业协议（EA）帐户的预订和第三方产品/服务。 Microsoft 客户协议帐户和使用即用即付费率的个人订阅仅包含 Azure 和 Marketplace 服务的使用情况。 不包括支持和其他成本。 在生成发票之前估计成本，而不考虑信用额度。

## <a name="supported-microsoft-azure-offers"></a>支持的 Microsoft Azure 产品

以下信息说明了 Azure 成本管理中目前支持的 [Microsoft Azure 套餐](https://azure.microsoft.com/support/legal/offer-details/)。 Azure 套餐是客户拥有的 Azure 订阅类型。 **从日期可用的数据**开始，成本管理中提供了数据。 如果订阅更改了产品/服务，则 "产品/服务" 更改之前的成本将不可用。

| **类别**  | **产品/服务名称** | **配额 ID** | **套餐编号** | **可用数据** |
| --- | --- | --- | --- | --- |
| **Azure 政府** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | 5月 2014<sup>1</sup> |
| **企业协议 (EA)** | Enterprise 开发/测试                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | 5月 2014<sup>1</sup> |
| **企业协议 (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | 5月 2014<sup>1</sup> |
| **Microsoft 客户协议** | [Microsoft Azure 计划](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | 不适用 | 2019年3月<sup>3</sup>日 |
| **Microsoft 客户协议** | [用于开发/测试的 Microsoft Azure 计划](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | 不适用 | 2019年3月<sup>3</sup>日 |
| **合作伙伴支持的 Microsoft 客户协议** | Microsoft Azure 计划 | CSP_2015-05-01、CSP_MG_2017 和 CSPDEVTEST_2018-05-01<br><br>此配额 ID 可重复用于 Microsoft 客户协议和旧 CSP 订阅。 目前仅支持 Microsoft 客户协议订阅。 | 不适用 | 2019年10月 |
| **Microsoft 开发人员网络 (MSDN)** | [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 10月2日，2018<sup>2</sup> |
| **即用即付** | [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 10月2日，2018<sup>2</sup> |
| **即用即付** | [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 10月2日，2018<sup>2</sup> |
| **即用即付** | [Microsoft 合作伙伴网络](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 10月2日，2018<sup>2</sup> |
| **即用即付** | [免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 10月2日，2018<sup>2</sup> |
| **即用即付** | [Azure 开放许可](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 10月2日，2018<sup>2</sup> |
| **即用即付** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P、MS-AZR-0122P - MS-AZR-0125P、MS-AZR-0128P - MS-AZR-0130P | 10月2日，2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 10月2日，2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 10月2日，2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 10月2日，2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 10月2日，2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise： BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 10月2日，2018<sup>2</sup> |

_<sup>**1**</sup>对于5月2014之前的数据，请访问[Azure 企业门户](https://ea.azure.com)。_

_<sup>**2**</sup>对于10月 2 2018 日之前的数据，请访问[Azure 帐户中心](https://account.azure.com/subscriptions)。_

_<sup>**3**</sup> 2019 年3月开始使用 Microsoft 客户协议，此点之前没有任何历史数据。_

_<sup>**4**</sup>基于信用额度和向外付费订阅的历史数据可能与发票不匹配。请参阅下面的[历史数据可能与发票不匹配](#historical-data-might-not-match-invoice)。_

尚不支持以下产品/服务：

| 类别  | **产品/服务名称** | **配额 ID** | **套餐编号** |
| --- | --- | --- | --- |
| **Azure 德国** | [Azure 德国即用即付](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **云解决方案提供商 (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **云解决方案提供商 (CSP)** | Azure 政府版 CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **云解决方案提供商 (CSP)** | Microsoft 德国云 CSP 中的 Azure 德国版   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **即用即付**                 | 面向学生的 Azure 入门版 | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **即用即付** | [面向学生的 Azure](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **即用即付**                 | [Microsoft Azure 赞助](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **支持计划** | 标准支持                    | Default_2014-09-01 | MS-AZR-0041P |
| **支持计划** | 专业直接支持         | Default_2014-09-01 | MS-AZR-0042P |
| **支持计划** | 开发人员支持                   | Default_2014-09-01 | MS-AZR-0043P |
| **支持计划** | 德国支持计划                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **支持计划** | Azure 政府版标准支持   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **支持计划** | Azure 政府版专业直接支持 | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **支持计划** | Azure 政府版开发人员支持  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>确定产品/服务类型
如果未看到订阅数据并想要确定你的订阅是否属于受支持的套餐，可以验证你的订阅是否受支持。 若要验证 Azure 订阅是否受支持，请登录到 [Azure 门户](https://portal.azure.com)。 在左窗格中选择“所有服务”。 在服务列表中选择“订阅”。 在订阅列表菜单中，单击要验证的订阅。 该订阅将显示在“概述”选项卡上，在其中可以看到“套餐”和“套餐 ID”。 下图显示了一个示例。

![“订阅概述”选项卡的示例，其中显示了“套餐”和“套餐 ID”](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>成本管理中包括的成本

下表显示了成本管理中包括或不包括的数据。 在生成发票之前，将估计所有成本。 显示的成本不包括免费信用额度和预付信用额度。

**成本和用量数据**

| **包括** | **不包括** |
| --- | --- |
| Azure 服务使用情况<sup>5</sup>        | 支持费用 - 有关详细信息，请参阅[发票条款说明](../billing/billing-understand-your-invoice.md)。 |
| Marketplace 产品/服务使用情况<sup>6</sup> | 税费 - 有关详细信息，请参阅[发票条款说明](../billing/billing-understand-your-invoice.md)。 |
| Marketplace 购买<sup>6</sup>      | 信用额度 - 有关详细信息，请参阅[发票条款说明](../billing/billing-understand-your-invoice.md)。 |
| 预订购买<sup>7</sup>      |  |
| 预订采购摊销<sup>7</sup>      |  |

_<sup>**5**</sup> Azure 服务使用情况基于预留和协商价格。_

_<sup>**6**</sup> Marketplace 购买目前不适用于即用即付、MSDN 和 Visual Studio 产品/服务。_

_<sup>**7**</sup>个预订购买目前仅适用于企业协议（EA）帐户。_

Metadata

| **包括** | **不包括** |
| --- | --- |
| 资源标记<sup>8</sup> | 资源组标记 |

_<sup>**8**</sup>个资源标记应用于从每个服务发出的使用情况，并且不可用于以追溯方式到历史使用情况。_

## <a name="rated-usage-data-refresh-schedule"></a>用量计费数据刷新计划

成本和用量数据在 Azure 门户上的“成本管理 + 计费”以及[支持 API](index.yml) 中提供。 查看成本时，请记住以下几点：

- 当前计费周期的估算费用每天更新六次。
- 由于用量会增大，当前计费周期的估算费用可能会更改。
- 每次更新都是累积性的，包括前次更新的所有细目和信息。
- Azure 在当前计费周期结束后的最多 72 小时（三个日历日）内终结或_关闭_该计费周期。

以下示例演示计费周期的结束方式。

企业协议 (EA) 订阅 – 如果计费月在 3 月 31 日结束，则在该日期后的最多 72 小时内更新估算费用。 在此示例中，更新截止时间为 4 月 4 日午夜 (UTC)。

即用即付订阅 – 如果计费月在 5 月 15 日结束，则在该日期后的最多 72 小时内更新估算费用。 在此示例中，更新截止时间为 5 月 19 日午夜 (UTC)。

### <a name="rerated-data"></a>重新计费数据

无论是使用[成本管理 api](index.yml)、Power BI 还是 Azure 门户来检索数据，都需要当前计费周期的费用以获取 rerated，并因此更改，直到发票关闭。

## <a name="cost-management-data-fields"></a>成本管理数据字段

在使用情况详细信息文件和成本管理 Api 中可以找到以下数据字段。 对于下面的加粗字段，合作伙伴可以使用筛选器并分组按成本分析中的功能来按多个字段对成本进行分析。 粗体字段仅适用于合作伙伴支持的 Microsoft 客户协议。

| **字段名称** | **说明** |
| --- | --- |
| invoiceId | 发票上显示的特定交易记录的发票 ID。 |
| previousInvoiceID | 引用原始发票时有退款（负成本）。 只有退款时才填充。 |
| billingAccountName | 表示合作伙伴的计费帐户的名称。 它在已载入 Microsoft 客户协议的客户和已进行授权购买的 CSP 客户（如 SaaS、Azure Marketplace 和预订）中，向客户收取费用。 |
| billingAccountID | 表示合作伙伴的计费帐户的标识符。 |
| billingProfileID | 计费配置文件的标识符，它在载入到 Microsoft 客户协议的客户和已进行权利购买的 CSP 客户（如 SaaS、Azure Marketplace 和预订. |
| billingProfileName | 计费配置文件的名称，该配置文件在已载入 Microsoft 客户协议的客户和 CSP 客户（如 SaaS、Azure Marketplace 和）中将成本按单一计费币种分组预订. |
| invoiceSectionName | 在发票中计费的项目的名称。 不适用于合作伙伴载入的 Microsoft 客户协议。 |
| invoiceSectionID | 发票中正在进行计费的项目的标识符。 不适用于合作伙伴载入的 Microsoft 客户协议。 |
| **CustomerTenantID** | 客户&#39;订阅的 Azure Active Directory 租户的标识符。 |
| **CustomerName** | 客户&#39;订阅的 Azure Active Directory 租户的名称。 |
| **CustomerTenantDomainName** | 客户&#39;订阅的 Azure Active Directory 租户的域名。 |
| **PartnerTenantID** | 合作伙伴&#39;Azure Active Directory 租户的标识符。 |
| **PartnerName** | Azure Active Directory 租户的合作伙伴的名称。 |
| **ResellerMPNID** | 与订阅关联的分销商的 MPNID。 |
| costCenter | 与订阅关联的成本中心。 |
| billingPeriodStartDate | 计费周期开始日期，如发票中所示。 |
| billingPeriodEndDate | 计费期间结束日期，如发票中所示。 |
| servicePeriodStartDate | 评估服务使用情况的评估期的开始日期。 Azure 服务的价格是针对评级周期确定的。 |
| servicePeriodEndDate | 评估服务使用情况的时间段的结束日期。 Azure 服务的价格取决于分级期限。 |
| 日期 | 对于 Azure 消耗数据，它会将 "使用日期" 显示为 "分级"。 对于预订实例，它显示购买日期。 对于定期收费和一次收费（如 Marketplace 和支持），它会显示购买日期。 |
| productID | 按消耗或购买将计费的产品标识符。 它是 productID 和 SKuID 的连接键，如合作伙伴中心中所示。 |
| product | 按消耗或购买将计费的产品的名称，如发票中所示。 |
| serviceFamily | 显示购买或收费的产品的服务系列。 例如，存储或计算。 |
| productOrderID | 订阅所属的资产或 Azure 计划名称的标识符。 例如，Azure 计划。 |
| productOrderName | 订阅所属的 Azure 计划的名称。 例如，Azure 计划。 |
| consumedService | 旧 EA 使用情况详细信息中使用的已使用服务（旧分类）。 |
| meterID | 度量的消耗的计量标识符。 |
| meterName | 标识度量消耗的计量的名称。 |
| meterCategory | 标识使用的顶级服务。 |
| meterSubCategory | 定义 Azure 服务的类型或子类别，它们可能会影响速度。 |
| meterRegion | 指明某些服务的数据中心的位置，这些服务根据数据中心位置进行定价。 |
| 订阅 ID | Microsoft 为 Azure 订阅生成的唯一标识符。 |
| subscriptionName | Azure 订阅的名称。 |
| 条款 | 显示产品/服务的有效性的术语。 例如，预订实例显示预订实例的每年12个月。 对于一次性购买或定期购买，术语针对 SaaS、Azure Marketplace 和支持显示一个月。 不适用于 Azure。 |
| publisherType （firstParty，thirdPartyReseller，thirdPartyAgency） | 将发布服务器标识为第一方、第三方经销商或第三方代理商的出版商类型。 |
| partNumber | 未使用的保留实例和 Azure Marketplace 服务的部件号。 |
| publisherName | 服务发布者的名称，包括 Microsoft 或第三方发布者。 |
| reservationId | 采购预订实例的标识符。 |
| reservationName | 预订实例的名称。 |
| reservationOrderId | 预订实例的订单 Id。 |
| frequency | 预订实例的付款频率。 |
| resourceGroup | 用于生命周期资源管理的 Azure 资源组的名称。 |
| instanceID （或） ResourceID | 资源实例的标识符。 |
| resourceLocation | 资源位置的名称。 |
| 位置 | 资源的规范化位置。 |
| effectivePrice | 服务的有效单价（定价货币）。 产品、服务系列、计量和产品/服务的独特之处。 与计费帐户的价目表中的定价一起使用。 如果有分层定价或包含数量，则会显示消耗的混合价格。 |
| 数量 | 购买或消耗的度量数量。 计费期间使用的计量量。 |
| unitOfMeasure | 指明服务的计价单位。 例如，GB 和小时。 |
| pricingCurrency | 定义单位价格的货币。 |
| billingCurrency | 定义计费成本的货币 |
| chargeType | 定义成本在 Azure 成本管理中代表的费用类型，如购买和退款。 |
| costinBillingCurrency | 按计费货币表示的税收之前的 ExtendedCost 或混合成本。 |
| costinPricingCurrency | 与价格关联的税收之前的 ExtendedCost 或混合成本。 |
| **costinUSD** | 以 USD 为单位的估计 ExtendedCost 或混合成本。 |
| **paygCostInBillingCurrency** | 如果定价价格为零售价格，则显示成本。 以计费币种显示即用即付价格。 仅在 RBAC 范围内可用。 |
| **paygCostInUSD** | 如果定价价格为零售价格，则显示成本。 显示即用即付价格（美元）。 仅在 RBAC 范围内可用。 |
| exchangeRate | 用于从定价货币转换为计费货币的汇率。 |
| exchangeRateDate | 用于从定价货币转换为计费&#39;货币的汇率的日期。 |
| isAzureCreditEligible | 指示成本是否适合由 Azure 额度支付。 |
| serviceInfo1 | 旧字段，用于捕获可选的服务特定元数据。 |
| serviceInfo2 | 旧字段，用于捕获可选的服务特定元数据。 |
| additionalInfo | 服务特定的元数据。 例如，虚拟机的映像类型。 |
| 标记 | 分配给计量器的标记。 使用标记对计费记录进行分组。 例如，可以使用标记按使用测定仪的部门分配费用。 |
| **partnerEarnedCreditRate** | 基于合作伙伴管理员链接访问权限的合作伙伴获得的信用额度（PEC）时应用的折扣率。 |
| **partnerEarnedCreditApplied** | 指示是否已应用合作伙伴获得的信用额度。 |


## <a name="usage-data-update-frequency-varies"></a>用量数据的更新频率存在变化

成本管理中是否显示产生的用量数据取决于多种因素，包括：

- Azure 服务（例如存储、计算、CDN 和 SQL）发出用量数据的频率。
- 通过计费引擎和成本管理管道处理用量数据所花费的时间。

有些服务比其他服务更频繁地发出用量数据。 因此在成本管理中，某些服务的数据比发送频率更低的其他服务的数据更快出现。 一般情况下，服务用量数据需要在 8-24 小时后才会出现在成本管理中。 请注意，随着用量的增大，未结月份的数据将会更新，因为更新是累积性的。

## <a name="historical-data-might-not-match-invoice"></a>历史数据可能与发票不匹配

基于信用额度和预付付产品/服务的历史数据可能与发票不匹配。 某些 Azure 即用即付、MSDN 和 Visual Studio 产品/服务可将 Azure 信用额度和高级付款应用于发票。 但是，成本管理中显示的历史数据仅基于估计的消耗费用。 成本管理历史记录数据不包括付款和信用额度。 因此，为以下产品/服务显示的历史数据可能与发票不完全匹配。

- 面向学生的 Azure （BC-OP-NT-AZR-0170P）
- Azure 开放许可（BC-OP-NT-AZR-0111P）
- Azure Pass （BC-OP-NT-AZR-0120P，BC-OP-NT-AZR-0123P，MS-BC-OP-NT-AZR-0125P，MS-BC-OP-NT-AZR-0128P，MS-bc-op-nt-azr-0129P）
- 免费试用（BC-OP-NT-AZR-0044P）
- MSDN (MS-AZR-0062P)
- Visual Studio （BC-OP-NT-AZR-0029P，BC-OP-NT-AZR-0059P，MS-BC-OP-NT-AZR-0060P，MS-BC-OP-NT-AZR-0063P，MS-bc-op-nt-azr-0064P）

## <a name="see-also"></a>另请参阅

- 如果尚未完成有关成本管理的第一个快速入门，请阅读[开始分析成本](quick-acm-cost-analysis.md)。
